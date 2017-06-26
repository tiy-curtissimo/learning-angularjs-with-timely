# Routing: Changing State on Login or Registration

You're coming back to your computer feeling pretty
good after having a little demo with Michaela showing
her the work that you've accomplished so far. Michaela
said something like, "That would've taken me a week!"
You thought, "Good job, me. Good job."

You sit down and run through the demo, again, checking
out the components that you've written for getting
access to Timely. You realize that it's time to take
the big jump and get into the real application itself.

Now, you want to tackle the ability to get past the
login and registration screens into the application
using AJAX and the SPA rather than having the browser
navigate away.

You decide to take a baby step and just get the
routing figured out before starting on adpating the
time entry, client, and reporting screens.

## Creating a Placeholder Component

You choose to create a component that will,
eventually, become the main application. But, for now
at least, it'll just be something to show that your
code is set up right by displaying a "SIGN OUT"
button that you can use to get back to the log-in
screen.

You create the component named `main-screen` with its
`.html` and `.js` file in an appropriately named
subdirectory. Then, you add it to the end of both
`session/login.html` and `users/form.html` grumbling
that updating both of those files all the time is
going to get *really* tedious, soon.

```html
  <script src="/app/login-page/login-page.component.js"></script>
  <script src="/app/sign-up-card/sign-up-card.component.js"></script>

  <!-- Hello, MAIN SCREEN! -->
  <script src="/app/main-screen/main-screen.component.js"></script>
</body>
```

Inside the `main-screen.component.html` file, you
place a single button bound to a method in the
controller.

```html
<button ng-click="mainScreen.logout()">Logout</button>
```

[callout-info]
When you just want to initiate an action on the
controller because someone clicked something in your
component, use the `ng-click` AngularJS attribute.
[/callout-info]

You create and register the `main-screen` component in
the `main-screen.component.js` file, including a
controller to allow you to handle the button click and
log out of the application. You peek in the
`SessionApiController`, again, to see how Michaela
wrote the server-side code and it looks like you need
to send a `DELETE` HTTP call to `/api/session/mine`.
Luckily, the `$http` service has the convenience
method on it!

```javascript
class MainScreenController {
  constructor($http, $window) {
    this.$http = $http;
    this.$window = $window;
  }

  logout() {
    this.$http
      .delete('/api/session/mine')
      .then(() => {
        this.$window.location.href = '/session/new';
      })
      .catch(() => {
        this.$window.location.href = '/session/new';
      });
  }
}

angular
  .module('app')
  .component('mainScreen', {
    templateUrl: '/app/main-screen/main-screen.component.html',
    controllerAs: 'mainScreen',
    controller: [
      '$http',
      '$window',
      ($http, $window) => new MainScreenController($http, $window)
    ]
  });
```

## Adding the New State

You open `app.config.js` and define and register
another state to the configuration.

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

      // Presumably, what will become the main screen!
      const mainScreen = {
        name: 'main',
        url: '/',
        component: 'mainScreen'
      };

      $stateProvider.state(loginState);
      $stateProvider.state(signUpState);

      // Register that new main screen!
      $stateProvider.state(mainScreen);

      $locationProvider.html5Mode(true);
      $urlServiceProvider.rules.otherwise({ state: 'login' });
    }
  ]);
```

## Responding to a Successful Login

You review the `login-card` and `sign-up-card`
components to remind yourself how they currently work.
They both use the `$window` service from AngularJS to
navigate the browser to a new page. You want to remove
that service and use something from `ui-router` to
transition the state to the `main` state as defined
in the `app.config.js` file.

You look over the documentation for `ui-router` and
find the [`go()` method of the `$state`
service](https://github.com/angular-ui/ui-router/wiki/Quick-Reference#stategoto--toparams--options)
that it provides via dependency injection. So, you
replace the injection of `$window` with that of
`$state` and modify the success handler of the
`submitForm()` method.

```javascript
class LoginCardController {
  constructor($http, $state) {
    this.username = '';
    this.password = '';
    this.$http = $http;

    this.$state = $state; // Save this $state object for later
  }

  submitForm() {
    let credentials = {
      username: this.username,
      password: this.password
    }
    this.$http
      .put('/api/session/mine', credentials)
      .then(() => {

        // No more $window! It's all about $state!
        this.$state.go('main');

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
      '$state', // Ask for $state, now
      ($http, $state) => new LoginCardController($http, $state)
    ]
  });
```

You make similar changes in the
`sign-up-card.component.js` file, too.

You run it and, sure enough, everything works! No more
browser redirection! When you log in, you see one
button that, when you click it, it returns you to the
log in page!

## Fixing the `main-screen` Component

"Dang it!" you shout when you realize that you just
coded the wrong thing into the `main-screen`
component. It still uses `$window`! You fix that, real
quick like, in `main-screen.component.js`, by
replacing the calls to `$window.location` with
`$state.go` just like you did in the `login-card` and
`sign-up-card` components.

## What Did You Do?

One little thing, really: `$state.go('state-name')`.
That's what you did, here. But, when you want to
transition states after doing some logic or calling
some AJAX API, it is indispensable.

You can sense that it will be pretty smooth sailing
from here. All you have to do is upgrade the rest of
the application.

How hard can that be?
