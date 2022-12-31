![Pusat Latihan CGN](../../assets/images/cgnbulat.png)
## Laravel Responses

Setiap ***HTTP request*** dari pengguna perlu dibalas kepada pengguna melaui webbrowser yang diguna. Laravel menyediakan beberapa cara berbeza untuk mengembalikan respons, yang paling ringkas dengan mengembalikan ***string*** daripada ***route*** atau ***controller***. Kerangka Laravel akan menukar ***string*** secara automatik menjadi respons HTTP penuh

### Cipta Responses
#### Kembalikan Strings & Arrays

Cth kembalikan string :

	Route::get('/', function () {
	    return 'Hello World';
	});
	
atau kembalikan array , akan diubah dalam bentuk JSON

	Route::get('/', function () {
	    return [1, 2, 3];
	});
	
	
#### Kembalikan Objects

	Route::get('/home', function () {
	    return response('Hello World', 200)
	                  ->header('Content-Type', 'text/plain');
	});
	

#### Eloquent Models & Collections

	use App\Models\User;
	 
	Route::get('/user/{user}', function (User $user) {
	    return $user;
	});
	

#### Kepilkan header

	return response($content)
	            ->header('Content-Type', $type)
	            ->header('X-Header-One', 'Header Value')
	            ->header('X-Header-Two', 'Header Value');
	
atau

	return response($content)
	            ->withHeaders([
	                'Content-Type' => $type,
	                'X-Header-One' => 'Header Value',
	                'X-Header-Two' => 'Header Value',
	            ]);
	
	
#### Cache Control Middleware

	Route::middleware('cache.headers:public;max_age=2628000;etag')->group(function () {
	    Route::get('/privacy', function () {
	        // ...
	    });
	 
	    Route::get('/terms', function () {
	        // ...
	    });
	});
	
	
#### Kepilkan Cookies

	return response('Hello World')->cookie(
	    'name', 'value', $minutes
	);
	

atau

	return response('Hello World')->cookie(
	    'name', 'value', $minutes, $path, $domain, $secure, $httpOnly
	);
	
atau

	use Illuminate\Support\Facades\Cookie;
	 
	Cookie::queue('name', 'value', $minutes);
	

#### Generating Cookie Instances

	$cookie = cookie('name', 'value', $minutes);
	 
	return response('Hello World')->cookie($cookie);
	

#### Expiring Cookies Early

	return response('Hello World')->withoutCookie('name');
	
atau 

	Cookie::expire('name');
	

### Redirect Response

Cth

	Route::get('/dashboard', function () {
	    return redirect('home/dashboard');
	});
	
atau

	Route::post('/user/profile', function () {
	    // Validate the request...
	 
	    return back()->withInput();
	});
	

#### Redirecting kpd Named Routes

	return redirect()->route('login');
	
atau 

	// For a route with the following URI: /profile/{id}
	 
	return redirect()->route('profile', ['id' => 1]);
	
#### Populating Parameters Via Eloquent Models
cth

	// For a route with the following URI: /profile/{id}
	 
	return redirect()->route('profile', [$user]);
	
Model dengan fungsi getRoutedKey

	/**
	 * Get the value of the model's route key.
	 *
	 * @return mixed
	 */
	public function getRouteKey()
	{
	    return $this->slug;
	}
	
#### Redirecting To Controller Actions

	use App\Http\Controllers\UserController;
	 
	return redirect()->action([UserController::class, 'index']);
	

atau

	return redirect()->action(
	    [UserController::class, 'profile'], ['id' => 1]
	);
	
#### Redirecting To External Domains

	return redirect()->away('https://www.google.com');
	

#### Redirecting With Flashed Session Data

	Route::post('/user/profile', function () {
	    // ...
	 
	    return redirect('dashboard')->with('status', 'Profile updated!');
	});
	
Cth blade sytax

	@if (session('status'))
	    <div class="alert alert-success">
	        {{ session('status') }}
	    </div>
	@endif
	
#### Redirecting With Input

	return back()->withInput();
	
### Other Response Types

#### View Responses
	
	return response()
	            ->view('hello', $data, 200)
	            ->header('Content-Type', $type);
	
	

#### JSON Responses
	
	return response()->json([
	    'name' => 'Abigail',
	    'state' => 'CA',
	]);
	
atau dengan callback
	
	return response()
	            ->json(['name' => 'Abigail', 'state' => 'CA'])
	            ->withCallback($request->input('callback'));
	

#### File Downloads
	
	return response()->download($pathToFile);
	 
	return response()->download($pathToFile, $name, $headers);
	
##### Streamed Downloads
	
	use App\Services\GitHub;
	 
	return response()->streamDownload(function () {
	    echo GitHub::api('repo')
	                ->contents()
	                ->readme('laravel', 'laravel')['contents'];
	}, 'laravel-readme.md');
	
#### File Responses
	
	return response()->file($pathToFile);
	 
	return response()->file($pathToFile, $headers);
	
	
### Response Macros

	
	<?php
	 
	namespace App\Providers;
	 
	use Illuminate\Support\Facades\Response;
	use Illuminate\Support\ServiceProvider;
	 
	class AppServiceProvider extends ServiceProvider
	{
	    /**
	     * Bootstrap any application services.
	     *
	     * @return void
	     */
	    public function boot()
	    {
	        Response::macro('caps', function ($value) {
	            return Response::make(strtoupper($value));
	        });
	    }
	}
	
	
helper
	
		return response()->caps('foo');
		 

