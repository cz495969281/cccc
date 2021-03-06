1.影响mysql性能的几个方面
  1.服务器硬件   CPU不够快 内存不够多 磁盘IO太慢
  
  2.服务器系统   
  
  3.数据库存储引擎的选择（mysql最大的特别是插件式存储引擎的设计）
  		MyISAM：不支持事物的  使用的是表级锁
  		InnoDB:事物级存储引擎，完美的支持行级锁，以及事物ACID特性
  4.数据库服务器的参数   有些参数的影响是微乎其微的  有些参数影响是决定性的

  5.数据库结构的设计和SQL语句编写和优化
  		慢查询是大多数数据库的罪魁祸首，大多数都是数据库的表结构设计不合理导致的

2.CPU资源和可用内存大小
   如何选择CPU?
      1.看我们的应用是否是CPU密集型的；
      		CPU密集型，首先要加快SQL语句的处理速度，显然我们要的事更好的CPU,而不是更多的CPU
			不支持多CPU对同一SQL并发的处理(mysql5.6或者5.7已结有了很大的改善)
	  2.我们系统的并发量是如何的？
	  		这里是并发量和QPS是有区别的，QPS是每秒同时处理SQL的数量 这里的并发是纳秒级别，具体的情况还要看SQL语句是执行情况
	  		MYSQL目前大量应用在web中，并发量也是非常大的，cpu的数量就显得比频率重要些
	  3.我们所使用的Mysql的版本
	  	    mysql5.6或者5.7对支持多核cpu的处理已经有了很大的改善

	  4.注意点：在64位的架构的CPU上运行使用32位的服务器版本，这种情况是经常发生的，尤其是在云服务器上和一些虚拟主机上，都是运维人员在安装的时候导致的；在32的操作系统上不能使用大量的内存，任何一个单独的进程都不能占用超过4G以上内存，大家要知道mysql是一种单线程的服务，使用32位系统会对mysql性能有一个极大的限制
  
  内存的大小直接影响数据库的性能高，目前内存的IO远远大于磁盘IO
  把数据放入内存中会大大提高服务器的性能
  常用Mysql存储引擎
     MyISAM ----> 索引 -----> 内存中  （把索引缓存到内存中）
        |
        |-------> 数据 ----->  OS  (把数据用操作系统进行缓存)


	  InnoDB ----- >索引 -----> 
	     |                      内存   （把索引和数据都放在内存中，提高数据库的运行效率）
	     |--------->数据 ----->

   提示:内存虽然是越多越好，但是对性能的影响也是有限的，并不能通过增加内存来无限增加性能


  缓存不但对读有益处同时对写也是有益处的，在缓存中的误区是只有读在缓存中才会受益，如果有足够的内存就可以完全避免磁盘读取的请求，如果所有的数据都放在缓存中，所有的读操作都会在缓存中命中，但是写入是不同的问题，写入也可以像读一样在内存中完成，但是迟早要把写在内存中的数据写入磁盘中，但是虽然是这样的，缓存还是对写入有好的影响，它不能避免磁盘的写入操作，但是可以对写入操作进行延缓，把多次写入改为一次写入；比如每个电商网站中对每个商品的浏览量的计数器，每次用户查看这个商品的时候都会进入这个计数器的话，会造成大量的IO操作，但是我们通过只更新内存中的计数器，当计数器达到一定值的时候统一写入磁盘,提高了服务器的性能，这就是在缓存池中对写入的多次操作变为一次操作


  3.磁盘的配置和选择
	1.传统机器磁盘   特点：最常见  使用最多  价格低  单盘的存储空间较大  读写速度相对较慢
				
					传统机器硬盘读取数据的过程：
							1.移动磁头到磁盘表面上的正确位置 （读取时间）
							2.等待磁盘旋转，使得所需的数据在磁头之下读取数据  （读取时间）
							3.等待磁盘旋转过去,所有所需的数据都被磁头读出 （传输速度）

					如何选择传统机器硬盘
					  1.存储容量
					  2.传输速度
					  3.访问时间
					  4.主轴转速
					  5.物理尺寸

 	2.使用RAID增加传统机器硬盘的性能
 	    1.什么是RAID?
 	       是磁盘冗余队列的简称，简单来说RAID的作用就是可以把多个容量较小的磁盘组成一组容量更大的磁盘，并提供数据冗余来保证数据完整性的技术

 	     RAID5 最常见的一种 （建议使用在从数据库上，以写读为主）  又称为分布式奇偶校验磁盘阵列
 	     	通过分布式奇偶校验块把数据分散到多个磁盘上，这样如果任何一个盘数据失效，都可以从奇偶校验块中重建，但是如果两块磁盘失效，则整个卷的数据域都无法恢复


		RAID10又称为分片的镜像
			它是对磁盘先做RAID1之后对两组RAID1的磁盘再做RAID0，所以对读写都有良好的性能，相对于RAID5重建起来更简单，速度也更快

	3.固态存储 闪存(Flash Memory)
		特点：1.相比机械磁盘固态磁盘有更好的随机读写性能（固态存储随机读的性能远远好于它写的性能）
			 2.相比机械磁盘固态磁盘能更好的支持并发，固态存储支持更多的并发操作，只有在大的并发情况下，才能体现出固态存储大的吞吐量，大并发下体现出随机IO性能
			3.相比机械磁盘固态磁盘更容易损坏，固态存储每次写入前都要做一些擦除操作，所以对固态存储有一些损耗，长时间密集的写操作，很容易损坏固态存储设备，随着使用空间的增加，性能也会有所下降

		SSD(固态硬盘)
		   主要特点：使用SATA接口，可以替换传统磁盘而不需任何改变，可以直接插在服务器上使用
		   使用场景：适用于存在大量随机I/O的场景，解决单线程负载i/o瓶颈
			
	4.网络存储SAN(存储区域网络)和NAS(存储附加网络) -----> web文件存储设备
		SAN(storage Area Network)和NAS(Network-Arrached Storage)是两种外部文件存储设备加载到服务器上的方法

		区别：
			SAN设备通过光纤连接到服务器，设备通过块接口访问，服务器可以将其当做硬盘使用
			特别：可以承受大量顺序读写，读写I/O、 缓存、I/O合并、随机读写慢、不如本地RAID磁盘

			NAS设备使用网络连接，通过基于文件的协议如NFS或SMB来访问，这类通常会有网络传输的延迟

		网络存储适用的场景：数据库备份