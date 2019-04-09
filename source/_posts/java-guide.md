---
title: JAVA 中需要注意的规范
date: 2018-01-06 19:50:00
author: lt
img: 
top: false # 如果top值为true，则会是首页推荐文章
# 如果要对文章设置阅读验证密码的话，就可以在设置password的值，该值必须是用SHA256加密后的密码，防止被他人识破
# password: 8d969eef6ecad3c29a3a629280e686cf0c3f5d5a86aff3ca12020c923adc6c92
# 本文章是否开启mathjax，且需要在主题的_config.yml文件中也需要开启才行
mathjax: false
categories: Java
tags:
  - Java
---
> 根据阿里的 JAVA 开发手册，记录自己平时不太注意的一些规范

## 1. 编程规约
##### 命名风格
```
【强制】抽象类命名使用 Abstract 或 Base 开头；异常类命名使用 Exception 结尾；测试类
命名以它要测试的类的名称开始，以 Test 结尾。
```

```
【强制】POJO 类中布尔类型的变量，都不要加 is 前缀，否则部分框架解析会引起序列化错误。
```

```
【强制】包名统一使用小写，点分隔符之间有且仅有一个自然语义的英语单词。包名统一使用
单数形式，但是类名如果有复数含义，类名可以使用复数形式。
```

```
【推荐】接口类中的方法和属性不要加任何修饰符号（public 也不要加），保持代码的简洁
性，并加上有效的 Javadoc 注释。尽量不要在接口里定义变量，如果一定要定义变量，肯定是
与接口方法相关，并且是整个应用的基础常量。
```

```
【推荐】如果是形容能力的接口名称，取对应的形容词为接口名（通常是–able 的形式）。
正例：AbstractTranslator 实现 Translatable 接口。
```

```
【参考】枚举类名建议带上 Enum 后缀，枚举成员名称需要全大写，单词间用下划线隔开。
说明：枚举其实就是特殊的类，域成员均为常量，且构造方法被默认强制是私有。
正例：枚举名字为 ProcessStatusEnum 的成员名称：SUCCESS / UNKNOWN_REASON。
```

```
【参考】各层命名规约：
A) Service/DAO 层方法命名规约
1） 获取单个对象的方法用 get 做前缀。
2） 获取多个对象的方法用 list 做前缀，复数形式结尾如：listObjects。
3） 获取统计值的方法用 count 做前缀。
4） 插入的方法用 save/insert 做前缀。
5） 删除的方法用 remove/delete 做前缀。
6） 修改的方法用 update 做前缀。
B) 领域模型命名规约
1） 数据对象：xxxDO，xxx 即为数据表名。
2） 数据传输对象：xxxDTO，xxx 为业务领域相关的名称。
3） 展示对象：xxxVO，xxx 一般为网页名称。
4） POJO 是 DO/DTO/BO/VO 的统称，禁止命名成 xxxPOJO。
```

##### 常量定义
```
【强制】在 long 或者 Long 赋值时，数值后使用大写的 L，不能是小写的 l，小写容易跟数字
1 混淆，造成误解。
```

```
【推荐】不要使用一个常量类维护所有常量，要按常量功能进行归类，分开维护。
说明：大而全的常量类，杂乱无章，使用查找功能才能定位到修改的常量，不利于理解和维护。
正例：缓存相关常量放在类 CacheConsts 下；系统配置相关常量放在类 ConfigConsts 下。
```

```
【推荐】如果变量值仅在一个固定范围内变化用 enum 类型来定义。
说明：如果存在名称之外的延伸属性应使用 enum 类型，下面正例中的数字就是延伸信息，表
示一年中的第几个季节。
正例：
public enum SeasonEnum {
 SPRING(1), SUMMER(2), AUTUMN(3), WINTER(4);
 private int seq;
 SeasonEnum(int seq){
 this.seq = seq;
 }
}
```

##### 代码格式

代码格式方面自己还是做得不错，包括换行、空格等。

```
【强制】IDE 的 text file encoding 设置为 UTF-8; IDE 中文件的换行符使用 Unix 格式，
不要使用 Windows 格式
```

```
【推荐】单个方法的总行数不超过 80 行。
说明：包括方法签名、结束右大括号、方法内代码、注释、空行、回车及任何不可见字符的总
行数不超过 80 行。
正例：代码逻辑分清红花和绿叶，个性和共性，绿叶逻辑单独出来成为额外方法，使主干代码
更加清晰；共性逻辑抽取成为共性方法，便于复用和维护。
```

##### OOP 规约

```
【强制】所有的覆写方法，必须加 @Override 注解。
```

```
【强制】相同参数类型，相同业务含义，才可以使用 Java 的可变参数，避免使用 Object。
说明：可变参数必须放置在参数列表的最后。（提倡同学们尽量不用可变参数编程）
正例：public List<User> listUsers(String type, Long... ids) {...}
```

```
【强制】外部正在调用或者二方库依赖的接口，不允许修改方法签名，避免对接口调用方产生
影响。接口过时必须加@Deprecated 注解，并清晰地说明采用的新接口或者新服务是什么。
```

```
【强制】Object 的 equals 方法容易抛空指针异常，应使用常量或确定有值的对象来调用
equals。
正例："test".equals(object);
反例：object.equals("test");
说明：推荐使用 java.util.Objects#equals（JDK7 引入的工具类）
```

```
关于基本数据类型与包装数据类型的使用标准如下：
1） 【强制】所有的 POJO 类属性必须使用包装数据类型。
2） 【强制】RPC 方法的返回值和参数必须使用包装数据类型。
3） 【推荐】所有的局部变量使用基本数据类型。
说明：POJO 类属性没有初值是提醒使用者在需要使用时，必须自己显式地进行赋值，任何
NPE 问题，或者入库检查，都由使用者来保证。
正例：数据库的查询结果可能是 null，因为自动拆箱，用基本数据类型接收有 NPE 风险。
反例：比如显示成交总额涨跌情况，即正负 x%，x 为基本数据类型，调用的 RPC 服务，调用
不成功时，返回的是默认值，页面显示为 0%，这是不合理的，应该显示成中划线。所以包装
数据类型的 null 值，能够表示额外的信息，如：远程调用失败，异常退出。
```

```
【强制】定义 DO/DTO/VO 等 POJO 类时，不要设定任何属性默认值。
```

```
【强制】序列化类新增属性时，请不要修改 serialVersionUID 字段，避免反序列失败；如
果完全不兼容升级，避免反序列化混乱，那么请修改 serialVersionUID 值。
说明：注意 serialVersionUID 不一致会抛出序列化运行时异常。
```

```
【强制】构造方法里面禁止加入任何业务逻辑，如果有初始化逻辑，请放在 init 方法中。
```

```
【强制】POJO 类必须写 toString 方法。使用 IDE 中的工具：source> generate toString
时，如果继承了另一个 POJO 类，注意在前面加一下 super.toString。
说明：在方法执行抛出异常时，可以直接调用 POJO 的 toString()方法打印其属性值，便于排
查问题
```

```
当一个类有多个构造方法，或者多个同名方法，这些方法应该按顺序放置在一起，
便于阅读。
类内方法定义的顺序依次是：公有方法或保护方法 > 私有方法 > getter/setter 方法。
```

```
【推荐】循环体内，字符串的连接方式，使用 StringBuilder 的 append 方法进行扩展。
```

```
【推荐】慎用 Object 的 clone 方法来拷贝对象。
说明：对象的 clone 方法默认是浅拷贝，若想实现深拷贝需要重写 clone 方法实现域对象的
深度遍历式拷贝。
```

```
【推荐】类成员与方法访问控制从严：
1） 如果不允许外部直接通过 new 来创建对象，那么构造方法必须是 private。
2） 工具类不允许有 public 或 default 构造方法。
3） 类非 static 成员变量并且与子类共享，必须是 protected。
4） 类非 static 成员变量并且仅在本类使用，必须是 private。
5） 类 static 成员变量如果仅在本类使用，必须是 private。
6） 若是 static 成员变量，考虑是否为 final。
7） 类成员方法只供类内部调用，必须是 private。
8） 类成员方法只对继承类公开，那么限制为 protected。
说明：任何类、方法、参数、变量，严控访问范围。过于宽泛的访问范围，不利于模块解耦。
思考：如果是一个 private 的方法，想删除就删除，可是一个 public 的 service 成员方法或
成员变量，删除一下，不得手心冒点汗吗？变量像自己的小孩，尽量在自己的视线内，变量作
用域太大，无限制的到处跑，那么你会担心的。
```

##### 集合处理

```
【强制】关于 hashCode 和 equals 的处理，遵循如下规则：
1） 只要重写 equals，就必须重写 hashCode。
2） 因为 Set 存储的是不重复的对象，依据 hashCode 和 equals 进行判断，所以 Set 存储的
对象必须重写这两个方法。
3） 如果自定义对象作为 Map 的键，那么必须重写 hashCode 和 equals。
说明：String 重写了 hashCode 和 equals 方法，所以我们可以非常愉快地使用 String 对象
作为 key 来使用。
```

```
【强制】使用集合转数组的方法，必须使用集合的 toArray(T[] array)，传入的是类型完全
一样的数组，大小就是 list.size()
正例：
List<String> list = new ArrayList<String>(2); 
list.add("guan"); 
list.add("bao"); 
String[] array = new String[list.size()]; 
array = list.toArray(array); 
```

```
【强制】使用工具类 Arrays.asList()把数组转换成集合时，不能使用其修改集合相关的方
法，它的 add/remove/clear 方法会抛出 UnsupportedOperationException 异常。

说明：asList 的返回对象是一个 Arrays 内部类，并没有实现集合的修改方法。Arrays.asList
体现的是适配器模式，只是转换接口，后台的数据仍是数组。
 String[] str = new String[] { "you", "wu" };
 List list = Arrays.asList(str);
第一种情况：list.add("yangguanbao"); 运行时异常。
第二种情况：str[0] = "gujin"; 那么 list.get(0)也会随之修改。
```

```
【强制】泛型通配符<? extends T>来接收返回的数据，此写法的泛型集合不能使用 add 方
法，而<? super T>不能使用 get 方法，作为接口调用赋值时易出错。

说明：扩展说一下 PECS(Producer Extends Consumer Super)原则：第一、频繁往外读取内
容的，适合用<? extends T>。第二、经常往里插入的，适合用<? super T>。
```

```
【强制】不要在 foreach 循环里进行元素的 remove/add 操作。remove 元素请使用 Iterator
方式，如果并发操作，需要对 Iterator 对象加锁。

正例：
List<String> list = new ArrayList<>(); 
list.add("1"); 
list.add("2"); 
Iterator<String> iterator = list.iterator(); 
while (iterator.hasNext()) { 
String item = iterator.next(); 
if (删除元素的条件) { 
iterator.remove(); 
} 
}
反例：
for (String item : list) { 
if ("1".equals(item)) { 
list.remove(item); 
} 
} 
说明：以上代码的执行结果肯定会出乎大家的意料，那么试一下把“1”换成“2”，会是同样的结果吗？
```

```
【强制】在 JDK7 版本及以上，Comparator 实现类要满足如下三个条件，不然 Arrays.sort，
Collections.sort 会报 IllegalArgumentException 异常。

说明：三个条件如下
1） x，y 的比较结果和 y，x 的比较结果相反。
2） x>y，y>z，则 x>z。
3） x=y，则 x，z 比较结果和 y，z 比较结果相同。

反例：下例中没有处理相等的情况，实际使用中可能会出现异常：
new Comparator<Student>() { 
@Override 
public int compare(Student o1, Student o2) { 
return o1.getId() > o2.getId() ? 1 : -1; 
} 
};
```

```
【推荐】集合泛型定义时，在 JDK7 及以上，使用 diamond 语法或全省略。
说明：菱形泛型，即 diamond，直接使用<>来指代前边已经指定的类型。
正例：
// <> diamond 方式
HashMap<String, String> userCache = new HashMap<>(16);
// 全省略方式
ArrayList<User> users = new ArrayList(10); 
```

```
【推荐】集合初始化时，指定集合初始值大小。
说明：HashMap 使用 HashMap(int initialCapacity) 初始化。
正例：initialCapacity = (需要存储的元素个数 / 负载因子) + 1。注意负载因子（即 loader 
factor）默认为 0.75，如果暂时无法确定初始值大小，请设置为 16（即默认值）。
反例：HashMap 需要放置 1024 个元素，由于没有设置容量初始大小，随着元素不断增加，容
量 7 次被迫扩大，resize 需要重建 hash 表，严重影响性能
```

```
【推荐】使用 entrySet 遍历 Map 类集合 KV，而不是 keySet 方式进行遍历。
说明：keySet 其实是遍历了 2 次，一次是转为 Iterator 对象，另一次是从 hashMap 中取出
key 所对应的 value。而 entrySet 只是遍历了一次就把 key 和 value 都放到了 entry 中，效
率更高。如果是 JDK8，使用 Map.foreach 方法。
正例：values()返回的是 V 值集合，是一个 list 集合对象；keySet()返回的是 K 值集合，是
一个 Set 集合对象；entrySet()返回的是 K-V 值组合集合。
```
![](https://upload-images.jianshu.io/upload_images/12625950-bae3ca1820eb6a3e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##### 并发处理

需要先好好学习一下并发的知识。

##### 控制语句

```
【强制】在高并发场景中，避免使用”等于”判断作为中断或退出的条件。
说明：如果并发控制没有处理好，容易产生等值判断被“击穿”的情况，使用大于或小于的区间
判断条件来代替。
反例：判断剩余奖品数量等于 0 时，终止发放奖品，但因为并发处理错误导致奖品数量瞬间变
成了负数，这样的话，活动无法终止。
```

```
【推荐】表达异常的分支时，少用 if-else 方式，这种方式可以改写成：
if (condition) { 
 ... 
 return obj; 
} 
// 接着写 else 的业务逻辑代码; if-else 请勿超过 3 层
```

```
【推荐】除常用方法（如 getXxx/isXxx）等外，不要在条件判断中执行其它复杂的语句，将
复杂逻辑判断的结果赋值给一个有意义的布尔变量名，以提高可读性。
说明：很多 if 语句内的逻辑相当复杂，阅读者需要分析条件表达式的最终结果，才能明确什么
样的条件执行什么样的语句，那么，如果阅读者分析逻辑表达式错误呢？
正例：
// 伪代码如下
final boolean existed = (file.open(fileName, "w") != null) && (...) || (...);
if (existed) {
 ...
} 
反例：
if ((file.open(fileName, "w") != null) && (...) || (...)) {
 ...
}
```

```
【参考】下列情形，需要进行参数校验：
1） 调用频次低的方法。
2） 执行时间开销很大的方法。此情形中，参数校验时间几乎可以忽略不计，但如果因为参
数错误导致中间执行回退，或者错误，那得不偿失。
3） 需要极高稳定性和可用性的方法。
4） 对外提供的开放接口，不管是 RPC/API/HTTP 接口。
5） 敏感权限入口。
```

```
【参考】下列情形，不需要进行参数校验：
1） 极有可能被循环调用的方法。但在方法说明里必须注明外部参数检查要求。
2） 底层调用频度比较高的方法。毕竟是像纯净水过滤的最后一道，参数错误不太可能到底
层才会暴露问题。一般 DAO 层与 Service 层都在同一个应用中，部署在同一台服务器中，所
以 DAO 的参数校验，可以省略。
3） 被声明成 private 只会被自己代码所调用的方法，如果能够确定调用方法的代码传入参
数已经做过检查或者肯定不会有问题，此时可以不校验参数
```

##### 注释规约

```
【强制】类、类属性、类方法的注释必须使用 Javadoc 规范，使用/**内容*/格式，不得使用
// xxx 方式。
```

```
【强制】所有的抽象方法（包括接口中的方法）必须要用 Javadoc 注释、除了返回值、参数、
异常说明外，还必须指出该方法做什么事情，实现什么功能。
说明：对子类的实现要求，或者调用注意事项，请一并说明
```

```
【强制】所有的类都必须添加创建者和创建日期
```

```
【强制】所有的枚举类型字段必须要有注释，说明每个数据项的用途。
```

```
【参考】特殊注释标记，请注明标记人与标记时间。注意及时处理这些标记，通过标记扫描，
经常清理此类标记。线上故障有时候就是来源于这些标记处的代码。
1） 待办事宜（TODO）:（ 标记人，标记时间，[预计处理时间]）
表示需要实现，但目前还未实现的功能。这实际上是一个 Javadoc 的标签，目前的 Javadoc
还没有实现，但已经被广泛使用。只能应用于类，接口和方法（因为它是一个 Javadoc 标签）。
2） 错误，不能工作（FIXME）:（标记人，标记时间，[预计处理时间]）
在注释中用 FIXME 标记某代码是错误的，而且不能工作，需要及时纠正的情况。
```

##### 其他

```
【强制】在使用正则表达式时，利用好其预编译功能，可以有效加快正则匹配速度。
说明：不要在方法体内定义：Pattern pattern = Pattern.compile(“规则”);
```

```
【强制】获取当前毫秒数 System.currentTimeMillis(); 而不是 new Date().getTime();
说明：如果想获取更加精确的纳秒级时间值，使用 System.nanoTime()的方式。在 JDK8 中，
针对统计时间等场景，推荐使用 Instant 类。
```

```
【推荐】任何数据结构的构造或初始化，都应指定大小，避免数据结构无限增长吃光内存。
```

## 2. 异常日志

##### 异常处理
```
【推荐】防止 NPE，是程序员的基本修养，注意 NPE 产生的场景：
1）返回类型为基本数据类型，return 包装数据类型的对象时，自动拆箱有可能产生 NPE。
 反例：public int f() { return Integer 对象}， 如果为 null，自动解箱抛 NPE。
2） 数据库的查询结果可能为 null。
3） 集合里的元素即使 isNotEmpty，取出的数据元素也可能为 null。
4） 远程调用返回对象时，一律要求进行空指针判断，防止 NPE。
5） 对于 Session 中获取的数据，建议 NPE 检查，避免空指针。
6） 级联调用 obj.getA().getB().getC()；一连串调用，易产生 NPE。
正例：使用 JDK8 的 Optional 类来防止 NPE 问题。
```

```
【参考】对于公司外的 http/api 开放接口必须使用“错误码”；而应用内部推荐异常抛出；
跨应用间 RPC 调用优先考虑使用 Result 方式，封装 isSuccess()方法、“错误码”、“错误简
短信息”。
说明：关于 RPC 方法返回方式使用 Result 方式的理由：
1）使用抛异常返回方式，调用方如果没有捕获到就会产生运行时错误。
2）如果不加栈信息，只是 new 自定义异常，加入自己的理解的 error message，对于调用
端解决问题的帮助不会太多。如果加了栈信息，在频繁调用出错的情况下，数据序列化和传输
的性能损耗也是问题
```

```
【参考】避免出现重复的代码（Don’t Repeat Yourself），即 DRY 原则。
说明：随意复制和粘贴代码，必然会导致代码的重复，在以后需要修改时，需要修改所有的副
本，容易遗漏。必要时抽取共性方法，或者抽象公共类，甚至是组件化。
正例：一个类中有多个 public 方法，都需要进行数行相同的参数校验操作，这个时候请抽取：
private boolean checkParam(DTO dto) {...} 
```

##### 日志规约

这一节内容都不太熟悉，看书。

```
【强制】应用中不可直接使用日志系统（Log4j、Logback）中的 API，而应依赖使用日志框架
SLF4J 中的 API，使用门面模式的日志框架，有利于维护和各个类的日志处理方式统一。
import org.slf4j.Logger; 
import org.slf4j.LoggerFactory;
private static final Logger logger = LoggerFactory.getLogger(Abc.class); 
```

```
【强制】异常信息应该包括两类信息：案发现场信息和异常堆栈信息。如果不处理，那么通过
关键字 throws 往上抛出。
正例：logger.error(各类参数或者对象 toString() + "_" + e.getMessage(), e);
```

## 单元测试和安全规约、数据库等看文档。