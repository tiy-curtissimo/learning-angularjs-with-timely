# Review Converting the Sign-Up Card

You did a great job on converting that sign-up card.
Here's one way to finish that assignment.

## In `src/main/resources/templates/users/form.html`

Add your `app.module.js` to this page \
Configure the page to be an AngularJS application
with the `ng-app` attribute** \
Create `.js` and `.html` files for a component named
`sign-up-card` and organize them correctly

```html
<!doctype html>
<html>

<head>
  <title>Timeliness</title>
  {{> common/head}}
</head>

<!-- Register the page as an AngularJS application -->
<body ng-app="app">

  <!-- Use the sign-up-card AngularJS component -->
  <sign-up-card class="card login-card"></sign-up-card>

  <script src="/js/angular-1.6.4.min.js"></script>
  <script src="/js/angular-resource-1.6.4.min.js"></script>
  <script src="/js/angular-ui-router-1.0.3.min.js"></script>

  <!-- Register the application module -->
  <script src="/app/app.module.js"></script>

  <!-- Register the sign-up-card component -->
  <script src="/app/sign-up-card/sign-up-card.component.js"></script>
</body>

</html>
```

## In `src/main/resources/static/app/sign-up-card/sign-up-card.component.html`

Extract the HTML from `users/form.html` for the
`.html` contents of the component \
Replace the HTML in `users/form.html` with your
custom `sign-up-card` component

```html
<!-- Bind the submission of the form to the controller -->
<form ng-submit="signUp.submitForm()">
  <div class="card-art"></div>
  <div class="card-content">
    <h1>Sign up!</h1>

    <!-- Show the error in signing up, if any -->
    <div class="error-message">{{ signUp.error }}</div>

    <!-- Bind the username to the controller -->
    <input autofocus type="text" placeholder="username" name="username" ng-model="signUp.username">

    <!-- Bind the password to the controller -->
    <input type="password" placeholder="password" name="password" ng-model="signUp.password">
  </div>
  <div class="card-actions">

    <!-- Disable the button based on the values of
         the username and password -->
    <button class="button-primary" ng-disabled="signUp.username.length === 0 || signUp.password.length === 0">Register!</button>
  </div>
</form>
```

## In `src/main/resources/static/app/sign-up-card/sign-up-card.component.js`

Write your controller to `POST` the new user's
username and password to the path `/api/users` \
Handle successful username creation by redirecting
them to the path `/` \
Handle unsuccessful registrationm by showing an
error.

```javascript
class SignUpCardController {
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
        .post('/api/users', credentials)
        .then(() => {
          this.$window.location.href = '/';
        })
        .catch(() => {
          this.error = 'Please select another username because that one is already being used';
        });
  }
}

angular
  .module('app')
  .component('signUpCard', {
    templateUrl: '/app/sign-up-card/sign-up-card.component.html',
    controllerAs: 'signUp',
    controller: [
      '$http',
      '$window',
      ($http, $window) => new SignUpCardController($http, $window)
    ]
  });
```
