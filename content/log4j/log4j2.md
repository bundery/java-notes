# Log4j

## logger
Logger通过`.`来表示继承关系，如`io.bundery`是`io.budery.dao`的父类，`Root`是所有Logger的祖先。

### additivity

- `true`：表示子logger所接受的日志信息也会在父logger中出现，即使父logger的level高于子logger。（即不考虑定义在父logger的level，但会考虑父logger关联的`Appender`定义的过滤器level。）
- `false`：表示子logger所接受的日志信息不会在父logger中出现。

## level
先过滤出符合定义在logger中的level的日志信息，再在Appender的Filter中过滤。

- 如在logger中定义了`level="INFO"`，而在Appender中的过滤的是`WARN`及以上的，则只会输出`WARN`及以上的。
- 如在logger中定义了`level="WARN"`，而在Appender中的过滤的是`INFO`及以上的，则只会输出`WARN`及以上的。

{%ace edit=false, lang='xml'%}
<?xml version="1.0" encoding="UTF-8"?>
<Configuration>
	<Properties>
		<Property name="LOG_DIR">F:/oxygen/log4j2</Property>
		<Property name="FILE_NAME">myLog</Property>
		<Property name="FILE_PATTERN">${LOG_DIR}/$${date:yyyy-MM}/${FILE_NAME}-%d{yyyy-MM-dd}-%i.log</Property>
		<Property name="LAYOUT_PATTERN">%d{yyyy-MM-dd HH:mm:ss.SSS} [%t] %-5level %logger{36} - %msg%n</Property>
	</Properties>

	<Appenders>
		<!-- 控制台Appender -->
		<Console name="Console" target="SYSTEM_OUT">
			<PatternLayout pattern="${LAYOUT_PATTERN}" />
			<!-- 如果日志信息大于等于level，则匹配onMatch，否则匹配onMismatch -->
			<ThresholdFilter level="WARN" onMatch="ACCEPT" onMismatch="DENY" />
		</Console>
		<!-- 按时间和文件大小滚动的Appender fileName:以指定路径及文件名生成日志文件；filePattern:指定日志归档时的路径及文件名 -->
		<RollingRandomAccessFile name="DailyFile" fileName="${LOG_DIR}/${FILE_NAME}.log" filePattern="${FILE_PATTERN}">
			<PatternLayout pattern="${LAYOUT_PATTERN}" />
			<Policies>
				<!-- 与filePattern配合，指定隔多久会将日志归档，上面最小粒度是dd，即隔一天归档一份日志 -->
				<TimeBasedTriggeringPolicy interval="1" />
				<!-- 日志文件超过size将会归档 -->
				<SizeBasedTriggeringPolicy size="10MB" />
			</Policies>
			<!-- 与SizeBasedTriggeringPolicy配合，归档文件最多不能超过max个，否则新归档覆盖旧归档文件，filePattern的%i就是归档文件的索引 -->
			<DefaultRolloverStrategy max="10" />
		</RollingRandomAccessFile>
	</Appenders>

	<Loggers>
		<!-- additivity=”true” 表示子logger所接受的日志信息也会在父logger中出现，即使父logger的level高于子logger。-->
		<Logger name="io.bundery" level="TRACE" additivity="true">
			<AppenderRef ref="DailyFile" />
		</Logger>
		<Root level="ERROR">
			<AppenderRef ref="Console" />
		</Root>
	</Loggers>
</Configuration>
{%endace%}
