# Your Second Component (Part 2 of 3): Data and Action Binding

You remember from a conversation that you had that
there are two types of binding in JavaScript
frameworks: data binding and action binding.

**Data binding** occurs when a UI framework like
AngularJS automatically associates the value of a
JavaScript variable with what's in a form field.

**Action binding** occurs when a UI framework ties
together an action, like a person clicking a button,
with a JavaScript method.

You recognize that there are four things that you
want to bind in the `login-card` component.

1. You want to bind the value of the `username` input
   field to a JavaScript variable;
1. You want to bind the value of the `password` input
   field to a JavaScript variable;
1. You want to use those values to bind to the
   `disabled` attribute of the "WATCH TIME" button so
   that it's enabled when both fields have something
   typed in them; and,
1. You want to call a JavaScript method when the
   person submits the login form.

## Readying the Component for Data Binding

Right now, the `login-card.component.js` file looks
like this:

```javascript
angular
  .module('app')
  .component('loginCard', {
    templateUrl: '/app/login-card/login-card.component.html'
  });
```

Whenever you introduce interactive behavior to a
component, you want to introduce the idea of a
**controller** for the component.

[callout-info]
A **controller** in a component is what controls and
directs the data binding and behavior of that
component.
[/callout-info]

AngularJS best practices informs you that you should
give that controller a name (or an alias, if you
prefer) that you will use in the HTML template when
refering to variables and methods. You do that using
the `controllerAs` property of the configuration
object.

Because this is the `login-card` component, you decide
to alias the controller for the HTML templates with
the name `login`.

```javascript
angular
  .module('app')
  .component('loginCard', {
    templateUrl: '/app/login-card/login-card.component.html',

    // Provide an alias for the component for use in
    // the component's HTML file.
    controllerAs: 'login'
  });
```

## Data Binding the Input Fields

Right now, the `login-card.component.html` file looks
like this:

```html
<form method="post" action="/session/mine">
  <div class="card-art"></div>
  <div class="card-content">
    <div>{{ error }}</div>
    <input autofocus type="text" placeholder="username" name="username">
    <input type="password" placeholder="password" name="password">
  </div>
  <div class="card-actions">
    <button class="button-primary">Watch time</button>
  </div>
</form>
```

To bind an `input`'s value to a variable of the
controller of the component, you use the `ng-model`
attribute. In the case of your form, you look at the
`username`  `input` field.

```html
<!-- Plain old HTML -->
<input autofocus type="text" placeholder="username" name="username">
```

You change that to use the `ng-model` attribute with
the name of the controller that you specified in the
last section, `login`. You decide that you want to
bind it to the `username` property on the `login`
controller. This changes the `<input>` field to look
like this.

```html
<!-- Now, with AngularJS data binding because of "ng-model"! -->
<input autofocus type="text" placeholder="username" ng-model="login.username">
```

You do the same with the `password` field so that your
component's HTML now looks like this:

```html
<form method="post" action="/session/mine">
  <div class="card-art"></div>
  <div class="card-content">
    <div>{{ error }}</div>

    <!-- Look at this lovely data binding! -->
    <input autofocus type="text" placeholder="username" ng-model="login.username">
    <input type="password" placeholder="password" ng-model="login.password">

  </div>
  <div class="card-actions">
    <button class="button-primary">Watch time</button>
  </div>
</form>
```

[callout-info]
You just used AngularJS to watch those `<input>`
fields. Now, whenever someone types something into
them, it will automagically get stored in the
`username` and `password` properties of the
component's controller!
[/callout-info]

## Data Binding the Button

AngularJS also provides a binding mechanism named
`ng-disabled` which will add the `disabled` attribute
to an element if the JavaScript expression that it's
set to is `true`.

In your case, you figure that you want the button
disabled if the `login.username` or `login.password`
values are empty, that is, if they have a length of 0.
Formally, you want to

> Disable the "WATCH TIME" button if the length of the
> `login.username` value is 0 *or* if the length of
> the `login.password` value is 0.
>
> In JavaScript logic:
> login.username.length === 0 || login.password.length === 0

You use that logic in the `ng-disabled` attribute and
get

```html
<form method="post" action="/session/mine">
  <div class="card-art"></div>
  <div class="card-content">
    <div>{{ error }}</div>
    <input autofocus type="text" placeholder="username" ng-model="login.username">
    <input type="password" placeholder="password" ng-model="login.password">
  </div>
  <div class="card-actions">

    <!-- Look at that fancy disabling! -->
    <button class="button-primary" ng-disabled="login.username.length === 0 || login.password.length === 0">Watch time</button>

  </div>
</form>
```

You smile after the page refreshes because, now, the
"WATCH TIME" button is disabled until you type some
values into both the username and password fields!

## Binding the Form Submission

Finally, you want to capture the submission of the
`<form>` to the server. You do this by using the
`ng-submit` attribute that AngularJS provides. The
value of that attribute should be the name of the
method that you want to call on the controller. So,
you add that little snippet of code to the `<form>`
tag while deleting the `method` and `action`
attributes because you don't want to submit the form,
anymore, using the normal browser stuff. The method
that this will call will make an AJAX call. The
response of the AJAX call will determine if the user
get's logged in.

Again, those steps you take are:

1. Add the `ng-submit` attribute to the `<form>` tag;
1. Remove the `action` attribute from the `<form>`
   tag; and,
1. Remove the `method` attribute from the `<form>`
   tag.

```html
<!-- Using ng-submit to call a method on the controller -->
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

Now, you just need to add a controller.

## Define a Controller and `submitForm` method

Now that you've completed the modifications to the
HTML to get it all bound, it's time for you to tie
that to the JavaScript stuff.

Back in the `login-card.component.js` file, you need
a new object that will be the controller for the
component. You define a `class` at the top of the file
to act as the controller and give it a meaningful
name.

```javascript
// Define the controller class
class LoginCardController {
}

angular
  .module('app')
  .component('loginCard', {
    templateUrl: '/app/login-card/login-card.component.html',
    controllerAs: 'login'
  });
```

Now that you have a controller class, you need to
include it in the registration of the component
beneath it. You do that by adding a `controller`
property to the configuration object of the component.
Then, you set that property to an array that contains
a function that creates a new instance of the
`LoginCardController` class. That should looks like
this:

```javascript
class LoginCardController {
}

angular
  .module('app')
  .component('loginCard', {
    templateUrl: '/app/login-card/login-card.component.html',
    controllerAs: 'login',

    // Register the controller of the component
    controller: [() => new LoginCardController()]
  });
```

You kind of shake your head at that weird syntax and
remember that you're going to use that really soon to
be able to make AJAX calls.

Next, you need a method to handle when a person tries
to submit the form. In the HTML template, you set the
form tag with `ng-submit="login.submitForm()"` so you
need a `submitForm` method on the controller. For now,
you just want to make sure it works, so you have it
create a console message to show you the values of
`username` and `password` and, then, clear out the
values in the `<input>` fields.

```javascript
class LoginCardController {

  // Fancy new method to handle form submission!
  submitForm() {

    // Print the values bound from the <input> fields
    // to the properties on this controller
    console.log('username:', this.username);
    console.log('password:', this.password);

    // Set the values of the properties on this
    // controller which, because they're bound to the
    // <input> fields, will change the value of the
    // <input> fields and clear them out!
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

Finally, you want to make sure that the `username` and
`password` properties have empty strings as their
initial values. You create a constructor for your
controller and set those values appropriately.

```javascript
class LoginCardController {

  // A constructor to set the initial values of
  // username and password
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

Your smile becomes even larger after you refresh the
page. You type in some text into the username and
password fields of the form, click the "WATCH TIME"
button or hit Enter, and the console messages appear
with the values of the `username` and `password`
properties that the `<input>` fields are bound to!

## What Did You Do?

You rub your eyes a little bit because this was a lot
of work and a lot of new stuff. Breaking it down,

You used `ng-model` to *bind controller properties to
form fields*.

You used `ng-disabled` to *control the disabled state
of a button*.

You used `ng-submit` to *bind a form submission to a
controller method*.

You used the `controller` property to *register a
method that creates a controller object*, in this
case, a new instance of the `LoginCardController`
class.
