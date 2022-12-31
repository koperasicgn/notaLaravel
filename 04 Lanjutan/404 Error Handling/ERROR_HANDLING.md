![Pusat Latihan CGN](../../assets/images/cgnbulat.png)

### Laravel Error Handling

Pengendalaian ralat datanf bersama2 dengan pakej laravel dalam kelas App\Exceptions\Handler

#### Configuration

kofigurarasi ralat beleh diubah dalam fail config/app.php dengan nilai pemboleh ubag APP_DEBUG diambil dari fail .env


#### The Exception Handler

##### Reporting Exceptions

	use App\Exceptions\InvalidOrderException;
	 
	/**
	 * Register the exception handling callbacks for the application.
	 *
	 * @return void
	 */
	public function register()
	{
	    $this->reportable(function (InvalidOrderException $e) {
	        //
	    });
	}
	

fungsi reportable

	$this->reportable(function (InvalidOrderException $e) {
	    //
	})->stop();
	 
	$this->reportable(function (InvalidOrderException $e) {
	    return false;
	});
	

##### Global Log Context

context

	/**
	 * Get the default context variables for logging.
	 *
	 * @return array
	 */
	protected function context()
	{
	    return array_merge(parent::context(), [
	        'foo' => 'bar',
	    ]);
	}

##### Exception Log Context

log messages

	<?php
	 
	namespace App\Exceptions;
	 
	use Exception;
	 
	class InvalidOrderException extends Exception
	{
	    // ...
	 
	    /**
	     * Get the exception's context information.
	     *
	     * @return array
	     */
	    public function context()
	    {
	        return ['order_id' => $this->orderId];
	    }
	}
	

##### The report Helper

	public function isValid($value)
	{
	    try {
	        // Validate the value...
	    } catch (Throwable $e) {
	        report($e);
	 
	        return false;
	    }
	}
	

##### Exception Log Levels

	use PDOException;
	use Psr\Log\LogLevel;
	 
	/**
	 * A list of exception types with their corresponding custom log levels.
	 *
	 * @var array<class-string<\Throwable>, \Psr\Log\LogLevel::*>
	 */
	protected $levels = [
	    PDOException::class => LogLevel::CRITICAL,
	];
	

##### Ignoring Exceptions By Type

	use App\Exceptions\InvalidOrderException;
	 
	/**
	 * A list of the exception types that are not reported.
	 *
	 * @var array<int, class-string<\Throwable>>
	 */
	protected $dontReport = [
	    InvalidOrderException::class,
	];
	

##### Rendering Exceptions


	use App\Exceptions\InvalidOrderException;
	 
	/**
	 * Register the exception handling callbacks for the application.
	 *
	 * @return void
	 */
	public function register()
	{
	    $this->renderable(function (InvalidOrderException $e, $request) {
	        return response()->view('errors.invalid-order', [], 500);
	    });
	}
	





	use Symfony\Component\HttpKernel\Exception\NotFoundHttpException;
	 
	/**
	 * Register the exception handling callbacks for the application.
	 *
	 * @return void
	 */
	public function register()
	{
	    $this->renderable(function (NotFoundHttpException $e, $request) {
	        if ($request->is('api/*')) {
	            return response()->json([
	                'message' => 'Record not found.'
	            ], 404);
	        }
	    });
	}
	

##### Reportable & Renderable Exceptions

	<?php
	 
	namespace App\Exceptions;
	 
	use Exception;
	 
	class InvalidOrderException extends Exception
	{
	    /**
	     * Report the exception.
	     *
	     * @return bool|null
	     */
	    public function report()
	    {
	        //
	    }
	 
	    /**
	     * Render the exception into an HTTP response.
	     *
	     * @param  \Illuminate\Http\Request  $request
	     * @return \Illuminate\Http\Response
	     */
	    public function render($request)
	    {
	        return response(/* ... */);
	    }
	}
	





	/**
	 * Render the exception into an HTTP response.
	 *
	 * @param  \Illuminate\Http\Request  $request
	 * @return \Illuminate\Http\Response
	 */
	public function render($request)
	{
	    // Determine if the exception needs custom rendering...
	 
	    return false;
	}
	




	/**
	 * Report the exception.
	 *
	 * @return bool|null
	 */
	public function report()
	{
	    // Determine if the exception needs custom reporting...
	 
	    return false;
	}
	

#### HTTP Exceptions

	abort(404);
	

##### Custom HTTP Error Pages

	<h2>{{ $exception->getMessage() }}</h2>
	
arahan artisan

		php artisan vendor:publish --tag=laravel-errors
		
	
##### Fallback HTTP Error Pages

		resources/views/errors
		
