**目录**

* [前言](#_label0)
* [新建项目](#_label1)
* [新建grpc客户端项目GrpcClient](#_label2)
* [结果展示](#_label3)
* [作者](#_label4)
 



---


[回到顶部](#_labelTop)# 前言


gRPC 是一种高性能、开源的远程过程调用（RPC）框架，它基于 Protocol Buffers（protobuf）定义服务，并使用 HTTP/2 协议进行通信。


[回到顶部](#_labelTop):[milou加速器](https://xinminxuehui.org)# 新建项目


新建解决方案GrpcDemo


新建webapi项目GrpcServer作为grpc服务端项目


添加包



```
    "Grpc.AspNetCore" Version="2.67.0" />
    "Grpc.Tools" Version="2.67.0">

```

新建文本文件greeter.proto



```
syntax = "proto3";

option csharp_namespace = "GrpcServer";

package greet;

// The greeting service definition.
service Greeter {
  // Sends a greeting
  rpc SayHello (HelloRequest) returns (HelloReply);
}

// The request message containing the user's name.
message HelloRequest {
  string name = 1;
}

// The response message containing the greetings.
message HelloReply {
  string message = 1;
}

```

编辑GrpcServer项目文件，添加


![](https://wxy-blog.oss-cn-hangzhou.aliyuncs.com/wxy-blog/2024/202411240739521.png)


新建类GreeterService.cs



```
using Grpc.Core;

namespace GrpcServer
{
    public class GreeterService : Greeter.GreeterBase
    {
        private readonly ILogger _logger;
        public GreeterService(ILogger logger)
        {
            _logger = logger;
        }

        public override Task SayHello(HelloRequest request, ServerCallContext context)
        {
            return Task.FromResult(new HelloReply
            {
                Message = "Hello " + request.Name
            });
        }
    }
}


```

修改Program.cs



```

using GrpcServer;

var builder = WebApplication.CreateBuilder(args);

// Add services to the container.

builder.Services.AddControllers();
// Learn more about configuring Swagger/OpenAPI at https://aka.ms/aspnetcore/swashbuckle
builder.Services.AddEndpointsApiExplorer();
builder.Services.AddSwaggerGen();

builder.Services.AddGrpc();

var app = builder.Build();

app.MapGrpcService();

// Configure the HTTP request pipeline.
if (app.Environment.IsDevelopment())
{
    app.UseSwagger();
    app.UseSwaggerUI();
}

app.UseHttpsRedirection();

app.UseAuthorization();

app.MapControllers();

app.Run();


```

就是添加下面两行代码



```
builder.Services.AddGrpc();

app.MapGrpcService();

```

[回到顶部](#_labelTop)# 新建grpc客户端项目GrpcClient


添加包



```
    "Google.Protobuf" Version="3.28.3" />
    "Grpc.Net.Client" Version="2.67.0" />
    "Grpc.Tools" Version="2.67.0">

```

复制服务器端端的greeter.proto到客户端项目


编辑GrpcClient项目文件，加


![](https://wxy-blog.oss-cn-hangzhou.aliyuncs.com/wxy-blog/2024/202411240744699.png)


编辑Program.cs文件



```
using Grpc.Net.Client;
using GrpcClient;

using var channel = GrpcChannel.ForAddress("https://localhost:7052");
var client = new Greeter.GreeterClient(channel);
var reply = await client.SayHelloAsync(
                  new HelloRequest { Name = "wxy" });
Console.WriteLine("Greeting: " + reply.Message);
Console.WriteLine("Press any key to exit...");
Console.ReadKey();

```

7052改成你的服务器端运行端口


[回到顶部](#_labelTop)# 结果展示


运行服务器端


![](https://wxy-blog.oss-cn-hangzhou.aliyuncs.com/wxy-blog/2024/202411240746032.png)


运行客户端


![](https://wxy-blog.oss-cn-hangzhou.aliyuncs.com/wxy-blog/2024/202411240746329.png)


[回到顶部](#_labelTop)# 作者


吴晓阳（手机：13736969112微信同号）


