# 网络请求

#### **NSURLSession** <a id="h3--strong-nsurlsession-strong-"></a>

```text
//NSURLSession
-(void)demo {
    //访问百度首页
    //1. 创建一个网络请求
    NSURL *url = [NSURL URLWithString:@"http://m.baidu.com"];
    //2.创建请求对象
    NSURLRequest *request = [NSURLRequest requestWithURL:url];
    //3.获得会话对象
    NSURLSession *session=[NSURLSession sharedSession];
    //4.根据会话对象创建一个Task(发送请求）
    /*
     第一个参数：请求对象
     第二个参数：completionHandler回调（请求完成【成功|失败】的回调）
     data：响应体信息（期望的数据）
     response：响应头信息，主要是对服务器端的描述
     error：错误信息，如果请求失败，则error有值
     */
    NSURLSessionDataTask *dataTask=[session dataTaskWithRequest:request completionHandler:^(NSData * _Nullable data, NSURLResponse * _Nullable response, NSError * _Nullable error) {
            //response ： 响应：服务器的响应
            //data：二进制数据：服务器返回的数据。（就是我们想要的内容）
            //error：链接错误的信息
            NSLog(@"网络响应：response：%@",response);
            //根据返回的二进制数据，生成字符串！NSUTF8StringEncoding：编码方式
            NSString *html = [[NSString alloc]initWithData:data encoding:NSUTF8StringEncoding];
            //在客户端直接打开一个网页！
            //客户端服务器：UIWebView
            //将浏览器加载到view上
            dispatch_async(dispatch_get_main_queue(), ^{
            //实例化一个客户端浏览器
            UIWebView *web = [[UIWebView alloc]initWithFrame:self.view.bounds];
            //加载html字符串：baseURL：基准的地址：相对路径/绝对路径
            [web loadHTMLString:html baseURL:nil];
            [self.view addSubview:web];
            });
            //        //在本地保存百度首页
            //        [data writeToFile:@"/Users/Liu/Desktop/baidu.html" atomically:YES];
        }
    ];
    //5.执行任务
    [dataTask resume];
}
```

使用NSURLSession发送GET请求的方法和NSURLConnection类似，整个过程如下：  
　　　　1）确定请求路径（一般由公司的后台开发人员以接口文档的方式提供），GET请求参数直接跟在URL后面  
　　　　2）创建请求对象（默认包含了请求头和请求方法【GET】），此步骤可以省略  
　　　　3）创建会话对象（NSURLSession）  
　　　　4）根据会话对象创建请求任务（NSURLSessionDataTask）  
　　　　5）执行Task  
　　　　6）当得到服务器返回的响应后，解析数据（XML\|JSON\|HTTP）

使用NSURLSession发送POST请求的方法和NSURLConnection类似，整个过程如下：  
　　　　1）确定请求路径（一般由公司的后台开发人员以接口文档的方式提供）  
　　　　2）创建可变的请求对象（因为需要修改），此步骤不可以省略  
　　　　3）修改请求方法为POST  
　　　　4）设置请求体，把参数转换为二进制数据并设置请求体  
　　　　5）创建会话对象（NSURLSession）  
　　　　6）根据会话对象创建请求任务（NSURLSessionDataTask）  
　　　　7）执行Task  
　　　　8）当得到服务器返回的响应后，解析数据（XML\|JSON\|HTTP）

```text
//post方法   
//1.创建会话对象
    NSURLSession *session=[NSURLSession sharedSession];

    //2.根据会话对象创建task
    NSURL *url=[NSURL URLWithString:@"http://123.207.175.144/Test1.php"];

    //3.创建可变的请求对象
    NSMutableURLRequest *request=[NSMutableURLRequest requestWithURL:url];

    //4.修改请求方法为POST
    request.HTTPMethod=@"POST";

    //5.设置请求体
    request.HTTPBody=[@"Login=1" dataUsingEncoding:NSUTF8StringEncoding];

    //6.根据会话对象创建一个Task(发送请求）
         /*
                  第一个参数：请求对象
                  第二个参数：completionHandler回调（请求完成【成功|失败】的回调）
                             data：响应体信息（期望的数据）
                             response：响应头信息，主要是对服务器端的描述
                             error：错误信息，如果请求失败，则error有值
          */
     NSURLSessionDataTask *dataTask = [session dataTaskWithRequest:request completionHandler:^(NSData * _Nullable data, NSURLResponse * _Nullable response, NSError * _Nullable error){

        //8.解析数据
        NSDictionary *dict=[NSJSONSerialization JSONObjectWithData:data options:kNilOptions error:nil];
        NSLog(@"%@",dict);
    }];

     //7.执行任务
    [dataTask resume];
```

有的时候，我们可能需要监听网络请求的过程（如下载文件需监听文件下载进度），那么就需要用到代理方法。

```text
//代理方法
#import "ViewController.h"

@interface ViewController ()<NSURLSessionDataDelegate>
@property (nonatomic, strong) NSMutableData *responseData;
@end

@implementation ViewController

-(NSMutableData *)responseData
 {
         if (_responseData == nil) {
                 _responseData = [NSMutableData data];
             }
         return _responseData;
}
- (void)viewDidLoad {
    [super viewDidLoad];
    // Do any additional setup after loading the view, typically from a nib.
     //[self performSelector:@selector(getAndSynchronousMethod)];
    [self delegateTest];
}
-(void)delegateTest
{
         //1.确定请求路径
         NSURL *url = [NSURL URLWithString:@"http://123.207.175.144/Test1.php?Login=1"];
         //2.创建请求对象
         //请求对象内部默认已经包含了请求头和请求方法（GET）
         NSURLRequest *request = [NSURLRequest requestWithURL:url];
         //3.获得会话对象,并设置代理
         /*
                  第一个参数：会话对象的配置信息defaultSessionConfiguration 表示默认配置
                  第二个参数：谁成为代理，此处为控制器本身即self
                  第三个参数：队列，该队列决定代理方法在哪个线程中调用，可以传主队列|非主队列
                  [NSOperationQueue mainQueue]   主队列：   代理方法在主线程中调用
                  [[NSOperationQueue alloc]init] 非主队列： 代理方法在子线程中调用
                  */
         NSURLSession *session = [NSURLSession sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration] delegate:self delegateQueue:[NSOperationQueue mainQueue]];
         //4.根据会话对象创建一个Task(发送请求）
         NSURLSessionDataTask *dataTask = [session dataTaskWithRequest:request];
         //5.执行任务
         [dataTask resume];
}
//1.接收到服务器响应的时候调用该方法
 -(void)URLSession:(NSURLSession *)session dataTask:(NSURLSessionDataTask *)dataTask didReceiveResponse:(NSURLResponse *)response completionHandler:(void (^)(NSURLSessionResponseDisposition))completionHandler
 {
         //在该方法中可以得到响应头信息，即response
         NSLog(@"didReceiveResponse--%@",[NSThread currentThread]);

         //注意：需要使用completionHandler回调告诉系统应该如何处理服务器返回的数据
         //默认是取消的
         /*
                     NSURLSessionResponseCancel = 0,        默认的处理方式，取消
                     NSURLSessionResponseAllow = 1,         接收服务器返回的数据
                     NSURLSessionResponseBecomeDownload = 2,变成一个下载请求
                     NSURLSessionResponseBecomeStream        变成一个流
                  */

         completionHandler(NSURLSessionResponseAllow);
}
//2.接收到服务器返回数据的时候会调用该方法，如果数据较大那么该方法可能会调用多次
 -(void)URLSession:(NSURLSession *)session dataTask:(NSURLSessionDataTask *)dataTask didReceiveData:(NSData *)data
 {
         NSLog(@"didReceiveData--%@",[NSThread currentThread]);

         //拼接服务器返回的数据
         [self.responseData appendData:data];
}
//3.当请求完成(成功|失败)的时候会调用该方法，如果请求失败，则error有值
 -(void)URLSession:(NSURLSession *)session task:(NSURLSessionTask *)task didCompleteWithError:(NSError *)error
 {
         NSLog(@"didCompleteWithError--%@",[NSThread currentThread]);

         if(error == nil)
             {
                     //解析数据
                     NSDictionary *dict = [NSJSONSerialization JSONObjectWithData:self.responseData options:kNilOptions error:nil];
                     NSLog(@"%@",dict);
                 }
}
@end
```

如果返回的字符有汉字，可以为NSDictionary提供一个分类，重写系统中的方法。

```text
//重写Dictionary
#import "NSDictionary+Log.h"

@implementation NSDictionary (Log)
-(NSString *)descriptionWithLocale:(id)locale indent:(NSUInteger)level
 {
         //初始化可变字符串
         NSMutableString *string = [NSMutableString string];
         //拼接开头[
         [string appendString:@"["];

         //拼接字典中所有的键值对
         [self enumerateKeysAndObjectsUsingBlock:^(id  _Nonnull key, id  _Nonnull obj, BOOL * _Nonnull stop) {
                 [string appendFormat:@"%@:",key];
                 [string appendFormat:@"%@",obj];
             }];
         //拼接结尾
         [string appendString:@"]"];
         return string;
}
@end
```

#### AFNetworking <a id="h3-afnetworking"></a>

* GET请求：

```text
//GET请求
-(void)demo1{
    NSString *urlString = @"http://www.liubaiqi.cn";

    AFHTTPSessionManager *manger =[AFHTTPSessionManager manager];

    [manger GET:urlString parameters:nil progress:^(NSProgress * _Nonnull downloadProgress) {

    } success:^(NSURLSessionDataTask * _Nonnull task, id  _Nullable responseObject) {
        NSLog(@"成功");
    } failure:^(NSURLSessionDataTask * _Nullable task, NSError * _Nonnull error) {
        NSLog(@"%@",error);
    }];
}
```

* POST请求：

```text
//POST请求
-(void)demo2{
    NSString *urlString = @"http://192.168.1.101:8080";

    AFHTTPSessionManager *manger =[AFHTTPSessionManager manager];

    NSMutableDictionary *parameter= @{@"":@"",@"":@""};

    [manger POST:urlString parameters:parameter progress:^(NSProgress * _NonnulluploadProgress){

    }success:^(NSURLSessionDataTask * _Nonnull task, id  _Nullable responseObject) {
        NSLog(@"成功");
    } failure:^(NSURLSessionDataTask * _Nullable task, NSError * _Nonnull error) {
        NSLog(@"%@",error);
    }];
}
```

* Download请求：

```text
//DownLoad请求
-(void)demo3{
    //1. 创建NSURLSessionConfiguration
    NSURLSessionConfiguration *configuration = [NSURLSessionConfiguration defaultSessionConfiguration];

    //2. 创建管理者对象
    AFURLSessionManager *manager = [[AFURLSessionManager alloc]initWithSessionConfiguration:configuration];

    //3. 设置url
    NSURL *url = [NSURL URLWithString:@"http://127.0.0.1/1.mp4"];

    //4. 创建请求对象
    NSURLRequest *request = [NSURLRequest requestWithURL:url];

    //5. 下载任务
    NSURLSessionDownloadTask *downloadTask = [manager downloadTaskWithRequest:request progress:^(NSProgress * _Nonnull downloadProgress) {
        //打印下载进度
        NSLog(@"%lf",1.0*downloadProgress.completedUnitCount/downloadProgress.totalUnitCount);

    } destination:^NSURL * _Nonnull(NSURL * _Nonnull targetPath, NSURLResponse * _Nonnull response) {
        //设置下载路径
        NSURL *documentsDirectoryURL = [[NSFileManager defaultManager]URLForDirectory:NSDocumentDirectory inDomain:NSUserDomainMask appropriateForURL:nil create:NO error:nil];

        return [documentsDirectoryURL URLByAppendingPathComponent:[response suggestedFilename]];

    } completionHandler:^(NSURLResponse * _Nonnull response, NSURL * _Nullable filePath, NSError * _Nullable error) {
        //下载完成
        NSLog(@"File downloaded to : %@",filePath);
    }];
    //启动任务
    [downloadTask resume];
}
```

* Upload请求：

```text
//UpLoad请求
-(void)demo4{
    //创建NSURLSessionConfiguration
    NSURLSessionConfiguration *configuration = [NSURLSessionConfiguration defaultSessionConfiguration];

    //创建管理者对象
    AFURLSessionManager *manager = [[AFURLSessionManager alloc]initWithSessionConfiguration:configuration];

    //设置url
    NSURL *url = [NSURL URLWithString:@"http://127.0.0.1"];
    //创建请求对象
    NSURLRequest *request = [NSURLRequest requestWithURL:url];
    //文件路径
    NSURL *filePath = [NSURL fileURLWithPath:@"file://Users/Liu/Desktop/Note"];

    //上传任务
    NSURLSessionUploadTask *uploadTask = [manager uploadTaskWithRequest:request fromFile:filePath progress:nil completionHandler:^(NSURLResponse * _Nonnull response, id  _Nullable responseObject, NSError * _Nullable error) {
        if(error){
            //错误
            NSLog(@"Error:%@",error);
        }else{
            //成功
            NSLog(@"Success %@ %@",response,responseObject);
        }
    }];
    //启动任务
    [uploadTask resume];
}
```

* 网络状态：

```text
//网络状态
-(void)demo5{
    //1. 创建网络监测者
    AFNetworkReachabilityManager *manager = [AFNetworkReachabilityManager sharedManager];

    [manager setReachabilityStatusChangeBlock:^(AFNetworkReachabilityStatus status) {
        //监测网络改变
        switch (status) {
            case AFNetworkReachabilityStatusUnknown:
                NSLog(@"未知网络状态");
                break;
            case AFNetworkReachabilityStatusNotReachable:
                NSLog(@"无网络");
                break;
            case AFNetworkReachabilityStatusReachableViaWWAN:
                NSLog(@"蜂窝数据网络");
                break;
            case AFNetworkReachabilityStatusReachableViaWiFi:
                NSLog(@"WiFi网络");
                break;

            default:
                break;
        }
    }];
}
```

