# 数据持久化

#### **沙盒** <a id="h3--strong-strong-"></a>

在介绍各种存储方法之前，有必要说明以下沙盒机制。iOS程序默认情况下只能访问程序自己的目录，这个目录被称为“沙盒”。

**结构**

```text
应用程序包
Documents
Library
    Caches
    Preferences
tmp
```

**目录特性**

应用程序包: 这里面存放的是应用程序的源文件，包括资源文件和可执行文件。

```text
NSString *path = [[NSBundle mainBundle] bundlePath];
```

Documents: 最常用的目录，iTunes同步该应用时会同步此文件夹中的内容，适合存储重要数据。

```text
NSString *path = NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES).firstObject;
```

Library/Caches: iTunes不会同步此文件夹，适合存储体积大，不需要备份的非重要数据。

```text
NSString *path = NSSearchPathForDirectoriesInDomains(NSCachesDirectory, NSUserDomainMask, YES).firstObject;
```

Library/Preferences: iTunes同步该应用时会同步此文件夹中的内容，通常保存应用的设置信息。

tmp: iTunes不会同步此文件夹，系统可能在应用没运行时就删除该目录下的文件，所以此目录适合保存应用中的一些临时文件，用完就删除。

```text
NSString *path = NSTemporaryDirectory();
```

#### **plist文件** <a id="h3--strong-plist-strong-"></a>

plist文件是将某些特定的类，通过XML文件的方式保存在目录中。

可以被序列化的类型只有如下几种：

```text
NSArray;
NSMutableArray;
NSDictionary;
NSMutableDictionary;
NSData;
NSMutableData;
NSString;
NSMutableString;
NSNumber;
NSDate;
```

**获得文件路径**

```text
 NSString *path = NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES).firstObject;
    NSString *fileName = [path stringByAppendingPathComponent:@"123.plist"];
```

**存储**

```text
NSArray *array = @[@"123", @"456", @"789"];
[array writeToFile:fileName atomically:YES];
```

**读取**

```text
NSArray *result = [NSArray arrayWithContentsOfFile:fileName];
```

**注意**

* 只有以上列出的类型才能使用plist文件存储。
* 存储时使用writeToFile: atomically:方法。 其中atomically表示是否需要先写入一个辅助文件，再把辅助文件拷贝到目标文件地址。这是更安全的写入文件方法，一般都写YES。
* 读取时使用arrayWithContentsOfFile:方法。

#### **Preference**\(NSUserDefaults\) <a id="h3--strong-preference-strong-nsuserdefaults-"></a>

**使用方法**

```text
NSUserDefaults *userDefaults = [NSUserDefaults standardUserDefaults];
//2.向文件中写入内容
[userDefaults setObject:@"AAA" forKey:@"a"];
//2.1立即同步
[userDefaults synchronize];
//3.读取文件
NSString *name = [userDefaults objectForKey:@"a"];
```

**注意**

* 偏好设置是专门用来保存应用程序的配置信息的，一般不要在偏好设置中保存其他数据。
* 如果没有调用synchronize方法，系统会根据I/O情况不定时刻地保存到文件中。所以如果需要立即写入文件的就必须调用synchronize方法。
* 偏好设置会将所有数据保存到同一个文件中。即preference目录下的一个以此应用包名来命名的plist文件。

#### **NSKeyedArchiver** <a id="h3--strong-nskeyedarchiver-strong-"></a>

归档在iOS中是另一种形式的序列化，只要遵循了NSCoding协议的对象都可以通过它实现序列化。由于决大多数支持存储数据的Foundation和Cocoa Touch类都遵循了NSCoding协议，因此，对于大多数类来说，归档相对而言还是比较容易实现的。

**遵循NSCoding协议**

NSCoding协议声明了两个方法，这两个方法都是必须实现的。一个用来说明如何将对象编码到归档中，另一个说明如何进行解档来获取一个新对象。

遵循协议和设置属性

```text
//1.遵循NSCoding协议 
@interface Person : NSObject <NSCoding>  
//2.设置属性
@property (strong, nonatomic) UIImage *avatar;
@property (copy, nonatomic) NSString *name;
@property (assign, nonatomic) NSInteger age;
@end
```

在实现文件中实现协议的两个方法:`-encodeWithCoder:`和`-initWithCoder:`实现方法如下:

```text
//进行编码的协议方法
- (void)encodeWithCoder:(NSCoder *)aCoder
{
    //编码
    [aCoder encodeObject:self.name forKey:@"name"];
    [aCoder encodeObject:self.time forKey:@"time"];
    [aCoder encodeObject:self.pictures forKey:@"pictures"];
}

//进行解码的协议方法
- (nullable instancetype)initWithCoder:(NSCoder *)aDecoder
{
    //解码
    if (self = [super init])
    {
        self.name = [aDecoder decodeObjectForKey:@"name"];
        self.time = [aDecoder decodeObjectForKey:@"time"];
        self.pictures = [aDecoder decodeObjectForKey:@"pictures"];
    }
    return self;
}

//辅助查看
-(NSString *)description
{
    return [NSString stringWithFormat:@"name = %@,time = %@,self.name,self.time];
}
```

既然是本地存储，肯定就是需要对本地文件进行操作了，说到本地操作，必然会请到`NSFileManager`这个功能强大的类，因此这里会封装一个对文件进行操作的类`FileManager`

声明只需要声明存数据以及获取数据的方法即可，这里声明了两对方法，一对是对单个Model的存和取，另一对是对存放Model数组的存和取。

```text
#import <Foundation/Foundation.h>
#import "MessageObject.h"

@interface FileManager : NSObject
/*
 *  存数据
 */
- (void)storgaeObject:(MessageObject * _Nonnull)messageObject;
- (void)storgaeObjects:(NSArray <MessageObject *> * _Nonnull)messageObjects;
/*
 *  获取数据
 */
- ( MessageObject * _Nullable )readMessageObject;
- ( NSArray<MessageObject *> * _Nullable )readMessageObjects;

@end
```

在这里是将数据存到沙盒路径下的一个目录中\(毕竟从目录中找是很方便的，不然多个文件会显得非常的乱\),因此写了几个属性的Getter方法，方便取值:

```text
#pragma mark - Getter Function
//沙盒目录
-(NSString *)documentPath
{
    return [NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, true) firstObject];
}
//沙盒目录下的存储目录，文件夹名可以随便取
- (NSString *)documentAchiverPath
{
    return [self.documentPath stringByAppendingPathComponent:@"Achiver"];
}
```

既然是存沙盒中的一个自定义文件夹中，那么就需要一开始创建一个文件夹，创建方法如下:

```text
#pragma mark - CreateFolder
- (void)createFolder
{
    //拼接路径
    NSString * path = [self documentAchiverPath];
    BOOL isDirectory;
    //查找是否存在这个文件夹,isDirectory用来判断是文件夹还是文件，如果路径不存在，返回为undefined，表示不能确定
    if ([[NSFileManager defaultManager] fileExistsAtPath:path isDirectory:&isDirectory])
    {
        //存在这个文件夹
        return;//不做操作
    }
    //不存在创建文件夹
    else {
        [[NSFileManager defaultManager] createDirectoryAtPath:path withIntermediateDirectories:true attributes:nil error:nil];
    }
}
```

准备工作已经完毕，接下来就是存数据与取数据的方法了，在创建对象的时候创建存储的文件夹，就是调用`createFolder`方法即可:

```text
- (instancetype)init
{
    if (self = [super init])
    {
        //创建文件夹
        [self createFolder];
    }
    return self;
}
```

最后就是存数据，大同小异:

```text
#pragma mark - 存储-获取单个数据
-(void)storgaeObject:(MessageObject * _Nonnull)messageObject
{
    //拼接路径，格式随意
    NSString * path = [[self documentAchiverPath] stringByAppendingPathComponent:@"messageObject.achiver"];
    //开始编码存数据
    [NSKeyedArchiver archiveRootObject:messageObject toFile:path];
}
-(MessageObject * _Nullable)readMessageObject
{
    //拼接路径
    NSString * path = [[self documentAchiverPath] stringByAppendingPathComponent:@"messageObject.achiver"];
    //开始解码获取
    return [NSKeyedUnarchiver unarchiveObjectWithFile:path];
}
#pragma mark - 存储-获取数组数据
-(void)storgaeObjects:(NSArray<MessageObject *> * _Nonnull)messageObjects
{
    //拼接路径
    NSString * path = [[self documentAchiverPath] stringByAppendingPathComponent:@"messageObjects.achiver"];
    //开始编码存数据
    [NSKeyedArchiver archiveRootObject:messageObjects toFile:path];
}
-(NSArray<MessageObject *> * _Nullable)readMessageObjects
{
    //拼接路径
    NSString * path = [[self documentAchiverPath] stringByAppendingPathComponent:@"messageObjects.achiver"];
    //开始解码获取
    return [NSKeyedUnarchiver unarchiveObjectWithFile:path];
}
```

使用：

```text
   //初始化一个FileManager
    FileManager * fileManager = [[FileManager alloc]init];
    //初始化一个Object对象
    MessageObject * messageObject1 = [[MessageObject alloc]init];
    messageObject1.name = @"Yue";
    messageObject1.time = @"2016-03-23";
    messageObject1.pictures = @[@"1111111",@"2222222",@"3333333"];
    NSArray <MessageObject *> *  messageObjects = @[messageObject1];
    //开始存数据
    [fileManager storgaeObject:messageObject1];
    [fileManager storgaeObjects:messageObjects];
    //获取数组
    NSLog(@"messObject = %@",[fileManager readMessageObject]);
    NSArray * datas = [fileManager readMessageObjects];
```

**注意**

* 必须遵循并实现NSCoding协议
* 保存文件的扩展名可以任意指定
* 继承时必须先调用父类的归档解档方法

#### **SQLite3** <a id="h3--strong-sqlite3-strong-"></a>

之前的所有存储方法，都是覆盖存储。如果想要增加一条数据就必须把整个文件读出来，然后修改数据后再把整个内容覆盖写入文件。所以它们都不适合存储大量的内容。

**字段类型**

表面上SQLite将数据分为以下几种类型：

* integer : 整数
* real : 实数（浮点数）
* text : 文本字符串
* blob : 二进制数据，比如文件，图片之类的

实际上SQLite是无类型的。即不管你在创表时指定的字段类型是什么，存储是依然可以存储任意类型的数据。而且在创表时也可以不指定字段类型。SQLite之所以什么类型就是为了良好的编程规范和方便开发人员交流，所以平时在使用时最好设置正确的字段类型！**主键必须设置成integer**

**准备工作**

准备工作就是导入依赖库啦，在iOS中要使用SQLite3，需要添加库文件：libsqlite3.dylib并导入主头文件，这是一个C语言的库，所以直接使用SQLite3还是比较麻烦的。

**使用**

* 创建数据库并打开

操作数据库之前必须先指定数据库文件和要操作的表，所以使用SQLite3，首先要打开数据库文件，然后指定或创建一张表。

```text
/**
*  打开数据库并创建一个表
*/
- (void)openDatabase {
   //1.设置文件名
   NSString *filename = [NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES).firstObject stringByAppendingPathComponent:@"person.db"];
   //2.打开数据库文件，如果没有会自动创建一个文件
   NSInteger result = sqlite3_open(filename.UTF8String, &_sqlite3);
   if (result == SQLITE_OK) {
       NSLog(@"打开数据库成功！");
       //3.创建一个数据库表
       char *errmsg = NULL;
       sqlite3_exec(_sqlite3, "CREATE TABLE IF NOT EXISTS t_person(id integer primary key autoincrement, name text, age integer)", NULL, NULL, &errmsg);
       if (errmsg) {
           NSLog(@"错误：%s", errmsg);
       } else {
           NSLog(@"创表成功！");
       }
   } else {
       NSLog(@"打开数据库失败！");
   }
}
```

* 执行指令

使用 sqlite3\_exec\(\) 方法可以执行任何SQL语句，比如创表、更新、插入和删除操作。但是一般不用它执行查询语句，因为它不会返回查询到的数据。

```text
/**
*  往表中插入1000条数据
*/
- (void)insertData {
NSString *nameStr;
NSInteger age;
for (NSInteger i = 0; i < 1000; i++) {
  nameStr = [NSString stringWithFormat:@"Bourne-%d", arc4random_uniform(10000)];
  age = arc4random_uniform(80) + 20;
  NSString *sql = [NSString stringWithFormat:@"INSERT INTO t_person (name, age) VALUES('%@', '%ld')", nameStr, age];
  char *errmsg = NULL;
  sqlite3_exec(_sqlite3, sql.UTF8String, NULL, NULL, &errmsg);
  if (errmsg) {
      NSLog(@"错误：%s", errmsg);
  }
}
NSLog(@"插入完毕！");
}
```

* 查询指令

前面说过一般不使用 sqlite3\_exec\(\) 方法查询数据。因为查询数据必须要获得查询结果，所以查询相对比较麻烦。示例代码如下：

* * sqlite3\_prepare\_v2\(\) : 检查sql的合法性
  * sqlite3\_step\(\) : 逐行获取查询结果，不断重复，直到最后一条记录
  * sqlite3\_coloum\_xxx\(\) : 获取对应类型的内容，iCol对应的就是SQL语句中字段的顺序，从0开始。根据实际查询字段的属性，使用sqlite3\_column\_xxx取得对应的内容即可。
  * sqlite3\_finalize\(\) : 释放stmt

```text
/**
*  从表中读取数据到数组中
*/
- (void)readData {
   NSMutableArray *mArray = [NSMutableArray arrayWithCapacity:1000];
   char *sql = "select name, age from t_person;";
   sqlite3_stmt *stmt;
   NSInteger result = sqlite3_prepare_v2(_sqlite3, sql, -1, &stmt, NULL);
   if (result == SQLITE_OK) {
       while (sqlite3_step(stmt) == SQLITE_ROW) {
           char *name = (char *)sqlite3_column_text(stmt, 0);
           NSInteger age = sqlite3_column_int(stmt, 1);
           //创建对象
           Person *person = [Person personWithName:[NSString stringWithUTF8String:name] Age:age];
           [mArray addObject:person];
       }
       self.dataList = mArray;
   }
   sqlite3_finalize(stmt);
}
```

**总结**

总得来说，SQLite3的使用还是比较麻烦的，因为都是些c语言的函数，理解起来有些困难。不过在一般开发过程中，使用的都是第三方开源库 FMDB，封装了这些基本的c语言方法，使得我们在使用时更加容易理解，提高开发效率。

#### **FMDB** <a id="h3--strong-fmdb-strong-"></a>

**简介**

FMDB是iOS平台的SQLite数据库框架，它是以OC的方式封装了SQLite的C语言API，它相对于cocoa自带的C语言框架有如下的优点:

使用起来更加面向对象，省去了很多麻烦、冗余的C语言代码

对比苹果自带的Core Data框架，更加轻量级和灵活

提供了多线程安全的数据库操作方法，有效地防止数据混乱

注：[FMDB的gitHub地址](https://github.com/ccgus/fmdb)

**核心类**

FMDB有三个主要的类：

* FMDatabase

一个FMDatabase对象就代表一个单独的SQLite数据库，用来执行SQL语句

* FMResultSet

使用FMDatabase执行查询后的结果集

* FMDatabaseQueue

用于在多线程中执行多个查询或更新，它是线程安全的

**打开数据库**

和c语言框架一样，FMDB通过指定SQLite数据库文件路径来创建FMDatabase对象，但FMDB更加容易理解，使用起来更容易，使用之前一样需要导入sqlite3.dylib。打开数据库方法如下：

```text
NSString *path = [NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES).firstObject stringByAppendingPathComponent:@"person.db"];
FMDatabase *database = [FMDatabase databaseWithPath:path];    
if (![database open]) {
    NSLog(@"数据库打开失败！");
}
```

值得注意的是，Path的值可以传入以下三种情况：

* 具体文件路径，如果不存在会自动创建
* 空字符串@””，会在临时目录创建一个空的数据库，当FMDatabase连接关闭时，数据库文件也被删除
* nil，会创建一个内存中临时数据库，当FMDatabase连接关闭时，数据库会被销毁

**更新**

在FMDB中，除查询以外的所有操作，都称为“更新”, 如：create、drop、insert、update、delete等操作，使用executeUpdate:方法执行更新：

```text
//常用方法有以下3种：   
- (BOOL)executeUpdate:(NSString*)sql, ...
- (BOOL)executeUpdateWithFormat:(NSString*)format, ...
- (BOOL)executeUpdate:(NSString*)sql withArgumentsInArray:(NSArray *)arguments
//示例
[database executeUpdate:@"CREATE TABLE IF NOT EXISTS t_person(id integer primary key autoincrement, name text, age integer)"];   
//或者  
[database executeUpdate:@"INSERT INTO t_person(name, age) VALUES(?, ?)", @"Bourne", [NSNumber numberWithInt:42]];
```

**查询**

查询方法也有3种，使用起来相当简单：

```text
- (FMResultSet *)executeQuery:(NSString*)sql, ...
- (FMResultSet *)executeQueryWithFormat:(NSString*)format, ...
- (FMResultSet *)executeQuery:(NSString *)sql withArgumentsInArray:(NSArray *)arguments
```

查询示例：

```text
//1.执行查询
FMResultSet *result = [database executeQuery:@"SELECT * FROM t_person"];
//2.遍历结果集
while ([result next]) {
    NSString *name = [result stringForColumn:@"name"];
    int age = [result intForColumn:@"age"];
}
```

**线程安全**

在多个线程中同时使用一个FMDatabase实例是不明智的。不要让多个线程分享同一个FMDatabase实例，它无法在多个线程中同时使用。 如果在多个线程中同时使用一个FMDatabase实例，会造成数据混乱等问题。所以，请使用 FMDatabaseQueue，它是线程安全的。以下是使用方法：

* 创建队列。

```text
FMDatabaseQueue *queue = [FMDatabaseQueue databaseQueueWithPath:aPath];
```

* 使用队列

```text
[queue inDatabase:^(FMDatabase *database) {    
          [database executeUpdate:@"INSERT INTO t_person(name, age) VALUES (?, ?)", @"Bourne_1", [NSNumber numberWithInt:1]];    
          [database executeUpdate:@"INSERT INTO t_person(name, age) VALUES (?, ?)", @"Bourne_2", [NSNumber numberWithInt:2]];    
          [database executeUpdate:@"INSERT INTO t_person(name, age) VALUES (?, ?)", @"Bourne_3", [NSNumber numberWithInt:3]];      
          FMResultSet *result = [database executeQuery:@"select * from t_person"];    
         while([result next]) {   
         }    
}];
```

而且可以轻松地把简单任务包装到事务里：

```text
[queue inTransaction:^(FMDatabase *database, BOOL *rollback) {    
          [database executeUpdate:@"INSERT INTO t_person(name, age) VALUES (?, ?)", @"Bourne_1", [NSNumber numberWithInt:1]];    
          [database executeUpdate:@"INSERT INTO t_person(name, age) VALUES (?, ?)", @"Bourne_2", [NSNumber numberWithInt:2]];    
          [database executeUpdate:@"INSERT INTO t_person(name, age) VALUES (?, ?)", @"Bourne_3", [NSNumber numberWithInt:3]];      
          FMResultSet *result = [database executeQuery:@"select * from t_person"];    
             while([result next]) {   
             }   
           //回滚
           *rollback = YES;  
    }];
```

FMDatabaseQueue 后台会建立系列化的G-C-D队列，并执行你传给G-C-D队列的块。这意味着 你从多线程同时调用调用方法，GDC也会按它接收的块的顺序来执行。

**CoreData**

详见：[我要娶你做我的CoreData！](http://www.jianshu.com/p/6e048f7c5812)

