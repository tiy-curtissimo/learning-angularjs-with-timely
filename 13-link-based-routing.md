# Routing: From Two Pages to One Page

In your application, right now, you have two separate
Web pages that have AngularJS components, the
`session/login.html` page and the `users/form.html`
page. That's nice, and all, but you *want* (no
**need**!) a single-page application. You strap
yourself into your Herman Miller and fire up the ol'
editor and browser to implement **routing**.

[callout-info]
**Routing** in a single-page Web application means
changing the entire view (or parts of the view) in
response to some event like a person clicking on a
button *without* refreshing the page from the server.
[/callout-info]

## Importing a Third-Party Module

From your earlier reading before taking on this
project for yourself and Michaela, you remember that
the AngularJS community had stopped using the routing
mechanism in the AngularJS framework and, instead, use
[ui-router](https://ui-router.github.io/ng1/) as its
replacement because it just works better. You decide
to go with best-of-breed and use `ui-router`, too.
Which is convenient because it seems Michaela already
included it in the project for us.

But, you have to get it into your application module.
You open the `app.module.js` file that you created
what feels like forever ago.

```javascript
angular
  .module('app', []);
```

That empty array after the name of the module is where
you put the third-party modules that you want to use.
According to the [ui-router Hello World!
tutorial](https://ui-router.github.io/ng1/tutorial/helloworld),
you include their module like this:

```javascript
angular
  .module('app', ['ui.router']);
```

Now, it should be available everywhere in your
application module.

[callout-warning]
You know it's not always sunshine and unicorns when
working with third-party code. Sometimes you find the
documentation hard to understand. Sometimes, other
developers follow crazy naming conventions.

For example, the name of this module that you're using
is `ui-router`, right? So, why do you import a module
named `ui.router`?!? The difference between a dash and
a period causes your application to work or crash.

«sigh»

Just remind yourself to pay attention to these dumb
details.
[/callout-warning]

## Planning

You know that you want to use `ui-router` to combine
the log-in and sign-up pages. You look at the UI and
determine that when a person clicks the "SIGN UP!"
link that it should replace the `login-page`
component with the `sign-up-card` component, a
one-for-one switch.

![timely - routing between two components](https://tiy-corp-train.github.io/newline-media/learning-angularjs-with-timely/simple-routing-design.png)

That should be easy enough. First, you have to get all
the components onto one page.

## Route Between `login-page` and `sign-up-card`

The `session/login.html` page, doesn't know anything,
yet, about the `sign-up-card` component. You decide to
include it, now.

```html
  <script src="/app/login-card/login-card.component.js"></script>
  <script src="/app/login-page/login-page.component.js"></script>

  <!-- Welcome to the party, sign-up-card! -->
  <script src="/app/sign-up-card/sign-up-card.component.js"></script>
</body>
```

That takes care of that. Now, you figure you need to
configure the router.

## Configuring the `ui-router`

You look at your different files that you have created
so far and don't see anywhere to comfortably
configure the settings of the `ui-router`. So, you
create a new file named `app.config.js` in the `app`
directory right next to the `app.module.js` file.

![timely - create the app.config.js file](https://tiy-corp-train.github.io/newline-media/learning-angularjs-with-timely/create-app.module.js.png)

You include the configuration file in the
`session/login.html` code.

```html
  <script src="/js/angular-ui-router-1.0.3.min.js"></script>
  <script src="/app/app.module.js"></script>

  <!-- Well, hello, configuration! Nice to have you. -->
  <script src="/app/app.config.js"></script>

  <script src="/app/registration-cta/registration-cta.component.js"></script>
  <script src="/app/login-card/login-card.component.js"></script>
```

The `ui-router` is a *state-based router* which means
that you describe a state that SPA should be in and
the router will show the correct components. A normal
state for the `ui-router` has the following
properties:

* the **name** of the
* the **url** that the state represents
* the **componment** that the router should render

Referring to the
[documentation](https://ui-router.github.io/guide/ng1/route-to-component),
you see that you have to ask AngularJS for the
`$stateProvider` service provided by `ui-router` in
your configuration file. You also see that you need to
ask AngularJS for the `$urlServiceProvider` to specify
a default state. And, since you're not coding in 2009,
you want to ask AngularJS for the `$locationProvider`
so you can use HTML5 full URLs instead of the ugly
*hashbang* URLs. In the `app.config.js`, you type
something very similar to the way that we specify
controllers for our components, the *dependency
injection syntax* of a list of dependency names and a
function to accept all of them.

```javascript
angular
  .module('app')
  .config([                 // Ask AngularJS for the
    '$stateProvider',       // to register states
    '$urlServiceProvider',  // to specify the default state
    '$locationProvider',    // to turn on HTML5 mode
    ($stateProvider, $urlServiceProvider, $locationProvider) => {
    }
  ]);
```

From your design, you identify two states, the log-in
state and the sign-up state. You define a couple of
states in the `app.config.js` and add them to the
`$stateProvider`.

```javascript
angular
  .module('app')
  .config([
    '$stateProvider',
    '$urlServiceProvider',
    '$locationProvider',
    ($stateProvider, $urlServiceProvider, $locationProvider) => {
      const loginState = {
        name: 'login',
        url: '/session/new',
        component: 'loginPage'
      };

      const signUpState = {
        name: 'signUp',
        url: '/users/new',
        component: 'signUpCard'
      };
    }
  ]);
```

Then, you register the states with the
`$stateProvider`, set the HTML5 mode to `true`, and
set the default state to the name of the login state,
"login".

```javascript
angular
  .module('app')
  .config([
    '$stateProvider',
    '$urlServiceProvider',
    '$locationProvider',
    ($stateProvider, $urlServiceProvider, $locationProvider) => {
      const loginState = {
        name: 'login',
        url: '/session/new',
        component: 'loginPage'
      };

      const signUpState = {
        name: 'signUp',
        url: '/users/new',
        component: 'signUpCard'
      };

      $stateProvider.state(loginState);
      $stateProvider.state(signUpState);

      $locationProvider.html5Mode(true);
      $urlServiceProvider.rules.otherwise({ state: 'login' });
    }
  ]);
```

Now that you have the states configured, you can use
the router to transition between the states rather
than navigating to another page.

## Showing the Current State

In the `session/login.html` page, the code still shows
the `<login-page></login-page>` component. The
`ui-router` demands that you use a different component
to show the configured states, the `ui-view`
component. You delete the `login-page` component and
replace it with the `ui-view` component. You also add
the helpful `full-sized` CSS class that Michaela has
in the application's stylesheet to make sure that
the page's layout is ok.

```html
<!doctype html>
<html>

<head>
  <title>Timeliness</title>
  {{> common/head}}
</head>

<body ng-app="app">

  <!-- Good-bye, login-page -->
  <!-- ui-view is our new best friend! -->
  <ui-view class="full-sized"></ui-view>

  <script src="/js/angular-1.6.4.min.js"></script>
  <script src="/js/angular-resource-1.6.4.min.js"></script>
  <script src="/js/angular-ui-router-1.0.3.min.js"></script>
  <script src="/app/app.module.js"></script>
  <script src="/app/app.config.js"></script>
  <script src="/app/registration-cta/registration-cta.component.js"></script>
  <script src="/app/login-card/login-card.component.js"></script>
  <script src="/app/login-page/login-page.component.js"></script>
  <script src="/app/sign-up-card/sign-up-card.component.js"></script>
</body>

</html>
```

## Transitioning Using a Link

You see the "SIGN UP!" link in the `registration-cta`
component and open the
`registration-cta.component.html` to refamiliarize
yourself with it.

```html
<div class="card-content">
  Not a user?
</div>
<div class="card-actions">
  <a class="button button-primary" href="/users/new">Sign up!</a>
</div>
```

The `<a>` tag has the `href` attribute which tells the
browser to navigate away from this page and go to the
`/users/new` location. You don't want that, anymore.
You want the `ui-router` to handle the transition.
According to the documentation, instead of using
`href`, the `<a>` tag should use the `ui-sref`
instead, `ui-sref` meaning "ui-router's state
reference". The value of the `ui-sref` attribute needs
to be the name of the state to which you want to
transition. In this case, that is the "signUp" state.

You change the `<a>` tag in the
`registration-cta.component.html` file to the
following.

```html
<div class="card-content">
  Not a user?
</div>
<div class="card-actions">
  <a class="button button-primary" ui-sref="signUp">Sign up!</a>
</div>
```

With that changed, when you click the "SIGN UP!"
buton, it successfully transitions to the new state
and changes the URL appropriately using the HTML5
history API!

But, the `sign-up-card` has lost its styling because
it requires the CSS classes to make it a card. To
solve that presentation issue, you open the
`sign-up-card.component.html` file and wrap everything
in a `<div>` with the appropriate CSS.

```html
<div class="card login-card">
  <form ng-submit="signUp.submitForm()">
    <div class="card-art"></div>
    <div class="card-content">
      <h1>Sign up!</h1>
      <div class="error-message">{{ signUp.error }}</div>
      <input autofocus type="text" placeholder="username" name="username" ng-model="signUp.username">
      <input type="password" placeholder="password" name="password" ng-model="signUp.password">
    </div>
    <div class="card-actions">
      <button class="button-primary" ng-disabled="signUp.username.length === 0 || signUp.password.length === 0">Register!</button>
    </div>
  </form>
</div>
```

And, now, you fix a UI bug that's been bothering you
since you started looking at this application.

> When you're on the registration page, there's no way
> to go back to the log-in page!

All you do is add an `<a>` tag before the "SIGN UP!"
button with the correct `ui-sref` back to the login
state.

```html
<div class="card login-card">
  <form ng-submit="signUp.submitForm()">
    <div class="card-art"></div>
    <div class="card-content">
      <h1>Sign up!</h1>
      <div class="error-message">{{ signUp.error }}</div>
      <input autofocus type="text" placeholder="username" name="username" ng-model="signUp.username">
      <input type="password" placeholder="password" name="password" ng-model="signUp.password">
    </div>
    <div class="card-actions">

      <!-- Thank Goodness! Some usability! -->
      <a class="button" ui-sref="login">Cancel</a>

      <button class="button-primary" ng-disabled="signUp.username.length === 0 || signUp.password.length === 0">Register!</button>
    </div>
  </form>
</div>
```

## A Band-Aid for the Java Views

You notice that when you click the "SIGN UP!" link and
transition to the sign-up state, everything looks fine
until you refresh your browser. Then, the "CANCEL"
button stops working.

You realize that the URL `/users/new` still serves the
content from the template `users/form.html` which
doesn't contain all the hard work that you've done.
You decide that the quick and dirty fix is ok, for
now, and just copy-and-paste all of the content from
`session/login.html` to `users/form.html`.

[callout-warning]
The thing about SPAs: they should *always* serve the
same content with every request. In this case, the
HTML at `session/login.html` and `users/form.html` are
out of sync. You will create a better solution for
this in the upcoming lessons.
[/callout-warning]

## What Did You Do?

While you know that this has only scraped the surface
of routing with `ui-router`, you sure do feel like you
learned a lot along the way! You learned to

* import a third-party module into your module;
* plan the transitions between states of your
  application;
* configure `ui-router` with states that describe your
  application;
* configure the default route;
* configure HTML5 history mode;
* use `ui-sref` on `<a>` tags to initiate transitions
  between different UI states; and,
* make sure that the server always sends back the same
  content so that the SPA can pick up where it left
  off.
