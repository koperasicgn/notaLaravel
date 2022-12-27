# Laravel Routing
***Routing*** adalah salah satu konsep penting dalam Laravel. Fungsi utama ***Routing*** adalah untuk mengarahkan semua permintaan aplikasi anda kepada ***Controllers*** yang sesuai.  

Lokasi fail ***Routing*** adalah dalam folder *route* dengan nama *web.php* :  

    laravel/route/web.php
    laravel/route/api.php
 
***Routing*** dalam web.php akan mempunyai ciri2 *session state* and kelesamatan *CSRF* untuk kegunaan antaramuka *web*.

Satu lagi fail ***Routing*** adalah *api.php* dalam folder yang sama tetapi tidak mempunyai ciri2 sebagaimana *web.php*

#### Asas Laravel Routing
Route Contoh 1 : ***Routing*** - memulangkan kenyataan *Salam Dunia* kepada pelayar   

    use Illuminate\Support\Facades\Route;
    
    Route::get('/greeting', function () {  
        return 'Salam Dunia';  
    });  

Route Contoh 2 : ***Routing*** - memulangkan kenyataan *Salam Dunia* kepada pelayar   


	use App\Http\Controllers\UserController;

	Route::get('/user', [UserController::class, 'index']);

#### Routing Parameters
#### Laravel Named Routes
#### Laravel Middleware
#### Laravel Route Groups
