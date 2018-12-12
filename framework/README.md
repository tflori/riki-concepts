# Concept for ríki framework

The framework itself has no fixed directory structure instead it provides only some classes that you otherwise have to
rewrite for every project. These classes should not contain high sophisticated logic to make them configurable but have
to be extendable and therefore reusable.

The framework is only for the application - not for the business models.

## Configuration

The configuration is hold by a configuration class within public properties. This way it is easy to have auto
completion.

For complex configuration you can use arrays or objects.

> Keep in mind that array declarations inside methods (also the constructor) are not recognized by the most IDEs.

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

## Environment

To configure the environment (development, testing, production etc.) you use this class. The configuration get's an
instance of this class. It defines the paths of your environment and what is being used (e. g. canCacheConfig or
usesDotEnv).

The environment to use is determined by the environment variable `APP_ENV` and the namespace. You can pass the namespace
to the init method for a small performance improvement. When no namespace is passed the namespace of the subclass is
used.

> **Note:** the `APP_ENV` environment variable is not read from the dotEnv (because the environment defines if this file
should be used at all) and the default is `development`.

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


## Class Main

This class will hold the main logic for the application. Because it is so difficult what the implementations are doing
we can not provide any logic here. For example a queue worker might start listening on a queue, a website or api might
want to deliver http responses to http requests, a command line interface starts commands based on parameters and so
on.

### Bootstrap

The bootstrap method get's called first and prepares the application for `run`. You can also define specific bootstrap
methods that are preparing different setups. For example a method `Main::bootstrap()` and a method
`Main::bootstrapAdmin()` that is getting executed from `admin.php`.

You could also load the constructor from the constructor but keep in mind that this stuff is then executed regardless of
the target (cli, web, api etc.).

### Run

The run method executes the application. Also this method could be split up for different targets. `Main::runCli()`
could start an interactive console application for example. Or `Main::runWebsocket()` starts a websocket service.

### Example Usage

For a http requests the intialization file (e. g. index.php) could look like this:

```php
<?php

use App\Main;
use App\Http\Request;

// load the composer autoloader
require __DIR__ . '../vendor/autoload.php';

// create the app
$app = new Main;

// bootstrap the app
$app->boostrap();

// run the app
$response = $app->run(Request::fromSuperGlobals());

// send the response
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
$app = new Main;

# bootstrap the app
$app->bootstrap();

# run the app (interactive)
$app->runCli(new Arguments(array_slice($_SERVER['argv'])));
```
