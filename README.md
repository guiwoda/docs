## <a id="toc"></a>Table of Contents

- [About Aidkit +](#about)
- [Installation +](#installation)
- [Structure +](#structure)
- [Configuration & Navigation +](#config)
- [Views +](#views)
- [Controllers +](#controllers)
- [Models +](#models)
- [Migration +](#migration)
- [Actionlog +](#actionlog)
- [Roadmap +](#roadmap)

## <a id="about"></a>About Aidkit

As a Webdeveloper I come across doing similar tasks again and again. One thing is creating **Custom-Backend-Solutions** and I was never a friend of using a CMS like wordpress far away from its original purpose. This would always create problems in the longterm. ( Ugly Codebase, Update Issues, Function Overload, ... )
So I was looking for a clean solution giving me much much power, a speeded-up development process and a clean and userfriendly ( and developerfreindly ) Interface. In Laravel and Composer I found the best tools available and hopefully *Aidkit* can be a another valuable tool for you in the process of creating powerfull webapps.

### Features
- Easy Installation ( Artisan Support )
- Clean Codebase ( Everything can be changed )
- Clean Structure ( Controllers and Views in theire own Folders )
- Easy Configuration ( Define the navigation and some values through config files )
- Usermanagment ( Migrations and Model comes out of the Box)
- Actionlog ( Intelligent Actionlog of creating/updating/deleting of any Model ( even thos created by you ))
- Clean Form Validation ( Define it in the model, *Aidkit* does the rest )
- Clean UI ( uses [PureCSS](http://purecss.io/), [FontAwesome](http://fortawesome.github.io/Font-Awesome/cheatsheet/), [jQuery](http://jquery.com))

### Note:

*
Be aware that this project is still in a very early state of development, and I have a lot of things planned for upcoming releases. I recommend to test aidkit on a new laravel installation. Feel freen to use it for any kind of project ( of course at your own risk ).
I would appreciate any feedback sent to  ( mail [ at ] codebryo [ dot ] com ). Otherwise if you have any tips, issues feel free to use the Github Issues.+

This guide should help you get started with *Aidkit*. If you run into any problems let me know. I will keep this documentatnion updated along with the project. New Parts will be marked with `+` until the next major update.

Commands that should be entered on the command line will get prefixed by this Command Line Icon <i class="icon-terminal"></i>
*

---

## <a id="installation"></a>Installation 

To use *Aidkit* in your next Laravel Project add following line to the `required` section in your composer.json file:

	"codebryo/aidkit": "dev-master"

To make the package available in your vendor folder call <i class="icon-terminal"></i> `composer update` from your commandline.
What's left to do now is adding the Service Provider to `app/config/app.php`

	'Codebryo\Aidkit\AidkitServiceProvider'

and also add *Aidkit* to the aliases

	'Aidkit'		  => 'Codebryo\Aidkit\Support\Facades\Aidkit'
	
and finally add following to the `autoload` array in your composer.json

	"autoload": {
		"classmap": [
			...,
			"app/Aidkit/"
		]
	}

After that you can take a look at the command line. Calling <i class="icon-terminal"></i> `php artisan` will reveal a new command available for you.
Now you can call 

<i class="icon-terminal"></i> `php artisan aidkit:install` 

and aidkit will create necessary Folders and Files, publish Config and Asset Files and will refresh the autoload File.

**Note:** Want to get started right away ? Please read about [Models](#models) and  the [Migrations](#migrations) first.

---

## <a id="structure"></a>Structure

After installing *Aidkit* you have a new folder in your `/app` directory. In there you'll find all the important folders and files for controllers, models and so on.

You'll also find a `routes` folder where all the different route files for the basic *Aidkit* and all of it's extensions will be stored.

You can also find the published config and asset Files in the correct `packages/codebryo/aidkit` folders.

Thats about it what *Aidkit* will create out-of-the-box.

---

## <a id="config"></a>Configuration + Navigation

Taking a look at `app/config/packages/codebryo/aidkit/config.php` you'll find some changeable Values when working with *Aidkit*.
You can set a title for your backend and define which layout files shall be used. **Note:** *Those layout files are relative to the `app/Aidkit/views` folder*.

The most important value so far is

	// What prefix shall be used for all the Generated urls
	'urlprefix' 	=> 'admin'

All your routes will be prefixed by this value by default. So if you create any route be sure they start with this value. 

	// Example 1: Create a url to the root of your backend.
	url('admin');

	// Example 2: Create a url to the login route defined in the routes_admin.php
	url('admin/login');

Now let's check out what can be found in `app/config/packages/codebryo/aidkit/navigation.php`

	return array(

		'navigation' => array(

			'dashboard' => array(
				'icon' 	=> 'icon-dashboard',
				'url' => route('dashboard'),
			),
			'users' => array(
				'icon' 	=> 'icon-user',
				'url'	=> route('admin.users.index'),
				
				'sub-menu' => array(
					'create' => array(
						'title' => 'New User',
						'url' 	=> route('admin.users.create')
					)	
				)
				
			),
			
		)

	);

Thats the way of providing a sitewide available navigation of your backend. At the moment you have a limited option of array keys, but they should be enough for creating a standard navigation. Every first-level key will be used as an anchor. In the array you can defince certain options for this anchor.

	Available Options:

	'icon' => '...' is to add icons available in (http://fortawesome.github.io/Font-Awesome/cheatsheet/)[FontAwesome]

	'url' => '...' provide a available url using url() or route() that this anchor sould point to.

	'title' => '...' By default the main key anchor will be used as name for the anchor. This can be overwritten by adding a title.

	'sub-menu' => array(...) This will create a correctly nested list as subnavigation.

**Note:** *The correctly formated navigation is available through `HTML::aidkitNavigation(...)`. The `<li>` containing the current Link will have a `class="on"`attached.*

---

## <a id="routing"></a>Routing

All your routes used in the backend can be stored in `app/Aidkit/routes.php`. This File is included through *Aidkit* by default and all Routes are prefixed by a value defined in the config file ( by default this is 'admin'). So all your routes pointing to ressources or urls shoud have this prefix.

The idea behind this is to provide a clean seperation of *Frontend* and *Backend* routes. You will also realise there are two groups included in your administrative routes as well. Those groups use the `adminauth` ( a copy of *auth* filter but it redirects you to the login route in the same file ) and `csrf` filters that laravel provides for us.

Happy Routing....

---

## <a id="views"></a>Views

In the process of using views *Aidkit* provides a clean structure for your administrative views as well. All views that are prefixed with `aidkit::` will be used relative to `app/Aidkit/views`. *Aidkit* provides a bunch of views ( layout, navigation, userressource ) from the beginning. By imitating this structure you will be able to use your own views in the backend in notime.

**Example:** load the view file located at `app/Aidkit/views/dashboard.blade.php`

	View::make('aidkit::dashboard')

**Example:** embed a view in the default template

	$this->layout->content = View::make('aidkit::dashboard')

---

## <a id="controllers"></a>Controllers

*Aidkit* also provides a nice place for storing your backend Controllers at `app/Aidkit/controllers`. You may have noticed this folder isn't empty. Some Controllers will be shipped along this package when using *Aidkit*. Let's take a look at the HomeController `app/Aidkit/controllers/AidkitHomeController.php`.

This Controller will look something like this:

	class AidkitHomeController extends AidkitController {

		public function showDashboard()
		{
			$this->layout->content = View::make('admin::dashboard');
		}
	
	}

Here you'll find some important points to notice.

1. The Classname of the Controller should always containt *Aidkit* as first part of the name.
2. This Controller extends a new BaseController called AidkitController.

Well that's it for providing special Backend Controllers. You won't have to hassle around with special Namespaces and run into issues of importing more and more Classes from different Namespaces. Let's take a look at the AidkitController you are going to extend.

### AidkitController

Long story short, this Controller doesn't do much at the Moment but as *Aidkit* will evolve, this Controller will do as well.

For now it offers the possibility of extending the default layout for providing your content. The default layout can be found in the `app/Aidkit/views/layouts/master.blade.php` file and is set in the *Aidkit* configuration.

---

## <a id="models"></a>Models

Big part in any Webapplication are Models. As it often makes sense to use the same Model throught different areas of your application they should all still be stored in the `app/Aidkit/models` folder. But of course *Aidkit* has something in store to make working with models a little more fun.

### AidkitModel

*Aidkit* provides a **AidkitModel** for you to extend. Don't be afraid of loosing anything. The **AidkitModel** still extends **Eloquent** so you have the most awesome ORM-System evaaaar at your fingertips. But be aware that you should use the **Migrations** provided by *Aidkit* to make a good use of it. What's cool about the **AidkitModel** is:

1. Automatic Form Validation on any create/update by providing a ruleset in your model
2. Store actions like creating/updateing/deleting to a [Actionlog](#actionlog) to keep track of who did what and when

### Validation

Let's take a look at the Formvalidation. I am not sure if this is a common practice for the majority of you but I found it to be the simplest and most efficient way of validating forms. I store a *static Varibale* in my Model which contains an array with the necessary rules. And as I like my rules to work on creating and updating without caring about those rules to much I improoved it a little. Here's an example:

Let's start working on the allready existent *Medic* Model available at `app/Aidkit/models/Medic.php`.

Medics are the Users working with Aidkit.

It allready includes some important definitions. The Comments in the Files should help you understand what each of them does.

You see the class *Medic* extends **AidkitModel** instead of **Eloquent**. Also *Aidkit* takes advantage of soft-deletes. And to to start working with this Model right away it allready has defined `$guarded` with an empty array.
Now to take use of easy form validation we only have to define a set of rules:

	public static $rules = array(
		'username' 	=> 'required|alpha_num|unique:medics,username,@id',
		'name'		=> 'required',
		'email'		=> 'required|email|unique:medics,email,@id',
		'password'	=> 'required'
	);

You may have noticed that theres a `@id` included in the rules. That's a benefit of using the **AidkitModel** for validation. When updating a user-record this @id will be replaced by the id of the corresponding record. So this wildcard will give you the option to require for example a unique username but will take care during the update process that the username won't be validated as taken if it's the same record.
Hope that makes sense to you, it cost me some time to figure it out. **Note:** *Let me know if I am totally stupid*

Let's see an example of creating a record and take power of the validation:

The `store()` function in `app/Aidkit/controllers/AidkitMedicsController.php` will gather all inputs, and store them in your database.
	
	// bind all inputvalues to my $inserArray
	$insertArray = Input::all();
	
	//Now lets create an Instace of the Medic Object using the gathered Inputs ( I know... less testable :( )
	$medic = new Medic($insertArray);
	
	// I like my passwords to be hashed so I replace 'password' with a hashed value
	$medic->setPassword(Input::get('password'));

	// Save and redirect if validation passes
	if($medic->save()) return Redirect::route('aidkit.medics.index');
	
	// Validation fails ? Do this
	return Redirect::back()->withInput()->withErrors($medic->errors);

I am sure I documented the above example in more detail than it would be necessary for you awesome Laravel Developers but maybe it helps someone. What should come through in this example is how easy you can take advantage of the the validation rules defined in the model as they will be used by the **AidkitModel** and return false if the validation fails.

---

## <a id="migration"></a>Migration

I am sure you all know about how awesome migrations are. *Aidkit* brings some along for you to get started pretty quickly. Considering you have defined your database connection and stuff ( [Click here if not](http://laravel.com/docs/database) ).

You can install the provided Migrations through calling <i class="icon-terminal"></i> `php artisan migrate --package=codebryo/aidkit` from your commandline. 

**Note:** *At the moment of writing Aidkit comes with migration for following tables*
- medics ( for storing all the users including some basic roles defined in the config.php File )
- actionlog ( for storing actions like creating/updating/deleting with information of the Object and the object_id )

### Seeding a default user

*Aidkit* also places a seed file in your database folder. `app/database/seeds/MedicsTableSeeder.php`
When calling <i class="icon-terminal"></i> `php artisan db:seed` from the commandline a user will be added to the userstable for you to be able to login to the backend right away.
By default the values are:
- username -> drhouse
- password -> aidkit

Feel free to change them in the *UsersTableSeeder* file before calling the `db:seed` command from the terminal.

---

## <a id="actionlog"></a>Actionlog

As allready mentioned somewhere above *Aidkit* comes with a **Actionlog**. After *migrating* tha tables provided by *Aidkit* you are good to go. By default your 10 latest Actions will be viewable on the dashboard after logging into your application. This can be found at `app/Aidkit/controllers/AidkitHomeController.php` and should look something like this.

	class AidkitHomeController extends AidkitController {

		public function showDashboard()
		{
			$actions = Actionlog::where('user_id',Auth::user()->id)->orderBy('created_at','desc')->take(10)->get();
			$this->layout->content = View::make('admin::dashboard',compact('actions'));
		}
		
	}

---

Feel free to get wild using *Aidkit* and as mentioned at the top, I appreciate any Feedback to help me make *Aidkit* a *I-don't-want-to-make-another-project-without-this* package.

---

## <a id="roadmap"></a>Roadmap

I have some hopefully awesome ideas for making Aidkit better, more powerfull and more supporting in the process of creating Backensolutions.

### Version 1.0 

This should be a little bit improved and of course totally stable Version of whats in store at the Moment.

- **Frontend Documentation** learn how to use some of the frontend functionalities like the alertbox or what helper classes can be used
- **Shortcut Support** Every user can create custom shortcuts to specific pages.
- **Notifications** Display clean Notification after various Events manually and automaticly
- **Modules** Aidkit will give you the option of installing different Modules for different Tasks like Upload etc...


### Planned Features

- **Markdown Support** I am planning on providing a easy way of using Markdown for writing and publishing contents if you want to have CMS Features in *Aidkit*
- **GoogleAnalytics Support** As a admin you should be able to set this up in the Backend with ease and have it available in the frontend through something like `HTML::aidkitAnalytics`
- **Image Manipulation** *Aidkit* should provide a Library for powerfull image manipulation ( maybe [Imagine](https://packagist.org/packages/imagine/imagine) )
- **HTML Widgets** I am planning on adding more HTML Widgets like graphs and stuff
- **Configuration** should be mucht more more powerfull, but only on what's needed
- **Fully Responsive** make the Administrationinterface fully Responsive.
- **Different Styles** Have the option to include different CSS Files for changing the presentation
- **Tests** yeah i know... TDD is the way to go... I still have to find my way into this
- **More to come...**
