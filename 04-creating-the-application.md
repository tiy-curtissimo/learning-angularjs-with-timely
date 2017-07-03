# Getting Started: Create an AngularJS Application

AngularJS has a specific way to define an application
called a **module**. You then use that module to
register all of the other stuff you create so that the
application knows where to get stuff to render in the
SPA or use to get data or use to make some pretty
formatting.

AngularJS developers normally put our application
module in a file named `app.module.js`. So, you should
do that, too.

[callout-info]
AngularJS has a naming and organization
scheme for things, not because it's required, but
because it makes sense once you have a lot of files to
manage. The naming scheme is made up of three parts
that go together like `[part1].[part2].[part3]`:

1. The name of the thing you want to write. In our
case, this is just the application module, so you name
it `app`.
1. The type of AngularJS thing that you're writing. In
our case, we're declaring a module, so you use `module`.
1. The content type of the file. In this case, you are
writing JavaScript, so you use `js`. Another common file
type extension that we'll use is `html`.
[/callout-info]

## Creating the AngularJS Module for Timely

In the Spring application that Michaela gave to you,
you know that you need to put your static JavaScript
content under the `static` directory where the Spring
app will serve that static content. In Eclipse (or
whatever Java IDE you're using), under the
`src/main/resources` package, you see the `static`
directory. Under there, you create the `app` directory.
Inside the `app` directory, you create a file named
`app.module.js`.

![timely - create app.module.js](https://tiy-corp-train.github.io/newline-media/learning-angularjs-with-timely/create-app-module-in-eclipse.png)

[callout-info]
To declare a *module* in AngularJS, you call the
`module` function of the `angular` object and give it
TWO parameters:

1. The name of the module which, in our case, is just
`app`
1. An array of other module names that you want to use
in your module. You don't have any, yet, so you just
do an empty array.
[/callout-info]

You save the file.

## Adding the Module to the Login Page

Now, you need to get that JavaScript into the Web page.
You look at the URL and see that the path currently
reads `/session/new` for the login page. Now, Michaela
is a pretty good developer. You know that file has to
live in the `templates` section because of Spring. So,
you look under the templates directory and see a
directory named `session` and a file under that named
`login.html`. That's probably the one.

![timely - find the login.html form](https://tiy-corp-train.github.io/newline-media/learning-angularjs-with-timely/find-login-form-in-eclipse.png)

In Eclipse, you open `login.html` and see that
Michaela already added references to AngularJS down
at the end of the `body` tag. Now, you add your newly
created module by inserting a `script` tag after the
others.

```html
  <script src="/js/angular-resource-1.6.4.min.js"></script>
  <script src="/js/angular-ui-router-1.0.3.min.js"></script>

  <!-- Your new AngularJS module! -->
  <script src="/app/app.module.js"></script>

</body>
```

[callout-warning]
We're going to do this a lot, this create a new
JavaScript file and add it to the HTML file that we're
working on. Don't forget to do it *every single time*!

If you forget to do it, you won't see the newly added
thing that you've created. You'll end up spending time
trying to figure out why it's not working when, in
reality, it does work and you just forgot to include
it in the page. I speak from personal experience with
countless "D'OH!" moments because I forgot to do the
`<script>` tag for the JavaScript file I just wrote.
[/callout-warning]

## Telling AngularJS to Use the Module

Find the opening `<body>` tag. To tell AngularJS that
we want it to control the content of the body, you add
the AngularJS instruction to manage it, the `ng-app`
attribute. you set the attribute to the name that we
used to declare our module "app".

```html
</head>

<!-- Add the ng-app instruction on the body -->
<body ng-app="app">
  <div class="card login-card">
    <form method="post" action="/session/mine">
```

That's it. AngularJS has now bootstrapped our app into
the Web page, "bootstrap" just being a silly term for
"starting up". Save your file and take a look to see
that nothing has changed.

## What Did You Just Do?

Even though it doesn't look like much (actually, it
looks like nothing!), you started on the path to
creating our AngularJS SPA. In this lesson, you

* Learned that AngularJS organizes its stuff in
**modules**;
* Learned how to declare a **module**;
* Created a JavaScript file that contains the
declaration of our main module; and,
* Included that JavaScript file with a `<script>` tag;
and,
* Added the AngularJS instruction that the application
should control the content of the `<body>`.

Now that you have that, you can start creating
*components* to replace the HTML that you have on the
screen with AngularJS-controlled behavior.
