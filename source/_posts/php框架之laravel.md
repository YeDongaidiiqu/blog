---
title: php框架之laravel
date: 2021-04-09 01:44:03
tags: php framework
---


##### 整体加载流程

1.  index.php
<!--more-->  
   ```
    <?php
   
   /**
    * Laravel - A PHP Framework For Web Artisans
    *
    * @package  Laravel
    * @author   Taylor Otwell <taylor@laravel.com>
    */
   
   /*
   |--------------------------------------------------------------------------
   | Register The Auto Loader
   |--------------------------------------------------------------------------
   |
   | Composer provides a convenient, automatically generated class loader for
   | our application. We just need to utilize it! We'll simply require it
   | into the script here so that we don't have to worry about manual
   | loading any of our classes later on. It feels great to relax.
   |
   */
   
   require __DIR__.'/../bootstrap/autoload.php'; //引入composer自动加载
 
   /*
   |--------------------------------------------------------------------------
   | Turn On The Lights
   |--------------------------------------------------------------------------
   |
   | We need to illuminate PHP development, so let us turn on the lights.
   | This bootstraps the framework and gets it ready for use, then it
   | will load up this application so that we can run it and send
   | the responses back to the browser and delight our users.
   |
   */
   
   $app = require_once __DIR__.'/../bootstrap/app.php'; //整个应用的实例化,及初始化
   1. 
       public function __construct($basePath = null)
       {
           if ($basePath) {
               $this->setBasePath($basePath);  //设置各个模块的路径
           }
   
           $this->registerBaseBindings(); //将基础的容器绑定到app上
   
           $this->registerBaseServiceProviders(); //初始化三个基本的服务
           	 $this->register(new EventServiceProvider($this)); //事件，里面初始化了一个队列
                $this->register(new LogServiceProvider($this)); //日志
                $this->register(new RoutingServiceProvider($this)); //路由
   
           $this->registerCoreContainerAliases(); //注册一些框架的核心组件的别名到app上
       }
   2.
   实例化web 的核心
   $app->singleton(
       Illuminate\Contracts\Http\Kernel::class,
       App\Http\Kernel::class
   );
   实例化cli的核心，所以artisn 命令能够执行
   $app->singleton(
       Illuminate\Contracts\Console\Kernel::class,
       App\Console\Kernel::class
   );
   实例化全局异常处理
   $app->singleton(
       Illuminate\Contracts\Debug\ExceptionHandler::class,
       App\Exceptions\Handler::class
   );    
   
   /*
   |--------------------------------------------------------------------------
   | Run The Application
   |--------------------------------------------------------------------------
   |
   | Once we have the application, we can handle the incoming request
   | through the kernel, and send the associated response back to
   | the client's browser allowing them to enjoy the creative
   | and wonderful application we have prepared for them.
   |
   */
   
   $kernel = $app->make(Illuminate\Contracts\Http\Kernel::class);
   
   $response = $kernel->handle(
       $request = Illuminate\Http\Request::capture()  //通过$_GET $SERVER $_POST 构造Request
   );
   ··· handle 核心方法
    protected function sendRequestThroughRouter($request)
       {
           $this->app->instance('request', $request);
   
           Facade::clearResolvedInstance('request');
   
           $this->bootstrap();
   
           return (new Pipeline($this->app))
                       ->send($request)
                       ->through($this->app->shouldSkipMiddleware() ? [] : $this->middleware)
                       ->then($this->dispatchToRouter());
                       ...
                       public function then(Closure $destination)
       				{
           				$pipeline = array_reduce(
              				 array_reverse($this->pipes) //中间件是倒叙执行的, $this->carry(), $this-          >prepareDestination($destination)
           				);
           				return $pipeline($this->passable);
       				}
                       
       }
   ···
   
   $response->send();  //response 发送数据 pipeline
   
   
   
   $kernel->terminate($request, $response); //调用一些回调中间件
   
   ```

   


