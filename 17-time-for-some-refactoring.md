# Time for Some Refactoring

### (*Stay tuned after the lesson for an unsupervised assignment*)

You look at your code and notice it's about time to
start refactoring your work because you're starting
to notice duplication and places for improvement.

You identify two kinds of clean-up that you can
perform:

1. Fix the copy-and-paste problem in the HTML files
   that you're starting to encounter; and,
2. Fix the direct AJAX calls that you have in your
   controllers by moving them to your own custom
   AngularJS service.

You decide to do this in that order.

## Fixing HTML Duplication

Luckily, Michaela used the Mustache template engine
for her Spring Boot project. You know that you can
include one template in another with this syntax:

```handlebars
{{> path_to_the_template }}
```

You create a new file named `spa.html` under the
common subdirectory of the templates area.

![timely - create the spa.html page](https://tiy-corp-train.github.io/newline-media/learning-angularjs-with-timely/create-the-spa.html-page.png)

You open the `session/login.html` file and copy the
contents of it into the `common/spa.html` file.

```handlebars
<!doctype html>
<!-- THIS IS SPA.HTML -->
<html>

<head>
  <title>Timeliness</title>
  {{> common/head}}
</head>

<body ng-app="app">
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
  <script src="/app/main-screen/main-screen.component.js"></script>
</body>

</html>
```

Then, you delete the content of `session/login.html`
and replace it with just one line of code.

```handlebars
{{> common/spa }}
```

Then, you delete the content of `users/form.html` and
replace it with the same content.

You feel good because you only need to add new
`<script>` tags in the `common/spa.html` file and all
the existing transitioned pages will get them. Also,
as you transition more pages, you can just remove the
old content and replace it with the one line. That's a
nice implementation of the Don't Repeat Yourself
principle.

## Extract an Authentication Service

Fundamentally, component-based programming like
AngularJS shares many traits with object-oriented
programming. You've known this for a long time.

One of the established tenets of object-oriented
programming is that an object should do one thing and
do it well. You look at your `login-card`,
`sign-up-card`, and `main-screen` components and see
that they're doing something beyond their established
duty of controllers: they talk directly to AJAX
services using `$http`. You feel that you should
centralize all of those authentication-related calls
in a single service that each of your components can
use.

[callout-info]
The "do one thing and do it well" saying is a
colloquial interpretation of the [single
responsibility
principle](https://en.wikipedia.org/wiki/Single_responsibility_principle)
which states that something like a controller should
have one and only one reason to change.

**Controllers** should have one reason for existing: to
respond to actions performed by people and display
data.

For reasons like fething data, wrapping DOM elements,
doing animation, and other concerns that aren't
directly about user interaction with data and actions,
AngularJS recommends using **services**.

**Services** provide common groupings of code that
components and other services can take advantage of.
[callout-info]

You create a new directory under `app` named
`authentication` and create a file under it named,
appropriately, `authentication.service.js`. You
*immediately* add it to the `common/spa.html` file.

![timely - add the authentication.service.js file](https://tiy-corp-train.github.io/newline-media/learning-angularjs-with-timely/create-authentication.service.js-file.png)

In the same way that you use the `component` method of
the `angular.module` object to create a component, you
use the `factory` method to create a service. You fill
in the service file with the same starter code that
you use for all of the AngularJS declarations and
registrations.

```javascript
class AuthenticationService {
}

angular
  .module('app')
  .factory('authentication', [() => new AuthenticationService()]);
```

The `AuthenticationService` will make use of the
`$http` AngularJS service to make all of the AJAX
calls. The components will use the
`AuthenticationService`. It makes sense, then, that
the `AuthenticationService` should ask AngularJS to
provide the `$http` service as part of its
construction.

```javascript
class AuthenticationService {
  constructor($http) {
    this.$http = $http;
  }
}

angular
  .module('app')
  .factory('authentication', [
      '$http',
      ($http) => new AuthenticationService($http)
  ]);
```

You recognize that you need three methods on this
service, the three that the current components need
to function. You create them from the logic that
already exists in the controllers.

```javascript
class AuthenticationService {
  constructor($http) {
    this.$http = $http;
  }

  login(username, password) {
    let credentials = { username, password };
    return this.$http
      .put('/api/session/mine', credentials);
  }

  register(username, password) {
    let credentials = { username, password };
    return this.$http
      .post('/api/users', credentials)
  }

  logout() {
    return this.$http
      .delete('/api/session/mine');
  }
}

angular
  .module('app')
  .factory('authentication', [
      '$http',
      ($http) => new AuthenticationService($http)
  ]);
```

Now, you go to the `login-card` component and change
its dependencies from needing `$http` to needing
`authentication`, the name of our service. Then, you
change the use in the controller, too. These changes
yield a `login-card.component.js` file that looks like
this.

```javascript
class LoginCardController {
  constructor(auth, $state) {
    this.username = '';
    this.password = '';
    this.auth = auth;
    this.$state = $state;
  }

  submitForm() {
    this.auth
      .login(this.username, this.password)
      .then(() => {
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
      'authentication',
      '$state',
      (auth, $state) => new LoginCardController(auth, $state)
    ]
  });
```

## What Did You Do?

First, you made the HTML side of the code easier to
maintain since there's now only one `.html` file that
contains the `<script>` tags for your code, and that's
in `common/spa.html`. Now, you won't have to remember
to update it in lots of places, just that one file.

Second, you worked out how to write your own AngularJS
service. Then, you moved the authentication logic into
it so that the `login-card` component can now use it
to perform its login. You recognize that having all
the authentication logic in one place makes it easy to
find and easy to maintain in case any of the URLs
change or the method by which authentication occurs
changes.

# Assignment: What More Should You Do?

You realize that you have two more components that
need to use the authentication service that you just
wrote. You feel that you should go fix them, now. So,
you do.
