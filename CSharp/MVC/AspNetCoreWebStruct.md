ASP.NET Core web结构解析
-----
#### [Kestrel服务器](https://www.cnblogs.com/Wddpct/p/6123653.html)
> `Kestrel`是一个基于libuv包含托管的跨平台`ASP.NET Core web`服务器，libuv是一个跨平台的异步I/O库。`ASP.NET Core`模板项目使用`Kestrel`作为默认的web服务器。它往往会被运行在一个如`IIS`或者`Nginx`的生产Web服务器之后



#### 文件结构 `[2.1 Core版本]`
![文件结构](/Image/CoreWeb.png)
* `Startup` 类 可用来定义请求处理管道和配置应用需要的服务。Startup类必须是公开的(public)  而且需要包含下列方法 `ConfigureServices`和 `Configure`.是应用程序的入口
     * **`ConfigureServices`** ：定义你的应用所使用的服务(ASP.NET MVC / Entity Framework Core / Identity) 
        ```
           服务:应用中用于通用调用的组件,服务通过依赖注入获取并使用.ASP.NET Core内置了一个简单的控制反转IOC容器
        
           默认支持构造器注入
           
        ```
        ```C#
        public void ConfigureServices(IServiceCollection services)
        {
            services.Configure<CookiePolicyOptions>(options =>
            {
                options.CheckConsentNeeded = context => true;
                options.MinimumSameSitePolicy = SameSiteMode.None;
            });


            services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
        }
        
        ```
        
     * **`Configure`** ：定义你的请求管道中的[中间件](https://baike.baidu.com/item/%E4%B8%AD%E9%97%B4%E4%BB%B6/452240?fr=aladdin)
        ```
          在ASP.NET Core中你可以使用中间件构建你的请求处理通道.ASP.NET Core中间件为一个HttpContent执行异步逻辑.
          
          然后按照顺序调研下一个中间件或者直接终止请求
          
          Configure指定如何响应每一个HTTP请求,必须接受一个IApplicationBuilder参数,一些额外服务,多个扩展方法用
          
          于配置管道以支持BrowserLink,错误,静态文件,ASP.NET MVC和Identity
          
          每一个Use方法都会把一个中间件加入请求管道，UseMvc 路由请求管道
        ```



        ```c#
            public void Configure(IApplicationBuilder app, IHostingEnvironment env)
            {
                if (env.IsDevelopment())
                {
                    app.UseDeveloperExceptionPage();
                }
                else
                {
                    app.UseExceptionHandler("/Home/Error");
                    app.UseHsts();
                }

                app.UseHttpsRedirection();
                app.UseStaticFiles();
                app.UseCookiePolicy();

                app.UseMvc(routes =>
                {
                    routes.MapRoute(
                        name: "default",
                        template: "{controller=Home}/{action=Index}/{id?}");
                });
            }
        ```
        
        
* `Program` 类 Main方法 关于Web服务器设置

    ```C#
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
        /*
          创建一个Web程序宿主,
         */
    }
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
          WebHost.CreateDefaultBuilder(args)
          .UseStartup<Startup>();
          /*
            CreateDefaultBuilder 按照生成模式来创建WEB应用程序主机,生产器提供定义WEB服务器例如UseKestrel和启动类
            UseStartup的方法Kestrel 是一个庆幸Web服务器可以在IIS或者其他Web服务器上面运行
           */
    }
    ```
    
* `网站根目录`：`是项目中类似于CSS,JS和图片文件公开,静态资源的目录.静态文件中间件将默认只读取We根目录和其子目录中的文件。Web根目录默认为
<content>/wwwroot，但是也可以通过WebHostBuilder`

------------

