## Modules

- [The Basics](#basics)
- [Setting Up A Module](#setup)
- [Module Views](#views)
- [Module Models & Libraries](#libraries)
- [Module Configuration & Language](#config)

<a name="basics"></a>
### The Basics

Modules are an amazing way to organize your application. All modules live in the **modules** directory. Picture each module as a sort of sub-application living within your main Laravel application. A module can have its own libraries, models, routes, views, composers, filters, language files, and configuration. Ready to learn how to use them? Let's jump in.

<a name="setup"></a>
### Setting Up A Module

To setup a module, create a directory in the **modules** directory. For example, you might create an **admin** directory under the **modules** directory. Great! The **admin** module will respond to all requests to your application beginning with **/admin**. Sound familiar? That's because it is similar to [using a route folder](/docs/start/routes#organize).

So, within your **modules/admin** directory, create a **routes.php** file with the following route:

	'GET /admin' => function()
	{
		return 'Hello Admins!';
	}

Next, in your **application/config/application.php** file, add **admin** to the array of active modules:

	'modules' => array('admin')

Now browse to this URL in your web browser. See "Hello Admins!"? Wonderful. You're learning fast.

> **Note:** Module route filters work just like application route filters. Just create a **filters.php** file within your module directory.

<a name="views"></a>
### Module Views

To create a view that will be used by your module, simple create a **views** directory within your module directory. Once you have created a view, you can create it like this:

	$view = View::make('module::view.name');

Notice the module qualifier followed by two colons? This tells Laravel which module the view belongs to. Of course, if the view is in the **application/views** directory, you do need to specify the module name. So, for example, if this view belongs to your **admin** module, you could create it like so:

	$view = View::make('admin::view.name');

> **Note:** Module composers work just like application composers. Just make a **composers.php** file within your module directory.

<a name="libraries"></a>
### Module Models & Libraries

Using models and libraries within modules is very similar to using them within your application. However, module models and libraries must be namespaced using the module name. So, if you have a User model within your **admin** model, it should be namespaced like so:

	namespace Admin;

	class User {}

Once you have created the model, you may use it anywhere within your application:

	$user = new Admin\User;

<a name="config"></a>
### Module Configuration and Language

You can create module configuration and language files just like you create their application counterparts. To load them, using double-colon module qualifier, just like you load module views:

	Config::get('module::option.name');

	Lang::line('module::language.line')->get();