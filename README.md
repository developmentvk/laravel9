# Laravel 8 Multi Auth (Authentication) Tutorial
In this example, you will learn laravel 8 multi auth. i would like to share with you laravel 8 multiple auth. This post will give you simple example of laravel 8 multiple authentication.

**Step 1: Install Laravel 8**

first of all we need to get fresh Laravel 8 version application using bellow command, So open your terminal OR command prompt and run bellow command:

<code>composer create-project --prefer-dist laravel/laravel blog</code>

**Step 2: Database Configuration**

In second step, we will make database configuration for example database name, username, password etc for our crud application of laravel 8. So let's open .env file and fill all details like as bellow:

**.env**

```
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=here your database name(blog)
DB_USERNAME=here database username(root)
DB_PASSWORD=here database password(root)
```

**Step 3: Update Migration and Model**<br>
In this step, we need to add new row "is_admin" in users table and model. than we need to run migration. so let's change that on both file.<br>
**database/migrations/000_create_users_table.php**<br>
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
