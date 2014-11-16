---
title: url encode
date: '2014-11-16'
description: 
categories: 'http'
---

## BitSet

{[0],[0],[0],[0],[0],[0],[0],[0]} * 4

java用long[]存储（取名word），而bitset可以理解为一个bit[]，这里就需要有一个映射bitIndex -> wordIndex。需要注意的是，例如7并不是直接换成bit形式，而是位于bit[]的第七位。  

```java
private long[] words; // 真实的用来存储bitIndex的内存空间
private final static int ADDRESS_BITS_PER_WORD = 6;
private final static int BITS_PER_WORD = 1 << ADDRESS_BITS_PER_WORD; // 1 << 2 = 2^6 = 64，每个long存储存64个bitIndex

private static int wordIndex(int bitIndex) {
    return bitIndex >> ADDRESS_BITS_PER_WORD; 
}

private void expandTo(int wordIndex) {
    int wordsRequired = wordIndex+1;
    if (wordsInUse < wordsRequired) {
        ensureCapacity(wordsRequired);
        wordsInUse = wordsRequired;
    }
}

 public void set(int bitIndex) {
    if (bitIndex < 0)
        throw new IndexOutOfBoundsException("bitIndex < 0: " + bitIndex);

        int wordIndex = wordIndex(bitIndex);
    expandTo(wordIndex);

    // 1L << bitIndex， 1L << 64是一个循环，实际上等于 1L << (bitIndex % 64)
    // 把bitIndex插入到合适的bit位
    words[wordIndex] |= (1L << bitIndex); // Restores invariants

    checkInvariants();
}

// 判断是否存在
public boolean get(int bitIndex) {
    if (bitIndex < 0)
        throw new IndexOutOfBoundsException("bitIndex < 0: " + bitIndex);

    checkInvariants();

    int wordIndex = wordIndex(bitIndex);
    return (wordIndex < wordsInUse)
        && ((words[wordIndex] & (1L << bitIndex)) != 0);
}

```


## UrlEncode

见:http://www.w3.org/TR/html40/appendix/notes.html#non-ascii-chars  

```java
public static String encode(String s, String enc) 
    throws UnsupportedEncodingException {

    boolean needToChange = false;
    StringBuffer out = new StringBuffer(s.length());
    Charset charset;
    CharArrayWriter charArrayWriter = new CharArrayWriter();

    if (enc == null)
        throw new NullPointerException("charsetName");

    try {
        charset = Charset.forName(enc);
    } catch (IllegalCharsetNameException e) {
        throw new UnsupportedEncodingException(enc);
    } catch (UnsupportedCharsetException e) {
        throw new UnsupportedEncodingException(enc);
    }

    for (int i = 0; i < s.length();) {
        int c = (int) s.charAt(i);
        //System.out.println("Examining character: " + c);
        // dontNeedEncoding包含：[a-zA-Z0-9 -_.*]
        if (dontNeedEncoding.get(c)) {
            if (c == ' ') {
                c = '+';
                needToChange = true;
            }
            //System.out.println("Storing: " + c);
            out.append((char)c);
            i++;
        } else {
        // convert to external encoding before hex conversion
        do {
            charArrayWriter.write(c);
            /*
             * If this character represents the start of a Unicode
             * surrogate pair, then pass in two characters. It's not
             * clear what should be done if a bytes reserved in the 
             * surrogate pairs range occurs outside of a legal
             * surrogate pair. For now, just treat it as if it were 
             * any other character.
             */
            // 处理一些UTF-16的字符：http://en.wikipedia.org/wiki/UTF-16
            if (c >= 0xD800 && c <= 0xDBFF) {
            /*
              System.out.println(Integer.toHexString(c) 
              + " is high surrogate");
            */
            if ( (i+1) < s.length()) {
                int d = (int) s.charAt(i+1);
                /*
                  System.out.println("\tExamining " 
                  + Integer.toHexString(d));
                */
                if (d >= 0xDC00 && d <= 0xDFFF) {
                /*
                  System.out.println("\t" 
                  + Integer.toHexString(d) 
                  + " is low surrogate");
                */
                    charArrayWriter.write(d);
                i++;
                }
            }
            } 
            i++;
        } while (i < s.length() && !dontNeedEncoding.get((c = (int) s.charAt(i))));

        charArrayWriter.flush();
        String str = new String(charArrayWriter.toCharArray());
        byte[] ba = str.getBytes(charset);
        for (int j = 0; j < ba.length; j++) {
            out.append('%');
            char ch = Character.forDigit((ba[j] >> 4) & 0xF, 16);
            // converting to use uppercase letter as part of
            // the hex value if ch is a letter.
            if (Character.isLetter(ch)) {
            ch -= caseDiff;
            }
            out.append(ch);
            ch = Character.forDigit(ba[j] & 0xF, 16);
            if (Character.isLetter(ch)) {
            ch -= caseDiff;
            }
            out.append(ch);
        }
        charArrayWriter.reset();
        needToChange = true;
        }
    }
```