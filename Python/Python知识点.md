###python学习

获取指定的日期或者是时间代码：  

		from datetime import datetime
		dt=datetime(2016,2,22,19,49)
		print(dt)
		//创建了一个当前时间，并且打印出来
		
在计算机中时间一般都是以数字来进行表示的，相对时间就是以1970年的时间为一个界限，相当于timestamp。也可以认为是当前的时间减去1970年的时间  
转换的代码如下所示：  

		from datetime import datetime
		t=1429417200.0
		print(datetime.fromtimestamp(t))
		
		//timestamp实际上是一个浮点数，没有时区的概念，但是  
		//datetime是存在时区的概念的，上述转换为了本地的时间
		//但是如果在其他的时区将会产生错误的时间，所以有必要要  
		//进行时区转换的操作  
		
时区转换  

		//获取到utc 时间，并且将时间强制设定到utc+8:00
		utc_dt=datetime.utcnow().replace(tzinfo=timezone.utc)
		//转换为北京时区
		utc_dt.astimezone(timezone(timedelta(hours=8)))  
		转换时区的关键在于拿到一个`datetime`要获取其正确的时区，然后要强制设置时区，作为基准时间  
		利用带时区的时间，任何带时区的时间都是可以正确的转换

使用集合collections  

collection是Python内建的一个集合模块，提供了很多有用的集合类  

> namedtuple ,tuple表示的是一个内容不变的集合，一个二维的坐标点就可以表示为：  
> p=(1,2)  
> 但是很难看出来这是表示的是一个坐标，但是定义个class同时也显得太小题大做了，所以这时候最简单的方式是使用namedtuple  
> 

		from collectionn import namedtuple
		Point=namedtuple('Point',['x','y'])  
		p=Point(1,2)  
		//这种简单的写作方式可以明确的声明一个类似C++ 
		Struct结构体  
		
deque 
 
使用list 存储数据的时候，按照索引访问元素的速度较快，但是插入以及删除元素的速度就变得比较慢，因为list是线性存储，数据量大的时候，插入以及删除的效率就变得很低  

deque是为了实现高效的实现插入以及删除操作的双向列表，适合用于队列以及堆栈    


defaultdict   

使用dict过程中，如果不存在某一个key 的时候，就会抛出`keyError`，如果希望在key不存在的时候，能够返回一个默认值，就可以使用defaultdict：  

		from collection import defaultdict
		dd=defaultdict(lambda:'N/A')
		dd['key1']='abc'
		dd['key1'] #key1已经存在 ,此时打印输出结果为'abc' 
		dd[‘key2’] #key2不存在，所以返回值为默认值‘N/A’
		//需要注意的地方在于出
		
使用PIL模块的知识点  

由于PIL仅仅支持到2.7，所以一般在3.x之后安装需要使用的命令行如下：  

		pip3 install pillow 
		

> 最常见的就是操作图像  

		from pil import Image 
		im=Image.open('test.jpg')
		w,h=im.size
		print('当前图片的尺寸为%d,%d' % (w,h))
		im.thumbnail((w/2,h/2))
		print('Resize image to : %s*%s' % (w/2,h/2))
		im.save('thumble.jpg','jpeg')
		//读取图片文件，生成缩略图，并且保存
		
		同时也是支持其他的功能，比如可以支持切片，文字，调色板等其他的特殊效果  
		
		from PIL import Image,ImageFilter
		im2=Image.open(‘test.jpg’)
		im2=im.filter(ImageFilter.BLUR)
		im2.save('blur.jpg','jpeg')
		//生成一个模糊背景图片  
		
> 同时PIL也提供了一系列的绘图方法，可以直接进行绘图，比如可以生成字母的验证码  

详细的使用的使用PIL文档可以参见  [PIL详细使用说明](https://pillow.readthedocs.org/)	  


TCP   

大多数可靠的链接都是TCP进行链接，创建socket代码一般如下：  

		import socket 
		#创建一个socket链接  
		s=socket.socket(socket.AF_INET,socket.SOCK_STREAM)
		#建立链接  
		s.connect(('www.baidu.com',80))
		#创建socket的是 AF_INET指定的是使用IPV4的协议，如果要使用IPv6，则需要指定  
		#为AF_INET6。socket.SOCK_STREAM指定了使用面向流的协议，这样一个socket对象  
		#就创立完成了，但是还没建立链接  
		
客户端主动发起链接，就必须要知道服务器的IP地址以及端口号  

		s.connnect('www.baidu.com',80)
		#参数必须是一组tuple,包含了地址以及端口号  
建立了链接之后就可以服务器端发送消息，要求其返回指定的内容  

TCP链接建立的是双向通信链接，双方都是可以同时给对方发送数据，但是具体的协调过程，需要根据具有的协议来决定，HTTP协议规定必须是客户端先发送消息，服务器收到消息之后才返回数据给客户端  
并且发送的文本格式必须要符合HTTP的规定，如果格式没有问题，就可以接收服务器返回的数据  

		#buffer 
		buffer = []
		while True:
			#每次最多接收1K
			d=s.recv(1024) //定义接收多少字节的数据  
			if d:  
				buffer.append(d)
			else: 
				break
		date=b''.join(buffer)
		
接收完毕之后，需要调用`close()`来关闭socket。这样，一次完成的网络通信过程就结束了  
接收到数据包括了HTTP头以及网页的本身，我们只需要把HTTP头以及网页分离一下，把Http头打印出来网页内容保存到文件中  

		header,html=data.split(b'\r\n\r\n',1)
		print(header.decode('utf-8'))
		#将数据写入到文件
		with open ('sina.html','wb') as f:
			f.write(html)
		//此时就网页保存到了本地，打开本地的网页，就能显示当前请求的网页信息  
		
服务器  

和客户端编程相比，服务器编程要复杂一些，服务器首相要绑定一个端口并且需要监听来自其他客户端的链接，如果某一个客户端链接过来了，服务器就需要与该客户端建立socket链接，随后的通信就是靠这个socket链接。  

所以服务器一般会打开固定端口来进行监听，每来一个客户监听就创建socket链接，由于服务器会有大量的来自客户端的链接，所以，服务器要能够区分是和哪一个客户端绑定的。一个socket依赖了四项：  
服务器地址、服务器端口、客户端地址、客户端端口来唯一确定一个socket。  

		import socket 
		
		s=socket.socket(socket.AT_INET,socket.SOCK_STREAM)
		s.bind('127.0.0.1',9999)
		
		s.listen(10)
		print('waiting for connection.... ')
		
		while True:
			sock,addr=s.accept()
			t=threading.Thread(target=tcplink,args=(sock,addr))
			t.start()
		
每个链接都需要创建新的线程或者进程来进行处理，否则在单线程中，无法接受其他客户端的链接。

###Web开发  

Web开发目前是当前软件开发中较为重要的一环，web开发也经历了多个阶段：  

1、 静态的web 页面，有文本编辑器直接编辑并且生成的静态的HTML页面  
2、 CGI： 由于静态的web页面无法进行交互，要处理与用户发送的动态数据，于是就产生了common Gateway interface，简称就是CGI ，c++或者c进行编写  
3、ASP、JSP、PHP 由于web的运用特点是修改特别频繁，使用低级语言不适合进行web开发，而脚本语言由于开发的效率较高，并且与HTML结合的较为紧密，因此迅速JSP成为了较为流行的开发模式  
4、MVC 为了解决用脚本语言嵌入HTML造成的可维护性差的问题，web应用MVC来简化Web端的开发  

目前，web 开发技术仍然在快速发展的过程中，由于Python是一种解释型的脚本语言，开发的效率比较高，所以非常适合来进行web开发。	  


一般使用框架的过程中需要展示图形页面，所以需要使用模板技术，也即是支持了MVC的设计模式。  

因为在python中支持关键字作为参数，所以很多web框架允许传入关键字参数，然后在框架的内部组装出一个`dict`作为Model。如以下的使用Flask框架进行搭建的动态页面   

和Web开发的框架一样，支持python的模板也有很多种，FLask默认支持的模板是Jinja2 ，所以需要首先安装模板  


异步IO操作  

由于CPU的速度远远快于磁盘，网络等Io，在一个线程中，CPU执行代码的速度极快，然而遇到IO阻塞。如读写文件，发送网络数据的时候，就需要等待IO操作完成之后才能继续下一步操作，这种情况称为同步Io  

在Io操作中，如果当前线程被挂起，其他需要CPU执行的代码就无法被当前线程执行了，因为一个IO操作就阻塞了当前线程的操作，所以必须使用多线程或者是多进程来并发的执行代码，为多个用户服务，每个用户都会分配一个线程，如果线程挂起的，其他用户的线程则不收到影响  

多线程以及多进程的模型虽然解决了并发的问题，但是系统不能无上限的增加线程，由于系统切换进程的花销也是很大，所以，一旦线程的数量过多，CPU的时间就花在线程切换上了，真正执行代码的时间就少了，结果导致性能的严重下降  

另一种解决的办法就是异步Io，当代码需要执行一个较为耗时的IO操作时，它只发出IO指令，并不等待IO结果，然后就去执行其他的代码，一段时间之后，当Io结果返回的时候，再通知CPU进行处理  

所以，按照普通的书写顺序写出的代码实际上是没法完成异步IO的操作  

		do_some_code()
		f=open('test.txt','r')
		r=f.read()
		do_some_code()
		
所以在异步Io中需要一个消息循环，在消息循环的过程中，主线程不断重复的读取消息-处理消息的这个过程，如以下的代码：  

		loop=get_event_loop()
		while True:
			event=loop.get_event()
			process_event(event)
		
		 

