一、 composer的安装：
--------------------------------------------------------------
  1.Composer是什么？
    是 PHP 用来管理依赖（dependency）关系的工具。
    你可以在自己的项目中声明所依赖的外部工具库（libraries），
    Composer 会帮你安装这些依赖的库文件。
  2.网址：https://getcomposer.org
     下载：https://getcomposer.org/download/
     
     中国全量镜像：http://pkg.phpcomposer.com/
        启用本镜像服务命令：
            composer config -g repo.packagist composer https://packagist.phpcomposer.com
            或
            composer config repo.packagist composer https://packagist.phpcomposer.com
  3.Composer常用命令：
        composer -v  查看版本
        composer selfupdate 更新composer
       
二、安装Laravel框架
------------------------------------------------------------------
    文档网站：http://www.golaravel.com/
    选择5.1版本：http://www.golaravel.com/laravel/docs/5.1/
    
    对运行环境的要求： 
        - PHP >= 5.5.9 
        - OpenSSL PHP 扩展 
        - PDO PHP 扩展 
        - Mbstring PHP 扩展 
        - Tokenizer PHP 扩展

    通过 Composer Create-Project 命令安装 Laravel：
       命令：composer create-project laravel/laravel --prefer-dist
    
三、本地域名解析与apapche虚拟主机配置（window下）
------------------------------------------------------------------
    1. 打开：C:\Windows\System32\drivers\etc目录中的hosts文件：
        配置信息：127.0.0.1 自定义主机名
        
    2. 在apache的httpd-vhosts.conf配置文件中配置
        <VirtualHost *:80>
            ServerAdmin zhangtao@lampbrother.net
            DocumentRoot "虚拟主机目录位置"
            ServerName 虚拟主机名
            ErrorLog "logs/虚拟主机名-error.log"
            CustomLog "logs/虚拟主机名-access.log" common
        </VirtualHost>
        
四、HTTP 路由
-----------------------------------------------------------------------
    1. 基本路由： 根路由 跳转根页面
        Route::get('/', function()
        {
            return 'Hello World';
        });
        Route::post('foo/bar', function()   //post传值
        {
            return 'Hello World';
        });
        Route::put('foo/bar', function()    //get传值
        {
            //
        });
        Route::delete('foo/bar', function()     //删除
        {
            //
        });
        Route::match(['get', 'post'], '/', function() //多种请求注册路由
        {
            return 'Hello World';
        });
    2. 路由参数   带参数传值
        Route::get('user/{id}', function($id)
        {
            return 'User '.$id;
        });

五. 环境配置与数据库连接
---------------------------------------------------------------------------
    修改：项目下的.env文件 修改数据库名 密码等
    
        
六、laravelDebug安装与调试命令
--------------------------------------------------------------------------
    网址：https://github.com/barryvdh/laravel-debugbar
    
    安装命令：composer require barryvdh/laravel-debugbar
    进入：config/app.php文件
        配置：
            Barryvdh\Debugbar\ServiceProvider::class,
            'Debugbar' => Barryvdh\Debugbar\Facade::class,
            
    执行命令：php artisan vendor:publish --provider="Barryvdh\Debugbar\ServiceProvider"
    
 
 
七、控制器的创建
----------------------------------------------------------------------------
    创建一个RESTful资源控制器
    命令：php artisan make:controller StuController
    
    命令：php artisan make:controller StuController --plain (不好用)
        --plain表示创建一个空的控制器
    
    控制器中代码
    //在控制器中查询数据,并加载模板输出
    public function index()
    {
        $list = \DB::table('stu')->get();
        return view('stu.index',["list"=>$list]);
        //return view('stu.index', compact('list'));
    }
    
    在routes.php的路由文件中配置
    Route::get('stu/index', 'StuController@index');
    
	//以下方法均为compser 生成控制器
    public function index()
    {
        //$list = \DB::table('stu')->get();
        $list = \DB::table('stu')->paginate(5);
        
       
        //return view('stu.index',['list'=>[]]);
        return view('stu.index',["list"=>$list]);
        //return view('stu.index', compact('list'));
    }
    
    public function create()
    {
        return view("stu.create");
    }
    
    public function store(Request $request)
    {   
        //dd($request);
        $input = $request->all();
        unset($input['_token']);
       
        $id = \DB::table('stu')->insertGetId($input);
        return "添加成功！id号".$id;       
    }
    
    public function update()
    {
        return "update";
    }
    
    public function show($id)
    {
        $stu = \DB::table('stu')->where("id","=",$id)->first();
        dd($stu);
    }

    public function destroy($id)
    {
        return "delete".$id;
    }
    
    
   八、Laravel 中Request请求对象的使用
-----------------------------------------------------------
    1. 使用方式：
        1.1 通过 Facade
            在控制器中使用： use Request导入
            在控制器的方法中获取参数信息：$name = Request::input('name');
            
        1.2 通过依赖注入
            在控制器中使用：use Illuminate\Http\Request; 导入
            在控制器的方法中使用参数注入request对象
                public function store(Request $request)
                {
                    $name = $request->input('name');
                }
                
    2. 取得输入数据:
        2.1 $name = Request::input('name'); 获取请求参数name的值
        2.2 $name = Request::input('name', 'Sally'); 获取参数name的值，若没有则使用Sally默认值
        2.3 if (Request::has('name')){ ... }  判断是否有此参数。
        
        2.4 Request::all();  获取所有参数值
        
        2.5 获取部分参数值
            $data = $request->only("name","id"); //获取部分参数值
            $data = $request->except("name"); //获取指定外部分参数值
            
        2.6 获取数组中的值
  
  九.  Laravel中的响应:Response
--------------------------------------------------------
    1. 基本响应
        1.1 从路由返回字串
                Route::get("/hh",function(){
                    return "Hello World!";
                });
                
        1.2 自定义响应:
              在控制器中使用response: use Illuminate\Http\Response;
              控制器方法中的代码
               $content="Hello Laravel!";
               $status = 200;
               $value = "text/html";
               return (new Response($content, $status))->header('Content-Type', $value);
        
        1.3 在响应送出视图  
               return response()->view('hello')->header('Content-Type',"text/html");
               
        1.4 附加 Cookies 到响应
                return response($content)->withCookie(cookie('name', 'value'));
                
    2. 重定向
        2.1 return redirect('user/login');
        
        
 十 视图
---------------------------------------------------------
    视图被保存在 resources/views 文件夹内 
    实例创捷一个vv.php视图文件
    
    //在控制器的方法中加载视图方式:
        1. return view("vv"); //加载视图
        2. return view("vv",['name'=>"zhangsan","age"=>20]); //加载视图,并携带参数
        3. return view("vv")->with("name","lisi")->with("age",30); //通过with携带参数值
        
    在视图中如何输出
        <body>
            <h2>Laravel框架视图测试</h2>
            姓名:<?php echo $name; ?>   年龄:<?php echo $age; ?>
        </body>
    

 十一 模板引擎:--Blade
---------------------------------------------------
    Blade 模板后缀名都要命名为 .blade.php
     
     
--------------------------------------------------
十二   数据填充
---------------------------------------------

    数据搜索加分页
-------------------------------------------------
    // 保存搜索的条件
    $where = [];
    $db = DB::table('user');
    if($request->has('name')){
        $db->where('name','like',"%{$request->input('name')}%");
        $where['name'] = $request->input('name');
    }
    if($request->has('sex')){
        $db->where('sex',$request->input('sex'));
        $where['sex'] = $request->input('sex');
    }
    $list = $db->paginate(3);
    
    return view('admin.user',['list'=>$list,'where'=>$where]);

    模板显示
    {{ $list->appends($where)->links() }}


    文件上传
--------------------------------------------------
    if($request->hasFile('mypic')){
        $file = $request->file('mypic');
        if ($file->isValid()) {
            //获取后缀
            $ext = $file->getClientOriginalExtension();
            $picname = time().rand(1000,9999).'.'.$ext;
            $file->move('./uploads/',$picname);
            if($file->getError()>0){
                echo '上传失败';
            }else{
                echo '上传成功';
            }
        }
    }
    // $name = $file->getClientOriginalName();          //获取上传文件的源文件名
    // $ext = $file->getClientOriginalExtension();      //获取源文件的后缀
    // $error = $file->getError();                      //获取错误号
    // $size = $file->getClientSize();                  //获取文件的大小
    // $type = $file->getClientMimeType();              //获取文件的类型



    自定义图片等比缩放类的使用
---------------------------------------------------
    1. 将事先定义好的Image.php类放置到App/Org/目录下(其中Org自定义目录).
       在类中定义命名空间:namespace App\Org;
       
    2. 在使用的控制类中引入当前类: use App\Org\Image;
        具体使用:
            //执行缩放
            $img = new Image();
            $img->open("./uploads/".$filename)->thumb(100,100)->save("./uploads/s_".$filename);
 


    使用第三方图片处理插件:intervention/image
-------------------------------------------------------
    1. 安装:使用Composer命令,需要在你的Laravel框架目录下执行如下命令执行安装
        
        $ php composer.phar require intervention/image 
        或 composer require intervention/image
        
    2. 添加配置
        修改/config/app.php配置文件
            在$providers属性中添加: Intervention\Image\ImageServiceProvider::class,
            在$aliases属性中添加:'Image' => Intervention\Image\Facades\Image::class,
            
    3. 执行命令让当前Laravel使用当前插件(原使用的是GD库)
        $ php artisan vendor:publish --provider="Intervention\Image\ImageServiceProviderLaravel5"
        
        
    4. 测试: 在当前项目控制器中就可以使用下面代码测试
        use Intervention\Image\ImageManagerStatic as Image;
    
        $img = Image::make("./uploads/".$filename)->resize(100,100);
        $img->save("./uploads/s_".$filename); //另存为
        return $img->response("jpg"); //输出
        
        //执行等比缩放
        $img->resize(null, 400, function ($constraint) {
                                    $constraint->aspectRatio();
                                    $constraint->upsize();
                                });
        

    表单验证
------------------------------------------------------------------------------------
    一、表单验证：
    1. 控制器验证：
      public function store(Request $request)
      {   
        //验证
        $this->validate($request, [
            'name' => 'required|max:255',
            'age' => 'required|numeric|max:100|min:10',
        ]);
        ...
      }
      
    2. 在表单页上显示：
        @if (count($errors) > 0)
            <div class="alert alert-danger">
                <ul style="color:red;">
                @foreach ($errors->all() as $error)
                    <li>{{ $error }}</li>
                @endforeach
                </ul>
            </div>
        @endif
        
   
    在Laravel框架中使用验证码扩展（gregwar/captcha）
－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－

首先呢在laravel5中默认是没有提供验证码的，这里我们需要使用第三方提供的库:gregwar/captcha

通过composer安装：

在composer.json的require中加入"gregwar/captcha": "dev-master"，具体代码如下

"require": {
        "laravel/framework": "5.0.*",
        "gregwar/captcha": "dev-master"
    },

然后运行: composer update命令

使用gregwar/captcha库

使用就非常简单了，直接上代码
记得在顶部use Gregwar\Captcha\CaptchaBuilder;

function captch(){
    $builder = new CaptchaBuilder;
    $builder->build(150,32);
    //Session::set('phrase',$builder->getPhrase()); //存储验证码
    return response($builder->output())->header('Content-type','image/jpeg');
}

在视图中调用
<img src="{{ url('你定义的captch函数的路由') }}" >

验证就更简单了
function index(){
    $yanzhengma = Session::get('phrase');
    if($_POST['yanzhengma'] == $yanzhengma){
        echo 'success';//验证成功
    }

}
