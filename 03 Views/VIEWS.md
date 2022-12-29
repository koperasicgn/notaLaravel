# Laravel Views

Views mengandungi kod html yang diperlukan oleh aplikasi anda, dan ia merupakan kaedah dalam Laravel yang memisahkan logik pengawal dan logik domain daripada logik pembentangan. Views diletakkan dalam folder laravel\resource. Nama fail Views berakhir dengan .blade.php dan menunjukan pemproses blade template.

#### Contoh fail views
	<!-- View stored in resources/views/greeting.blade.php -->
	 
	<html>
	    <body>
	        <h1>Hello, {{ $name }}</h1>
	    </body>
	</html>
	

Cth ***routing*** terus ke view dengan parameter.

	Route::get('/', function () {
	    return view('greeting', ['name' => 'James']);
	});
	

atau

	Route::get('/', function () {
	    return view('greeting', ['name' => 'James']);
	});
	

atau

	use Illuminate\Support\Facades\View;
	 
	return View::make('greeting', ['name' => 'James']);
	
#### Pilihan views

Ada beberapa pilihan untuk bina antaramuka pengguna yang disediakan oleh laravel

- Blade templates
- Inertia - menawarkan kepada pengguna Laravel ialah: Laluan aplikasi semuanya terkandung dalam satu fail. Inertia menghapuskan sepenuhnya kerumitan ***routing*** pihak pelanggan untuk berkomunikasi dengan Vue atau React.
	- Vue - Kerangka JavaScript untuk membina antara muka pengguna. Ia dibina di atas HTML standard, CSS dan JavaScript dan menyediakan model pengaturcaraan deklaratif dan berasaskan komponen yang membantu anda membangunkan antara muka pengguna dengan cekap, sama ada mudah atau kompleks.

	- React - Kerangka JavaScript sumber terbuka dan ***library*** yang dibangunkan oleh Facebook. Ia digunakan untuk membina antara muka pengguna interaktif dan aplikasi web dengan cepat dan cekap dengan kod yang jauh lebih sedikit daripada yang anda lakukan dengan JavaScript vanila.


#### Struktur folder views  dan rujukan
***Nested views*** , contoh rujukan kepada fail resources/views/admin/profile.blade.php

	return view('admin.profile', $data);
	

***First Available View*** contoh

	use Illuminate\Support\Facades\View;
	 
	return View::first(['custom.admin', 'admin'], $data);
	
Fungsi semakan sekiranya ada, contoh

	use Illuminate\Support\Facades\View;
	 
	if (View::exists('emails.customer')) {
	    //
	}
	
#### Hantar data ke Views
##### Cth Guna Array dengan pasangan key / value

	return view('greetings', ['name' => 'Victoria']);
	
##### Cth Guna fungsi helper

	return view('greeting')
	            ->with('name', 'Victoria')
	            ->with('occupation', 'Astronaut');
	

##### Kongsi Data dengan semua Views
Boleh tambah data yang ingin di kongsi di App\Providers\AppServiceProvider


	<?php
	 
	namespace App\Providers;
	 
	use Illuminate\Support\Facades\View;
	 
	class AppServiceProvider extends ServiceProvider
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
	        View::share('key', 'value');
	    }
	}
	
#### View Composers
***View Composer*** mungkin terbukti sangat berguna jika paparan yang sama dikembalikan oleh berbilang ***route*** atau ***controller*** dalam aplikasi anda dan sentiasa memerlukan data tertentu. ***View Composer*** perlu dicipta dan dalam folder ***service providers***

	<?php
	 
	namespace App\Providers;
	 
	use App\View\Composers\ProfileComposer;
	use Illuminate\Support\Facades\View;
	use Illuminate\Support\ServiceProvider;
	 
	class ViewServiceProvider extends ServiceProvider
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
	        // Using class based composers...
	        View::composer('profile', ProfileComposer::class);
	 
	        // Using closure based composers...
	        View::composer('dashboard', function ($view) {
	            //
	        });
	    }
	}
	
Contoh penggunaan :

	<?php
	 
	namespace App\View\Composers;
	 
	use App\Repositories\UserRepository;
	use Illuminate\View\View;
	 
	class ProfileComposer
	{
	    /**
	     * The user repository implementation.
	     *
	     * @var \App\Repositories\UserRepository
	     */
	    protected $users;
	 
	    /**
	     * Create a new profile composer.
	     *
	     * @param  \App\Repositories\UserRepository  $users
	     * @return void
	     */
	    public function __construct(UserRepository $users)
	    {
	        $this->users = $users;
	    }
	 
	    /**
	     * Bind data to the view.
	     *
	     * @param  \Illuminate\View\View  $view
	     * @return void
	     */
	    public function compose(View $view)
	    {
	        $view->with('count', $this->users->count());
	    }
	}
	


##### Kepilkan Composer kepada pelbagai Views
Contoh :

	use App\Views\Composers\MultiComposer;
	 
	View::composer(
	    ['profile', 'dashboard'],
	    MultiComposer::class
	);
	

Contoh kepil ke semua views

	View::composer('*', function ($view) {
	    //
	});
	
#### View Creators
Contoh penggunaan view creator , alternatif kepada view composer

	use App\View\Creators\ProfileCreator;
	use Illuminate\Support\Facades\View;
	 
	View::creator('profile', ProfileCreator::class);
	

### Arahan Artisan

	php artisan view:cache
	php artisan view:clear


