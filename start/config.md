## Basic Configuration

- [Quick Start](#quick)
- [Environments](#environments)
- [Cleaner URLs](#clean)
- [Errors & Logging](#errors)

<a name="quick"></a>
### Quick Start

When starting a new project, you shouldn't be bombarded with loads of confusing configuration decisions. For that reason, Laravel is intelligently configured out of the box. The **application/config/application.php** file contains the basic configuration options for your application.

There is only one option that **must** be set when starting a new application. Laravel needs to know the URL you will use to access your application. Simply set the url in the **application/config/application.php** file:

	'url' => 'http://localhost';

> **Note:** If you are using mod_rewrite, you should set the index option to an empty string.

<a name="environments"></a>
### Environments

Most likely, the configuration options you need for local development are not the same as the options you need on your production server. Laravel provides a convenient way to manage these differences using the **LARAVEL_ENV** environment variable. To get started, set the environment variable in your **httpd.conf** file:

	SetEnv LARAVEL_ENV local

> **Note:** Using a web server other than Apache? No problem. Just follow your server's method of setting environment variables.

Great! Next, create an **application/config/local** directory. Any files and options you place in this directory will override the options in the base **application/config** directory. For example, you may wish to create an **application.php** file within your new **local** configuration directory:

	return array(

		'url' => 'http://localhost/laravel/public',

	);

In this example, the local **URL** option will override the **URL** option in **application/config/application.php**.

If you do not have access to your server's configuration files, you may manually set the **LARAVEL_ENV** variable at the top of the **public/index.php** file:

	$_SERVER['LARAVEL_ENV'] = 'local';

> **Note:**

<a name="clean"></a>
### Cleaner URLs

Most likely, you do not want your application URLs to contain "index.php". You can remove it using HTTP rewrite rules. If you are using Apache to serve your application, make sure to enable mod_rewrite and create a **.htaccess** file like this one in your **public** directory:

	<IfModule mod_rewrite.c>
	     RewriteEngine on

	     RewriteCond %{REQUEST_FILENAME} !-f
	     RewriteCond %{REQUEST_FILENAME} !-d

	     RewriteRule ^(.*)$ index.php/$1 [L]
	</IfModule>

Is the .htaccess file above not working for you? Try this one:

	Options +FollowSymLinks
	RewriteEngine on

	RewriteCond %{REQUEST_FILENAME} !-f
	RewriteCond %{REQUEST_FILENAME} !-d

	RewriteRule . index.php [L]

After setting up HTTP rewriting, you should set the **index** configuration option in **application/config/application.php** to an empty string.

> **Note:** Each web server has a different method of doing HTTP rewrites, and may require a slightly different .htaccess file.

<a name="errors"></a>
### Errors & Logging

- [404 Errors](#error-404)
- [Error Detail](#error-detail)
- [Logging](#error-logging)

<a name="error-404"></a>
#### 404 Errors

When a request is made to your application that cannot be matched to a route, the 404 error view will be sent to the browser. This view lives in **application/views/error/404.php** and you are free to modify it however you wish.

<a name="error-detail"></a>
#### Error Detail

You can easily control the level of error detail via the **detail** option in the **application/config/errors.php** file.

	'detail' => true;

When set to **true**, error messages will be detailed with a stack trace and snippet of the relevant file. When set to **false**, the generic error page (**application/views/error/500.php**) will be displayed. Feel free to modify this view.

> **Note:** In a production environment, it is strongly suggested that you turn off error details.

<a name="error-logging"></a>
#### Logging

You may wish to log any errors that occur in your application. Laravel makes it a breeze. You can turn on logging by setting the log option to **true** in the **application/config/errors.php** file:

	'log' => true;

You have total control over how your errors are logged via the **logger** function defined in **application/config/error.php**. This function is called every time there is an unhandled error or exception in your application. 

As you can see, the default logger implementation writes to the **application/storage/log.txt** file; however, you are free to modify this function however you wish.