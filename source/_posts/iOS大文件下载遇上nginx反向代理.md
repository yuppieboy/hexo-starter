---
title: iOS大文件下载遇上nginx反向代理
date: 2016-07-08 10:44:15
categories: iOS
tags: nginx

---

内网部署```nginx```，直接挂上2g多的zip包，```iOS```下载正常，将zip包挂到公司服务器，下载到49.1%（差不多1.07G）就中断链接，于是就各种猜测原因。

- 文件下载代码写的有问题？

	```NSURLSessionDownload```,```NSURLConnection```,```AFN```换了个遍都一样,虽然没有找到原因，但是发现```NSURLSessionDownload```下载时占用内存比```NSURLConnection```少，机制更优。

- 服务器的问题？

	安卓可以正常下载，```windows```浏览器可以正常下载，```safari```下载到49.1%同样网络中断？what？开始抓包，发现没有异常，只有```nginx```版本不一样，后来发现公司服务器用了```反向代理```。
	
	google后尝试写改配置```nginx.conf```，关闭buffer，```proxy_buffering off```，可以正常下载了。再尝试开启buffer，然后配置相关参数，也能正常下载，下载的速度和配置的参数有关系，配置参考如下:
	
		proxy_cache参数配置文件/usr/local/nginx/conf/proxy.conf
 
		proxy_temp_path /home/proxy_temp_dir; 
		proxy_cache_path /home/proxy_cache_dir levels=1:2 keys_zone=cache_one:500m inactive=1d max_size=30g; 
		client_body_buffer_size 512k; 
		proxy_connect_timeout 60; 
		proxy_read_timeout 60; 
		proxy_send_timeout 60; 
		proxy_buffer_size 32k; 
		proxy_buffers 4 64k; 
		proxy_busy_buffers_size 128k; 
		proxy_temp_file_write_size 128k; 
		proxy_next_upstream error timeout invalid_header http_500 http_503 http_404; 
		proxy_cache cache_one; 
		
		
至此事情告一段落。
