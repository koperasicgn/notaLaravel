![Pusat Latihan CGN](../../assets/images/cgnbulat.png)

### Laravel Middleware

Middleware menyediakan mekanisme yang mudah untuk memeriksa dan menapis permintaan HTTP yang memasuki aplikasi anda. Sebagai contoh, Laravel menyertakan perisian tengah yang mengesahkan pengguna aplikasi anda disahkan. Jika permintaan pengguna disahkan maka permintaan itu dihantar ke bahagian belakang. Jika permintaan pengguna tidak disahkan, maka middleware akan mengubah hala pengguna ke skrin log masuk.

#### Mentakrifkan Middleware

Guna arahan artisan untuk mentakrifkan middleware

php artisan make:middleware EnsureTokenIsValid

1 fail akan dicipta dalam app/Http/Middleware

	<?php
	 
	namespace App\Http\Middleware;
	 
	use Closure;
	 
	class EnsureTokenIsValid
	{
	    /**
	     * Handle an incoming request.
	     *
	     * @param  \Illuminate\Http\Request  $request
	     * @param  \Closure  $next
	     * @return mixed
	     */
	    public function handle($request, Closure $next)
	    {
	        if ($request->input('token') !== 'my-secret-token') {
	            return redirect('home');
	        }
	 
	        return $next($request);
	    }
	}
	
##### Middleware & Responses

	<?php
	 
	namespace App\Http\Middleware;
	 
	use Closure;
	 
	class BeforeMiddleware
	{
	    public function handle($request, Closure $next)
	    {
	        // Perform action
	 
	        return $next($request);
	    }
	}
	
Buat task selapas request

		<?php
		 
		namespace App\Http\Middleware;
		 
		use Closure;
		 
		class AfterMiddleware
		{
		    public function handle($request, Closure $next)
		    {
		        $response = $next($request);
		 
		        // Perform action
		 
		        return $response;
		    }
		}
		


#### Daftar Middleware
##### Global Middleware

Sekiranya ingin menbuakan semakan setiap kali http request, middleware perlu di daftarkan dalam app/Http/Kernel.php

##### Middleware dalam route

daftar middleware dalam kernel.

	// Within App\Http\Kernel class...
	 
	protected $routeMiddleware = [
	    'auth' => \App\Http\Middleware\Authenticate::class,
	    'auth.basic' => \Illuminate\Auth\Middleware\AuthenticateWithBasicAuth::class,
	    'bindings' => \Illuminate\Routing\Middleware\SubstituteBindings::class,
	    'cache.headers' => \Illuminate\Http\Middleware\SetCacheHeaders::class,
	    'can' => \Illuminate\Auth\Middleware\Authorize::class,
	    'guest' => \App\Http\Middleware\RedirectIfAuthenticated::class,
	    'signed' => \Illuminate\Routing\Middleware\ValidateSignature::class,
	    'throttle' => \Illuminate\Routing\Middleware\ThrottleRequests::class,
	    'verified' => \Illuminate\Auth\Middleware\EnsureEmailIsVerified::class,
	];
	

guna middleware dalam route
	
	Route::get('/profile', function () {
	    //
	})->middleware('auth');
	
guna lebih dari 1 middle dalam satu route
	
	Route::get('/', function () {
	    //
	})->middleware(['first', 'second']);
	
atau hantarkan kelas
	
	use App\Http\Middleware\EnsureTokenIsValid;
	 
	Route::get('/profile', function () {
	    //
	})->middleware(EnsureTokenIsValid::class);
	
##### Pengecualian Middleware

Boleh kecualikan middleware dalam group
	
	use App\Http\Middleware\EnsureTokenIsValid;
	 
	Route::middleware([EnsureTokenIsValid::class])->group(function () {
	    Route::get('/', function () {
	        //
	    });
	 
	    Route::get('/profile', function () {
	        //
	    })->withoutMiddleware([EnsureTokenIsValid::class]);
	});
	
	Boleh kecualikan bagi keseruahan group
	
	use App\Http\Middleware\EnsureTokenIsValid;
	 
	Route::withoutMiddleware([EnsureTokenIsValid::class])->group(function () {
	    Route::get('/profile', function () {
	        //
	    });
	});
	

#### Kumpulkan middleware

Masukkan route dalam satu kumpulan middleware
	
	/**
	 * The application's route middleware groups.
	 *
	 * @var array
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
	        'throttle:api',
	        \Illuminate\Routing\Middleware\SubstituteBindings::class,
	    ],
	];
	
	
	Guna dalam route
	
	
	Route::get('/', function () {
	    //
	})->middleware('web');
	 
	Route::middleware(['web'])->group(function () {
	    //
	});
	

#### Sorting Middleware

Boleh susun keutamaan middleware

	/**
	 * The priority-sorted list of middleware.
	 *
	 * This forces non-global middleware to always be in the given order.
	 *
	 * @var string[]
	 */
	protected $middlewarePriority = [
	    \Illuminate\Foundation\Http\Middleware\HandlePrecognitiveRequests::class,
	    \Illuminate\Cookie\Middleware\EncryptCookies::class,
	    \Illuminate\Session\Middleware\StartSession::class,
	    \Illuminate\View\Middleware\ShareErrorsFromSession::class,
	    \Illuminate\Contracts\Auth\Middleware\AuthenticatesRequests::class,
	    \Illuminate\Routing\Middleware\ThrottleRequests::class,
	    \Illuminate\Routing\Middleware\ThrottleRequestsWithRedis::class,
	    \Illuminate\Contracts\Session\Middleware\AuthenticatesSessions::class,
	    \Illuminate\Routing\Middleware\SubstituteBindings::class,
	    \Illuminate\Auth\Middleware\Authorize::class,
	];
	
#### Middleware Parameters

Boleh menerima parameter tambahan

	<?php
	 
	namespace App\Http\Middleware;
	 
	use Closure;
	 
	class EnsureUserHasRole
	{
	    /**
	     * Handle the incoming request.
	     *
	     * @param  \Illuminate\Http\Request  $request
	     * @param  \Closure  $next
	     * @param  string  $role
	     * @return mixed
	     */
	    public function handle($request, Closure $next, $role)
	    {
	        if (! $request->user()->hasRole($role)) {
	            // Redirect...
	        }
	 
	        return $next($request);
	    }
	 
	}
	
Guna dalam route

	Route::put('/post/{id}', function ($id) {
	    //
	})->middleware('role:editor');
	
	
#### Terminable Middleware

	<?php
	 
	namespace Illuminate\Session\Middleware;
	 
	use Closure;
	 
	class TerminatingMiddleware
	{
	    /**
	     * Handle an incoming request.
	     *
	     * @param  \Illuminate\Http\Request  $request
	     * @param  \Closure  $next
	     * @return mixed
	     */
	    public function handle($request, Closure $next)
	    {
	        return $next($request);
	    }
	 
	    /**
	     * Handle tasks after the response has been sent to the browser.
	     *
	     * @param  \Illuminate\Http\Request  $request
	     * @param  \Illuminate\Http\Response  $response
	     * @return void
	     */
	    public function terminate($request, $response)
	    {
	        // ...
	    }
	}
	

guna dalam route  


	use App\Http\Middleware\TerminatingMiddleware;
	 
	/**
	 * Register any application services.
	 *
	 * @return void
	 */
	public function register()
	{
	    $this->app->singleton(TerminatingMiddleware::class);
	}
	

