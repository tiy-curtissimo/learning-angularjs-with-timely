# Getting Started: Create an AngularJS Application

AngularJS has a specific way to define an application
called a **module**. We then use that *module* to
register all of the other stuff we create so that the
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
case, this is just the application module, so we name
it `app`.
1. The type of AngularJS thing that you're writing. In
our case, we're declaring a module, so we use `module`.
1. The content type of the file. In this case, we are
writing JavaScript, so we use `js`. Another common file
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

![timely - create app.module.js](https://tiy-corp-train.github.io/newline-media/learning-angular-with-timely/create-app-module-in-eclipse.png)

[callout-info]
To declare a *module* in AngularJS, you call the
`module` function of the `angular` object and give it
TWO parameters:

1. The name of the module which, in our case, is just
`app`
1. An array of other module names that you want to use
in your module. We don't have any, yet, so we just
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

In Eclipse, you open
