### Laravel Routing
***Routing*** adalah salah satu konsep penting dalam Laravel. Fungsi utama ***Routing*** adalah untuk mengarahkan semua permintaan aplikasi anda kepada ***Controllers*** yang sesuai.  

Lokasi fail ***Routing*** adalah dalam folder *route* dengan nama *web.php* :  

    laravel/route/web.php
    laravel/route/api.php
 
***Routing*** dalam web.php akan mempunyai ciri2 *session state* and kelesamatan *CSRF* untuk kegunaan antaramuka *web*.

Satu lagi fail ***Routing*** adalah *api.php* dalam folder yang sama tetapi tidak mempunyai ciri2 sebagaimana *web.php*

### 1. Routing Statik
##### Route Contoh 1 : ***Routing*** - memulangkan kenyataan *Salam Dunia* kepada pelayar   

    use Illuminate\Support\Facades\Route;
    
    Route::get('/greeting', function () {  
        return 'Salam Dunia';  
    });  

##### Route Contoh 2 : ***Routing*** - diarahkan kepada **Controller** untuk memproses data sebelum memulangkan maklumat kepada pelayar   

    use App\Http\Controllers\UserController;

    Route::get('/user', [UserController::class, 'index']);

Dalam contoh diatas , sistem akan memanggil fungsi index dalam fail

    laravel/app/Http/Caontrollers/UserController.php

#### Ada 6 Kaedah Route  
Terdapat 6 jenis kata buat yang dihantar oleh HTTP iaitu ***GET***,***POST***,***PUT***,***PATCH***,***DELETE*** dan ***OPTION*** :

    Route::get($uri, $callback);
    Route::post($uri, $callback);
    Route::put($uri, $callback);
    Route::patch($uri, $callback);
    Route::delete($uri, $callback);
    Route::options($uri, $callback);

Nota : kata buat dari ***form*** iaitu POST, PUT, PATCH, or DELETE mestilah disertakan dengan CSRF token cth :

    <form method="POST" action="/profile">
        @csrf
        ...
    </form>

Walaubagaimanapun, 1 kaedah route boleh melakukan beberapa kata buat hantaran dari HTTP dengan menggunakan kaedah ***MATCH*** dan ***ANY***

    Route::match(['get', 'post'], '/', function () {
    //
    });
     
    Route::any('/', function () {
        //
    });

#### Ada 2 Keadah Routing biasa diguna  
2 contoh route dari kata buat HTTP ***get*** yang biasa digunakan dan boleh diringkaskan :  
- Redirect Route  
- View Route   
- 

    Route::redirect('/here', '/there');  
    Route::redirect('/here', '/there', 301);  
    Route::permanentRedirect('/here', '/there');  
    
    Route::view('/welcome', 'welcome');  
    Route::view('/welcome', 'welcome', ['name' => 'Taylor']);  

#### Routing dependency injection
***Routing*** boleh menerima data dari data HTTP  dengan penggunaan Laravel ***service container*** Illuminate\Http\Request

    use Illuminate\Http\Request;
     
    Route::get('/users', function (Request $request) {
        // ...
    });

### 2. Routing Parameters
Ada 2 jenis ***Routing Parameters*** iaitu :

- Parameter wajid
- Parameter opsyen

##### Parameter wajid cth

    Route::get('/user/{id}', function ($id) {
        return 'User '.$id;
    });
    
    Route::get('/posts/{post}/comments/{comment}', function ($postId, $commentId) {
    //
    });
    
###### Parameter wajid dengan dependency injection cth

    use Illuminate\Http\Request;
     
    Route::get('/user/{id}', function (Request $request, $id) {
        return 'User '.$id;
    });
    

##### Parameter opsyen cth

    Route::get('/user/{name?}', function ($name = null) {
        return $name;
    });
     
    Route::get('/user/{name?}', function ($name = 'John') {
        return $name;
    });
    
###### Parameter opsyen dengan semakan input regular expresion cth

    Route::get('/user/{name}', function ($name) {
        //
    })->where('name', '[A-Za-z]+');
     
    Route::get('/user/{id}', function ($id) {
        //
    })->where('id', '[0-9]+');
     
    Route::get('/user/{id}/{name}', function ($id, $name) {
        //
    })->where(['id' => '[0-9]+', 'name' => '[a-z]+']);

###### Parameter opsyen dengan semakan input regular expresion dgn helper cth

    Route::get('/user/{id}/{name}', function ($id, $name) {
        //
    })->whereNumber('id')->whereAlpha('name');
     
    Route::get('/user/{name}', function ($name) {
        //
    })->whereAlphaNumeric('name');
     
    Route::get('/user/{id}', function ($id) {
        //
    })->whereUuid('id');
     
    Route::get('/user/{id}', function ($id) {
        //
    })->whereUlid('id');
     
    Route::get('/category/{category}', function ($category) {
        //
    })->whereIn('category', ['movie', 'song', 'painting']);
    
###### Parameter opsyen dengan semakan secara global cth
Kaedah ini boleh dibuat dengan memasukkan kod dalam App\Providers\RouteServiceProvider


    **
     * Define your route model bindings, pattern filters, etc.
     *
     * @return void
     */
    public function boot()
    {
        Route::pattern('id', '[0-9]+');
    }
    
dalam web.php

    Route::get('/user/{id}', function ($id) {
        // Only executed if {id} is numeric...
    });
    
###### Parameter opsyen dengan Encoded Forward Slashes

Membolehkkan / diterima sebagai parameter

    Route::get('/search/{search}', function ($search) {
        return $search;
    })->where('search', '.*');
    

##### Penamaan Routes
Penamaan ***route*** sangat berfaedah terutama untuk digunakan semasa membuat pautan dalan ***views*** dan mesti unik

Cth :

    Route::get('/user/profile', function () {
        //
    })->name('profile');
    

Cth untuk ***route controller***

    Route::get(
        '/user/profile',
        [UserProfileController::class, 'show']
    )->name('profile');
    

Penggunaan penamaan route :

    // Generating URLs...
    $url = route('profile');
     
    // Generating Redirects...
    return redirect()->route('profile');
     
    return to_route('profile');
    


Contoh Hantar parameter :

    Route::get('/user/{id}/profile', function ($id) {
        //
    })->name('profile');
     
    $url = route('profile', ['id' => 1]);


Contoh Hantar parameter ***array*** :

    Route::get('/user/{id}/profile', function ($id) {
        //
    })->name('profile');
     
    $url = route('profile', ['id' => 1, 'photos' => 'yes']);
     
    // /user/1/profile?photos=yes

##### Inspecting The Current Route
penamaan boleh digunan untuk menyemak sekiranya ***route*** di halakan kepada ***nama route*** , cth : 

    /**
     * Handle an incoming request.
     *
     * @param  \Illuminate\Http\Request  $request
     * @param  \Closure  $next
     * @return mixed
     */
    public function handle($request, Closure $next)
    {
        if ($request->route()->named('profile')) {
            //
        }
     
        return $next($request);
    }


### 3. Route Groups
**Route groups** membenarkan berkongsi **route** yang mempunyai sifat ***route*** yang sama seperti ***middleware*** , ***controller***

#### cth route groups middleware:

    Route::middleware(['first', 'second'])->group(function () {
        Route::get('/', function () {
            // Uses first & second middleware...
        });
     
        Route::get('/user/profile', function () {
            // Uses first & second middleware...
        });
    });
    



#### cth route groups controller:

    use App\Http\Controllers\OrderController;
     
    Route::controller(OrderController::class)->group(function () {
        Route::get('/orders/{id}', 'show');
        Route::post('/orders', 'store');
    });
    

#### cth route groups Subdomain

    Route::domain('{account}.example.com')->group(function () {
        Route::get('user/{id}', function ($account, $id) {
            //
        });
    });
    



#### cth route Prefixes

    Route::prefix('admin')->group(function () {
        Route::get('/users', function () {
            // Matches The "/admin/users" URL
        });
    });


#### cth route Name Prefixes

    Route::name('admin.')->group(function () {
        Route::get('/users', function () {
            // Route assigned name "admin.users"...
        })->name('users');
    });



### 4. Route Model Binding
Laravel ***route model binding*** memberi kemudahan membolehkan model terus kedalam route

#### Contoh Implicit Binding

    use App\Models\User;
     
    Route::get('/users/{user}', function (User $user) {
        return $user->email;
    });
    


#### Contoh Route Model dengan class Controller

    use App\Http\Controllers\UserController;
    use App\Models\User;
     
    // Route definition...
    Route::get('/users/{user}', [UserController::class, 'show']);
     
    // Controller method definition...
    public function show(User $user)
    {
        return view('user.profile', ['user' => $user]);
    }

#### Contoh Route Model dengan Soft Deleted Models

    use App\Models\User;
     
    Route::get('/users/{user}', function (User $user) {
        return $user->email;
    })->withTrashed();


#### Contoh Route Model dengan Customizing The Key

Boleh guna field lain selain dari default , iaitu id.

    use App\Models\Post;
     
    Route::get('/posts/{post:slug}', function (Post $post) {
        return $post;
    });

atau anda boleh tukar default key 

    /**
     * Get the route key for the model.
     *
     * @return string
     */
    public function getRouteKeyName()
    {
        return 'slug';
    }


#### Contoh Route Model dengan Custom Keys & Scoping

    use App\Models\Post;
    use App\Models\User;
     
    Route::get('/users/{user}/posts/{post:slug}', function (User $user, Post $post) {
        return $post;
    });


    use App\Models\Post;
    use App\Models\User;
     
    Route::get('/users/{user}/posts/{post}', function (User $user, Post $post) {
        return $post;
    })->scopeBindings();


    Route::scopeBindings()->group(function () {
        Route::get('/users/{user}/posts/{post}', function (User $user, Post $post) {
            return $post;
        });
    });

    Route::get('/users/{user}/posts/{post:slug}', function (User $user, Post $post) {
        return $post;
    })->withoutScopedBindings();


### 5. Lain2 Route

##### Customizing Missing Model Behavior

    use App\Http\Controllers\LocationsController;
    use Illuminate\Http\Request;
    use Illuminate\Support\Facades\Redirect;
     
    Route::get('/locations/{location:slug}', [LocationsController::class, 'show'])
            ->name('locations.view')
            ->missing(function (Request $request) {
                return Redirect::route('locations.index');
            });
    

##### Implicit Enum Binding

    <?php
     
    namespace App\Enums;
     
    enum Category: string
    {
        case Fruits = 'fruits';
        case People = 'people';
    }
    


    use App\Enums\Category;
    use Illuminate\Support\Facades\Route;
     
    Route::get('/categories/{category}', function (Category $category) {
        return $category->value;
    });
    

##### Explicit Binding

use App\Models\User;
use Illuminate\Support\Facades\Route;
 
    /**
     * Define your route model bindings, pattern filters, etc.
     *
     * @return void
     */
    public function boot()
    {
        Route::model('user', User::class);
     
        // ...
    }


    use App\Models\User;
     
    Route::get('/users/{user}', function (User $user) {
        //
    });
    



##### Customizing The Resolution Logic

    use App\Models\User;
    use Illuminate\Support\Facades\Route;
     
    /**
     * Define your route model bindings, pattern filters, etc.
     *
     * @return void
     */
    public function boot()
    {
        Route::bind('user', function ($value) {
            return User::where('name', $value)->firstOrFail();
        });
     
        // ...
    }
    

    /**
     * Retrieve the model for a bound value.
     *
     * @param  mixed  $value
     * @param  string|null  $field
     * @return \Illuminate\Database\Eloquent\Model|null
     */
    public function resolveRouteBinding($value, $field = null)
    {
        return $this->where('name', $value)->firstOrFail();
    }
    


    /**
     * Retrieve the child model for a bound value.
     *
     * @param  string  $childType
     * @param  mixed  $value
     * @param  string|null  $field
     * @return \Illuminate\Database\Eloquent\Model|null
     */
    public function resolveChildRouteBinding($childType, $value, $field)
    {
        return parent::resolveChildRouteBinding($childType, $value, $field);
    }
    
### 6. Fallback Routes
Mengendalikan route yang tidak dikesan

Route::fallback(function () {
    //
});


### 7. Rate Limiting

#### Defining Rate Limiters

use Illuminate\Cache\RateLimiting\Limit;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\RateLimiter;
 
    /**
     * Configure the rate limiters for the application.
     *
     * @return void
     */
    protected function configureRateLimiting()
    {
        RateLimiter::for('global', function (Request $request) {
            return Limit::perMinute(1000);
        });
    }
    


    RateLimiter::for('global', function (Request $request) {
        return Limit::perMinute(1000)->response(function (Request $request, array $headers) {
            return response('Custom response...', 429, $headers);
        });
    });


    RateLimiter::for('uploads', function (Request $request) {
        return $request->user()->vipCustomer()
                    ? Limit::none()
                    : Limit::perMinute(100);
    });
    

#### Segmenting Rate Limits


    RateLimiter::for('uploads', function (Request $request) {
        return $request->user()->vipCustomer()
                    ? Limit::none()
                    : Limit::perMinute(100)->by($request->ip());
    });
    


    RateLimiter::for('uploads', function (Request $request) {
        return $request->user()
                    ? Limit::perMinute(100)->by($request->user()->id)
                    : Limit::perMinute(10)->by($request->ip());
    });
    

#### Multiple Rate Limits

    RateLimiter::for('login', function (Request $request) {
        return [
            Limit::perMinute(500),
            Limit::perMinute(3)->by($request->input('email')),
        ];
    });
    


#### Attaching Rate Limiters To Routes

    Route::middleware(['throttle:uploads'])->group(function () {
        Route::post('/audio', function () {
            //
        });
     
        Route::post('/video', function () {
            //
        });
    });
    

#### Throttling With Redis

    'throttle' => \Illuminate\Routing\Middleware\ThrottleRequestsWithRedis::class,
    

#### Form Method Spoofing

HTML forms tidak menerima ***PUT***, ***PATCH***, atau ***DELETE*** . Jadi, anda perlu tambah ***hidden _method*** dalam form

    <form action="/example" method="POST">
        <input type="hidden" name="_method" value="PUT">
        <input type="hidden" name="_token" value="{{ csrf_token() }}">
    </form>
    

atau

    <form action="/example" method="POST">
        @method('PUT')
        @csrf
    </form>
    


### 8. Accessing The Current Route

    use Illuminate\Support\Facades\Route;
     
    $route = Route::current(); // Illuminate\Routing\Route
    $name = Route::currentRouteName(); // string
    $action = Route::currentRouteAction(); // string
    

### 9. Cross-Origin Resource Sharing (CORS)


### 10. Arahan Artisan 

    php artisan route:list
    php artisan route:list -v
    php artisan route:list --path=api
    php artisan route:list --except-vendor
    php artisan route:list --only-vendor
    php artisan route:cache
    php artisan route:clear
    