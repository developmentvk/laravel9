# Laravel 9 Installation Tutorial
In this example, you will learn laravel 9 multi auth. i would like to share with you laravel 9 multiple auth.<br>

However, in this example, we will create very simple way and you can easily use with your laravel 9 application. so let's follow this step.<br><br>

**Your First Laravel Project**<br><br>

Before creating your first [Laravel](https://laravel.com) project, you should ensure that your local machine has [PHP](https://computingforgeeks.com/how-to-install-php-on-ubuntu-linux-system/) and [Composer](https://getcomposer.org/download/) installed (https://getcomposer.org/download/). If you are developing on macOS, PHP and Composer can be installed via Homebrew. In addition, we recommend installing [Node](https://nodejs.org/en/download/) and [NPM](https://docs.npmjs.com/downloading-and-installing-node-js-and-npm/).<br>

After you have installed PHP and Composer, you may create a new Laravel project via the Composer create-project command:<br><br>

**Step 1: Install Laravel 9**<br>
first of all we need to get fresh Laravel 9 version application using bellow command, So open your terminal OR command prompt and run bellow command:
```
composer create-project laravel/laravel demo
```
**OR**
```
composer create-project --prefer-dist laravel/laravel demo
```

After the project has been created, let's switch the directory of a newly created blog project<br>
```
cd demo
```
**Step 2: Database Configuration**<br>
In second step, we will make database configuration for example database name, username, password etc for our crud application of laravel 9. So let's open .env file and fill all details like as bellow:<br>
**.env**
```
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=here your database name(demo)
DB_USERNAME=here database username(root)
DB_PASSWORD=here database password(root)
```

**Step 3: Update Migration and Model**<br>
In this step, we need to add new row "is_admin" in users table and model. than we need to run migration. so let's change that on both file.<br>
<code>database/migrations/000_create_users_table.php</code>
```
<?php
use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;
class CreateUsersTable extends Migration {
    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up() {
        Schema::create('users', function (Blueprint $table) {
            $table->id();
            $table->string('name');
            $table->string('email');
            $table->timestamp('email_verified_at')->nullable();
            $table->boolean('is_admin')->nullable();
            $table->string('password');
            $table->rememberToken();
            $table->timestamps();
        });
    }
    /**
     * Reverse the migrations.
     *
     * @return void
     */
    public function down() {
        Schema::dropIfExists('users');
    }
}

```
<code>app/Models/User.php</code>
```
<?php
namespace App\Models;
use Illuminate\Contracts\Auth\MustVerifyEmail;
use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Foundation\Auth\User as Authenticatable;
use Illuminate\Notifications\Notifiable;
class User extends Authenticatable {
    use HasFactory, Notifiable;
    /**
     * The attributes that are mass assignable.
     *
     * @var array
     */
    protected $fillable = ['name', 'is_admin', 'email', 'password', 'is_admin'];
    /**
     * The attributes that should be hidden for arrays.
     *
     * @var array
     */
    protected $hidden = ['password', 'remember_token', ];
    /**
     * The attributes that should be cast to native types.
     *
     * @var array
     */
    protected $casts = ['email_verified_at' => 'datetime', ];
}
```
Now we need to run migration.<br>
so let's run bellow command:
```
php artisan migrate
```
**Step 4: Create Auth using scaffold**<br>
Now, in this step, we will create auth scaffold command to create login, register and dashboard. so run following commands:<br>
<code>Laravel 9 UI Package</code>
```
composer require laravel/ui 
```
**Generate auth**
```
php artisan ui bootstrap --auth 
```
```
npm install && npm run dev
```
**Step 5: Create IsAdmin Middleware**<br>
In this step, we require to create admin middleware that will allows only admin access users to that routes. so let's create admin user with following steps.<br>
```
php artisan make:middleware IsAdmin
```
<code>app/Http/middleware/IsAdmin.php</code>
```
<?php

namespace App\Http\Middleware;

use Closure;
use Illuminate\Http\Request;

class IsAdmin
{
    /**
     * Handle an incoming request.
     *
     * @param  \Illuminate\Http\Request  $request
     * @param  \Closure(\Illuminate\Http\Request): (\Illuminate\Http\Response|\Illuminate\Http\RedirectResponse)  $next
     * @return \Illuminate\Http\Response|\Illuminate\Http\RedirectResponse
     */
    public function handle(Request $request, Closure $next)
    {
        if (auth()->user()->is_admin == 1) {
            return $next($request);
        }
        return redirect('home')->with('error', "You don't have admin access.");
    }
}

```
<code>app/Http/Kernel.php</code>
```
<?php

namespace App\Http;

use Illuminate\Foundation\Http\Kernel as HttpKernel;

class Kernel extends HttpKernel
{
    /**
     * The application's global HTTP middleware stack.
     *
     * These middleware are run during every request to your application.
     *
     * @var array<int, class-string|string>
     */
    protected $middleware = [
        // \App\Http\Middleware\TrustHosts::class,
        \App\Http\Middleware\TrustProxies::class,
        \Illuminate\Http\Middleware\HandleCors::class,
        \App\Http\Middleware\PreventRequestsDuringMaintenance::class,
        \Illuminate\Foundation\Http\Middleware\ValidatePostSize::class,
        \App\Http\Middleware\TrimStrings::class,
        \Illuminate\Foundation\Http\Middleware\ConvertEmptyStringsToNull::class,
    ];

    /**
     * The application's route middleware groups.
     *
     * @var array<string, array<int, class-string|string>>
     */
    protected $middlewareGroups = [
        'web' => [
            \App\Http\Middleware\EncryptCookies::class,
            \Illuminate\Cookie\Middleware\AddQueuedCookiesToResponse::class,
            \Illuminate\Session\Middleware\StartSession::class,
            \Illuminate\View\Middleware\ShareErrorsFromSession::class,
            \App\Http\Middleware\VerifyCsrfToken::class,
            \Illuminate\Routing\Middleware\SubstituteBindings::class,
        ],

        'api' => [
            // \Laravel\Sanctum\Http\Middleware\EnsureFrontendRequestsAreStateful::class,
            'throttle:api',
            \Illuminate\Routing\Middleware\SubstituteBindings::class,
        ],
    ];

    /**
     * The application's route middleware.
     *
     * These middleware may be assigned to groups or used individually.
     *
     * @var array<string, class-string|string>
     */
    protected $routeMiddleware = [
        'auth' => \App\Http\Middleware\Authenticate::class,
        'auth.basic' => \Illuminate\Auth\Middleware\AuthenticateWithBasicAuth::class,
        'auth.session' => \Illuminate\Session\Middleware\AuthenticateSession::class,
        'cache.headers' => \Illuminate\Http\Middleware\SetCacheHeaders::class,
        'can' => \Illuminate\Auth\Middleware\Authorize::class,
        'guest' => \App\Http\Middleware\RedirectIfAuthenticated::class,
        'password.confirm' => \Illuminate\Auth\Middleware\RequirePassword::class,
        'signed' => \Illuminate\Routing\Middleware\ValidateSignature::class,
        'throttle' => \Illuminate\Routing\Middleware\ThrottleRequests::class,
        'verified' => \Illuminate\Auth\Middleware\EnsureEmailIsVerified::class,
        'is_admin' => \App\Http\Middleware\IsAdmin::class,
    ];
}
```
**Step 6: Create Route**<br>
Here, we need to add one more route for admin user home page so let's add that route in web.php file.<br>
<code>routes/web.php</code>
```
<?php

use Illuminate\Support\Facades\Route;

/*
|--------------------------------------------------------------------------
| Web Routes
|--------------------------------------------------------------------------
|
| Here is where you can register web routes for your application. These
| routes are loaded by the RouteServiceProvider within a group which
| contains the "web" middleware group. Now create something great!
|
*/

Route::get('/', function () {
    return view('welcome');
});

Auth::routes();

Route::get('/home', [App\Http\Controllers\HomeController::class, 'index'])->name('home');
Route::get('admin/home', [App\Http\Controllers\HomeController::class, 'adminHome'])->name('admin.home')->middleware('is_admin');

```
**Step 7: Add Method on Controller**<br>
Here, we need add adminHome() method for admin route in HomeController. so let's add like as bellow:<br>
<code>app/Http/Controllers/HomeController.php</code>
```
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;

class HomeController extends Controller
{
    /**
     * Create a new controller instance.
     *
     * @return void
     */
    public function __construct()
    {
        $this->middleware('auth');
    }

    /**
     * Show the application dashboard.
     *
     * @return \Illuminate\Contracts\Support\Renderable
     */
    public function index()
    {
        return view('home');
    }
    /**
     * Show the application dashboard.
     *
     * @return \Illuminate\Contracts\Support\Renderable
     */
    public function adminHome()
    {
        return view('adminHome');
    }
}

```
**Step 8: Create Blade file**<br>
In this step, we need to create new blade file for admin and update for user blade file. so let's change it.<br>
<code>resources/views/home.blade.php</code>
```
@extends('layouts.app')

@section('content')
<div class="container">
    <div class="row justify-content-center">
        <div class="col-md-8">
            <div class="card">
                <div class="card-header">{{ __('Dashboard') }}</div>

                <div class="card-body">
                    @if (session('status'))
                        <div class="alert alert-success" role="alert">
                            {{ session('status') }}
                        </div>
                    @endif

                    {{ __('You are logged in as user!') }}
                </div>
            </div>
        </div>
    </div>
</div>
@endsection

```
<code>resources/views/adminHome.blade.php</code>
```
@extends('layouts.app')

@section('content')
<div class="container">
    <div class="row justify-content-center">
        <div class="col-md-8">
            <div class="card">
                <div class="card-header">{{ __('Dashboard') }}</div>

                <div class="card-body">
                    @if (session('status'))
                        <div class="alert alert-success" role="alert">
                            {{ session('status') }}
                        </div>
                    @endif

                    {{ __('You are logged in as admin!') }}
                </div>
            </div>
        </div>
    </div>
</div>
@endsection

```
**Step 9: Update on LoginController**<br>
In this step, we will change on LoginController, when user will login than we redirect according to user access. if normal user than we will redirect to home route and if admin user than we redirect to admin route. so let's change.<br>
<code>app/Http/Controllers/Auth/LoginController.php</code>
```
<?php

namespace App\Http\Controllers\Auth;

use App\Http\Controllers\Controller;
use App\Providers\RouteServiceProvider;
use Illuminate\Foundation\Auth\AuthenticatesUsers;
use Illuminate\Http\Request;

class LoginController extends Controller
{
    /*
    |--------------------------------------------------------------------------
    | Login Controller
    |--------------------------------------------------------------------------
    |
    | This controller handles authenticating users for the application and
    | redirecting them to your home screen. The controller uses a trait
    | to conveniently provide its functionality to your applications.
    |
     */

    use AuthenticatesUsers;

    /**
     * Where to redirect users after login.
     *
     * @var string
     */
    protected $redirectTo = RouteServiceProvider::HOME;

    /**
     * Create a new controller instance.
     *
     * @return void
     */
    public function __construct()
    {
        $this->middleware('guest')->except('logout');
    }

    public function login(Request $request)
    {
        $input = $request->all();
        $this->validate($request, [
            'email' => 'required|email',
            'password' => 'required',
        ]);
        if (auth()->attempt(array('email' => $input['email'], 'password' => $input['password']))) {
            if (auth()->user()->is_admin == 1) {
                return redirect()->route('admin.home');
            } else {
                return redirect()->route('home');
            }
        } else {
            return redirect()->route('login')->with('error', 'Email-Address And Password Are Wrong.');
        }
    }
}

```
**Step 10: Create Seeder**<br>
We will create seeder for create new admin and normal user. so let's create seeder using following command:
```
php artisan make:seeder CreateUsersSeeder
```
<code>database/seeds/CreateUsersSeeder.php</code>
```
<?php

namespace Database\Seeders;

use Illuminate\Database\Console\Seeds\WithoutModelEvents;
use Illuminate\Database\Seeder;

class CreateUsersSeeder extends Seeder
{
    /**
     * Run the database seeds.
     *
     * @return void
     */
    public function run()
    {
        $user = [
            [
                'name' => 'Admin',
                'email' => 'admin@example.com',
                'is_admin' => '1',
                'password' => bcrypt('123456'),
            ],
            [
                'name' => 'User',
                'email' => 'user@example.com',
                'is_admin' => '0',
                'password' => bcrypt('123456'),
            ],
        ];

        foreach ($user as $key => $value) {

            \App\Models\User::create($value);

        }
    }
}
```
Now let's run seeder:
```
php artisan db:seed --class=CreateUsersSeeder
```
Ok, now we are ready to run.<br><br>
So let's run project using this command:
```
php artisan serve
```
**Admin User**
```
Login URL : http://127.0.0.1:8000/login
Email: admin@example.com
Password: 123456
```

**User User**
```
Login URL : http://127.0.0.1:8000/login
Email: user@example.com
Password: 123456
```

<br>
I hope it can help you...
