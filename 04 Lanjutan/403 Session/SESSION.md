## Laravel Sessions

Session ialah satu cara laravel menyimpan maklumat pengguna merentas pelbagai permintaan pengguna. Ia menjejaki semua pengguna yang melawat aplikasi.

### Configuration
fail konfigurasi session config/session.php

- file - sessions are stored in storage/framework/sessions.
- cookie - sessions are stored in secure, encrypted cookies.
- database - sessions are stored in a relational database.
- memcached / redis - sessions are stored in one of these fast, cache based stores.
- dynamodb - sessions are stored in AWS DynamoDB.
- array - sessions are stored in a PHP array and will not be persisted.

#### Driver Prerequisites
	
	Schema::create('sessions', function ($table) {
	    $table->string('id')->primary();
	    $table->foreignId('user_id')->nullable()->index();
	    $table->string('ip_address', 45)->nullable();
	    $table->text('user_agent')->nullable();
	    $table->text('payload');
	    $table->integer('last_activity')->index();
	});
	
arahan artisan :
	
	php artisan session:table
	 
	php artisan migrate
	

### Capaian dan penggunaan Session

2 cara utama untuk capai data session dalam Laravel: 
- the global session helper 
- Request instance

#### Retrieving Data

	<?php
	 
	namespace App\Http\Controllers;
	 
	use App\Http\Controllers\Controller;
	use Illuminate\Http\Request;
	 
	class UserController extends Controller
	{
	    /**
	     * Show the profile for the given user.
	     *
	     * @param  Request  $request
	     * @param  int  $id
	     * @return Response
	     */
	    public function show(Request $request, $id)
	    {
	        $value = $request->session()->get('key');
	 
	        //
	    }
	}
	

kembalikan


	$value = $request->session()->get('key', 'default');
	 
	$value = $request->session()->get('key', function () {
	    return 'default';
	});
	
	
##### The Global Session Helper

	Route::get('/home', function () {
	    // Retrieve a piece of data from the session...
	    $value = session('key');
	 
	    // Specifying a default value...
	    $value = session('key', 'default');
	 
	    // Store a piece of data in the session...
	    session(['key' => 'value']);
	});
	
#### Dapatkan semua data Session

	$data = $request->session()->all();
	
#### Semakan data Session

	if ($request->session()->has('users')) {
	    //
	}
	

atau gun fungsi exist

if ($request->session()->exists('users')) {
    //
}


atau guna fungsi missing

	if ($request->session()->missing('users')) {
	    //
	}
	
### Simpan data Sessions

	// Via a request instance...
	$request->session()->put('key', 'value');
	 
	// Via the global "session" helper...
	session(['key' => 'value']);
	

#### Pushing To Array Session Values

	$request->session()->push('user.teams', 'developers');
	
#### Retrieving & Deleting An Item

	$value = $request->session()->pull('key', 'default');
	
#### Incrementing & Decrementing Session Values

	$request->session()->increment('count');
	 
	$request->session()->increment('count', $incrementBy = 2);
	 
	$request->session()->decrement('count');
	 
	$request->session()->decrement('count', $decrementBy = 2);
	

### Flash data

	$request->session()->flash('status', 'Task was successful!');
	
guna data flash untuk beberapa request

	$request->session()->reflash();
	 
	$request->session()->keep(['username', 'email']);
	
guna data flash untuk request semasa shj

	$request->session()->now('status', 'Task was successful!');
	

### Deleting Data

	// Forget a single key...
	$request->session()->forget('name');
	 
	// Forget multiple keys...
	$request->session()->forget(['name', 'status']);
	 
	$request->session()->flush();
	
	### Regenerating The Session ID
	
	$request->session()->regenerate();
	
atau

	$request->session()->invalidate();
	

## Hadang Session

	Route::post('/profile', function () {
	    //
	})->block($lockSeconds = 10, $waitSeconds = 10)
	 
	Route::post('/order', function () {
	    //
	})->block($lockSeconds = 10, $waitSeconds = 10)
	
atau

	Route::post('/profile', function () {
	    //
	})->block()
	
## Adding Custom Session Drivers  
### Implementing The Driver

	<?php
	 
	namespace App\Extensions;
	 
	class MongoSessionHandler implements \SessionHandlerInterface
	{
	    public function open($savePath, $sessionName) {}
	    public function close() {}
	    public function read($sessionId) {}
	    public function write($sessionId, $data) {}
	    public function destroy($sessionId) {}
	    public function gc($lifetime) {}
	}
	
### Registering The Driver


	<?php
	 
	namespace App\Providers;
	 
	use App\Extensions\MongoSessionHandler;
	use Illuminate\Support\Facades\Session;
	use Illuminate\Support\ServiceProvider;
	 
	class SessionServiceProvider extends ServiceProvider
	{
	    /**
	     * Register any application services.
	     *
	     * @return void
	     */
	    public function register()
	    {
	        //
	    }
	 
	    /**
	     * Bootstrap any application services.
	     *
	     * @return void
	     */
	    public function boot()
	    {
	        Session::extend('mongo', function ($app) {
	            // Return an implementation of SessionHandlerInterface...
	            return new MongoSessionHandler;
	        });
	    }
	}
	
