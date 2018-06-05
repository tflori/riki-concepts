# Concept for ríki framework

The framework itself has no fixed directory structure instead it provides only some classes that you otherwise have to
rewrite for every project. These classes should not contain high sophisticated logic to make them configurable but have
to be extendable and therefore reusable.

The framework is only for the application - not for the business models.

## Configuration

The configuration is hold by a configuration class within public properties. This way it is easy to have auto
completion:

```php
<?php
namespace App {
    class Config
    {
        public $title = 'Ríki Community';
        
        /** @var Config\Database */
        public $database;
        
        public $redis = [
            'host' => 'redis',
            'port' => 3769,
            'db' => 0,
        ];
    }
}

namespace App\Config {
    class Database {
        public $dsn;
        public $username;
        public $Password;
    }
}
```

For complex configuration you can use arrays or objects.

> Keep in mind that array declarations inside methods (also the constructor) are not recognized by the most IDEs.

## Environment

To configure the environment (development, testing, production etc.) you use this class. The configuration get's an
instance of this class. It defines the paths of your environment and what is being used (e. g. canCacheConfig or
usesDotEnv).

The environment to use is determined by the environment variable `APP_ENV` and the namespace. You can pass the namespace
to the init method or call the init method on your subclass (somebit slower). When no namespace is passed the namespace
of the subclass is used.

> **Note:** the `APP_ENV` environment variable is not read from the dotEnv (because the environment defines if this file
should be used at all) and the default is `development`. 

## Class Main

This class will hold the main logic for the application. Because we do not force him to use specific libraries it will
be almost empty.

### Bootstrap

The bootstrap method get's called first and prepares the application for `run`. You can also define specific bootstrap
methods that are preparing different setups. For example a method `Main::bootstrap()` and a method 
`Main::bootstrapAdmin()` that is getting executed from `admin.php`.

### Run

The run method executes the application. The most understandable way is to pass the request object to `Main::run()` and
return the response object:

## Example execution of the application

For a http request this could look like this:

```php
<?php

use App\Main;
use App\Http\Request;

# load the composer autoloader
require __DIR__ . '../vendor/autoload.php';

# create the app
$app = new App\Main;

# bootstrap the app
$app->boostrap();

# run the app
$response = $app->run(new Request($_GET, $_POST, $_COOKIE, $_FILES, $_SERVER));

# send the response
$response->send();
```

A command line script could look like this:

```php
<?php

use App\Main;
use GetOpt\Arguments;

# load the composer autoloader
require __DIR__ . '../vendor/autoload.php';

# create the app
$app = new App\Main;

# bootstrap the app
$app->bootstrap();

# run the app (interactive)
$app->runCli(new Arguments(array_slice($_SERVER['argv'])));
```
