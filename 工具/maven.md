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

# scope作用


| scope取值 | 有效范围（compile, runtime, test） | 依赖传递 | 例子 |
| --- | --- | --- | --- |
| compile | all | 是 | spring-core |
| provided | compile, test | 否 | servlet-api |
| runtime | runtime, test | 是 | JDBC驱动 |
| test | test | 否 | JUnit |
| system | compile, test | 是 |  |

正如上表所示，
* compile ：为默认的依赖有效范围。如果在定义依赖关系的时候，没有明确指定依赖有效范围的话，则默认采用该依赖有效范围。此种依赖，在编译、运行、测试时均有效。
* provided ：在编译、测试时有效，但是在运行时无效。例如：servlet-api，运行项目时，容器已经提供，就不需要Maven重复地引入一遍了。
* runtime ：在运行、测试时有效，但是在编译代码时无效。例如：JDBC驱动实现，项目代码编译只需要JDK提供的JDBC接口，只有在测试或运行项目时才需要实现上述接口的具体JDBC驱动。
* test ：只在测试时有效，例如：JUnit。
* system ：在编译、测试时有效，但是在运行时无效。和provided的区别是，使用system范围的依赖时必须通过systemPath元素显式地指定依赖文件的路径。由于此类依赖不是通过Maven仓库解析的，而且往往与本机系统绑定，可能造成构建的不可移植，因此应该谨慎使用。systemPath元素可以引用环境变量。例如：

    ```xml
            <dependency>
                <groupId>javax.sql</groupId>
                <artifactId>jdbc-stdext</artifactId>
                <version>2.0</version>
                <scope>system</scope>
                <systemPath>${java.home}/lib/rt.jar</systemPath>
            </dependency>
    ```

## scope的分类

### compile
默认就是compile，什么都不配置也就是意味着compile。compile表示被依赖项目需要参与当前项目的编译，当然后续的测试，运行周期也参与其中，是一个比较强的依赖。打包的时候通常需要包含进去。

### test
scope为test表示依赖项目仅仅参与测试相关的工作，包括测试代码的编译，执行。比较典型的如junit。

### runntime
runntime表示被依赖项目无需参与项目的编译，不过后期的测试和运行周期需要其参与。与compile相比，跳过编译而已，说实话在终端的项目（非开源，企业内部系统）中，和compile区别不是很大。比较常见的如JSR×××的实现，对应的API jar是compile的，具体实现是runtime的，compile只需要知道接口就足够了。oracle jdbc驱动架包就是一个很好的例子，一般scope为runntime。另外runntime的依赖通常和optional搭配使用，optional为true。我可以用A实现，也可以用B实现。

### provided
provided意味着打包的时候可以不用包进去，别的设施(Web Container)会提供。事实上该依赖理论上可以参与编译，测试，运行等周期。相当于compile，但是在打包阶段做了exclude的动作。

### system
从参与度来说，也provided相同，不过被依赖项不会从maven仓库抓，而是从本地文件系统拿，一定需要配合systemPath属性使用。

## scope的依赖传递
A–>B–>C。当前项目为A，A依赖于B，B依赖于C。知道B在A项目中的scope，那么怎么知道C在A中的scope呢？答案是： 
当C是test或者provided时，C直接被丢弃，A不依赖C； 
否则A依赖C，C的scope继承于B的scope。

# 复合项目中只编译指定model

-pl modelname 打包指定模块
-am 同时编译该模块的依赖模块
-amd 同时编译依赖该模块的模块
-N 不递归子模块
-rf 从指定模块开始继续处理
