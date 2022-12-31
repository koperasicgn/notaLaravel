![Pusat Latihan CGN](../../assets/images/cgnbulat.png)


### Laravel Logging

Laravel menyediakan perkhidmatan pengelogan teguh yang membolehkan anda log mesej ke fail, log ralat sistem, dan juga ke Slack untuk memberitahu seluruh pasukan anda.

Laravel menggunakan perpustakaan Monolog, yang menyediakan sokongan untuk pelbagai pengendali log yang berkuasa.

#### Konfigurasi

fail config/logging.php

##### Configuring The Channel Name
Monolog dibuat seketika dengan "nama saluran" yang sepadan dengan persekitaran semasa, seperti  ***production*** or ***local***


	'stack' => [
	    'driver' => 'stack',
	    'name' => 'channel-name',
	    'channels' => ['single', 'slack'],
	],
	
##### Available Channel Drivers

	Name 		Description
	custom 		A driver that calls a specified factory to create a channel
	daily 		A RotatingFileHandler based Monolog driver which rotates daily
	errorlog 	An ErrorLogHandler based Monolog driver
	monolog 	A Monolog factory driver that may use any supported Monolog handler
	null 		A driver that discards all log messages
	papertrail 	A SyslogUdpHandler based Monolog driver
	single 		A single file or path based logger channel (StreamHandler)
	slack 		A SlackWebhookHandler based Monolog driver
	stack 		A wrapper to facilitate creating "multi-channel" channels
	syslog 		A SyslogHandler based Monolog driver
	

#### Channel Prerequisites
##### Configuring The Single and Daily Channels
The single and daily channels have three optional configuration options: bubble, permission, and locking.

	Name 		Description 				Default
	bubble 		Indicates if messages should bubble up to other channels after being handled 			true
	locking 	Attempt to lock the log file before writing to it 	false
	permission 	The log file's permissions 	0644
	

Additionally, the retention policy for the daily channel can be configured via the days option:


	Name 		Description 				Default
	days 		The number of days that daily log files should be retained 							7
	

##### Configuring The Papertrail Channel


The papertrail channel requires the host and port configuration options. You can obtain these values from Papertrail.

##### Configuring The Slack Channel

The slack channel requires a url configuration option. This URL should match a URL for an incoming webhook that you have configured for your Slack team.

#### Logging Deprecation Warnings

	'deprecations' => env('LOG_DEPRECATIONS_CHANNEL', 'null'),
	 
	'channels' => [
	    ...
	]
	
atau

	'channels' => [
	    'deprecations' => [
	        'driver' => 'single',
	        'path' => storage_path('logs/php-deprecation-warnings.log'),
	    ],
	],
	

#### Building Log Stacks

the stack driver allows you to combine multiple channels into a single log channel for convenience

	'channels' => [
	    'stack' => [
	        'driver' => 'stack',
	        'channels' => ['syslog', 'slack'],
	    ],
	 
	    'syslog' => [
	        'driver' => 'syslog',
	        'level' => 'debug',
	    ],
	 
	    'slack' => [
	        'driver' => 'slack',
	        'url' => env('LOG_SLACK_WEBHOOK_URL'),
	        'username' => 'Laravel Log',
	        'emoji' => ':boom:',
	        'level' => 'critical',
	    ],
	],
	
##### Log Levels

level configuration option present on the syslog and slack channel

	Log::debug('An informational message.');
	
syslog

	Log::emergency('The system is down!');
	

#### Writing Log Messages

cth log

	use Illuminate\Support\Facades\Log;
	 
	Log::emergency($message);
	Log::alert($message);
	Log::critical($message);
	Log::error($message);
	Log::warning($message);
	Log::notice($message);
	Log::info($message);
	Log::debug($message);
	

cth 2

	<?php
	 
	namespace App\Http\Controllers;
	 
	use App\Http\Controllers\Controller;
	use App\Models\User;
	use Illuminate\Support\Facades\Log;
	 
	class UserController extends Controller
	{
	    /**
	     * Show the profile for the given user.
	     *
	     * @param  int  $id
	     * @return \Illuminate\Http\Response
	     */
	    public function show($id)
	    {
	        Log::info('Showing the user profile for user: '.$id);
	 
	        return view('user.profile', [
	            'user' => User::findOrFail($id)
	        ]);
	    }
	}
	
	

#### Contextual Information

contextual data will be formatted and displayed with the log message

	use Illuminate\Support\Facades\Log;
	 
	Log::info('User failed to login.', ['id' => $user->id]);
	

call the Log facade's withContext method

	<?php
	 
	namespace App\Http\Middleware;
	 
	use Closure;
	use Illuminate\Support\Facades\Log;
	use Illuminate\Support\Str;
	 
	class AssignRequestId
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
	        $requestId = (string) Str::uuid();
	 
	        Log::withContext([
	            'request-id' => $requestId
	        ]);
	 
	        return $next($request)->header('Request-Id', $requestId);
	    }
	}


called from the boot method of an application service provider:

	use Illuminate\Support\Facades\Log;
	use Illuminate\Support\Str;
	 
	class AppServiceProvider
	{
	    /**
	     * Bootstrap any application services.
	     *
	     * @return void
	     */
	    public function boot()
	    {
	        Log::shareContext([
	            'invocation-id' => (string) Str::uuid(),
	        ]);
	    }
	}


#### Writing To Specific Channels

use the channel method on the Log facade to retrieve and log to any channel defined in your configuration file:

	use Illuminate\Support\Facades\Log;
	 
	Log::channel('slack')->info('Something happened!');
	
	

use the stack method:

	Log::stack(['single', 'slack'])->info('Something happened!');
	


#### On-Demand Channels

pass a configuration array to the Log facade's build method:

	use Illuminate\Support\Facades\Log;
	 
	Log::build([
	  'driver' => 'single',
	  'path' => storage_path('logs/custom.log'),
	])->info('Something happened!');


achieved by including your on-demand channel instance in the array passed to the stack method:

	use Illuminate\Support\Facades\Log;
	 
	$channel = Log::build([
	  'driver' => 'single',
	  'path' => storage_path('logs/custom.log'),
	]);
	 
	Log::stack(['slack', $channel])->info('Something happened!');

### Monolog Channel Customization

Customizing Monolog For Channels

	'single' => [
	    'driver' => 'single',
	    'tap' => [App\Logging\CustomizeFormatter::class],
	    'path' => storage_path('logs/laravel.log'),
	    'level' => 'debug',
	],
	

Illuminate\Log\Logger instance proxies all method calls to the underlying Monolog instance:

	<?php
	 
	namespace App\Logging;
	 
	use Monolog\Formatter\LineFormatter;
	 
	class CustomizeFormatter
	{
	    /**
	     * Customize the given logger instance.
	     *
	     * @param  \Illuminate\Log\Logger  $logger
	     * @return void
	     */
	    public function __invoke($logger)
	    {
	        foreach ($logger->getHandlers() as $handler) {
	            $handler->setFormatter(new LineFormatter(
	                '[%datetime%] %channel%.%level_name%: %message% %context% %extra%'
	            ));
	        }
	    }
	}
	
#### Creating Monolog Handler Channels

constructor parameters the handler needs may be specified using the with configuration option:

	'logentries' => [
	    'driver'  => 'monolog',
	    'handler' => Monolog\Handler\SyslogUdpHandler::class,
	    'with' => [
	        'host' => 'my.logentries.internal.datahubhost.company.com',
	        'port' => '10000',
	    ],
	],
	

##### Monolog Formatters

	'browser' => [
	    'driver' => 'monolog',
	    'handler' => Monolog\Handler\BrowserConsoleHandler::class,
	    'formatter' => Monolog\Formatter\HtmlFormatter::class,
	    'formatter_with' => [
	        'dateFormat' => 'Y-m-d',
	    ],
	],
	
set the value of the formatter configuration option to default:

	'newrelic' => [
	    'driver' => 'monolog',
	    'handler' => Monolog\Handler\NewRelicHandler::class,
	    'formatter' => 'default',
	],
	
#### Creating Custom Channels Via Factories

include a via option that contains the name of the factory class which will be invoked to create the Monolog instance:

	'channels' => [
	    'example-custom-channel' => [
	        'driver' => 'custom',
	        'via' => App\Logging\CreateCustomLogger::class,
	    ],
	],
	
receive the channels configuration array as its only argument:

	<?php
	 
	namespace App\Logging;
	 
	use Monolog\Logger;
	 
	class CreateCustomLogger
	{
	    /**
	     * Create a custom Monolog instance.
	     *
	     * @param  array  $config
	     * @return \Monolog\Logger
	     */
	    public function __invoke(array $config)
	    {
	        return new Logger(/* ... */);
	    }
	}
	


