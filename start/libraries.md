## Models, Libraries, & Packages

- [Models](#models)
- [Libraries](#libraries)
- [Packages](#packages)

<a name="models"></a>
## Models

Models are the meat of your application, and they live in the **application/models** directory. A model is simply a class. Nothing more. It should contain business logic related to your application. Models may also be [Eloquent models](/docs/database/eloquent), which allow you to work with your database elegantly and beautifully. Models are auto-loaded by Laravel, so there is no need to **require** them before using them.

<a name="libraries"></a>
## Libraries

Libraries can be considered the "helper" classes of your application. Usually, they are classes that do not contain logic specific to your application; however, they are important to its functionality. For example, a Twitter library may be used to fetch your recent tweets and display them on a view. Fetching Tweets from Twitter isn't specific to your application, but it is still important. Place your generic, helper libraries in the **application/libraries** directory. Like models, libraries are also auto-loaded by Laravel.

<a name="packages"></a>
## Packages

- [The Basics](#package-basics)
- [Auto-Loading Packages](#package-auto)
- [Registering Load Paths](#package-register)

<a name="package-basics"></a>
### The Basics

Packages and libraries have many things in common. Often, they provide functionality that is not specific to your application. However, packages are generally not written specifically for Laravel. For example, the wonderful e-mailing package [SwiftMailer](http//swiftmailer.org) is not written just for Laravel, but it can be used in a variety of frameworks, including Laravel. All packages live in the **packages** directory.

Often, it is necessary for packages to be bootstrapped. For example, the package may need to register its own auto-loader. Not a problem. The **package** class provides an easy way to load packages. If a **bootstrap.php** file is present in the package root directory, it will be run.

	Package::load('swift-mailer');

Need to load more than one package? Just pass an array to the **load** method:

	Package::load(array('swift-mailer', 'facebook'));

> **Note:** Package classes are not auto-loaded by Laravel. Each package is responsible for registering its own auto-loader.

<a name="package-auto"></a>
### Auto-Loading Packages

Sometimes you may want to load a package for every request to your application. No problem. Just add the package to the **packages** option in the **application/config/application.php** file:

	'packages' => array('swift-mailer')

Alternatively, you can use the [**needs** keyword on a route](/docs/start/routes#dependencies).

<a name="package-register"></a>
### Registering Load Paths

Have you written a package and want its classes to be auto-loaded by Laravel? Checkout the **register** method on the **Loader** class. All it needs is a directory:

	Loader::register('path/to/package/root');

Once a directory has been registered, the Laravel auto-loader will search the directory for classes in exactly the same way it searches the application **models** and **libraries** directories.