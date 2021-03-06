#   ActiveRouter简介    　
当前版本: Beta 0.1	by usher.yue	

Email:   usher.yue@gmail.com		
			
<br>
##  <b>简单介绍</b>		
### 一、通过ActivedRouter可以实现什么功能？		

	1、可以集群服务器的监控,通过提供的web Dashboard可以时刻监控每一台服务器的负载、内存、网络、
	磁盘等信息。
	
        
	2、反向代理服务器功能,例如Nginx的功能,可以针对不同的域名对不同的业务服务器集群进行反向代理。
        
	3、Server & Client 模式组合下,可以用作服务器监控使用,并且可以设置监控报警,Server端可以选择性
	开启监控Dashboard,用于直观的web界面监控。
	
	4、服务器监控报警,通过配置每一台服务器的监控报警阀值,来触发不同的脚本,从而达到服务器报警功能,
	后期支持:短信报警、邮件报警等等。
        
	5、实现基于真实负载的反向代理和请求路由。 支持 Random 和 Alived 模式,分别代表随机转发和基于负载转发。
        
	6、快速配置反向代理服务,省去配置nginx反向代理的麻烦步骤,一般流量下的负载足以应付。 
        
##  二、快速入门		
###  2.1、编译安装
在系统下运行如下脚本,会在当前目录生成ActivedRouter二进制程序,然后我们配置相应的配置文件,并且运行二进制程序即可启动服务。

	linux64下    boot_linux64.sh
	darwin64下   boot_darwin64.sh
	linux32下    boot_linux32.sh
## 三、详细配置教程
</br>
 3.1、[ActivedRouter实现反向代理服务器](www.xxx.com)
 		
 	
 3.2、[ActivedRouter实现服务器监控功能](www.xxx.com)		
 		
 3.3、[ActivedRouter实现基于真机负载的反向代理服务器](www.xxx.com)
	
	
 3.4、[ActivedRouter配合第三方语言实现API路由](www.xxx.com)	
               
##  四、工作模式和配置文件
### 4.1、http/https反向代理模式(Reserve Proxy),类似nginx的反向代理功能。

`编译命令： boot_*.sh 选择不通平台的boot文件开始编译安装`
`运行命令： ActivedRouter --runmode=proxy 运行反向代理服务`
`配置文件：`
        
 	{	
 		"http_switch":"on",           //http开关 on off
  		"proxy_addr":"127.0.0.1:80",  //http监听端口
		"proxy_method":"random",      //proxy方法 random 混合模式下可支持alived方法
		"https_switch":"off",        //是否开启https  on开启https 支持
		"https_crt":"a.crt",         //https证书
		"https_key":"a.key",         //https key
		"https_proxy_addr":"127.0.0.1:443",//https监听地址
		"reserve_proxy":[
			{
				"domain":"1.12xue.com",
				"clients":[
				    {
					   "host":"12xuetest.com",
					   "port":"80"	
			        }
				]
			},
			{
				"domain":"2.12xue.com",
				"clients":[
				    {
					   "host":"12xuedev.com",
					   "port":"80"	
			        }
				]
			},
			{
					"domain":"proxy.12xue.com",
					"clients":[
				 	   {
						   "host":"goapi.com",
						   "port":"80"	
		  		  	    }
					]
			}
		
		]
	}
### 4.2、server和client模式可以配合完全服务器监控,提供web仪表盘。
`客户端运行: ActivedRouter --runmode=server `	
`client.json`	

		{
			"domain":"wwww.xxx.com",  //客户端服务器的域名可空
			"cluster":"UIA集群",		  //服务器集群的标志 可自定义
			"router_list":[          //要连接的远程路由服务器列表
			"127.0.0.1:8888",
			"172.16.200.202:9999"
			]
	   }
		
`服务器运行: ActivedRouter --runmode=client `	
`server.ini`	

		#服务器模式下的配置选项
		#路由服务器监听的地址
		host=127.0.0.1
		#服务器监听的端口号
		port=8888   
		#服务模式 moniter & router  
		srvmode=router
		#http 提供http路由服务的ip,端口。
		httphost=127.0.0.1
		httpport=8080



### 4.3、服务器监控模式触发脚本
`当监控服务器下,路由服务器开启srvmode=moniter之后才会触发`		

	{   
			"script":[
		{
				"host":"127.0.0.1",
				"hookscript":[
					{
						"eventtarget":"disk",
						"attr":"used",
						"gt":"15",
						"callback":"ls"
					},
					{
						"eventtarget":"load",
						"attr":"load",
						"gt":"1.5",
						"callback":"ls"
					},
					{
						"eventtarget":"mem",
						"attr":"used",
						"gt":"75",
						"callback":"netstat -ant"
					}
				]
			}
		]
	}
##  <b>运行模式</b>
        服务器模式和客户端模式
<table >
   <thead>
     <tr>
        <th>运行模式</th>
        <th>介绍</th>
     </tr>
   </thead>
   <tbody>
    <tr>
      <td>
         ServerMode
      </td>
      <td>
            在服务器模式下监听客户端的状态 <br/>
            1、 第三方应用通过sdk提供的功能进行自动路由到合适的api服务器上,此处属于代理转发功能。<br/>
            2、 第三方应用通过sdk提供的功能获取到一个空闲合适的服务器域名或ip。<br/>
            3、 监听客户端模式下的服务器的服务状态
      </td>
    </tr>
    <tr>
      <td>
         ClientMode
      </td>
      <td>
            在客服端模式下通告服务器当前服务状态 <br/>
            1、 启动挂在到路由服务器 可以挂在到一个至个服务器上。<br/>
            2、 实时通告路由服务器当前服务器信息,用作路由分析。
      </td>
    </tr>
	<tr>
      <td>
         Reserve Proxy
      </td>
      <td>
         内网集群配置反向代理功能
      </td>
    </tr>
    <tr>
      <td>
         MixProxy
      </td>
      <td>
         基于监控模式下,真机负载的反向代理服务。
      </td>
    </tr>
    <tr>
      <td>
         ThirdPartSDK
      </td>
      <td>
        针对第三方提供php golang 等sdk,提供基于路由负载、以及反向代理负载的http请求机制。
      </td>
    </tr>
   </tbody>
</table>    
##  提供api服务器监控功能可以实时返回各服务器状态  
<table >
   <thead>
     <tr>
        <th>监控功能</th>
        <th>介绍</th>
     </tr>
   </thead>
   <tbody>
    <tr>
      <td>
         虚拟内存
      </td>
      <td>
             时刻监控服务器的虚拟内存
      </td>
    </tr>
     <tr>
      <td>
         load average
      </td>
      <td>
             时刻监控服务器的负载状态
      </td>
    </tr>
    <tr>
      <td>
         网络连接
      </td>
      <td>
        时刻监控服务器的网络连接状态
      </td>
    </tr>
    <tr>
          <td>
         CPU使用率
      </td>
            <td>
         时刻监控CPU的使用率
      </td>
    </tr>
    <tr>
      <td>
         磁盘状态
      </td>
      <td>
        时刻监控服务器的磁盘存储容量
      </td>
    </tr>
    <tr>
      <td>
         ThirdPartSDK
      </td>
      <td>
         为应用提供监控接口,可直接展示监控内容
      </td>
    </tr>
   </tbody>
</table>
