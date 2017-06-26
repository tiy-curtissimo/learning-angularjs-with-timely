# Nesting Components

You really enjoyed creating those components. But, as
you look at your login page, you see two components
living on the same page, the `login-card` and the
`registration-cta` components. They really belong
together since someone using the software should only
see them together according to the current UI design.

You decide to create a parent component for these two
components as an exercise in discipline and to explore
how difficult it would be.

## Create Files for a `login-page` Parent Component

You create the `login-page` directory with the
`login-page.component.js` and
`login-page.component.html` files in it.

You immediately go include it in the
`session/login.html` page.

```html
  <script src="/app/registration-cta/registration-cta.component.js"></script>
  <script src="/app/login-card/login-card.component.js"></script>

  <!-- The new parent component -->
  <script src="/app/login-page/login-page.component.js"></script>
</body>
```

## Create JavaScript for the `login-page` Parent Component

In the `login-page.component.js` file, you just
declare an AngularJS component and point it to the
corresponding `.html` file.

```javascript
angular
  .module('app')
  .component('loginPage', {
     templateUrl: '/app/login-page/login-page.component.html'
  });
```

## Create HTML for the `login-page` Parent Component

In the `login-page.component.html` file, you take the
HTML from `session/login.html` and make it the content
of the component's `.html` file.

```html
<login-card class="card login-card"></login-card>
<registration-cta class="card login-card"></registration-cta>
```

Then, you replace those two lines in
`session/login.html` with your new component.

```html
<body ng-app="app">

  <!-- This replaces the login-card and
       registration-cta components -->
  <login-page></login-page>

  <script src="/js/angular-1.6.4.min.js"></script>
  <script src="/js/angular-resource-1.6.4.min.js"></script>
```

You save your files and Everything Just Works!

## What Did You Do?

You experimented with AngularJS components and created
a simple parent component to render two other
AngularJS components.

You smile at how easy that seemed. You decide it's
time to tackle something potentially harder.
