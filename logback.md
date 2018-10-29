# LogBack #
## 一、配置文件 ##
1、默认配置的步骤
	
	1).尝试在classpath下查找文件logback-test.xml;
	2).如果文件不存在，则查找文件logback.xml
	3).如果两个文件都不存在，logback用BasicConfigurator自动对自己进行配置，这会导致记录输出到控制台
2、配置文件

    <?xml version="1.0" encoding="UTF-8"?>
	<configuration debug=false scanPeroid="60 seconds" scan="true">
		# 控制台输出
		<appender name="consoleAppender" class="ch.qos.logback.core.Console">
			<Encoding>UTF-8</Encoding>
			<layout class="ch.qos.logback.classic.PatternLayout">
				<pattern>
					%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{50} - %msg%n
				</pattern>
			</layout>
		<appender>

		<logger>
					
		</logger>
	</configuration>
3、配置文件详解

	1).根节点<configuration>，包含三个属性：
		scan:当此属性设置为true时，配置文件如果发生改变，将会被重新加载，默认值为true
		scanPeriod:设置监测配置文件是否有修改时间间隔，默认单位为毫秒。当scan为true时生效。默认事件间隔为1分钟
		debug：当此属性设置为true时，将打印出logback内部日志信息，实时查看logback运行状态。默认值为false
		<configuration debug="false" scan="true" scanPeriod="60 seconds">
			<!-- 其他配置 -->
		</configuration>

	2).子节点<contextName>用来设置上下文名称，每个logger都关联到logger上下文，默认上下文名称为default。但可以使用<contextName>设置成其他名字，用于区分不同应用程序的记录。一旦设置，不能更改。
		<configuration debug="false" scan="true" scanPeriod="60 seconds">
			<contextName>myAppName</contextName>
			<!-- 其他配置 -->
		</configuration>

	3).子节点<property>用于定义变量值，可以使用"${}"来使用变量
		name:变量的名称
		value:变量定义的值
		<configuration scan="true" scanPeriod="60 seconds" debug="false"> 
				<property name="APP_Name" value="myAppName"/>
    　　　		<contextName>${APP_Name}</contextName> 
    　　　		<!--其他配置省略--> 
		</configuration>

	4).子节点<timestamp>获取时间戳字符串,两个属性key和dataPattern
		key:标识此<timestamp>的名字；
		dataPattern:设置将当前时间转换成字符串的模式，遵循java.txt.SimpleDataFormat的格式
		<configuration scan="true" scanPeriod="60 seconds" debug="false">
			<timestamp key="bySecond" dataPattern="yyyyMMdd'T'HHmmss/>
			<contextName>${bySecond}</contextName>
		</configuration>

	5).子节点<appender>负责写日志的组件，有两个必要的属性name和class
		5.1)ConsoleAppender 把日志输出到控制台，有以下子节点：
			<encoder>把日志进行格式化
			<target>字符串System.out(默认)或者System.err
			<configuration>
				<appender name="consoleAppender" class="ch.qos.logback.core.ConsoleAppender">
					<encoder>
						<pattern>%-4relative [%thread] %-5level %logger{35} - %msg %n</pattern>
					</encoder>
				<appender>
			</configuration>

		5.2)FileAppender 把日志添加到文件，有以下子节点：
			<file>被写入的文件名，没有默认值
			<append>如果时true，日志被追加到文件结尾，false则覆盖文件，默认值时true
			<encoder>对记录事件进行格式化
			<prudent>如果是true，日志会被安全的写入文件，即使其他的FileAppender也在向此文件做写入操作，效率低，默认为false
			<configuration>
				<appender name="fileAppender" class="ch.qos.logback.core.FileAppender">
					<file>logback.log</file>
					<append>true</append>
					<encoder>
						<pattern>%-4relative [%thread] %-5level %logger{35} - %msg%n</pattern>
					</encoder>
				</appender>
			</configuration>

		5.3)RollingFileAppender 滚动记录文件，先将日志记录到指定文件，当符合某个条件时，将日志记录到其他文件。有以下节点：
			<file>被写入的文件名，没有默认值
			<append>如果时true，日志被追加到文件结尾，false则覆盖文件，默认值时true
			<rollingPolicy>当发生滚动时，决定RollingFileAppender的行为，涉及文件移动和重命名。属性class定义具体的滚动策略类

			<appender name="FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
				<rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
				<!--日志文件输出的文件名-->
					<FileNamePattern>${LOG_HOME}/TestWeb.log.%d{yyyy-MM-dd}.log</FileNamePattern>
					<!--日志文件保留天数-->
					<MaxHistory>30</MaxHistory>
				</rollingPolicy>
				<encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
					<!--格式化输出：%d表示日期，%thread表示线程名，%-5level：级别从左显示5个字符宽度%msg：日志消息，%n是换行符-->
					<pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{50} - %msg%n</pattern>
				</encoder>
					<!--日志文件最大的大小-->
				<triggeringPolicy class="ch.qos.logback.core.rolling.SizeBasedTriggeringPolicy">
					<MaxFileSize>10MB</MaxFileSize>
				</triggeringPolicy>
			</appender>

	6).子节点<logger>用来设置某一个报或者具体的某一个类的日志打印级别，以及指定<appender>。<logger>仅有一个name属性，一个可选的level和一个可选的additivity属性
		可以包含零个或者多个<appender-ref ref="">元素，标识这个appender将会添加到这个logger
		name:用来指定受此logger约束的某一个包或者具体的类
		level:用来设置打印级别，大小写无关：TRACE, DEBUG, INFO, WARN, ERROR, ALL和OFF，还有一个特殊值INHERITED或者NULL,代表强制执行上级的级别。如果为设置此属性，那么当前的logger将会继承上级的级别
		additivity:是否向上级logger传递打印信息。默认时true
![](https://i.imgur.com/eYjE4M3.png)
	7).子节点<root>它也是<logger>元素，但是它是根logger，是所有<logger>的上级。只有一个level属性，因为name已经被命名为"root"，且已经是最上级了。同<logger>一样，可以包含零个或多个<appender-ref>元素，标识这个appender将会添加到这个logger。level: 用来设置打印级别，大小写无关：TRACE, DEBUG, INFO, WARN, ERROR, ALL和OFF，不能设置为INHERITED或者同义词NULL。 默认是DEBUG。


		

