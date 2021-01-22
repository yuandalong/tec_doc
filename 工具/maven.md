# 基础命令
* clean 清理
* compile 编译
* install 发布到本地仓库
* package 打包
* deploy 发布到代理仓库

# 修改版本号
`versions:set -DnewVersion=1.0.1-SNAPSHOT` 
修改版本号，主要用于多模块的项目，在父项目里执行

# 忽略junit
`-Dmaven.test.skip=true`

# debug
-X debug模式

# 强制更新sanpshot
-U 强制更新snapshot

# 指定配置
-P 使用pom配置中指定的profile，如-P online 打包时就会使用profile 的id为online的配置

# 打印依赖树
`dependency:tree` 打印依赖树

# 查看当前生效的配置
## 查看当前生效的settings
`help:effective-settings`

## 查看当前生效的pom
`help:effective-pom`

## 查看当前处于激活状态的profile
`help:active-profiles`


# profiles设置

```xml
<profiles>
	<profile>
	       <!--定义不同环境Profile的唯一id，mvn命令通过-P指定激活哪个profile-->
            <id>prod</id>
            <!--定义变量，当打包项目时，激活不同的环境，profiles.active字段就会被赋予不同的值。-->
            <properties>
                <profiles.active>prod</profiles.active>
            </properties>
            <!--activation用来指定激活方式，可以根据jdk环境，环境变量，文件的存在或缺失-->
            <activation>
                <!--配置默认激活-->
                <activeByDefault>true</activeByDefault>
                
                <!--通过jdk版本-->
                <!--当jdk环境版本为1.5时，此profile被激活-->
                <jdk>1.5</jdk>
                <!--当jdk环境版本1.5或以上时，此profile被激活-->
                <jdk>[1.5,)</jdk>

                <!--根据当前操作系统-->
                <os>
                    <name>Windows XP</name>
                    <family>Windows</family>
                    <arch>x86</arch>
                    <version>5.1.2600</version>
                </os>

                <!--通过系统环境变量，name-value自定义-->
                <property>
                    <name>env</name>
                    <value>test</value>
                </property>

                <!--通过文件的存在或缺失-->
                <file>
                    <missing>target/generated-sources/axistools/wsdl2java/
                        com/companyname/group</missing>
                    <exists/>
                </file>
            </activation>
            <!--可以定义dependencies，适用于开发环境和发布环境scope不一样的情况，如flink、spark等，通过激活不同profile实现不同的scope-->
            <dependencies>
    				<dependency>
    					<groupId>org.apache.flink</groupId>
    					<artifactId>flink-scala_${scala.binary.version}</artifactId>
    					<version>${flink.version}</version>
    					<scope>compile</scope>
    				</dependency>
			</dependencies>
        </profile>
</profiles>

```

# 多线程并行编译


| 参数 | 说明 |
| --- | --- |
| -T 1C  | 代表每个CPU核心跑一个工程。就是假设，现在现在1个物理CPU，有4个核心，8个线程。那么此时-T 1C 就是8线程 |
| -T 4 | 直接指定4线程 |
| -Dmaven.compile.fork=true | 使用多线程编译 |
