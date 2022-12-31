![Pusat Latihan CGN](../../assets/images/cgnbulat.png)
## Laravel Request
Laravel menyediakan objek kelas Illuminate\Http\Request untuk berinteraksi dengan permintaan HTTP untuk mendapatkan semula input, ***cookeis*** dan fail yang diserahkan bersama permintaan itu.

### Interaksi dengan Request
#### Capaian kepada Request

Cth capaian dalam ***Controller***

	<?php
	 
	namespace App\Http\Controllers;
	 
	use Illuminate\Http\Request;
	 
	class UserController extends Controller
	{
	    /**
	     * Store a new user.
	     *
	     * @param  \Illuminate\Http\Request  $request
	     * @return \Illuminate\Http\Response
	     */
	    public function store(Request $request)
	    {
	        $name = $request->input('name');
	 
	        //
	    }
	}
	

Cth capaian dalam ***route***

use Illuminate\Http\Request;
 
Route::get('/', function (Request $request) {
    //
});

##### Dependency Injection & Route Parameters
Hantar input dari ***route*** ke ***controller***

	use App\Http\Controllers\UserController;
	 
	Route::put('/user/{id}', [UserController::class, 'update']);
	


Fail ***controller*** 

	<?php
	 
	namespace App\Http\Controllers;
	 
	use Illuminate\Http\Request;
	 
	class UserController extends Controller
	{
	    /**
	     * Update the specified user.
	     *
	     * @param  \Illuminate\Http\Request  $request
	     * @param  string  $id
	     * @return \Illuminate\Http\Response
	     */
	    public function update(Request $request, $id)
	    {
	        //
	    }
	}
	
#### Request Path, Host, & Method
Dapatakan Request Path

	$uri = $request->path();
	
Semak The Request Path / Route

	if ($request->is('admin/*')) {
	    //
	}
	
atau 

	if ($request->routeIs('admin.*')) {
	    //
	}
	
Retrieving The Request URL

	$url = $request->url();
	 
	$urlWithQueryString = $request->fullUrl();
	
atau

	$request->fullUrlWithQuery(['type' => 'phone']);
	
Retrieving The Request Host

	$request->host();
	$request->httpHost();
	$request->schemeAndHttpHost();
	
Retrieving The Request Method
	
	$method = $request->method();
	 
	if ($request->isMethod('post')) {
	    //
	}
	
#### Request Headers
Cth 

	$value = $request->header('X-Header-Name');
	 
	$value = $request->header('X-Header-Name', 'default');
	
atau

	if ($request->hasHeader('X-Header-Name')) {
	    //
	}
	
atau

	$token = $request->bearerToken();
	

#### Request IP Address

	$ipAddress = $request->ip();
	
#### Negosiasi Kandungan
Cth 1

	$contentTypes = $request->getAcceptableContentTypes();
	
Cth 2

	if ($request->accepts(['text/html', 'application/json'])) {
	    // ...
	}
	
Cth 3 

	$preferred = $request->prefers(['text/html', 'application/json']);
	
Cth 4

	if ($request->expectsJson()) {
	    // ...
	}
	
#### PSR-7 Requests

dapatkan dari ***composer*** :

	composer require symfony/psr-http-message-bridge
	composer require nyholm/psr7
	
daftar dalam servie laravel 

	use Psr\Http\Message\ServerRequestInterface;
	 
	Route::get('/', function (ServerRequestInterface $request) {
	    //
	});

	
### Input

#### Dapatkan Input
Dapatkan semua data Input :

	$input = $request->all();
	
atau 

	$input = $request->collect();
	
atau 

	$request->collect('users')->each(function ($user) {
	    // ...
	});
	
Dapatkan satu nilai Input

	$name = $request->input('name');
	
atau sebagai default input

	$name = $request->input('name', 'Sally');
	
atau dalam array khusus

		$name = $request->input('products.0.name');
		 
		$names = $request->input('products.*.name');
		
atau kesemua dalam array

	$input = $request->input();
	
#### Dapatkan nilai input JSON
***Content-Type header*** perlu disetkan kepada application/json untuk capaian data JSON melalui fungsi.

	$name = $request->input('user.name');
	
#### Dapatkan nilai input ***string***

	$name = $request->string('name')->trim();
	

#### Dapatkan nilai input ***boolean***

	$archived = $request->boolean('archived');
	
#### Dapatkan nilai input tarikh

	$birthday = $request->date('birthday');
	
atau

	$elapsed = $request->date('elapsed', '!H:i', 'Europe/Madrid');
	
#### Dapatkan nilai input Enum

	use App\Enums\Status;
	 
	$status = $request->enum('status', Status::class);
	
#### Dapatkan input melalui Dynamic Properties

	$name = $request->name;
	

#### Dapatkan sebahagian dari input Data

	$input = $request->only(['username', 'password']);
	 
	$input = $request->only('username', 'password');
	 
	$input = $request->except(['credit_card']);
	 
	$input = $request->except('credit_card');
	

### Mengesahkan kewujudan Input


	if ($request->has('name')) {
	    //
	}
	
atau semak dalam arrary

	if ($request->has(['name', 'email'])) {
	    //
	}
	
atau whenHas 

	$request->whenHas('name', function ($input) {
	    //
	});
	
atau whenHas hantar parameter

	$request->whenHas('name', function ($input) {
	    // The "name" value is present...
	}, function () {
	    // The "name" value is not present...
	});
	
	atau hasAny
	
	if ($request->hasAny(['name', 'email'])) {
	    //
	}
	
atau whenFilled

	if ($request->filled('name')) {
	    //
	}
	
ataun whenFilled dengan parameter

	$request->whenFilled('name', function ($input) {
	    //
	});
	
	$request->whenFilled('name', function ($input) {
	    // The "name" value is filled...
	}, function () {
	    // The "name" value is not filled...
	});
	
	
whenMissing

	if ($request->missing('name')) {
	    //
	}
	 
whenMissing dengan parameter 
 
	$request->whenMissing('name', function ($input) {
	    // The "name" value is missing...
	}, function () {
	    // The "name" value is present...
	});
	

### Merging Additional Input

	$request->merge(['votes' => 0]);
	
atau

	$request->mergeIfMissing(['votes' => 0]);
	

### Old Input
Laravel membolehkan anda menyimpan input daripada satu permintaan semasa permintaan seterusnya. Ciri ini amat berguna untuk mengisi semula borang selepas mengesan ralat pengesahan. Walau bagaimanapun, jika anda menggunakan ***Laravel validation features*** yang disertakan, ada kemungkinan anda tidak perlu menggunakan kaedah flash input sesi ini secara manual, kerana beberapa kemudahan pengesahan terbina dalam Laravel akan memanggilnya secara automatik.

#### Flashing Input kpd The Session

	$request->flash();
	
atau

	$request->flashOnly(['username', 'email']);
	 
	$request->flashExcept('password');
	
#### Flashing Input kemudian Redirecting

	return redirect('form')->withInput();
	 
	return redirect()->route('user.create')->withInput();
	 
	return redirect('form')->withInput(
	    $request->except('password')
	);
	

#### Dapatkan Old Input

	$username = $request->old('username');
	
atau

	<input type="text" name="username" value="{{ old('username') }}">
	
### Cookies

#### Dapatkan Cookies dari Requests

	$value = $request->cookie('name');
	

### Trim dan Normalize Input
Secara default , laravel akan trim input dan tukar null kepada ''

#### Disabling Input Normalization

	use App\Http\Middleware\TrimStrings;
	use Illuminate\Foundation\Http\Middleware\ConvertEmptyStringsToNull;
	 
	/**
	 * Bootstrap any application services.
	 *
	 * @return void
	 */
	public function boot()
	{
	    TrimStrings::skipWhen(function ($request) {
	        return $request->is('admin/*');
	    });
	 
	    ConvertEmptyStringsToNull::skipWhen(function ($request) {
	        // ...
	    });
	}
	

### Input Fail

#### Dapatkan fail yang di muatnaik

	$file = $request->file('photo');
	 
	$file = $request->photo;
	
atau semak dulu

	if ($request->hasFile('photo')) {
	    //
	}
	

#### Pengesahan berjaya di muatnaik

	if ($request->file('photo')->isValid()) {
	    //
	}
	

#### Dapatkan File Paths & Extensions

	$path = $request->photo->path();
	 
	$extension = $request->photo->extension();
	
#### Lain fungsi File

### Simpan fail yang dimuat naik

	$path = $request->photo->store('images');
	 
	$path = $request->photo->store('images', 's3');
	
atau storeAs

	$path = $request->photo->storeAs('images', 'filename.jpg');
	 
	$path = $request->photo->storeAs('images', 'filename.jpg', 's3');
	
### Configuring Trusted Proxies

	<?php
	 
	namespace App\Http\Middleware;
	 
	use Illuminate\Http\Middleware\TrustProxies as Middleware;
	use Illuminate\Http\Request;
	 
	class TrustProxies extends Middleware
	{
	    /**
	     * The trusted proxies for this application.
	     *
	     * @var string|array
	     */
	    protected $proxies = [
	        '192.168.1.1',
	        '192.168.1.2',
	    ];
	 
	    /**
	     * The headers that should be used to detect proxies.
	     *
	     * @var int
	     */
	    protected $headers = Request::HEADER_X_FORWARDED_FOR | Request::HEADER_X_FORWARDED_HOST | Request::HEADER_X_FORWARDED_PORT | Request::HEADER_X_FORWARDED_PROTO;
	}
	

#### Trusting All Proxies
	
	/**
	 * The trusted proxies for this application.
	 *
	 * @var string|array
	 */
	protected $proxies = '*';
	

#### Configuring Trusted Hosts

	/**
	 * Get the host patterns that should be trusted.
	 *
	 * @return array
	 */
	public function hosts()
	{
	    return [
	        'laravel.test',
	        $this->allSubdomainsOfApplicationUrl(),
	    ];
	}
	
