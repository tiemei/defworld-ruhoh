---
title: 生活中多人购物一人付账算钱程序
date: '2013-06-03'
description: 
categories: 'funny'
---

几个朋友一起旅行或者购物吃个烧烤啥的，账总是一个人先付掉，AA的话支付宝
的AA功能还行，不过如果其中的物品有的是甲的，有的是乙的，有的需要某几个
人均摊，就麻烦了。  
tiemei经常跟朋友出去shopping，回来算账就一堆乱麻，程序员嘛，喜欢偷懒，
写个程序自算账，交给机器做，我就去享受买回来的好吃的了。  

**账单**  

    6   jia   大号印刷背心袋
    8.6 wang  八宝茶
    14.6 all  咸鸭蛋
    4.9 jia  干柠檬
    2.7 all   鸡蛋面
    12.5 jia  酒
    9.9 all   馒头
    3.9 jia   酸奶
    15  all   刀切馒头
    4.9 all   鸡蛋面
    25.55 all  八宝米
    16.33 jia,wang 凉菜
    4.7  wang  陈皮
    3.3 jia    枣粽
    2.53  all  榨菜
    3.9  jia   肉粽
    6    wang  老爷爷的餐巾纸
    -----------------------
    140.9

**算账程序**  

````ruby
#!/Users/jiadongkai/.rvm/rubies/ruby-2.0.0-p0/bin/ruby

items = IO.readlines('wumeizhangdan')

result = {}
split="\t\t"
isPrices = false
expectedPrices = 0
realPrices = 0

items.each do |item|
  
  # 总价
  if isPrices
    expectedPrices = item.to_f
    if realPrices != expectedPrices
      puts "总价计算出错，期望总价 #{expectedPrices}，实际总价#{realPrices}"
    end
    break
  end

  # 总价前一行
  if(item.start_with?("--"))
    isPrices = true
    next
  end
  
  puts "统计 #{item}"
  colums = item.split(/\s+/)
  price = colums[0].to_f
  who = colums[1]
  name = colums[2]

  # 几个人平摊一个商品
  whos = who.split(",")
  if whos.size > 1 
    ava_price = price / whos.size
  
    whos.each do |who|
      result[who] ||= [0, '']
      result[who][0] += ava_price
      result[who][1] += "#{ava_price}#{split}#{name}|总价#{price}\n"
    end
    
    realPrices += price
    next
  end
  result[who] ||= [0, '']
  result[who][0] += price
  result[who][1] += "#{price}#{split}#{name}\n"

  realPrices += price
end

f = File.new('zhangdanresult', 'w')
result.each do |who, money_items|
  f.write("#{money_items[1]}")
  f.write("--------------------\n")
  f.write("#{money_items[0]}#{split}#{who}\n")
  f.write("++++++++++++++++++++\n")
end
````
  
代码写的仓促，各位凑活看  
  
**结果**  

    6.0		大号印刷背心袋
    4.9		干柠檬
    12.5		酒
    3.9		酸奶
    8.165		凉菜|总价16.33
    3.3		枣粽
    3.9		肉粽
    --------------------
    42.66499999999999		jia
    ++++++++++++++++++++
    8.6		八宝茶
    8.165		凉菜|总价16.33
    4.7		陈皮
    6.0		老爷爷的餐巾纸
    --------------------
    27.465		wang
    ++++++++++++++++++++
    14.6		咸鸭蛋
    2.7		鸡蛋面
    9.9		馒头
    15.0		刀切馒头
    4.9		鸡蛋面
    25.55		八宝米
    2.53		榨菜
    --------------------
    75.18		all
    ++++++++++++++++++++
