---
title: markdown所用图片上传自动化脚本
date: '2013-05-18'
description:
categories: "funny"
tags: script
---

## 我在做一件什么事?
我在写markdown笔记时，有些本地图片需要嵌入，而上传并获取外链实在是一个繁琐的
过程。这明显是可以自动化掉的过程。  
更进一步，图片上传和获取外链自动化掉，但还需要手动拷贝替换makrdown中的图片链接，这个过程亦可自动化掉。

## 思路
调用[flickr](http://flikr.com/)将图片上传并获取外链

## 操作步骤
### flickr授权验证
我写了一个工具，方便拿到flickr授权后的access_token & access_secret, [here](/tools)  
或者你写个脚本来拿，类似下面我用ruby实现的这段：  

    require 'flickraw'

    FlickRaw.api_key = "933e8bb7bee8381c46a9b3cb1e321ed6"
    FlickRaw.shared_secret="99bb0541ec349ae5"

    # authentication 
    token = flickr.get_request_token
    auth_url =  flickr.get_authorize_url(token['oauth_token'], :perms => 'delete')

    puts "Open this url in your process to complete the authication process : #{auth_url}"

### flickr图片自动上传&获取分享外链
用ruby实现，所以若想成功运行请确保安装了ruby，且`gem install flickraw`  

    require 'flickraw'

    FlickRaw.api_key = "933e8bb7bee8381c46a9b3cb1e321ed6"
    FlickRaw.shared_secret="99bb0541ec349ae5"

    flickr.access_token = "72157633501230371-9da780bd34f" # !!! 填你的acess_token
    flickr.access_secret = "f448a228f71afecd" # !!! 填你的acess_secret

    puts 'photo path:'
    photo_path = gets.strip # 命令行输入图片路径

    puts 'photo tilte:'
    photo_title = gets.strip 
    if photo_title.empty?
        photo_title = File.basename(photo_path, ".*") # 若没有输入title，默认用图片名
    end

    puts 'photo description:'
    photo_description = gets.strip


    # 所有图片都存在这个set中
    SET_TITLE="markdown_imgs"
    SET_DESCRIPTION="iFlikcr应用，tiemei支持，详见defworld.com"

    photo_id = flickr.upload_photo photo_path, :title => photo_title, 
                                   :description => photo_description 

    
    set = nil 

    has_set = false
    # !!! 换成你自己的user_id，右侧可拿到：http://www.flickr.com/services/api/explore/flickr.auth.oauth.getAccessToken
    flickr.photosets.getList(:user_id => '79104125@N05').each do |e| 
      if e.to_hash['title'] == SET_TITLE
        has_set = true
        set = e.to_hash
      end 
    end
    # title相同的set还不存在
    if not has_set
      set = flickr.photosets.create(:title       => SET_TITLE, 
                                    :description => SET_DESCRIPTION,
                                    :primary_photo_id    => photo_id)
    else
      flickr.photosets.addPhoto(:photoset_id => set['id'], 
                                :photo_id    => photo_id)
    end

    # 获取图片外链
    info = flickr.photos.getInfo(:photo_id => photo_id)
    puts FlickRaw.url(info) # !!! 替换这个方法可选择外链的图片尺寸

演示示例：  
  
![auto upload imgs & get share url demo](http://farm8.staticflickr.com/7401/8749136193_2d19cbb140.jpg)  
  
另，返回的share url的图片支持是可调节的:  
![flickr图片尺寸规则](http://farm3.staticflickr.com/2833/8749133645_83b09f7396.jpg)  
  
对应到代码里是`puts FlickRaw.url(info)`这行:  
![flickraw接口](http://farm8.staticflickr.com/7368/8749149989_2f285b06da.jpg)  


## 遇到的坑
* pi的默认时区不是CST

        cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
        如果你之前用date修改了时间没改时区，惊醒oauth验证会报错，
        改完时区后不仅需要手动date修改时间，还需要进到root下去修改
        sudo -s; date MMDDhhmmYYYY

## tips
* install ruby in pi

        sudo apt-get install ruby 

* install node.js in pi

        sudo apt-get install g++

        wget http://nodejs.org/dist/v0.10.7/node-v0.10.7.tar.gz
        tar -xzvf node-v0.10.7.tar.gz
        cd node-v0.10.7
        ./configure --prefix=/usr/local/node
        make 
        sudo make install

ok ! enjoy it !  
