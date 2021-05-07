# Jmeter自动化工程说明

## 1.项目说明

本项目是Maven+jmeter结成的项目，通过jmeter-maven-plugin驱动jmx脚本执行测试用例；并生成最终的报告

### 1.1. 项目归档说明

项目目前在src/jmeter/下有finance/inventory/order/purchase几个工作目录

- finance/    财务产品线的项目

- inventory/    仓储产品线的项目

- order/    订单产品线的项目

- purchase/    采购产品线的项目

可以根据自己的项目讲脚本归档在对应的目录中

**注意：脚本中有依赖的数据文件跟jmx文件在相同路径下即可（jmx脚本中使用相对路径指定==之前已经在开发规范中明确）**

### 1.2. 常用的git命令

```bash
# clone当前项目到本地
git clone https://gitlab.gongkongsaas.com/yanghai/auto-jmeter-new.git 
# 查看本地的修改状态
git status
# 本地提交代码
git commit -m "本地提交的注释信息--一定要写，不然commit多了不知道每次的修改；需要具体查看文件才能区分"
# push代码到远程master分支
git push origin master:master
```



## 2.pom依赖说明

```xml
			<plugin>
				<!-- 核心插件，用来执行jmx脚本，注意版本号，2.1.0可以使用用jmeter3.1生成的脚本。最新的2.2.0使用jmeter3.2生成的脚本 -->
				<groupId>com.lazerycode.jmeter</groupId>
				<artifactId>jmeter-maven-plugin</artifactId>
				<version>2.5.1</version>
				<configuration>
					<!-- 默认报告优化，修改jmeter.properties配置 -->
					<propertiesJMeter>
						<log_level.jmeter>DEBUG</log_level.jmeter>
					</propertiesJMeter>
					<resultsFileFormat>xml</resultsFileFormat>
					<ignoreResultFailures>true</ignoreResultFailures>
					<testResultsTimestamp>false</testResultsTimestamp>
					<!-- 如果不想查看jmeter生成的log,设置为true -->
					<suppressJMeterOutput>true</suppressJMeterOutput>
					<!-- 增加扩展插件 -->
					<jmeterExtensions>
						<!--此处自己引入的jar包 -->
					</jmeterExtensions>

					<!-- 设置jmeter生成结果文件格式 -->
					<resultsFileFormat>xml</resultsFileFormat>
					<!-- 设置忽略失败是否停止运行 -->
					<ignoreResultFailures>true</ignoreResultFailures>
					<!--设置结果是否有时间戳,与appendResultsTimestamp 或resultsFileNameDateFormat 搭配使用 -->
					<testResultsTimestamp>false</testResultsTimestamp>
					<testFilesIncluded>
						<!-- 使用<jMeterTestFiles>选择执行用例 -->
						 <jMeterTestFile>product/*.jmx</jMeterTestFile>
						<!-- 标签排除用例 -->
						<!--<excludeJMeterTestFile>*.jmx</excludeJMeterTestFile>-->
					</testFilesIncluded>
					<resultsDirectory>${jmeter.result.jtl.dir}</resultsDirectory>
					<!-- 使用<testFilesDirectory>指定测试用例的路径 -->
					<!-- <testFilesDirectory>/scratch/testfiles/</testFilesDirectory> -->
				</configuration>

				<executions>
					<execution>
						<id>jmeter-tests</id>
						<phase>verify</phase>
						<!--脚本所在的文件夹 -->
						<goals>
							<goal>jmeter</goal>
						</goals>
						<configuration>
							<appendResultsTimestamp>true</appendResultsTimestamp>
							<skipTests>${skipTests}</skipTests>
						</configuration>
					</execution>
				</executions>
			</plugin>
```

## 3.项目本地启动说明

### 3.1. 启动命令

```bash
mvn clean verify
```

注意：一般idea中都有自带的maven依赖，不需要自己安装；如果命令行运行的话，需要安装maven

### 3.2. 结果在项目目录的当前目录${env.BUILD_ID}下

分为details/results/summary三个目录（目前是一个jmx文件对应三个子文件结果）

- details中是所有采样器的请求详细结果
- results中是结果的原始文件jtl（可以在本地的jmeter的查看结果数中加载查看）
- summary中是所有结果的汇总

### 3.3. pom中可以指定文件夹驱动用例

- 下面是指定product的文件夹下面的所有jmx脚本运行

```xml
					<testFilesIncluded>
						<!-- 使用<jMeterTestFiles>选择执行用例 -->
						 <jMeterTestFile>product/*.jmx</jMeterTestFile>
						<!-- 标签排除用例 -->
						<!--<excludeJMeterTestFile>*.jmx</excludeJMeterTestFile>-->
					</testFilesIncluded>
```

### 3.4. 更多jmeter开发规范可以参考文档

[Jmeter接口自动化开发规范 - gongkongsaas - Confluence](https://confluence.gongkongsaas.com/pages/viewpage.action?pageId=81386154) 