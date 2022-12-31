![Pusat Latihan CGN](../assets/images/cgnbulat.png)
# Laravel Controllers

## Controller

***Controller*** adalah ciri penting dalam kerangka Laravel dimana logik permintaan dikendalikan menggunakan kelas ***Controller***. ***Controller***  ditakrifkan dalam direktori "app/http/Controllers". Kerangka Laravel mengikut seni bina MVC (Model View Controller) di mana ***Controller*** bertindak sebagai menggerakkan trafik antara ***model*** dan ***view***.

### Asas Controllers

#### Contoh UserController

Contoh fail  app/Http/ControllersUserController.php

	<?php
	 
	namespace App\Http\Controllers;
	 
	use App\Models\User;
	 
	class UserController extends Controller
	{
	    /**
	     * Show the profile for a given user.
	     *
	     * @param  int  $id
	     * @return \Illuminate\View\View
	     */
	    public function show($id)
	    {
	        return view('user.profile', [
	            'user' => User::findOrFail($id)
	        ]);
	    }
	}
	


Fail ini dipanggin dari routing fail , web.php

	use App\Http\Controllers\UserController;
	 
	Route::get('/user/{id}', [UserController::class, 'show']);
	

### Jenis2 Controller

#### 1. Controllers Aksi Tunggal

Hanya ada satu fungsi __invoke dalam kelas untuk memproses logik

	<?php
	 
	namespace App\Http\Controllers;
	 
	use App\Models\User;
	 
	class ProvisionServer extends Controller
	{
	    /**
	     * Provision a new web server.
	     *
	     * @return \Illuminate\Http\Response
	     */
	    public function __invoke()
	    {
	        // ...
	    }
	}
	
	

Cara Routing	
	
	use App\Http\Controllers\ProvisionServer;
	 
	Route::post('/server', ProvisionServer::class);
	


#### 2. Controller Middleware

Routing middleware

	Route::get('profile', [UserController::class, 'show'])->middleware('auth');
	


Logik dalam Controller

	class UserController extends Controller
	{
	    /**
	     * Instantiate a new controller instance.
	     *
	     * @return void
	     */
	    public function __construct()
	    {
	        $this->middleware('auth');
	        $this->middleware('log')->only('index');
	        $this->middleware('subscribed')->except('store');
	    }
	}

define an inline middleware for a single controller without defining an entire middleware class

	$this->middleware(function ($request, $next) {
	    return $next($request);
	});
	

#### 3. Resource Controllers

Routing resource

	use App\Http\Controllers\PhotoController;
	 
	Route::resource('photos', PhotoController::class);
		

Routing resource dengan parameter

	Route::resources([
	    'photos' => PhotoController::class,
	    'posts' => PostController::class,
	]);



Resource Controller HTTP request
	
	Verb 		URI 					Action 		Route Name
	GET 		/photos 				index 		photos.index
	GET 		/photos/create 			create 		photos.create
	POST 		/photos 				store 		photos.store
	GET 		/photos/{photo} 		show 		photos.show
	GET 		/photos/{photo}/edit 	edit 		photos.edit
	PUT/PATCH 	/photos/{photo} 		update 		photos.update
	DELETE 		/photos/{photo} 		destroy 	photos.destroy
	

### Arahan2 logik dalam Controller
#### Customizing Missing Model Behavior

	use App\Http\Controllers\PhotoController;
	use Illuminate\Http\Request;
	use Illuminate\Support\Facades\Redirect;
	 
	Route::resource('photos', PhotoController::class)
	        ->missing(function (Request $request) {
	            return Redirect::route('photos.index');
	        });
	 

#### Soft Deleted Models

	use App\Http\Controllers\PhotoController;
	 
	Route::resource('photos', PhotoController::class)->withTrashed();
	

atau

	Route::resource('photos', PhotoController::class)->withTrashed(['show']);
	

#### Partial Resource Routes

	use App\Http\Controllers\PhotoController;
	 
	Route::resource('photos', PhotoController::class)->only([
	    'index', 'show'
	]);
	 
	Route::resource('photos', PhotoController::class)->except([
	    'create', 'store', 'update', 'destroy'
	]);
	


#### API Resource Routes
	
	use App\Http\Controllers\PhotoController;
	 
	Route::apiResource('photos', PhotoController::class);
	
atau

	use App\Http\Controllers\PhotoController;
	use App\Http\Controllers\PostController;
	 
	Route::apiResources([
	    'photos' => PhotoController::class,
	    'posts' => PostController::class,
	]);
	
	
#### Nested Resources
	
	use App\Http\Controllers\PhotoCommentController;
	 
	Route::resource('photos.comments', PhotoCommentController::class);
	


	/photos/{photo}/comments/{comment}
	

##### Shallow Nesting
	
	use App\Http\Controllers\CommentController;
	 
	Route::resource('photos.comments', CommentController::class)->shallow();
	

	
	Verb 	URI 	Action 	Route Name
	GET 	/photos/{photo}/comments 	index 	photos.comments.index
	GET 	/photos/{photo}/comments/create 	create 	photos.comments.create
	POST 	/photos/{photo}/comments 	store 	photos.comments.store
	GET 	/comments/{comment} 	show 	comments.show
	GET 	/comments/{comment}/edit 	edit 	comments.edit
	PUT/PATCH 	/comments/{comment} 	update 	comments.update
	DELETE 	/comments/{comment} 	destroy 	comments.destroy
	


#### Naming Resource Routes

	use App\Http\Controllers\AdminUserController;
	 
	Route::resource('users', AdminUserController::class)->parameters([
	    'users' => 'admin_user'
	]);
	
Cth URI

	/users/{admin_user}
	


#### Scoping Resource Routes
	
	use App\Http\Controllers\PhotoCommentController;
	 
	Route::resource('photos.comments', PhotoCommentController::class)->scoped([
	    'comment' => 'slug',
	]);
	

Cth URI

	/photos/{photo}/comments/{comment:slug}
	


#### Localizing Resource URIs
Tukar kata buat resource 
	
	/**
	 * Define your route model bindings, pattern filters, etc.
	 *
	 * @return void
	 */
	public function boot()
	{
	    Route::resourceVerbs([
	        'create' => 'crear',
	        'edit' => 'editar',
	    ]);
	 
	    // ...
	}
	

Cth URI
	
	/publicacion/crear
	 
	/publicacion/{publicaciones}/editar
	


#### Supplementing Resource Controllers
Overide resource route
	
	use App\Http\Controller\PhotoController;
	 
	Route::get('/photos/popular', [PhotoController::class, 'popular']);
	Route::resource('photos', PhotoController::class);
	


#### Singleton Resource Controllers
resources that may only have a single instance

	use App\Http\Controllers\ProfileController;
	use Illuminate\Support\Facades\Route;
	 
	Route::singleton('profile', ProfileController::class);
	


	
	Verb 	URI 	Action 	Route Name
	GET 	/profile 	show 	profile.show
	GET 	/profile/edit 	edit 	profile.edit
	PUT/PATCH 	/profile 	update 	profile.update
	


atau
	
	Route::singleton('photos.thumbnail', ThumbnailController::class);
	


	Verb 	URI 	Action 	Route Name
	GET 	/photos/{photo}/thumbnail 	show 	photos.thumbnail.show
	GET 	/photos/{photo}/thumbnail/edit 	edit 	photos.thumbnail.edit
	PUT/PATCH 	/photos/{photo}/thumbnail 	update 	photos.thumbnail.update
	
##### Creatable Singleton Resources
	
	Route::singleton('photos.thumbnail', ThumbnailController::class)->creatable();
	




	Verb 	URI 	Action 	Route Name
	GET 	/photos/{photo}/thumbnail/create 	create 	photos.thumbnail.create
	POST 	/photos/{photo}/thumbnail 	store 	photos.thumbnail.store
	GET 	/photos/{photo}/thumbnail 	show 	photos.thumbnail.show
	GET 	/photos/{photo}/thumbnail/edit 	edit 	photos.thumbnail.edit
	PUT/PATCH 	/photos/{photo}/thumbnail 	update 	photos.thumbnail.update
	DELETE 	/photos/{photo}/thumbnail 	destroy 	photos.thumbnail.destroy
	


#### API Singleton Resources

	Route::apiSingleton('profile', ProfileController::class);
	
	


	Route::apiSingleton('photos.thumbnail', ProfileController::class)->creatable();


### Dependency Injection & Controllers

Cth 


	<?php
	 
	namespace App\Http\Controllers;
	 
	use App\Repositories\UserRepository;
	 
	class UserController extends Controller
	{
	    /**
	     * The user repository instance.
	     */
	    protected $users;
	 
	    /**
	     * Create a new controller instance.
	     *
	     * @param  \App\Repositories\UserRepository  $users
	     * @return void
	     */
	    public function __construct(UserRepository $users)
	    {
	        $this->users = $users;
	    }
	}
	
	




#### Method Injection



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
	        $name = $request->name;
	 
	        //
	    }
	}
	

Cth Routing

	use App\Http\Controllers\UserController;
	 
	Route::put('/user/{id}', [UserController::class, 'update']);
	


Cth Controller


	<?php
	 
	namespace App\Http\Controllers;
	 
	use Illuminate\Http\Request;
	 
	class UserController extends Controller
	{
	    /**
	     * Update the given user.
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


### Arahan Artisan

	php artisan make:controller ProvisionServer --invokable
	php artisan make:controller PhotoController --resource
	php artisan make:controller PhotoController --model=Photo --resource
	php artisan make:controller PhotoController --model=Photo --resource --requests
	php artisan make:controller PhotoController --api
	

