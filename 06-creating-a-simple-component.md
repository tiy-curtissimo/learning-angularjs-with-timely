# Your First Component: The "Not a user?" Box

The first component that you'll create is the
replacement for the "Not a user?" box on the login
screen.

![timely - the registration call-to-action box](https://tiy-corp-train.github.io/newline-media/learning-angularjs-with-timely/sign-up-link-box-highlighted.png)

From this, you can learn the bare minimum that it
takes to make an AngularJS component. There's nothing
fancy, here, no input fields or lists of things or
magical buttons. Just some text and a link. Nice and
easy, see?

## Creating the Component's Files

The best practices of AngularJS would have you create
a new subdirectory for each of our components named
after the component itself.

You recognize that this is a "registration call to
action" component, so you decide to call it
`registration-cta` in which you create the two files
for the component, a `.js` file that will hold the
configuration and behavior (none for this example),
and the `.html` file that will hold the HTML for the
component.

![timely - created registration-cta files](https://tiy-corp-train.github.io/newline-media/learning-angularjs-with-timely/created-registration-cta-files.png)

With those files in place, you *immediately go to the
`login.html` file and add a new `<script>` tag!*

```html
  <script src="/js/angular-ui-router-1.0.3.min.js"></script>
  <script src="/app/app.module.js"></script>

  <!-- Include your new component! -->
  <script src="/app/registration-cta/registration-cta.component.js"></script>
</body>
```

## Defining the Component

Defining a component in AngularJS takes two steps:
register it in JavaScript and put some HTML in a
corresponding file. You start with the registration
step, first.

### Registering the Component

You open the `registration-cta.component.js` file. You
remember [reading about
components](https://docs.angularjs.org/guide/component)
in the AngularJS Developer Guide, so you start typing
as you exercise your memory.

First, you get the application module out of the
`angular` object using the `module` function.

```javascript
// In registration-cta.component.js
angular
  .module('app') // This returns the app module
```

Then, you use the component function of the module to
declare a new component.

```javascript
// In registration-cta.component.js
angular
  .module('app')
  .component('registrationCta', { // This will
                                  // register the
  });                             // component.
```

You smile because you remember the crazy AngularJS
naming scheme that everyone hates.

[callout-info] Assume that you have something that you
want to write in AngularJS that represents a progress
bar.

When you register that component with AngularJS in
your JavaScript code, you use [camel
case](https://en.wikipedia.org/wiki/Camel_case)
naming. That means you'll register it like
`progressBar` in JavaScript.

When you refer to it in your HTML, you use [kebab
case](https://lodash.com/docs#kebabCase) so it looks
like an HTML tag. That means, in HTML, you'd refer to
in like `<progress-bar>`.
[/callout-info]

The final step in registering this simple component is
to tell AngularJS from which file it can get the
associated HTML. So, you put the entry in registration
object.

```javascript
// In registration-cta.component.js
angular
  .module('app')
  .component('registrationCta', {
    // Tell AngularJS where to go get the template.
    templateUrl: '/app/registration-cta/registration-cta.component.html'
  });
```

### Specifying the HTML for the Component

If you look inside `login.html`, you see that the
current HTML for this portion of the UI looks like
this:

```html
<div class="card login-card">
  <div class="card-content">
    Not a user?
  </div>
  <div class="card-actions">
    <a class="button button-primary" href="/users/new">Sign up!</a>
  </div>
</div>
```

You know that you're going to replace that outer
`<div>` with the component tag, so you copy and paste
the content of the `<div>` element into the
`registration-cta.component.html` file for your
component.

```html
<!-- In registration-cta.component.html -->
<div class="card-content">
  Not a user?
</div>
<div class="card-actions">
  <a class="button button-primary" href="/users/new">Sign up!</a>
</div>
```

Now, you go back to the `login.html` page and remove
the old HTML and replace it with yours. That means
instead of the `<div class="card login-card>` nonsense
that you had, you'll replace all of that with this.

```html
<registration-cta class="card login-card"></registration-cta>
```

When you refresh the page, you see nothing has changed
visually. That's what you expected. But, then, you
open the developer tools in your browser and smile
when you see that AngularJS has inserted the contents
of your component's HTML.

![timely - component in inspector](https://tiy-corp-train.github.io/newline-media/learning-angularjs-with-timely/component-in-inspector.png)

## What Did You Just Do?

In this lesson, you built your very first AngularJS
component. It didn't do much, but it shows you that
you can extract portions of your Web page and create
a component from it that AngularJS can then put on
your Web page for you. You did this by

1. Creating a directory for your component;
1. Creating a `.js` and `.html` file for you
component;
1. Adding a new `<script>` tag that includes the
component's `.js` file.
1. Declaring the component with its name in
**camelCase** and registering the component using the
`component` method of the `angular.module`;
1. Adding the component's HTML to its `.html` file;
and,
1. Adding the newly created custom tag to the the
Web page.
