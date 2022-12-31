![Pusat Latihan CGN](../../assets/images/cgnbulat.png)

### Laravel Validation

***Validation*** ialah proses menyemak data yang masuk. Laravel menyediakan kelas asas ***controller*** yang menggunakan sifat ValidatesRequests untuk mengesahkan semua permintaan Http yang masuk.

#### Pengenalan Validation

Belajar melalui contoh

##### Routes

Tambahan arahan routes dalam routes\web.php

	use App\Http\Controllers\PostController;
	 
	Route::get('/post/create', [PostController::class, 'create']);
	Route::post('/post', [PostController::class, 'store']);
	
###### Creating The Controller

Cipta satu fail ***controller***

	<?php
	 
	namespace App\Http\Controllers;
	 
	use App\Http\Controllers\Controller;
	use Illuminate\Http\Request;
	 
	class PostController extends Controller
	{
	    /**
	     * Show the form to create a new blog post.
	     *
	     * @return \Illuminate\View\View
	     */
	    public function create()
	    {
	        return view('post.create');
	    }
	 
	    /**
	     * Store a new blog post.
	     *
	     * @param  \Illuminate\Http\Request  $request
	     * @return \Illuminate\Http\Response
	     */
	    public function store(Request $request)
	    {
	        // Validate and store the blog post...
	    }
	}
	

##### Writing The Validation Logic

Cipta fail model untuk masukkan fungsi login berikut :

	/**
	 * Store a new blog post.
	 *
	 * @param  \Illuminate\Http\Request  $request
	 * @return \Illuminate\Http\Response
	 */
	public function store(Request $request)
	{
	    $validated = $request->validate([
	        'title' => 'required|unique:posts|max:255',
	        'body' => 'required',
	    ]);
	 
	    // The blog post is valid...
	}
	

atau boleh guna 1 baris shj


	$validatedData = $request->validate([
	    'title' => ['required', 'unique:posts', 'max:255'],
	    'body' => ['required'],
	]);
	

atau guna validationbag untuk untuk sah dan simpan

	$validatedData = $request->validateWithBag('post', [
	    'title' => ['required', 'unique:posts', 'max:255'],
	    'body' => ['required'],
	]);
	

##### Stopping On First Validation Failure

pengunaan bail

	$request->validate([
	    'title' => 'bail|required|unique:posts|max:255',
	    'body' => 'required',
	]);
	
##### A Note On Nested Attributes

penggunaan dot

	$request->validate([
	    'title' => 'required|unique:posts|max:255',
	    'author.name' => 'required',
	    'author.description' => 'required',
	]);
	
escape dot

	$request->validate([
	    'title' => 'required|unique:posts|max:255',
	    'v1\.0' => 'required',
	]);
	

#### Displaying The Validation Errors

ralat pengesahan dan input permintaan akan dipancarkan secara automatik ke session.

The $errors variable will be an instance of Illuminate\Support\MessageBag

	<!-- /resources/views/post/create.blade.php -->
	 
	<h1>Create Post</h1>
	 
	@if ($errors->any())
	    <div class="alert alert-danger">
	        <ul>
	            @foreach ($errors->all() as $error)
	                <li>{{ $error }}</li>
	            @endforeach
	        </ul>
	    </div>
	@endif
	 
<!-- Create Post Form -->


##### Customizing The Error Messages

lang/en/validation.php

##### XHR Requests & Validation

 Laravel generates a JSON response containing all of the validation errors. This JSON response will be sent with a 422 HTTP status code.


##### The @error Directive

Within an @error directive, you may echo the $message variable to display the error message

	<!-- /resources/views/post/create.blade.php -->
	 
	<label for="title">Post Title</label>
	 
	<input id="title"
	    type="text"
	    name="title"
	    class="@error('title') is-invalid @enderror">
	 
	@error('title')
	    <div class="alert alert-danger">{{ $message }}</div>
	@enderror
	
named error bags

	<input ... class="@error('title', 'post') is-invalid @enderror">
	
##### Repopulating Forms
invoke the old method on an instance of Illuminate\Http\Request

	$title = $request->old('title');

dalam view

	<input type="text" name="title" value="{{ old('title') }}">

##### A Note On Optional Fields

Laravel includes the TrimStrings and ConvertEmptyStringsToNull middleware

	$request->validate([
	    'title' => 'required|unique:posts|max:255',
	    'body' => 'required',
	    'publish_at' => 'nullable|date',
	]);
	

#### Validation Error Response Format

 application throws a Illuminate\Validation\ValidationException 
 
	{
	    "message": "The team name must be a string. (and 4 more errors)",
	    "errors": {
	        "team_name": [
	            "The team name must be a string.",
	            "The team name must be at least 1 characters."
	        ],
	        "authorization.role": [
	            "The selected authorization.role is invalid."
	        ],
	        "users.0.email": [
	            "The users.0.email field is required."
	        ],
	        "users.2.email": [
	            "The users.2.email must be a valid email address."
	        ]
	    }
	}
	

#### Form Request Validation

form validation

##### Creating Form Requests

arahan artisan ,form request class will be placed in the app/Http/Requests directory

	php artisan make:request StorePostRequest
	
Laravel has two methods: authorize and rules.

	/**
	 * Get the validation rules that apply to the request.
	 *
	 * @return array
	 */
	public function rules()
	{
	    return [
	        'title' => 'required|unique:posts|max:255',
	        'body' => 'required',
	    ];
	}

validation logic

	/**
	 * Store a new blog post.
	 *
	 * @param  \App\Http\Requests\StorePostRequest  $request
	 * @return Illuminate\Http\Response
	 */
	public function store(StorePostRequest $request)
	{
	    // The incoming request is valid...
	 
	    // Retrieve the validated input data...
	    $validated = $request->validated();
	 
	    // Retrieve a portion of the validated input data...
	    $validated = $request->safe()->only(['name', 'email']);
	    $validated = $request->safe()->except(['name', 'email']);
	}
	
##### Adding After Hooks To Form Requests

add an "after" validation hook to a form request


	/**
	 * Configure the validator instance.
	 *
	 * @param  \Illuminate\Validation\Validator  $validator
	 * @return void
	 */
	public function withValidator($validator)
	{
	    $validator->after(function ($validator) {
	        if ($this->somethingElseIsInvalid()) {
	            $validator->errors()->add('field', 'Something is wrong with this field!');
	        }
	    });
	}

##### Stopping On First Validation Failure Attribute

adding a stopOnFirstFailure

	/**
	 * Indicates if the validator should stop on the first rule failure.
	 *
	 * @var bool
	 */
	protected $stopOnFirstFailure = true;
	
##### Customizing The Redirect Location

	/**
	 * The URI that users should be redirected to if validation fails.
	 *
	 * @var string
	 */
	protected $redirect = '/dashboard';
	
guna $redirectRoute

	/**
	 * The route that users should be redirected to if validation fails.
	 *
	 * @var string
	 */
	protected $redirectRoute = 'dashboard';
	
#### Authorizing Form Requests

form request class also contains an authorize method


	use App\Models\Comment;
	 
	/**
	 * Determine if the user is authorized to make this request.
	 *
	 * @return bool
	 */
	public function authorize()
	{
	    $comment = Comment::find($this->route('comment'));
	 
	    return $comment && $this->user()->can('update', $comment);
	}
	
dalam route

	Route::post('/comment/{comment}');
	
dalam model

	return $this->user()->can('update', $this->comment);
	
	
If the authorize method returns false

	/**
	 * Determine if the user is authorized to make this request.
	 *
	 * @return bool
	 */
	public function authorize()
	{
	    return true;
	}
	

#### Customizing The Error Messages

method should return an array of attribute / rule pairs and their corresponding error messages

	/**
	 * Get the error messages for the defined validation rules.
	 *
	 * @return array
	 */
	public function messages()
	{
	    return [
	        'title.required' => 'A title is required',
	        'body.required' => 'A message is required',
	    ];
	}
	
##### Customizing The Validation Attributes

contain an :attribute placeholder

	/**
	 * Get custom attributes for validator errors.
	 *
	 * @return array
	 */
	public function attributes()
	{
	    return [
	        'email' => 'email address',
	    ];
	}

#### Preparing Input For Validation

	use the prepareForValidation
	
	use Illuminate\Support\Str;
	 
	/**
	 * Prepare the data for validation.
	 *
	 * @return void
	 */
	protected function prepareForValidation()
	{
	    $this->merge([
	        'slug' => Str::slug($this->slug),
	    ]);
	}

#### Manually Creating Validators

using the Validator facade

	<?php
	 
	namespace App\Http\Controllers;
	 
	use App\Http\Controllers\Controller;
	use Illuminate\Http\Request;
	use Illuminate\Support\Facades\Validator;
	 
	class PostController extends Controller
	{
	    /**
	     * Store a new blog post.
	     *
	     * @param  Request  $request
	     * @return Response
	     */
	    public function store(Request $request)
	    {
	        $validator = Validator::make($request->all(), [
	            'title' => 'required|unique:posts|max:255',
	            'body' => 'required',
	        ]);
	 
	        if ($validator->fails()) {
	            return redirect('post/create')
	                        ->withErrors($validator)
	                        ->withInput();
	        }
	 
	        // Retrieve the validated input...
	        $validated = $validator->validated();
	 
	        // Retrieve a portion of the validated input...
	        $validated = $validator->safe()->only(['name', 'email']);
	        $validated = $validator->safe()->except(['name', 'email']);
	 
	        // Store the blog post...
	    }
	}
	
##### Stopping On First Validation Failure

stopOnFirstFailure method 

	if ($validator->stopOnFirstFailure()->fails()) {
	    // ...
	}
	
#### Automatic Redirection

	If validation fails, the user will automatically be redirected
	
	Validator::make($request->all(), [
	    'title' => 'required|unique:posts|max:255',
	    'body' => 'required',
	])->validate();
	

penggunaan validateWithBag

	Validator::make($request->all(), [
	    'title' => 'required|unique:posts|max:255',
	    'body' => 'required',
	])->validateWithBag('post');

#### Named Error Bags

multiple forms on a single page

	return redirect('register')->withErrors($validator, 'login');


access the named MessageBag
 
	{{ $errors->login->first('email') }}
	

#### Customizing The Error Messages

you may provide custom error messages that a validator instance

$validator = Validator::make($input, $rules, $messages = [
    'required' => 'The :attribute field is required.',
]);


the :attribute placeholder will be replaced by the actual name of the field 

	$messages = [
	    'same' => 'The :attribute and :other must match.',
	    'size' => 'The :attribute must be exactly :size.',
	    'between' => 'The :attribute value :input is not between :min - :max.',
	    'in' => 'The :attribute must be one of the following types: :values',
	];


##### Specifying A Custom Message For A Given Attribute

specify a custom error message only for a specific attribute.

	$messages = [
	    'email.required' => 'We need to know your email address!',
	];
	
##### Specifying Custom Attribute Values

	$validator = Validator::make($input, $rules, $messages, [
	    'email' => 'email address',
	]);

#### After Validation Hook

attach callbacks to be run after validation is completed

	$validator = Validator::make(/* ... */);
	 
	$validator->after(function ($validator) {
	    if ($this->somethingElseIsInvalid()) {
	        $validator->errors()->add(
	            'field', 'Something is wrong with this field!'
	        );
	    }
	});
	 
	if ($validator->fails()) {
	    //
	}
	
#### Working With Validated Input

retrieve the incoming request data that actually underwent validation.

	$validated = $request->validated();
	 
	$validated = $validator->validated();
	

 call the safe method on a form request or validator instance

	$validated = $request->safe()->only(['name', 'email']);
	 
	$validated = $request->safe()->except(['name', 'email']);
	 
	$validated = $request->safe()->all();
	

iterated over and accessed like an array

	// Validated data may be iterated...
	foreach ($request->safe() as $key => $value) {
	    //
	}
	 
	// Validated data may be accessed as an array...
	$validated = $request->safe();
	 
	$email = $validated['email'];
	
call the merge method

	$validated = $request->safe()->merge(['name' => 'Taylor Otwell']);

call the collect method:

	$collection = $request->safe()->collect();

#### Working With Error Messages

$errors variable that is automatically made available to all views is also an instance of the MessageBag class

##### Retrieving The First Error Message For A Field

retrieve the first error message for a given field

	$errors = $validator->errors();
	 
	echo $errors->first('email');
	
##### Retrieving All Error Messages For A Field

retrieve an array of all the messages for a given field

	foreach ($errors->get('email') as $message) {
	    //
	}

retrieve all of the messages for each of the array elements using the * character

	foreach ($errors->get('attachments.*') as $message) {
	    //
	}

##### Retrieving All Error Messages For All Fields

retrieve an array of all messages for all fields

	foreach ($errors->all() as $message) {
	    //
	}

Determining If Messages Exist For A Field

	if ($errors->has('email')) {
	    //
	}

#### Specifying Custom Messages In Language Files

lang/en/validation.php file.

##### Custom Messages For Specific Attributes

lang/xx/validation.php language file


	'custom' => [
	    'email' => [
	        'required' => 'We need to know your email address!',
	        'max' => 'Your email address is too long!'
	    ],
	],
	
##### Specifying Attributes In Language Files

replaced with a custom value, you may specify the custom attribute name in the attributes

	'attributes' => [
	    'email' => 'email address',
	],

##### Specifying Values In Language Files

error messages contain a :value placeholder

	Validator::make($request->all(), [
	    'credit_card_number' => 'required_if:payment_type,cc'
	]);
	
produce the following error message:

The credit card number field is required when payment type is cc.

specify a more user-friendly value representation in your lang/xx/validation.php

	'values' => [
	    'payment_type' => [
	        'cc' => 'credit card'
	    ],
	],
	
validation rule will produce the following error message:

The credit card number field is required when payment type is credit card.

#### Available Validation Rules



	Accepted
	Accepted If
	Active URL
	After (Date)
	After Or Equal (Date)
	Alpha
	Alpha Dash
	Alpha Numeric
	Array
	Ascii
	Bail
	Before (Date)
	Before Or Equal (Date)
	Between
	Boolean
	Confirmed
	Current Password
	Date
	Date Equals
	Date Format
	Decimal
	Declined
	Declined If
	Different
	Digits
	Digits Between
	Dimensions (Image Files)
	Distinct
	Doesnt Start With
	Doesnt End With
	Email
	Ends With
	Enum
	Exclude
	Exclude If
	Exclude Unless
	Exclude With
	Exclude Without
	Exists (Database)
	File
	Filled
	Greater Than
	Greater Than Or Equal
	Image (File)
	In
	In Array
	Integer
	IP Address
	JSON
	Less Than
	Less Than Or Equal
	Lowercase
	MAC Address
	Max
	Max Digits
	MIME Types
	MIME Type By File Extension
	Min
	Min Digits
	Multiple Of
	Not In
	Not Regex
	Nullable
	Numeric
	Password
	Present
	Prohibited
	Prohibited If
	Prohibited Unless
	Prohibits
	Regular Expression
	Required
	Required If
	Required Unless
	Required With
	Required With All
	Required Without
	Required Without All
	Required Array Keys
	Same
	Size
	Sometimes
	Starts With
	String
	Timezone
	Unique (Database)
	Uppercase
	URL
	ULID
	UUID



#### Conditionally Adding Rules

##### Skipping Validation When Fields Have Certain Values

using the exclude_if validation rule

	use Illuminate\Support\Facades\Validator;
	 
	$validator = Validator::make($data, [
	    'has_appointment' => 'required|boolean',
	    'appointment_date' => 'exclude_if:has_appointment,false|required|date',
	    'doctor_name' => 'exclude_if:has_appointment,false|required|string',
	]);
	
the exclude_unless rule 

	$validator = Validator::make($data, [
	    'has_appointment' => 'required|boolean',
	    'appointment_date' => 'exclude_unless:has_appointment,true|required|date',
	    'doctor_name' => 'exclude_unless:has_appointment,true|required|string',
	]);

##### Validating When Present

	$v = Validator::make($data, [
	    'email' => 'sometimes|required|email',
	]);
	
##### Complex Conditional Validation

	use Illuminate\Support\Facades\Validator;
	 
	$validator = Validator::make($request->all(), [
	    'email' => 'required|email',
	    'games' => 'required|numeric',
	]);
	

use the sometimes method

	$validator->sometimes('reason', 'required|max:500', function ($input) {
	    return $input->games >= 100;
	});
	

argument passed to the sometimes method

	$validator->sometimes(['reason', 'cost'], 'required', function ($input) {
	    return $input->games >= 100;
	});

##### Complex Conditional Array Validation

validate a field based on another field in the same nested array

	$input = [
	    'channels' => [
	        [
	            'type' => 'email',
	            'address' => 'abigail@example.com',
	        ],
	        [
	            'type' => 'url',
	            'address' => 'https://example.com',
	        ],
	    ],
	];
	 
	$validator->sometimes('channels.*.address', 'email', function ($input, $item) {
	    return $item->type === 'email';
	});
	 
	$validator->sometimes('channels.*.address', 'url', function ($input, $item) {
	    return $item->type !== 'email';
	});
	

#### Validating Arrays

the array rule accepts a list of allowed array keys

	use Illuminate\Support\Facades\Validator;
	 
	$input = [
	    'user' => [
	        'name' => 'Taylor Otwell',
	        'username' => 'taylorotwell',
	        'admin' => true,
	    ],
	];
	 
	Validator::make($input, [
	    'user' => 'array:username,locale',
	]);
	

#### Validating Nested Array Input

use "dot notation" to validate attributes within an array

	use Illuminate\Support\Facades\Validator;
	 
	$validator = Validator::make($request->all(), [
	    'photos.profile' => 'required|image',
	]);
	
	validate each element of an array. 
	
	$validator = Validator::make($request->all(), [
	    'person.*.email' => 'email|unique:users',
	    'person.*.first_name' => 'required_with:person.*.last_name',
	]);
	
use the * character

	'custom' => [
	    'person.*.email' => [
	        'unique' => 'Each person must have a unique email address',
	    ]
	],
	
##### Accessing Nested Array Data

using the Rule::forEach method

	use App\Rules\HasPermission;
	use Illuminate\Support\Facades\Validator;
	use Illuminate\Validation\Rule;
	 
	$validator = Validator::make($request->all(), [
	    'companies.*.id' => Rule::forEach(function ($value, $attribute) {
	        return [
	            Rule::exists(Company::class, 'id'),
	            new HasPermission('manage-company', $value),
	        ];
	    }),
	]);
	

##### Error Message Indexes & Positions

include the :index and :position
 
	use Illuminate\Support\Facades\Validator;
	 
	$input = [
	    'photos' => [
	        [
	            'name' => 'BeachVacation.jpg',
	            'description' => 'A photo of my beach vacation!',
	        ],
	        [
	            'name' => 'GrandCanyon.jpg',
	            'description' => '',
	        ],
	    ],
	];
	 
	Validator::validate($input, [
	    'photos.*.description' => 'required',
	], [
	    'photos.*.description.required' => 'Please describe photo #:position.',
	]);

#### Validating Files

validate uploaded files, such as mimes, image, min, and max

	use Illuminate\Support\Facades\Validator;
	use Illuminate\Validation\Rules\File;
	 
	Validator::validate($input, [
	    'attachment' => [
	        'required',
	        File::types(['mp3', 'wav'])
	            ->min(1024)
	            ->max(12 * 1024),
	    ],
	]);

the dimensions rule may be used to limit the dimensions of the image:


	use Illuminate\Support\Facades\Validator;
	use Illuminate\Validation\Rules\File;
	 
	Validator::validate($input, [
	    'photo' => [
	        'required',
	        File::image()
	            ->min(1024)
	            ->max(12 * 1024)
	            ->dimensions(Rule::dimensions()->maxWidth(1000)->maxHeight(500)),
	    ],
	]);


##### File Types

validates the MIME type of the file by reading the file's contents and guessing its MIME type

#### Validating Passwords

Laravel's Password rule object:


	use Illuminate\Support\Facades\Validator;
	use Illuminate\Validation\Rules\Password;
	 
	$validator = Validator::make($request->all(), [
	    'password' => ['required', 'confirmed', Password::min(8)],
	]);


The Password rule object allows you to easily customize the password complexity

	// Require at least 8 characters...
	Password::min(8)
	 
	// Require at least one letter...
	Password::min(8)->letters()
	 
	// Require at least one uppercase and one lowercase letter...
	Password::min(8)->mixedCase()
	 
	// Require at least one number...
	Password::min(8)->numbers()
	 
	// Require at least one symbol...
	Password::min(8)->symbols()
	

using the uncompromised method

	Password::min(8)->uncompromised()

customize this threshold using the first argument of the uncompromised method:

	// Ensure the password appears less than 3 times in the same data leak...
	Password::min(8)->uncompromised(3);
	

chain all the methods

	Password::min(8)
	    ->letters()
	    ->mixedCase()
	    ->numbers()
	    ->symbols()
	    ->uncompromised()
	

##### Defining Default Password Rules

 using the Password::defaults method

	use Illuminate\Validation\Rules\Password;
	 
	/**
	 * Bootstrap any application services.
	 *
	 * @return void
	 */
	public function boot()
	{
	    Password::defaults(function () {
	        $rule = Password::min(8);
	 
	        return $this->app->isProduction()
	                    ? $rule->mixedCase()->uncompromised()
	                    : $rule;
	    });
	}

invoke the defaults method with no arguments

	'password' => ['required', Password::defaults()],

use the rules method to accomplish this

	use App\Rules\ZxcvbnRule;
	 
	Password::defaults(function () {
	    $rule = Password::min(8)->rules([new ZxcvbnRule]);
	 
	    // ...
	});
	

#### Custom Validation Rules

##### Using Rule Objects

arahan artisan 

	php artisan make:rule Uppercase --invokable

invoked on failure with the validation error message:

	<?php
	 
	namespace App\Rules;
	 
	use Illuminate\Contracts\Validation\InvokableRule;
	 
	class Uppercase implements InvokableRule
	{
	    /**
	     * Run the validation rule.
	     *
	     * @param  string  $attribute
	     * @param  mixed  $value
	     * @param  \Closure  $fail
	     * @return void
	     */
	    public function __invoke($attribute, $value, $fail)
	    {
	        if (strtoupper($value) !== $value) {
	            $fail('The :attribute must be uppercase.');
	        }
	    }
	}

attach it to a validator by passing an instance of the rule object with your other validation rules:

	use App\Rules\Uppercase;
	 
	$request->validate([
	    'name' => ['required', 'string', new Uppercase],
	]);
	
instruct Laravel to translate the error message

	if (strtoupper($value) !== $value) {
	    $fail('validation.uppercase')->translate();
	}
	
the first and second arguments to the translate method:

	$fail('validation.location')->translate([
	    'value' => $this->value,
	], 'fr')
	

##### Accessing Additional Data

 invoked by Laravel (before validation proceeds) with all of the data under validation:

	<?php
	 
	namespace App\Rules;
	 
	use Illuminate\Contracts\Validation\DataAwareRule;
	use Illuminate\Contracts\Validation\InvokableRule;
	 
	class Uppercase implements DataAwareRule, InvokableRule
	{
	    /**
	     * All of the data under validation.
	     *
	     * @var array
	     */
	    protected $data = [];
	 
	    // ...
	 
	    /**
	     * Set the data under validation.
	     *
	     * @param  array  $data
	     * @return $this
	     */
	    public function setData($data)
	    {
	        $this->data = $data;
	 
	        return $this;
	    }
	}
	
implement the ValidatorAwareRule interface:

	<?php
	 
	namespace App\Rules;
	 
	use Illuminate\Contracts\Validation\InvokableRule;
	use Illuminate\Contracts\Validation\ValidatorAwareRule;
	 
	class Uppercase implements InvokableRule, ValidatorAwareRule
	{
	    /**
	     * The validator instance.
	     *
	     * @var \Illuminate\Validation\Validator
	     */
	    protected $validator;
	 
	    // ...
	 
	    /**
	     * Set the current validator.
	     *
	     * @param  \Illuminate\Validation\Validator  $validator
	     * @return $this
	     */
	    public function setValidator($validator)
	    {
	        $this->validator = $validator;
	 
	        return $this;
	    }
	}

##### Using Closures

The closure receives the attribute's name, the attribute's value, and a $fail callback that should be called if validation fails:

	use Illuminate\Support\Facades\Validator;
	 
	$validator = Validator::make($request->all(), [
	    'title' => [
	        'required',
	        'max:255',
	        function ($attribute, $value, $fail) {
	            if ($value === 'foo') {
	                $fail('The '.$attribute.' is invalid.');
	            }
	        },
	    ],
	]);

##### Implicit Rules

the unique rule will not be run against an empty string:

	use Illuminate\Support\Facades\Validator;
	 
	$rules = ['name' => 'unique:users,name'];
	 
	$input = ['name' => ''];
	 
	Validator::make($input, $rules)->passes(); // true

arahan artisan

	php artisan make:rule Uppercase --invokable --implicit


