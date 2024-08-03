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
### Commands
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








## Reference
[Laravel Documentaion](https://laravel.com/docs/11.x)