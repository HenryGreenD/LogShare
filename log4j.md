# Log4j #

## 一、配置文件 ##
    # 配置根Logger
	log 4j.rootLogger = [level] , appenderName1,appenderName2

	# 配置日志消息输出目的地Appender
	log4j.appender.appenderName=fully.qualified.name.of.appender.class
	log4j.appender.appenderName.option1 = value1	
	...
	log4j.appender.appenderName.optionN=valueN

	# 配置日志消息的格式（布局）
	log4j.appender.appenderName.layout=full.qualified.name.of.layout.class
	log4j.appender.appenderName.layout.option1=value1
	...
	log4j.appender.appenderName.layout.optionN=valueN

#### 1、[level]为日志输出级别，共有OFF、FATAL、ERROR、WARN、INFO、DEBUG、TRACE和ALL(从左到右级别越来越低) ####
    1). OFF　  最高等级的，用于关闭所有的日志记录
    2). FATAL  严重的错误事件将会导致系统的继续运行
    3). ERROR  虽然发生错误事件，但是仍然不影响系统的继续运行。打印错误和异常消息，如果不想输出太多的日志，可以使用这个级别
    4). WARN   表明会出现潜在的错误的情形，有些消息不是错误消息，但是也要给程序员的一些提示
    5). INFO   消息在粗粒度级别上突出强调应用程序的运行过程，可以用于生产环境中输出程序运行的一些重要消息
    6). DEBUG  指出细粒度消息事件对调试应用程序是非常有帮助的，主要用于开发过程中打印一些运行消息
    7). TRACE  指定比DEBUG更精细的消息时间，1.2.12之前的版本是没有的，一般不会使用
    8). ALL    最低等级的，用于打开所有的日志记录

#### 2、Appender为日志消息输出目的地，Log4j提供的appender有以下几种 ####
	1). org.apache.log4j.ConsoleAppender(应用于控制台)
		- Threshold=WARN      指定日志消息的最低输出级别，默认为DEBUG
		- ImmediateFlush=true 表示所有消息都会被立即输出，设为false则不输出，默认值为true
		- Target=System.err   默认值是System.out
	2). org.apache.log4j.FileAppender(应用于文件)
		- Threshold=WARN      指定日志消息的最低输出级别，默认为DEBUG
		- ImmediateFlush=true 表示所有消息都会被立即输出，设为false则不输出，默认值为true
		- Appende=false       true表示消息追加到指定文件中，false表示将覆盖指定的文件内容，默认值是true
		- File=D：\\Log4j.log 指定消息输出到Log4j.log文件中
	3). org.apache.log4j.DailyRollingFileAppender(每天产生一个日志文件，应用于文件回滚)
		- Threshold=WARN      指定日志消息的最低输出级别，默认为DEBUG
		- ImmediateFlush=true 表示所有消息都会被立即输出，设为false则不输出，默认值为true
		- Appende=false       true表示消息追加到指定文件中，false表示将覆盖指定的文件内容，默认值是true
		- File=D：\\Log4j.log 指定消息输出到Log4j.log文件中
		- DatePattern='.'yyyy-MM 每月滚动一次日志文件，当月的日志文件名为Log4j.log，下月的文件名为Log4j.log.2018-10
		  * '.'yyyy-MM          每天
		  * '.'yyyy-ww          每周
		  * '.'yyyy-MM-dd       每天
		  * '.'yyyy-MM-dd-a     每天两次
		  * '.'yyyy-MM-dd-HH    每小时
		  * '.'yyyy-MM-dd-HH-mm 每分钟
	4). org.apache.log4j.RollingFileAppender(文件到达指定尺寸的时候产生一个新的文件)
		- Threshold=WARN      指定日志消息的最低输出级别，默认为DEBUG
		- ImmediateFlush=true 表示所有消息都会被立即输出，设为false则不输出，默认值为true
		- Appende=false       true表示消息追加到指定文件中，false表示将覆盖指定的文件内容，默认值是true
		- File=D：\\Log4j.log 指定消息输出到Log4j.log文件中
		- MaxFileSize=100KB   后缀可以是KB，MB或者GB。在日志文件到达该大小时，将会自动滚动，即将原来的内容移到Log4j.log.1文件中
		- MaxBackupIndex=2    指定可以产生的滚动文件的最大数，如设为2则可以产生Log4j.log.1,Log4j.log.2两个滚动文件和一个Log4j.log文件
	5). org.apache.log4j.WriterAppender(将日志消息以流格式发送到任意指定的地方)
	6). org.apache.log4j.jdbc.JDBCAppender(应用于数据库)
		- bufferSize=10 达到数量插入数据
		- Driver=com.mysql.jdbc.Driver 数据库驱动
		- URL=jdbc:mysql:///database   数据库URL
		- user=root	连接数据库用户名
		- password=123456 连接数据库密码
		- sql=INSERT INTO C_TASK_LOGINFO (UUID,CDATED,CLOGGER,CLEVEL,CMESSAGE) VALUES('%x','%d{yyyy-MM-dd HH:mm:ss}','%C','%p','%m')
		- layout=org.apache.log4j.Pattern 
	7). 自定义Appender
		- 继承AppenderSkeleton类
		- 指定你的appender是否需要layout
		- 如果某些属性必须同时激活，则应该在activateOptions()方法内完成
		- 实现close方法。它必须把closed字段的值设为true。记得释放所有资源
		- 可选地指定要使用的默认 ErrorHandler 对象。系统默认为OnlyOnceErrorHandler,它发送出第一个错误的消息并忽略其余的所有错误,错误消息将输出到 System.err
		- 编写append()方法的代码。这个方法负责附加日志记录事件，并在错误发生时负责调用错误处理程序

#### 3、Layout为日志输出格式，Log4j提供的layout有以下几种 ####
	1). org.apache.log4j.HTMLLayout(以HTML表格形式布局)
		- LocationInfo=true 输出Java文件名称和行号，默认值是false
		- Title=MyLogging 定义.html文件的title，默认是Log4J Log Messages
	2). org.apache.log4j.PatternLayout(可以灵活地指定布局模式)		
		- ConversionPattern=%m%n 设定以怎样的格式显示消息
		- 格式化符号说明：
		  * %p 输出日志消息的优先级，即DEBUG，INFO，WARN，ERROR和FATAL
		  * %d 输出日志消息点的日期或者时间，默认格式是ISO8601，也可以在其后指定格式，如%d{yyyy-MM-dd HH:mm:ss,SSS}
		  * %r 输出自应用程序启动到输出该log消息耗费的毫秒数
		  * %t 输出产生该日志事件的线程名
		  * %l 输出日志时间的发生位置，相当于%c.%M(%F:%L)的组合，例如test.TestLog4j.main(TestLog4j.java:10)
		  * %c 输出日志消息所属的类目，即全类名
		  * %M 输出产生日志消息的方法名
		  * %F 输出日志消息产生时所在的文件名称
		  * %L 输出代码中产生日志消息的行号
		  * %m 输出代码中指定的具体日志消息
		  * %n 输出一个回车换行符，Windows平台为"\r\n"，Unix平台为"\n"
		  * %x 输出和当前线程相关联的NDC(嵌套诊断环境)，尤其用到像java servlets这样的多客户多线程的应用中
		  * %% 输出一个"%"字符
		 另外，还可以在%与格式字符之间加上修饰符来控制其最小长度、最大长度和文本的对齐方式，如：
		  * c     指定输出category的名称，最小的长度是20，如果category的名称长度小于20的话，默认的情况下右对齐
		  * %-20c "-"号表示左对齐
		  * %.30c 指定输出category的名称，最大长度是30，如果category的名称长度大于30，将会将左边多出的字符截掉，但小于30的话也不会不空格
	3). org.apache.log4j.SimpleLayout(包含日志消息地级别和消息字符串)
	4). org.apache.log4j.TTCCLayout(包含日志产生地时间、线程、类别等消息)
#### 4、指定类的日志格式 ####
	1).log4j.logger.全类名=[level] , appenderName1,appenderName2
		private final static Logger LOGGER = Logger.getLogger(className.class);
	2).log4j.logger.自定义命名=[level] , appenderName1,appenderName2
		private final static Logger LOGGER = Logger.getLogger("自定义命名");
## 二、日志调用 ##
	PropertyConfigurator.configure(path);	//指定加载的配置文件

	LOGGER.debug("msg")
	LOGGER.info("msg")
	LOGGER.warn("msg")
	LOGGER.error("msg")
https://blog.csdn.net/azheng270/article/details/2173430/
http://wiki.10101111.com/pages/viewpage.action?pageId=180843993&tdsourcetag=s_pctim_aiomsg