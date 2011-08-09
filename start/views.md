## Views & Responses

- [Creating Views](/docs/start/views#create)
- [Binding Data To Views](/docs/start/views#bind)
- [Nesting Views Within Views](/docs/start/views#nest)
- [Named Views](/docs/start/views#named-views)
- [View Composers](/docs/start/views#composers)
- [Managing Assets](/docs/start/views#assets)
- [Redirects](/docs/start/views#redirect)
- [Downloads](/docs/start/views#downloads)
- [Building URLs](/docs/start/views#urls)
- [Building HTML](/docs/start/views#html)
- [Pagination](/docs/start/views#pagination)
- [Errors](/docs/start/views#errors)

<a name="create"></a>
## Creating Views

Typically, a route function returns a **View**. Views consist of plain ole' HTML and are stored in your **application/views** directory.

A very simple view file could look like this:

	<html>
		Welcome to my website!
	</html>

Assuming this view lives in **application/views/simple.php**, we could return it from a route like so:

	'GET /home' => function()
	{
	     return View::make('simple');
	}

When building a large application, you may want to organize your views into sub-directories. That's a great idea! Assuming a view lives in **application/views/user/login.php**, we could return it like so:

	'GET /home' => function()
	{
	     return View::make('user/login');
	}

It's that simple. Of course, you are not required to return a View. Strings are also welcome:

	'GET /home' => function()
	{
	     return json_encode('This is my response!');
	}

<a name="bind"></a>
## Binding Data To Views

You can pass data to a view by "binding" the data to a variable. This is done using the **bind** method on the View:

	'GET /home' => function()
	{
	     return View::make('simple')->bind('email', 'example@gmail.com');
	}

In the example above, the first parameter is the **name** of the view variable. The second parameter is the **value** that will be assigned to the variable.

Now, in our view, we can access the e-mail address like so:

	<html>
	     <?php echo $email; ?>
	</html>

Of course, we can bind as many variables as we wish:

	'GET /home' => function()
	{
	     return View::make('simple')
	                      ->bind('name', 'Taylor')
	                      ->bind('email', 'example@gmail.com');
	}

You may also bind view data by simply setting properties on a view instance:

	'GET /home' => function()
	{
	     $view = View::make('user/login');

	     $view->name = 'Taylor';
	     $view->email = 'example@gmail.com';

	     return $view;
	}

<a name="nest"></a>
## Nesting Views Within Views

Want to nest views inside of views? There are two ways to do it, and they are both easy. First, you can bind the view to a variable:

	'GET /home' => function()
	{
	     $view = View::make('user/login');

	     $view->content = View::make('partials/content');
	     $view->footer = View::make('partials/footer');

	     return $view;
	}

Nested calls to **View::make** can get a little ugly. For that reason, Laravel provides a simple **partial** method:

	View::make('layout/default')->partial('content', 'partials/home');

The **partial** method is very similar to the **bind** method; however, you simply pass a view name in the second parameter to the method. The view will created and bound to the variable for you.

Need to bind data to a partial? No problem. Pass the data in the third parameter to the method:

	View::make('layout/default')
					->partial('content', 'partials/home', array('name' => 'Taylor'));

In some situations, you may need to get the string content of a view from within another view. It's easy using the **get** method:

	<html>
		<?php echo View::make('content')->get(); ?>
		<?php echo View::make('footer')->get(); ?>
	</html>

<a name="named-views"></a>
## Named Views

Named views make your code more expressive and beautiful. Using them is simple. All of your named views are defined in the **application/composers.php** file. By default, a name has been defined for the **home/index** view:

	'home.index' => array('name' => 'home', function($view)
	{
		//
	})

Notice the **name** value in the array? This defines the "home" named view as being associated with the **application/views/home/index.php** file. Now, you can use the **View::of** dynamic method to create named view instances using simple, expressive syntax:

	return View::of_home();

Of course, you may pass bindings into the **of** method:

	return View::of_home(array('email' => 'example@gmail.com'));

Since the **of** method returns an instance of the **View** class, you may use any of the View class methods:

	return View::of_home()->bind('email', $email);

Using named views makes templating a breeze:

	return View::of_layout()->bind('content', $content);

<a name="composers"></a>
## View Composers

View composers will free you from repetitive, brittle code, and help keep your application beautiful and maintainable. All view composers are defined in the **application/composers.php** file. Each time a view is created, its composer will be called. The composer can bind data to the view, register its assets, or even gather common data needed for the view. When the composer is finished working with the view, it should return the view instance. Here's an example composer:

	return array(

			'layouts/default' => function($view)
			{
				Asset::add('jquery', 'js/jquery.js');
				Asset::add('jquery-ui', 'js/jquery-ui.js', 'jquery');

				$view->partial('header', 'partials/header');
				$view->partial('footer', 'partials/footer');

				return $view;
			}

	);

Great! We have defined a composer for the **layouts/default** view. Now, every time the view is created, this composer will be called. As you can see, the composer is registering some common assets, as well as binding partial views to the layout. Of course, we can create an instance of the view using the same syntax we're used to:

	return View::make('layouts/default');

There is no need to specify you want to use the composer. It will be called automatically when the view is created. The composer helps keep your code clean by allowing you to declare assets and common partials for views in a single location. Your code will be cleaner than ever. Enjoy the elegance.

<a name="assets"></a>
## Managing Assets

The **Asset** class provides a simple, elegant way to manage the CSS and JavaScript used by your application. Registering an asset is simple. Just call the **add** method on the **Asset** class:

	Asset::add('jquery', 'js/jquery.js');

Wonderful. The **add** method accepts three parameters. The first is the name of the asset, the second is the path to the asset relative to the **public** directory, and the third is a list of asset dependencies (more on that later). Notice that we did not tell the method if we were registering JavaScript or CSS. The **add** method will use the file extension to determine the type of file we are registering.

When you are ready to place the links to the registered assets on your view, you may use the **styles** or **scripts** methods:

	<head>
		<?php echo Asset::styles(); ?>
		<?php echo Asset::scripts(); ?>
	</head>

Sometimes you may need to specify that an asset has dependencies. This means that the asset requires other assets to be declared in your view before it can be declared. Managing asset dependencies couldn't be easier in Laravel. Remember the "names" you gave to your assets? You can pass them as the third parameter to the **add** method to declare dependencies:

	Asset::add('jquery-ui', 'js/jquery-ui.js', 'jquery');

Great! In this example, we are registering the **jquery-ui** asset, as well as specifying that it is dependent on the **jquery** asset. Now, when you place the asset links on your views, the jQuery asset will always be declared before the jQuery UI asset. Need to declare more than one dependency? No problem:

	Asset::add('jquery-ui', 'js/jquery-ui.js', array('first', 'second'));

To increase response time, it is common to place JavaScript at the bottom of HTML documents. But, what if you also need to place some assets in the head of your document? No problem. The asset class provides a simple way to manage asset **containers**. Simple call the **container** method on the Asset class and mention the container name. Once you have a container instance, you are free to add any assets you wish to the container using the same syntax you are used to:

	Asset::container('footer')->add('example', 'js/example.js');

Getting the links to your container assets is just as simple:

	echo Asset::container('footer')->scripts();

<a name="redirect"></a>
## Redirects

### Redirect Using URIs

You will often need to redirect the browser to another location within your application. The **Redirect** class makes this a piece of cake. Here's how to do it:

	'GET /redirect' => function()
	{
	     return Redirect::to('user/profile');
	}

Of course, you can also redirect to the root of your application:

	return Redirect::to('/');

Need to redirect to a URI that should be accessed over HTTPS? Check out the **to_secure** method:

	return Redirect::to_secure('user/profile');

You can even redirect the browser to a location outside of your application:

	return Redirect::to('http://www.google.com/');

<a name="named"></a>
### Redirect Using Named Routes

So far we've created redirects by specifying a URI to redirect to. But, what if the URI to the user's profile changes? It would be better to create a redirect based on the [route name](/docs/start/routes#named). Let's learn how to do it. Assuming the user profile route is named **profile**, you can redirect to the route using a dynamic, static method call like this:

	return Redirect::to_profile();

Need to redirect to a route that should be accessed over HTTPS? No problem:

	return Redirect::to_secure_profile();

That's great! But, what if the route URI looks like this:

	'GET /user/profile/(:any)/(:any)' => array('name' => 'profile', 'do' => function()
	{
	     //
	})

You need to redirect to the named route, but you also need to replace the **(:any)** place-holders in the URI with actual values. It sounds complicated, but Laravel makes it a breeze. Just pass the parameters to the method in an array:

	return Redirect::to_profile(array('taylor', 'otwell'));

The statement above will redirect the user to the following URL:

	http://example.com/index.php/user/profile/taylor/otwell

<a name="with"></a>
### Redirect With Flash Data

After a user creates an account or signs into your application, it is common to display a welcome or status message. But, how can you set the status message so it is available for the next request? The **Response** and **Redirect** classes make it simple using the **with** method. This method will add a value to the Session flash data, which will be available for the next request:

	return Redirect::to('user/profile')->with('status', 'Welcome Back!');

> **Note:** For more information regarding Sessions and flash data, check out the [Session documentation](/docs/session/config).

<a name="downloads"></a>
## Downloads

Perhaps you just want to force the web browser to download a file? Check out the **download** method on the **File** class:

	'GET /file' => function()
	{
	     return File::download('path/to/your/file.jpg');
	}

In the example above, the image will be downloaded as "file.jpg", however, you can easily specify a different filename for the download in the second parameter to the method:

	'GET /file' => function()
	{
	     return File::download('path/to/your/file.jpg', 'photo.jpg');
	}

<a name="urls"></a>
## Building URLs

- [Generating URLs Using URIs](#uri-urls)
- [Generating URLs Using Named Routes](#named-urls)
- [URL Slugs](#url-slugs)

While developing your application, you will probably need to generate a large number of URLs. Hard-coding your URLs can cause headaches if you switch domains or application locations. Nobody wants to dig through every view in their application to change URLs. Thankfully, the **URL** class provides some simple methods that allow you to easily generate URLs for your application.

<a name="uri-urls"></a>
### Generating URLs Using URIs

To generate a URL for your application, use the **to** method on the URL class:

	echo URL::to();

The statement above will simply return the URL specified in your **application/config/application.php** file.

However, this method can also append a specified string to the end of your application URL:

	echo URL::to('user/profile');

Now the statement will generate a URL something like this:

	http://example.com/index.php/user/login

Need to generate a HTTPS URL? No problem. Check out the **to\_secure** method:

	echo URL::to_secure('user/profile');

Often, you will need to generate URLs to assets such as images, scripts, or styles. You don't want "index.php" inserted into these URLs. So, use the **to_asset** method:

	echo URL::to_asset('img/picture.jpg');

<a name="named-urls"></a>
### Generating URLs To Named Routes

Alright, you have learned how to generate URLs from URIs, but what about from named routes? You can do it by making a dynamic, static method call to the URL class. The syntax is beautiful:

	echo URL::to_profile();

Have a route that needs to be accessed over HTTPS? No problem:

	echo URL::to_secure_profile();

Could it get any easier? Now, imagine a route that is defined like this:

	'GET /user/profile/(:any)/(:any)' => array('name' => 'profile', 'do' => function() 
	{
	     //
	})

To generate a URL for the route, you need to replace the **(:any)** place-holders with actual values. It's easy. Just pass the parameters to the method in an array:

	echo URL::to_profile(array('taylor', 'otwell'));

The method above will generate the following URL:

	http://example.com/index.php/user/profile/taylor/otwell

If you don't want to use dynamic methods, you can simply call the **to_route** method:

	echo URL::to_route('profile', array('taylor', 'otwell'));

<a name="url-slugs"></a>
### URL Slugs

When writing an application like a blog, it is often necessary to generate URL "slugs". Slugs are URL friendly strings of text that represent something like a blog post title. Generating slugs is a piece of cake using the **slug** method:

	echo URL::slug('My blog post title!');

The statement above will return the following string:

	my-blog-post-title

Want to use a different separator character? Just mention it to the method:

	echo URL::slug('My blog post title!', '_');

<a name="html"></a>
## Building HTML

- [Entities](#html-entities)
- [JavaScript & Style Sheets](#html-styles)
- [Links](#html-links)
- [Links To Named Routes](#html-route-links)
- [Mail-To Links](#html-mailto)
- [Images](#html-images)
- [Lists](#html-lists)

Need some convenient methods that make writing HTML a little less painful? Introducing the **HTML** class. Enjoy.

<a name="html-entities"></a>
### Entities

When displaying user input in your Views, it is important to convert all characters which have signifance in HTML to their "entity" representation. 

For example, the < symbol should be converted to its entity representation. Converting HTML characters to their entity representation helps protect your application from cross-site scripting. Thankfully, using the **entities** method on the HTML class, it's easy to add this layer of protection to your Views:

	echo HTML::entities('<script>alert('hi');</script>');

<a name="html-styles"></a>
### JavaScript & Style Sheets

What trendy web application doesn't use JavaScript? Generating a reference to a JavaScript file is as simple using the **script** method.

	echo HTML::script('js/scrollTo.js');

Referencing cascading style sheets is just as simple. Check out the **style method**:

	echo HTML::style('css/common.css');

Need to specify a media type on your style sheet? No problem. Just pass it in the second parameter to the method:

	echo HTML::style('css/common.css', 'print');

> **Note:** All scripts and stylesheets should live in the **public** directory of your application.

<a name="html-links"></a>
### Links

Generating links couldn't be easier. Just mention the URL and title to the **link** method:

	echo HTML::link('user/profile', 'User Profile');

Need to generate a link to a URL that should be accessed over HTTPS? Use the **secure_link** method:

	echo HTML::secure_link('user/profile', 'User Profile');

Of course, you may generate links to locations outside of your application:

	echo HTML::link('http://www.google.com', 'Search the Intrawebs!');

Any other attributes that should be applied to the link may be passed in the third parameter to the method:

	echo HTML::link('http://www.google.com', 'Search the Intrawebs!', array('id' => 'google'));

<a name="html-route-links"></a>
### Links To Named Routes

If you are using [named routes](#routes-named), you use intuitive, expressive syntax to create links to those routes via dynamic methods:

	echo HTML::link_to_login('Login');

Or, if you need an HTTPS link:

	echo HTML::link_to_secure_login('Login');

Now let's assume the **login** route is defined like this:

	'GET /login/(:any)' => array('name' => 'login', 'do' => function() {})

To generate a link to the route, you need to replace the **(:any)** place-holder with an actual value. It's easy. Just pass the parameters to the method in an array:

	echo HTML::link_to_login(array('user'));

This statement will generate a link to the following URL:

	http://example.com/login/user

<a name="html-mailto"></a>
### Mail-To Links

It's great to allow your users to get in touch with you, but you don't want to be bombarded by spam. Laravel doesn't want you to be either. The **mailto** method allows you to create a safe mail-to link that obfuscates your e-mail address:

	echo HTML::mailto('example@gmail.com');

Want the link text to be something besides your e-mail address? No problem. Just mention it to the method:

	echo HTML::mailto('example@gmail.com', 'E-Mail Me!');

Need to obfuscate an e-mail address but don't want to generate an entire mail-to link? Simply pass the e-mail address to the **email** method:

	echo HTML::email('example@gmail.com');

<a name="html-images"></a>
### Images

Since you're building a creative application, you will need to include some images. The **image** method makes simple. Just pass the URL and Alt text to the method:

	echo HTML::image('img/smile.jpg', 'A Smiling Face');

Like the link method, any other attributes that should be applied to the image may be passed in the third parameter to the method:

	echo HTML::image('img/smile.jpg', 'A Smiling Face', array('id' => 'smile'));

<a name="html-lists"></a>
### Lists

Generating HTML lists doesn't have to be a headache. Check out the **ol** method. All it needs is an array of items:

	echo HTML::ol(array('Get Peanut Butter', 'Get Chocolate', 'Feast'));

As usual, you may specify any other attributes that should be applied to the list:

	echo HTML::ol(array('Get Peanut Butter', 'Get Chocolate', 'Feast'), array('class' => 'awesome'));

Need an un-ordered list? No problem. Just use the **ul** method:

	echo HTML::ul(array('Ubuntu', 'Snow Leopard', 'Windows'));

<a name="pagination"></a>
## Pagination

Just looking at pagination libraries probably makes you cringe. It doesn't have to. Laravel makes pagination enjoyable. Really.

> **Note:** Before learning about pagination, you should probably read up on [Eloquent models](/docs/database/eloquent).

To learn about pagination, we'll use an "User" Eloquent model. First, let's define a **per_page** static property on our model. This will tell Laravel how many users to show per page when paginating lists of users:

	class User extends Eloquent {

		public static $per_page = 10;

	}

Great! Now you're ready to get paginated results from the database. It couldn't be any easier. Just use the **paginate** method:

	$users = User::where('posts', '>', 100)->paginate();

The **paginate** method will return an instance of the **Paginator** class. Notice you didn't have to specify the current page or the total amount of users. Laravel makes your life easier by figuring that stuff out for you. The results of your query can be accessed via the **results** property on the Paginator instance:

	$results = $users->results;

Need to paginate results using the [Fluent query builder](/docs/database/query)? Simply pass the number of items to show per page into the **paginate** method:

	$users = DB::table('users')->where('votes', '>' 100)->paginate(10);

You can even specify which columns to retrieve. Just pass an array of column names as the second parameter to the method:

	$users = DB::table('users')
						->where('votes', '>' 100)
						->paginate(10, array('id', 'email'));

Alright, now you are ready to display the results on a View:

	<?php foreach ($users->results as $user): ?>
		<p><?php echo $user->name; ?></p>
	<?php endforeach; ?>

Ready to create the pagination links? Simply call the **links** method on the Paginator instance:

	echo $users->links();

The links method will create an intelligent, sliding list of page links that looks something like this:

	Previous 1 2 ... 24 25 26 27 28 29 30 ... 78 79 Next

Feeling more minimal? You can create simple "Previous" and "Next" links using the **previous** and **next** methods on the Paginator instance:

	<?php echo $users->previous().' | '.$users->next(); ?>

Need to style your links? No problem. All pagination link elements can be style using CSS classes. Here is an example of the HTML elements generated by the **links** method:

	<div class="pagination">
		<a href="foo" class="prev_page">Previous</a>

		<a href="foo">1</a>
		<a href="foo">2</a>

		<span class="dots">...</span>

		<a href="foo">11</a>
		<a href="foo">12</a>

		<span class="current">13</span>

		<a href="foo">14</a>
		<a href="foo">15</a>

		<span class="dots">...</span>

		<a href="foo">25</a>
		<a href="foo">26</a>

		<a href="foo" class="next_page">Next</a>
	</div>

When you are on the first page of results, the "Previous" link will be disabled. Likewise, the "Next" link will be disabled when you are on the last page of results. The generated HTML will look like this:

	<span class="disabled prev_page">Previous</span>

<a name="errors"></a>
## Errors

Sometimes you may need to return an error response, such as the **404** or **500** views. It can be done like this:

	return Response::make(View::make('error/404'), 404);

But, that's a little cumbersome. Instead, use the **error** method on the **Response** class. Just mention the error you want to return:

	return Response::error('404');

> **Note:** The error passed the **error** method must have a corresponding view in the **application/views/error** directory.