# Concept for ríki framework

The framework itself has no fixed directory structure instead it provides only some classes that you otherwise have to
rewrite for every project. These classes should not contain high sophisticated logic to make them configurable. Instead
all classes are abstract and need to be extended.

The framework is only for the application - not for the business models.

## Configuration

The configuration is hold by a configuration class within public properties. This way it is easy to have auto
completion.

For complex configuration you can use arrays or objects.

> Keep in mind that array declarations inside methods (also the constructor) are not recognized by the
most IDEs.

```php
<?php
namespace App {
    class Config extends \Riki\Config
    {
        public $title = 'Ríki Community';

        /** @var Config\Database */
        public $database;

        public $redis = [
            'host' => 'redis',
            'port' => 3769,
            'db' => 0,
        ];
        
        public function __construct(\Riki\Environment $environment)
        {
            parent::__construct($environment);
            $this->database = new Config\Database;
        }
    }
}

namespace App\Config {
    class Database {
        public $dsn = 'mysql:host=mysql;name=my-app';
        public $username = 'user';
        public $Password = 'password';
    }
}
```

## Environment

To configure the environment (development, testing, production etc.) you use this class. The configuration get's an
instance of this class. It defines the capabilities and restrictions of the environment (e. g. canCacheConfig or
usesDotEnv).

The environment to use is determined by the environment variable `APP_ENV` and the namespace. You can pass the
namespace to the init method for a small performance improvement. When no namespace is passed the namespace of the
subclass is used.

> **Note:** the `APP_ENV` environment variable is not read from the dotEnv (because the environment
defines if this file should be used at all) and the default is `development`.

You have to define a basic environment class that defines all methods that are changed from your environment.

### Example

```php
<?php

namespace App\Environment {
    class Development extends \App\Environment {
        public function showExceptionBacktrace() {
            return true;
        }
    }
    class Production extends \App\Environment {
        public function showExceptionBacktrace() {
            return PHP_SAPI === 'cli';
        }
    }
    class Staging extends Production {
        public function showExceptionBacktrace() {
            return true;
        }
    }
}

namespace App {
    class Environment extends \Riki\Environment {
        public function showExceptionBacktrace() {
            return false;
        }
    }
    $environment = Environment::init('App\\Environment');
}
```


## Class Application

The Application extends the dependency injection container from tflori/dependency-injector. Therefore it is a PSR-11
compatible dependency injector. For convenience the application stores a static variable to the application instance
that allows you to use the static magic `__callStatic` to get your dependencies.

> There are a lot of advices not to pass around the container or access the container from within your
classes. I can't confirm that any of this is a real problem - neither for testing nor for the 
maintainability of your application. 

The application also initializes the dependencies, detects the environment based on the environment variable `APP_ENV`
and loads the configuration when it gets created (in the constructor). The **main** responsibility for this class
is to execute the bootstrapper of a kernel and run it afterwards.

### Example

A `{main}` script could look like this:

```php
<?php

require __DIR__ . '/../vendor/autoload.php';

$app = new \App\Application(realPath(__DIR__ . '/..'));
$kernel = new \App\Http\HttpKernel();
$response = $app->run($kernel);

// .. $response->send() or what ever your kernel returns
```

## Kernel

While the application describes the basics of your application the kernel describes the abstract functionality of 
executing things. For example a `HttpKernel` routes the incoming request to the appropriate controller and might
execute some middleware between and a `QueueKernel` checks in an endless loop if there is a new job to execute and
executes it.

### Bootstrapper

A kernel might define bootstrapper that have to be executed before the run method gets executed. Other than the run
method the bootstrapper gets the application instance. The reason for this is you could want to load something without
executing the kernel but the logic should stay in the kernel.
  
```php
<?php

class HttpKernel extends \Riki\Kernel
{
    protected $routes;
    
    public function __construct()
    {
        $this->addBootstrappers(
            [$this, 'loadRoutes']
        );
    }
    
    public function loadRoutes(\Riki\Application $app)
    {
        // store an array of routes in $this->routes or whatever
    }
    
    public function getRoutes(string $filter = null)
    {
        return array_filter($this->routes, function ($route) use ($filter) {
            return $route->matches($filter);
        });
    }
    
    public function run(?HttpRequest $httpRequest = null)
    {
        // find the controller for the request and execute it
    }
}
```
