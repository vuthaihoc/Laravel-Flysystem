Laravel Flysystem
=================

## Note: This is a custom version based on [GrahamCampbell/Laravel-Flysystem](https://github.com/GrahamCampbell/Laravel-Flysystem)

## What was added 

- You can custom connector. Example when you want to connect minio/seaweedfs(s3 interface).

```php
<?php
/**
 * Minio Connector Class
 * User: hocvt
 * Date: 11/9/18
 * Time: 16:27
 */

namespace App\Colombo\Minio;


use GrahamCampbell\Flysystem\Adapters\AwsS3Connector;

class MinioConnector extends AwsS3Connector {
    
    protected function getAuth( array $config ) {
        
        $auth = parent::getAuth( $config );
        
        if (array_key_exists('use_path_style_endpoint', $config)) {
            $auth['use_path_style_endpoint'] = $config['use_path_style_endpoint'];
        }
        return $auth;
    }
    
}
```

Connection config 

```php
'minio' => [
        'driver'          => \App\Colombo\Minio\MinioConnector::class,
        'key'             => '0NTTRHXQDBWE0N2Y9YEB',
        'secret'          => 'VC7rw7X4nA1ZJgx8SOIoh6fBmQjVl0JFOnk4vkTz',
        'bucket'          => 'local',
        'region'          => '',
        'version'         => 'latest',
        'bucket_endpoint' => false,
        'use_path_style_endpoint' => true,// this will be passed to S3Client
        // 'calculate_md5'   => true,
        // 'scheme'          => 'https',
         'endpoint'        => 'http://127.0.0.1:9000',
//       'prefix'          => '',
        // 'visibility'      => 'public',
        // 'pirate'          => false,
        // 'eventable'       => true,
        // 'cache'           => 'foo'
    ],
```

Laravel Flysystem was created by, and is maintained by [Graham Campbell](https://github.com/GrahamCampbell), and is a [Flysystem](https://github.com/thephpleague/flysystem) bridge for [Laravel 5](http://laravel.com). It utilises my [Laravel Manager](https://github.com/GrahamCampbell/Laravel-Manager) package. Feel free to check out the [change log](CHANGELOG.md), [releases](https://github.com/GrahamCampbell/Laravel-Flysystem/releases), [license](LICENSE), and [contribution guidelines](CONTRIBUTING.md).

![Laravel Flysystem](https://cloud.githubusercontent.com/assets/2829600/4432299/c12eac50-468c-11e4-93c3-5d587a2a56fa.PNG)

<p align="center">
<a href="https://styleci.io/repos/15766264"><img src="https://styleci.io/repos/15766264/shield" alt="StyleCI Status"></img></a>
<a href="https://travis-ci.org/GrahamCampbell/Laravel-Flysystem"><img src="https://img.shields.io/travis/GrahamCampbell/Laravel-Flysystem/master.svg?style=flat-square" alt="Build Status"></img></a>
<a href="https://scrutinizer-ci.com/g/GrahamCampbell/Laravel-Flysystem/code-structure"><img src="https://img.shields.io/scrutinizer/coverage/g/GrahamCampbell/Laravel-Flysystem.svg?style=flat-square" alt="Coverage Status"></img></a>
<a href="https://scrutinizer-ci.com/g/GrahamCampbell/Laravel-Flysystem"><img src="https://img.shields.io/scrutinizer/g/GrahamCampbell/Laravel-Flysystem.svg?style=flat-square" alt="Quality Score"></img></a>
<a href="LICENSE"><img src="https://img.shields.io/badge/license-MIT-brightgreen.svg?style=flat-square" alt="Software License"></img></a>
<a href="https://github.com/GrahamCampbell/Laravel-Flysystem/releases"><img src="https://img.shields.io/github/release/GrahamCampbell/Laravel-Flysystem.svg?style=flat-square" alt="Latest Version"></img></a>
</p>


## Installation

Laravel Flysystem requires [PHP](https://php.net) 7. This particular version supports Laravel 5.1, 5.2, 5.3, 5.4, or 5.5 only.

To get the latest version, simply require the project using [Composer](https://getcomposer.org):

```bash
$ composer require stupiddev/flysystem
```

There are also some additional dependencies you will need to install for some of the features:

* The AwsS3 adapter requires `league/flysystem-aws-s3-v3` (`^1.0`).
* The Azure adapter requires `league/flysystem-azure` (`^1.0`).
* The Dropbox adapter requires `spatie/flysystem-dropbox` (`^1.0`).
* The GridFS adapter requires `league/flysystem-gridfs` (`^1.0`).
* The Rackspace adapter requires `league/flysystem-rackspace` (`^1.0`).
* The Sftp adapter requires `league/flysystem-sftp` (`^1.0`).
* The WebDav adapter requires `league/flysystem-webdav` (`^1.0`).
* The ZipAdapter adapter requires `league/flysystem-ziparchive` (`^1.0`).
* The adapter caching support requires `league/flysystem-cached-adapter` (`^1.0`).
* The eventable filesystem support requires `league/flysystem-eventable-filesystem` (`^1.0`).

Once installed, you need to register the `GrahamCampbell\Flysystem\FlysystemServiceProvider` service provider in your `config/app.php`, or if you're using Laravel 5.5, this can be done via the automatic package discovery.

You can also optionally alias our facade:

```php
        'Flysystem' => GrahamCampbell\Flysystem\Facades\Flysystem::class,
```


## Configuration

Laravel Flysystem requires connection configuration.

To get started, you'll need to publish all vendor assets:

```bash
$ php artisan vendor:publish
```

This will create a `config/flysystem.php` file in your app that you can modify to set your configuration. Also, make sure you check for changes to the original config file in this package between releases.

There are three config options:

##### Default Connection Name

This option (`'default'`) is where you may specify which of the connections below you wish to use as your default connection for all work. Of course, you may use many connections at once using the manager class. The default value for this setting is `'local'`.

##### Flysystem Connections

This option (`'connections'`) is where each of the connections are setup for your application. Examples of configuring each supported driver are included in the [config file](config/flysystem.php#L40-L179), which you should have "published". You can of course have multiple connections per driver.

##### Flysystem Cache

This option (`'cache'`) is where each of the cache configurations setup for your application. There are currently two drivers: illuminate and adapter. Examples of configuration are included. You can of course have multiple connections per driver as shown.


## Usage

##### FlysystemManager

This is the class of most interest. It is bound to the ioc container as `'flysystem'` and can be accessed using the `Facades\Flysystem` facade. This class implements the `ManagerInterface` by extending `AbstractManager`. The interface and abstract class are both part of my [Laravel Manager](https://github.com/GrahamCampbell/Laravel-Manager) package, so you may want to go and checkout the docs for how to use the manager class over at [that repo](https://github.com/GrahamCampbell/Laravel-Manager#usage). Note that the connection class returned will always be an instance of a class that implements `\League\Flysystem\FilesystemInterface` which will be `\League\Flysystem\Filesystem` by default.

##### Facades\Flysystem

This facade will dynamically pass static method calls to the `'flysystem'` object in the ioc container which by default is the `FlysystemManager` class.

##### FlysystemServiceProvider

This class contains no public methods of interest. This class should be added to the providers array in `config/app.php`. This class will setup ioc bindings.

##### Real Examples

Here you can see an example of just how simple this package is to use. Out of the box, the default adapter is `local`, and it will just work straight away:

```php
use GrahamCampbell\Flysystem\Facades\Flysystem;
// you can alias this in config/app.php if you like

Flysystem::put('hi.txt', 'foo');
// we're done here - how easy was that, it just works!

Flysystem::read('hi.txt'); // this will return foo
```

The flysystem manager will behave like it is a `\League\Flysystem\Filesystem` class. If you want to call specific connections, you can do with the `connection` method:

```php
use GrahamCampbell\Flysystem\Facades\Flysystem;

// note the foo connection does not ship with this package, it's hypothetical
Flysystem::connection('foo')->put('test.txt', 'bar');

// now we can read that file
Flysystem::connection('foo')->read('test.txt'); // this will return bar
```

With that in mind, note that:

```php
use GrahamCampbell\Flysystem\Facades\Flysystem;

// writing this:
Flysystem::connection('local')->read('test.txt');

// is identical to writing this:
Flysystem::read('test.txt');

// and is also identical to writing this:
Flysystem::connection()->read('test.txt');

// this is because the local connection is configured to be the default
Flysystem::getDefaultConnection(); // this will return local

// we can change the default connection
Flysystem::setDefaultConnection('foo'); // the default is now foo
```

If you prefer to use dependency injection over facades like me, then you can easily inject the manager like so:

```php
use GrahamCampbell\Flysystem\FlysystemManager;
use Illuminate\Support\Facades\App; // you probably have this aliased already

class Foo
{
    protected $flysystem;

    public function __construct(FlysystemManager $flysystem)
    {
        $this->flysystem = $flysystem;
    }

    public function bar()
    {
        $this->flysystem->read('test.txt');
    }
}

App::make('Foo')->bar();
```

For more information on how to use the `\League\Flysystem\Filesystem` class we are calling behind the scenes here, check out the docs at https://github.com/thephpleague/flysystem#general-usage, and the manager class at https://github.com/GrahamCampbell/Laravel-Manager#usage.

##### Further Information

There are other classes in this package that are not documented here. This is because they are not intended for public use and are used internally by this package.


## Security

If you discover a security vulnerability within this package, please send an e-mail to Graham Campbell at graham@alt-three.com. All security vulnerabilities will be promptly addressed.


## License

Laravel Flysystem is licensed under [The MIT License (MIT)](LICENSE).
