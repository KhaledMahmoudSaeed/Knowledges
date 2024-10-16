# Welcome in the best framework in the world dude

## Topics 
- [Why Laravel](#why-laravel)
- [Request Lifecycle](#)
- [Configuration](#configuration)
- [Commands](#commands)
- [Reference](#reference)



### Why Laravel 

1. A Progressive Framework
   1. Laravel grows with you. If you're just taking your first steps into web development, Laravel's vast library of documentation, guides, and video tutorials will help you learn the ropes without becoming overwhelmed. 
   2. If you're a senior developer, Laravel gives you robust tools for dependency injection, unit testing, queues, real-time events
2. A Scalable Framework
   1. Laravel's built-in support for fast, distributed cache systems like Redis, horizontal scaling with Laravel is a breeze. In fact, Laravel applications have been easily scaled to handle hundreds of millions of requests per month.  
   2. Need extreme scaling? Platforms like Laravel Vapor allow you to run your Laravel application at nearly limitless scale on AWS's latest serverless technology.
3. A Community Framework
   1. Laravel combines the best packages in the PHP ecosystem to offer the most robust and developer friendly framework available
   

### Request Lifecycle

1. **First Steps**
   1. The entry point for all requests to a Laravel application is the ```public/index.php``` file ,all requests are directed  to there by web server (Apache / Nginx)
   2. The index.php file loads the Composer generated autoloader definition, and then retrieves an instance of the Laravel application from ```bootstrap/app.php```
2. **HTTP / Console Kernels** (here we will fouce on HTTP Kernal which  HTTP kernel, which is an instance of Illuminate\Foundation\Http\Kernel )
   1.  the incoming request is sent to ```handleRequest```   method from Application instantce 
   2.  The HTTP kernel defines an array of bootstrappers that will be run before the request is executed. These bootstrappers configure error handling, configure logging, detect the application environment, and perform other tasks
   3.  The HTTP kernel is also responsible for passing the request through the application's middleware stack. These middleware handle reading and writing the HTTP session, determining if the application is in maintenance mode, verifying the CSRF token, and more
   4.  The method signature for the HTTP kernel's handle method is quite simple: it receives a Request and returns a Response. 
3. **Service Providers**
   1. Essentially every major feature offered by Laravel is bootstrapped and configured by a service provider, Service providers are responsible for bootstrapping all of the framework's various components, such as the database, queue, validation, and routing components.
   2. Laravel will iterate through this list of providers and instantiate each of them. After instantiating the providers, the register method will be called on all of the providers. Then, once all of the providers have been registered, the boot method will be called on each provider. This is so service providers may depend on every container binding being registered and available by the time their boot method is executed.
4. **Routing**
   1. Once the application has been bootstrapped and all service providers have been registered, the Request will be handed off to the router for dispatching. The router will dispatch the request to a route or controller, as well as run any route specific middleware.
   2. Middleware provide a convenient mechanism for filtering or examining HTTP requests entering your application such as Authentication
   3. If the request passes through all of the matched route's assigned middleware, the route or controller method will be executed and the response returned by the route or controller method will be sent back through the route's chain of middleware.
5. **Finishing Up**
   1. Once the route or controller method returns a response, the response will travel back outward through the route's middleware, giving the application a chance to modify or examine the outgoing response.
   2. Finally, once the response travels back through the middleware, the HTTP kernel's handle method returns the response object to the handleRequest of the application instance, and this method calls the send method on the returned response. The send method sends the response content to the user's web browser

At The end we can summary that in [**Service providers are truly the key to bootstrapping a Laravel application. The application instance is created, the service providers are registered, and the request is handed to the bootstrapped application. It's really that simple!**]


### Configuration
All of the configuration files for the Laravel framework are stored in the config directory

These configuration files allow you to configure things like your database connection information, your mail server information, as well as various other core configuration values such as your application timezone and encryption key.


Laravel's default ```.env``` file contains some common configuration values that may differ based on whether your application is running locally or on a production web server. These values are then read by the configuration files within the config directory using Laravel's ```env``` function.

Any variable in your ```.env``` file can be overridden by external environment variables such as server-level or system-level environment variables.

Your .env file should not be committed to your application's source control, since each developer / server using your application could require a different environment configuration.

However, it is possible to encrypt your environment file using Laravel's built-in environment encryption. Encrypted environment files may be placed in source control safely.

Before loading your application's environment variables, Laravel determines if an ```APP_ENV``` environment variable has been externally provided or if the -```-env``` CLI argument has been specified. If so, Laravel will attempt to load an ```.env.[APP_ENV]``` file if it exists. If it does not exist, the default ```.env``` file will be loaded.


The current application environment is determined via the ```APP_ENV``` variable from your ```.env``` file. You may access this value via the ```environment``` method on the ```App/facade```:
```php
use Illuminate\Support\Facades\App;
 
$environment = App::environment();
```

The length of the key provided should match the key length required by the encryption cipher being used. By default, Laravel will use the AES-256-CBC cipher which requires a 32 character key. You are free to use any cipher supported by Laravel's encrypter by passing the --cipher option when invoking the command.


Accessing Configuration Values : A default value may also be specified and will be returned if the configuration option does not exist:

```php
use Illuminate\Support\Facades\Config;
 
$value = Config::get('app.timezone');
 
$value = config('app.timezone');
 
// Retrieve a default value if the configuration value does not exist...
$value = config('app.timezone', 'Asia/Seoul');
```
To set configuration values at runtime, you may invoke the Config facade's set method or pass an array to the config function
```php
Config::set('app.timezone', 'America/Chicago');
 
config(['app.timezone' => 'America/Chicago']);
```

Once the configuration has been cached, your application's ```.env``` file will not be loaded by the framework during requests or Artisan commands; therefore, the``` env``` function will only return external, system level environment variables.

For this reason, you should ensure you are only calling the ```env``` function from within your application's configuration (config) files. 

For local development, you should set the APP_DEBUG environment variable to true. In your production environment, this value should always be false. If the variable is set to true in production, you risk exposing sensitive configuration values to your application's end users.

**Maintenance Mode**
When your application is in maintenance mode, a custom view will be displayed for all requests into your application. This makes it easy to "disable" your application while it is updating or when you are performing maintenance.

While your application is in maintenance mode, no queued jobs will be handled. The jobs will continue to be handled as normal once the application is out of maintenance mode.

### Directory Structure
**The Routes Directory** 
The web.php file contains routes that Laravel places in the web middleware group, which provides session state, CSRF protection, and cookie encryption. If your application does not offer a stateless, RESTful API then all your routes will most likely be defined in the web.php file.

The console.php file is where you may define all of your closure based console commands. Each closure is bound to a command instance allowing a simple approach to interacting with each command's IO methods. Even though this file does not define HTTP routes, it defines console based entry points (routes) into your application. You may also schedule tasks in the console.php file.

The api.php file contains routes that are intended to be stateless, so requests entering the application through these routes are intended to be authenticated via tokens and will not have access to session state.

**The Storage Directory**
The storage directory contains your logs, compiled Blade templates, file based sessions, file caches, and other files generated by the framework. This directory is segregated into app, framework, and logs directories. The app directory may be used to store any files generated by your application. The framework directory is used to store framework generated files and caches. Finally, the logs directory contains your application's log files.

The storage/app/public directory may be used to store user-generated files, such as profile avatars, that should be publicly accessible. You should create a symbolic link at public/storage which points to this directory. You may create the link using the ```php artisan storage:link``` Artisan command.

**The Http Directory**
The Http directory contains your controllers, middleware, and form requests. Almost all of the logic to handle requests entering your application will be placed in this directory.

**The Job Directory**
 The Jobs directory houses the queueable jobs for your application. Jobs may be queued by your application or run synchronously within the current request lifecycle. Jobs that run synchronously during the current request are sometimes referred to as "commands" since they are an implementation of the command pattern.

**There are more directories like[Listeners - Mail - Models - Notifications - Policies - Providers -Rules ]**


### Route
The router allows you to register routes that respond to any HTTP verb:

```php 
Route::get($uri, $callback);
Route::post($uri, $callback);
Route::put($uri, $callback);
Route::patch($uri, $callback);
Route::delete($uri, $callback);
Route::options($uri, $callback);
```

Sometimes you may need to register a route that responds to multiple HTTP verbs. You may do so using the ```match``` method. Or, you may even register a route that responds to all HTTP verbs using the ```any``` method:
```php
 Route::match(['get', 'post'], '/', function () {
    // ...
});
 
Route::any('/', function () {
    // ...
});
```

When defining multiple routes that share the same URI, routes using the ```get```, ```post```, ```put```, ```patch```, ```delete```, and ```options``` methods should be defined before routes using the ```any```, ```match```, and ```redirect``` methods. This ensures the incoming request is matched with the correct route

**Don't miss** 
```php
<form method="POST" action="/profile">
    @csrf
    ...
</form>
```
 If you are defining a route that redirects to another URI, you may use the ```Route::redirect``` method. This method provides a convenient shortcut so that you do not have to define a full route or controller for performing a simple redirect:
```php
Route::redirect('/here', '/there');
```
By default, ```Route::redirect``` returns a ```302``` status code. You may customize the status code using the optional third parameter:
```php
Route::redirect('/here', '/there', 301);
```
Or, you may use the ```Route::permanentRedirect``` method to return a ```301``` status code:
```php
Route::permanentRedirect('/here', '/there');
```
**When using route parameters in redirect routes, the following parameters are reserved by Laravel and cannot be used: destination and status.**

**View Route**
If your route only needs to return a view, you may use the ```Route::view``` method. Like the ```redirect``` method, this method provides a simple shortcut so that you do not have to define a full route or controller. The ```view``` method accepts a URI as its first argument and a view name as its second argument. In addition, you may provide an array of data to pass to the view as an optional third argument:
```php
Route::view('/welcome', 'welcome');
 
Route::view('/welcome', 'welcome', ['name' => 'Taylor']);
```
**Route paramete**
Sometimes you will need to capture segments of the URI within your route. For example, you may need to capture a user's ID from the URL. You may do so by defining route parameters:
```php
Route::get('/user/{id}', function (string $id) {
    return 'User '.$id;
});
```
You may define as many route parameters as required by your route:
```php
Route::get('/posts/{post}/comments/{comment}', function (string $postId, string $commentId) {
    // ...
});
```
**Route parameters are injected into route callbacks / controllers based on their order - the names of the route callback / controller arguments do not matter.**

Occasionally you may need to specify a route parameter that may not always be present in the URI. You may do so by placing a ```?``` mark after the parameter name. Make sure to give the route's corresponding variable a default value:
```php
Route::get('/user/{name?}', function (?string $name = null) {
    return $name;
});
 
Route::get('/user/{name?}', function (?string $name = 'John') {
    return $name;
});
```

**Regular Expression Constraints**
You may constrain the format of your route parameters using the where method on a route instance. The where method accepts the name of the parameter and a regular expression defining how the parameter should be constrained:
```php
Route::get('/user/{name}', function (string $name) {
    // ...
})->where('name', '[A-Za-z]+');
 
Route::get('/user/{id}', function (string $id) {
    // ...
})->where('id', '[0-9]+');
 
Route::get('/user/{id}/{name}', function (string $id, string $name) {
    // ...
})->where(['id' => '[0-9]+', 'name' => '[a-z]+']);
```
For convenience, some commonly used regular expression patterns have helper methods that allow you to quickly add pattern constraints to your routes:

```php
Route::get('/user/{id}/{name}', function (string $id, string $name) {
    // ...
})->whereNumber('id')->whereAlpha('name');
 
Route::get('/user/{name}', function (string $name) {
    // ...
})->whereAlphaNumeric('name');
 
Route::get('/user/{id}', function (string $id) {
    // ...
})->whereUuid('id');
 
Route::get('/user/{id}', function (string $id) {
    // ...
})->whereUlid('id');
 
Route::get('/category/{category}', function (string $category) {
    // ...
})->whereIn('category', ['movie', 'song', 'painting']);
 
Route::get('/category/{category}', function (string $category) {
    // ...
})->whereIn('category', CategoryEnum::cases());
```



**Global Constraints**
If you would like a route parameter to always be constrained by a given regular expression, you may use the pattern method. You should define these patterns in the boot method of your application's App\Providers\AppServiceProvider class:
```php
use Illuminate\Support\Facades\Route;
 
/**
 * Bootstrap any application services.
 */
public function boot(): void
{
    Route::pattern('id', '[0-9]+');
}
```
Once the pattern has been defined, it is automatically applied to all routes using that parameter name:
```php
Route::get('/user/{id}', function (string $id) {
    // Only executed if {id} is numeric...
});
```





### Middleware
Middleware provide a convenient mechanism for inspecting and filtering HTTP requests entering your application. For example, Laravel includes a middleware that verifies the user of your application is authenticated. If the user is not authenticated, the middleware will redirect the user to your application's login screen. However, if the user is authenticated, the middleware will allow the request to proceed further into the application.

Additional middleware can be written to perform a variety of tasks besides authentication. For example, a logging middleware might log all incoming requests to your application. A variety of middleware are included in Laravel, including middleware for authentication and CSRF protection; however, all user-defined middleware are typically located in your application's app/Http/Middleware directory.
### CSRF Protection
Cross-site request forgeries are a type of malicious exploit whereby unauthorized commands are performed on behalf of an authenticated user. Thankfully, Laravel makes it easy to protect your application from cross-site request forgery (CSRF) attacks.
### Controllers
Instead of defining all of your request handling logic as closures in your route files, you may wish to organize this behavior using "controller" classes. Controllers can group related request handling logic into a single class. For example, a ```UserController``` class might handle all incoming requests related to users, **including** **showing**, **creating**, **updating**, and **deleting** users. By default, controllers are stored in the ```app/Http/Controllers``` directory.

Once you have written a controller class and method, you may define a route to the controller method like so:
```php
use App\Http\Controllers\UserController;
 
Route::get('/user/{id}', [UserController::class, 'show']);
```


**Single Action Controllers**
If a controller action is particularly complex, you might find it convenient to dedicate an entire controller class to that single action. To accomplish this, you may define a single ```__invoke``` method within the controller:
```php
<?php
 
namespace App\Http\Controllers;
 
class ProvisionServer extends Controller
{
    /**
     * Provision a new web server.
     */
    public function __invoke()
    {
        // ...
    }
}
```
When registering routes for single action controllers, you do not need to specify a controller method. Instead, you may simply pass the name of the controller to the router:
```php
use App\Http\Controllers\ProvisionServer;
 
Route::post('/server', ProvisionServer::class);
```

This single route declaration creates multiple routes to handle a variety of actions on the resource. The generated controller will already have methods stubbed for each of these actions. Remember, you can always get a quick overview of your application's routes by running the route:list Artisan command.
```php
use App\Http\Controllers\PhotoController;
 
Route::resource('photos', PhotoController::class);
```

**Actions Handled by Resource Controllers**

GET  => [index - create - show - edit]
POST => [store ]
PUT/PATCH => [update ]
DELETE => [destroy ]

Typically, a 404 HTTP response will be generated if an implicitly bound resource model is not found. However, you may customize this behavior by calling the missing method when defining your resource route. The missing method accepts a closure that will be invoked if an implicitly bound model can not be found for any of the resource's routes:
```php
use App\Http\Controllers\PhotoController;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Redirect;
 
Route::resource('photos', PhotoController::class)
        ->missing(function (Request $request) {
            return Redirect::route('photos.index');
        });
```

### Request
Laravel's Illuminate\Http\Request class provides an object-oriented way to interact with the current HTTP request being handled by your application as well as retrieve the input, cookies, and files that were submitted with the request.

**Methods**
- path()      [returns the request's path information]
- is()        [allows you to verify that the incoming request path matches a given pattern. You may use the * character as a wildcard when utilizing this method:if($request->is('admin/*')) {}]
- routeIs ()  [ you may determine if the incoming request has matched a named route:if ($request->routeIs('admin.*')) {}]
- url()/fullUrl()       [To retrieve the full URL for the incoming request ]
- host()/httpHost()/schemeAndHttpHost()   [retrieve the "host" of the incoming request]
- method()          [return the HTTP verb for the request]
- isMethod ()       [to verify that the HTTP verb matches a given string] 
### Commands
npm run dev         [ to run app for devlopment]
npm run build         [ to run app for production]
- **Configuration**     Laravel can display an overview of your application's configuration, drivers, and environment via the ```about``` Artisan command.
  - php artisan about
  - php artisan about --only=environment
  - php artisan config:show database
  - php artisan env:encrypt   [To encrypt an environment file place the encrypted contents in an ```.env.encrypted``` once you run this command the decrypted key will be generated if you want to provide your own key use next command]
  - php artisan env:encrypt --key=3UVsEgGVK36XN82KKeyLFMhvosbZN1aF    [ to adjust you decrypted key]
  - php artisan env:encrypt --env=staging   [ to specify which env file you want to encrypt .env.staging]
  - php artisan env:decrypt     [Laravel will retrieve from the LARAVEL_ENV_ENCRYPTION_KEY environment variable Or, the key may be provided directly to the command via the --key option] php artisan env:decrypt --key=3UVsEgGVK36XN82KKeyLFMhvosbZN1aF
  - php artisan config:cache  [To give your application a speed boost, you should cache all of your configuration files into a single file .. The command should not be run during local development as configuration options will frequently need to be changed during the course of your application's development.]
  - php artisan config:clear   [to purge the cached configuration]
  - php artisan config:publish    or use php artisan config:publish --all    [to publish any configuration files that are not published by default]
  - php artisan down   [To enable maintenance mode]
  - php artisan up   [To disable maintenance mode]
  - php artisan down --refresh=15  [The Refresh header will instruct the browser to automatically refresh the page after the specified number of seconds]
  - php artisan down --secret="1630542a-246b-4b66-afa1-dd72a4c43515" [To allow maintenance mode to be bypassed using a secret token, and can access it by https://example.com/1630542a-246b-4b66-afa1-dd72a4c43515]
  - php artisan down --with-secret  [if you would like Laravel to generate the secret token for you]
- **Directory**
  - php artisan install:api                [install additional route files]
  - php artisan install:broadcasting       [install additional route files]
  - php artisan storage:link               []
  - php artisan make:command               [to create app/Console]
  - php artisan list make                  [To review the available commands]
  - php artisan make:channel               [to create Broadcasting directory which contains all of the broadcast channel classes for your application. These classes are generated using the]
  - php artisan event:generate and php artisan make:event [to create  Events Directory which may be used to alert other parts of your application that a given action has occurred, providing a great deal of flexibility and decoupling.]
  - php artisan make:exception             [to create Exceptions Directory which contains all of the custom exceptions for your application]
  - php artisan make:job                   [to make Job directory ]
- **Route**
  - php artisan route:list                 [provide an overview of all of the routes that are defined by your application]
  - php artisan route:list -v              [to view middel ware  and if you add more v it will Expand middleware groups]
  - php artisan route:list --path=api      [You may also instruct Laravel to only show routes that begin with a given URI]
- **Controllers**'
  - php artisan make:controller ProvisionServer --invokable     [generate an invokable controller]
  - php artisan make:controller PhotoController --resource      [create a controller that handel CRUD operation]







## Reference
[Laravel Documentaion](https://laravel.com/docs/11.x)