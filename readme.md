# Demo Laravel Auth Once

Demo for Laravel authentication one per session.

This project creation is very simple.

### 1. Initialize the project

```
$ composer create-project laravel/laravel demo-laravel-auth-once

$ cd demo-laravel-auth-once
```

### 2. Setup the database

Edit the *.env* file in the project

### 3. Add default Auth template

```
$ php artisan make:auth
```

### 4. Add session_id to user table in migration

Check the file **database/migrations/2014_10_12_000000/create_users_table.php**

```
class CreateUsersTable extends Migration
{
    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::create('users', function (Blueprint $table) {
            $table->increments('id');
            $table->string('name');
            $table->string('email')->unique();
            $table->string('password');
            $table->string('session_id'); // Add session_id
            $table->rememberToken();
            $table->timestamps();
        });
    }
...
```

### 5. Execute the migration

```
$ php artisan migrate
```

### 6. Handle the authentication

Edit the **app/Http/Controllers/Auth/AuthController.php**

```

use App\User;
use Validator;
use App\Http\Controllers\Controller;
use Illuminate\Foundation\Auth\ThrottlesLogins;
use Illuminate\Foundation\Auth\AuthenticatesAndRegistersUsers;
use Illuminate\Http\Request;
use Auth;

...


class AuthController extends Controller
{
...

    // handle authenticated user for using one session only
    public function authenticated(Request $request, User $user){
        $previous_session = $user->session_id;

        if ($previous_session) {
            \Session::getHandler()->destroy($previous_session);
        }

        Auth::user()->session_id = \Session::getId();
        Auth::user()->save();
        return redirect()->intended($this->redirectPath());
    }

```

All done!!!
