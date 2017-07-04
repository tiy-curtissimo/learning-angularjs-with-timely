# Your Second Component (Part 3 of 3): Using AJAX

Now is the time to get this page talking to the
server!

AngularJS has it's own AJAX service, named `$http`.
You decide that you should use that to talk to the
server.

Right now, your files, `login-card.component.js` and
`login-card.component.html` look like this:

```javascript
class LoginCardController {
  constructor() {
    this.username = '';
    this.password = '';
  }

  submitForm() {
    console.log('username:', this.username);
    console.log('password:', this.password);
    this.username = '';
    this.password = '';
  }
}

angular
  .module('app')
  .component('loginCard', {
    templateUrl: '/app/login-card/login-card.component.html',
    controllerAs: 'login',
    controller: [() => new LoginCardController()]
  });
```

```html
<form ng-submit="login.submitForm()">
  <div class="card-art"></div>
  <div class="card-content">
    <div>{{ error }}</div>
    <input autofocus type="text" placeholder="username" ng-model="login.username">
    <input type="password" placeholder="password" ng-model="login.password">
  </div>
  <div class="card-actions">
    <button class="button-primary" ng-disabled="login.username.length === 0 || login.password.length === 0">Watch time</button>
  </div>
</form>
```

## Getting the `$http` Service Into Your Controller

You want to use the `$http` service that AngularJS
provides to make AJAX calls. You want to *depend* on
that service. Moreover, you want to ask AngularJS to
give the `$http` service to your controller, to
*inject* it into your controller so that you can use
it.

This is known as **dependency injection**.

[callout-info]
**Dependency injection** is when you ask a framework,
like AngularJS, to provide something to you so that
you don't have to figure out how to get it yourself.

AngularJS has a lot of things that it can provide to
you, `$http` being just one. As you continue your
exploration of AngularJS, you'll run into more.
[/callout-info]

To use dependency injection, you tell AngularJS the
name of the thing that you want injected. AngularJS
finds it and passes it to your controller or raises
an error.

To get the `$http` service into your controller, you
modify the registration of the controller in the
`login-card.component.js` file. You provide the name
of the service, `$http`, and then capture it in a
parameter that you pass to the controller's
constructor. You do this in the array of the
`controller` property of the component's configuration
object.

```javascript
controller: [
  // Ask for the service by name
  '$http',

  // Capture it in a parameter and pass it to the
  // constructor of the controller
  ($http) => new LoginCardController($http)
]
```

Then, you need to create a constructor on the class
to capture and save the service so you can use it
when a person clicks the "WATCH TIME" button and
submits the form.

```javascript
class LoginCardController {

  // Add the $http parameter to the constructor's
  // signature so you can get it from AngularJS
  constructor($http) {
    this.username = '';
    this.password = '';

    // Set a property of the controller to the value
    // of the provided service.
    this.$http = $http;
  }

  submitForm() {
    console.log('username:', this.username);
    console.log('password:', this.password);
    this.username = '';
    this.password = '';
  }
}
```

Now, your `login-card.component.js` file should look
like this:

```javascript
class LoginCardController {
  constructor($http) {
    this.username = '';
    this.password = '';
    this.$http = $http;
  }

  submitForm() {
    console.log('username:', this.username);
    console.log('password:', this.password);
    this.username = '';
    this.password = '';
  }
}

angular
  .module('app')
  .component('loginCard', {
    templateUrl: '/app/login-card/login-card.component.html',
    controllerAs: 'login',
    controller: [
      '$http',
      ($http) => new LoginCardController($http)
    ]
  });
```

## Using the `$http` Service

You look through Michaela's Java code and find the
`SessionApiController` class that provides a way for
you to log in a person by `PUT`ting a JSON object with
`username` and `password` properties in an HTTP
request to the URL path `/api/session/mine`.

Now that you have the `$http` service in your
controller, you head over to the [$http
documentation](https://docs.angularjs.org/api/ng/service/$http)
page and see that it has a shortcut method named
`put()` that returns a
[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)
you can use to log in a person that's filled out the
login form.

You delete the contents of the `submitForm` method of
the `LoginCardController` class in your component's
`.js` file and use the `$http` service to make a call
to the API endpoint. You put in the following code:

```javascript
submitForm() {
  // Create an object to send to the API endpoint
  // for logging in
  let credentials = {
    username: this.username,
    password: this.password
  }

  // Use AJAX to PUT the credentials to the API
  this.$http
    .put('/api/session/mine', credentials)
    .then(() => {

      // Show SUCCEEDED if all goes well!
      alert('SUCCEEDED!');

    })
    .catch(() => {

      // Show NO LOGIN if all goes poorly.
      alert('NO LOGIN!');

    });
}
```

After you refresh the page and type a bad username and
password, you see an alert that reads "NO LOGIN!".
When you type in good credentials, like *maria* for
both the username and password, you see an alert that
reads "SUCCEEDED!" That was *so* easy!

## Handling a Bad Login with More Data Binding

In the event of a bad login, you want the UI to show
that the person provided a bad set of credentials. So,
you change the `catch` handler of the Promise to look
like this.

```javascript
submitForm() {
  let credentials = {
    username: this.username,
    password: this.password
  }

  this.$http
    .put('/api/session/mine', credentials)
    .then(() => {
      alert('SUCCEEDED!');
    })
    .catch(() => {

      // Show an error
      this.error = 'Cannot login with that username and password';

    });
}
```

That will set the `error` property of the controller
to that message. You remember seeing a place for an
error message over in the HTML and find it where it
reads

```html
<div>{{ error }}</div>
```

You feel lucky because Michaela used the Handlebars
syntax for her Java templating which is the same
syntax that AngularJS uses to output values into the
HTML, too! All you need to do is put the controller's
name in front of that variable name and you should be
good to go!

```html
<div>{{ login.error }}</div>
```

You try logging in, again, with a bad username or
password and, now, it shows the error message!

## Handling a Good Login with a Redirect

Last, and certainly not least, you want to redirect
the browser to the main page if the person
successfully logs in. Normally, you would just use
`window.location.href = "some url"` to do that.
However, AngularJS has a `$window` service that you
can use for the same purpose which will prevent your
code from having to rely on the global `window`
object.

You change your controller's registration in the
`login-card.component.js` file to tell AngularJS that
you want the `$window` service, as well.

```javascript
angular
  .module('app')
  .component('loginCard', {
    templateUrl: '/app/login-card/login-card.component.html',
    controllerAs: 'login',
    controller: [
      '$http',

      '$window', // Ask for the $window service

      // Include it in the parameter list for your
      // controller
      ($http, $window) => new LoginCardController($http, $window)
    ]
  });
```

Then, you change the constructor of your controller to
accept the new parameter.

```javascript
class LoginCardController {
  constructor($http, $window) { // New parameter
    this.username = '';
    this.password = '';
    this.$http = $http;
    this.$window = $window; // Save the service
  }
```

Now, you can use that when the user provides the
correct login information in the `submitForm` method.

```javascript
submitForm() {
  let credentials = {
    username: this.username,
    password: this.password
  }
  this.$http
    .put('/api/session/mine', credentials)
    .then(() => {

      // Change the location of the browser to go to
      // the main page.
      this.$window.location.href = '/';

    })
    .catch(() => {
      this.error = 'Cannot login with that username and password';
    });
}
```

And, **IT WORKS!** *YAY!*

You take a deep breath and commit your code to source
control so that you don't lose any of this hard work
and learning that you've done, so far.

## What Did You Do?

In this section, you just created your first
interactive component! That's amazing! You look down
at your notes to review everything you did.

* Created a component's files in a subdirectory, just
  like last time, with the HTML and AngularJS
  registration.
* Defined and registered a controller to handle
  interactive behavior
* Used `ng-model` to bind to input fields
* Used `ng-disabled` to disable a button based on the
  values of the input fields
* Used `ng-submit` to call a controller method when
  a person submits the login form
* Used AngularJS' AJAX service `$http` to make a PUT
  request to the server to log in the person
* Displayed an error message by using AngularJS'
  one-way data binding with `{{ variable_name }}`
* Used AngularJS' `$window` service to redirect the
  browser to the main page on a successful login

You accomplished a lot! Most of the work from now on
will look like this, you realize: creating components
that make AJAX calls and binding values of those AJAX
calls to the HTML or into and out-of `<input>` and
other form fields. But, now that you've learned it
once, you know that you can do it, again, and even
better!

## The Final File Contents for the Component

`login-card.component.html`

```html
<form ng-submit="login.submitForm()">
  <div class="card-art"></div>
  <div class="card-content">
    <div>{{ login.error }}</div>
    <input autofocus type="text" placeholder="username" ng-model="login.username">
    <input type="password" placeholder="password" ng-model="login.password">
  </div>
  <div class="card-actions">
    <button class="button-primary" ng-disabled="login.username.length === 0 || login.password.length === 0">Watch time</button>
  </div>
</form>
```

`login-card.component.js`

```javascript
class LoginCardController {
  constructor($http, $window) {
    this.username = '';
    this.password = '';
    this.$http = $http;
    this.$window = $window;
  }

  submitForm() {
    let credentials = {
      username: this.username,
      password: this.password
    }
    this.$http
      .put('/api/session/mine', credentials)
      .then(() => {
        this.$window.location.href = '/';
      })
      .catch(() => {
        this.error = 'Cannot login with that username and password';
      });
  }
}

angular
  .module('app')
  .component('loginCard', {
    templateUrl: '/app/login-card/login-card.component.html',
    controllerAs: 'login',
    controller: [
      '$http',
      '$window',
      ($http, $window) => new LoginCardController($http, $window)
    ]
  });
```
