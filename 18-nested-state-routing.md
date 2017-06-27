# Nested-State Routing

While you were looking through the documentation of
`ui-router`, you noticed something that you thought
might help you with the routing of the real
application parts: nested states. Instead of having
just one big state controlling the whole screen, you
read that you can use a state to control the screen
while its nested (or child) states can control other
parts of it.

You go back to the
[documentation](https://ui-router.github.io/guide/states#nested-states)
and reread the description of nested states.

> Child states can be used to drill down from a more
> general feature to more specific one, or to
> implement a master/detail pattern. For instance, you
> might have a parent state called contacts which
> defines the contacts module, and renders a list of
> all the contacts.
>
> Then, you might have two child
> states contacts.contact (to view a specific
> contact’s details) and contacts.new (to create a new
> contact). These substates share the common parent
> state contacts and inherit data and behavior from
> the parent. They are rendered inside the UI for the
> parent state.

While that makes sense, it's not totally clear what
the instructions are. You text your friend Alberto to
see if he know anything about this.

<style>
.callout-cool::before,
.callout-cool::after {
  background-image: none !important;
}
</style>
[callout-cool]
*You*: Hey, Alberto, I have a question.

*Alberto*: whats up?

*You*: I wanted to know about nested states in
ui-router.

*Alberto*: yeah, no prob - like what?

*You*: Like how to use them?

*Alberto*: oh! ezpz! you create a nested state when
you give it a name that is a dot-suffix of another
state's name

*Alberto*: say you have a state named 'book'

*Alberto*: you can make a child state by naming it
'book.shelf'

*Alberto*: you can create another child state by
naming it 'book.club'

*Alberto*: ui-router will put the child states view
into a ui-view in the template of the parent state

*You*: Ok, so if I understand this correctly...

*Alberto*: notime! archer just started! chat soon!
[/callout-cool]

You reread Alberto's messages and start nodding to
yourself. Right now you have a `main` state. If you
create child states of that, say `main.entries`, then
that can be the state for the entries page. And, if
you create a state named `main.clients`, then that
can be the state for the clients list page. And, if
you create a state named `main.report`, then that
could be the state of the report page!

You're so excited you can barely contain yourself! You
set to creating some new states in the `app.config.js`
file.

```javascript
angular
  .module('app')
  .config([
    '$stateProvider',
    '$urlServiceProvider',
    '$locationProvider',
    ($stateProvider, $urlServiceProvider, $locationProvider) => {
      // ...loginState and signUpState
      // still defined here...

      // Oh, no! Poor mainScreen lost its "url"
      // property because time entries should show
      // at the URL "/"
      const mainScreen = {
        name: 'main',
        component: 'mainScreen'
      };

      // HELLO, NESTED STATES!
      const timeEntriesState = {
        name: 'main.entries',
        url: '/',
        component: 'timeEntries'
      };

      const clientsListState = {
        name: 'main.clients',
        url: '/clients',
        component: 'clientsList'
      };

      const reportState = {
        name: 'main.report',
        url: '/report',
        component: 'report'
      }

      $stateProvider.state(loginState);
      $stateProvider.state(signUpState);
      $stateProvider.state(mainScreen);

      // All nice and registered are you, now?
      $stateProvider.state(timeEntriesState);
      $stateProvider.state(clientsListState);
      $stateProvider.state(reportState);

      $locationProvider.html5Mode(true);
      $urlServiceProvider.rules.otherwise({ state: 'login' });
    }
  ]);
```

Now, you create those three components,
`time-entries`, `clients-list`, and `report`. In each
of those components, you declare and register them
with the appropriate name, and put differentiating
text in each of the component HTML files. (No, really,
you go create them, now. :-)

For example, in `time-entries`, you put in this
`time-entries.component.js`

```javascript
class TimeEntriesController {}

angular
  .module('app')
  .component('timeEntries', {
    templateUrl: '/app/time-entries/time-entries.component.html',
    controllerAs: 'timeEntries',
    controller: [() => new TimeEntriesController()]
  });
```

and this in `time-entries.component.html`

```html
<h1>Here be Time Entries!</h1>
```

[callout-warning]
Did you remember to put a new `<script>` tag for each
of the new components' JavaScript files in the
`spa.html` file?
[/callout-warning]

Looking back at what Alberto wrote to you, you see
that these nested states will show up in a `<ui-view>`
element in the parent state. The parent state for
these three new nested states is the `main` state; you
go add a `<ui-view>` viewport to the
`main-screen.component.html` file.

```html
<header class="app-bar">
  <h1 class="app-bar-title">Timely</h1>
  <div class="app-bar-spacer"></div>
  <button class="button" ng-click="mainScreen.logout()">Sign out</button>
</header>
<nav class="tabs">
  <ul class="tabs-list">
    <li class="tabs-item tabs-item-selected"><a href="/">Today's Work</a></li>
    <li class="tabs-item"><a href="/clients">Clients</a></li>
    <li class="tabs-item"><a href="/report">Report</a></li>
  </ul>
</nav>

<!-- A new way to see the world! -->
<ui-view></ui-view>
```

ARRRGH! Even though you followed Alberto's sparse
instructions, it doesn't seem to work! You log into
the application and don't see a nested state at all!

Wait.

When you click on the tabs, they actually show up...

You realize you must have missed something...

You take a moment to try to figure it out without the
help of some non-existent narrator...

(Okay, maybe there are some hints from this
narrator...)

[reveal]
Look at the URL after you log in...
[/reveal]

[reveal]
It didn't change to a new URL. It still reads
'/session/new'.
[/reveal]

[reveal]
What state did this just transition to after you
logged in?
[/reveal]

[reveal]
Go look in `login-card.component.js` to see what state
this transitioned to.
[/reveal]

[reveal]
That's right, it went to the 'main' state. Which
state should it go to?
[/reveal]

[reveal]
It should go to the new state that you created,
'main.entries'. So you change it and everything works,
now.
[/reveal]

Now that you fixed that bug, you realize you should
also fix it in `sign-up-card.component.js`, too.

## Time to Fix the Tabs

Now, you want the tabs to work correctly. Right now,
they work using `href` attributes. While that's fine,
it's not the recommended way to change states as you
know from creating the `registration-cta` component.
You need to change them from `href` attributes to
`ui-sref` attributes that refer to the correct state.
You open the `main-screen.component.js` file and
change the `href`s.

```html
<header class="app-bar">
  <h1 class="app-bar-title">Timely</h1>
  <div class="app-bar-spacer"></div>
  <button class="button" ng-click="mainScreen.logout()">Sign out</button>
</header>
<nav class="tabs">
  <ul class="tabs-list">

    <!-- Welcome, ui-srefs! Good-bye hrefs! -->
    <li class="tabs-item tabs-item-selected"><a ui-sref="main.entries">Today's Work</a></li>
    <li class="tabs-item"><a ui-sref="main.clients">Clients</a></li>
    <li class="tabs-item"><a ui-sref="main.report">Report</a></li>

  </ul>
</nav>
<ui-view></ui-view>

```

The tabs still work correctly when you click on them.
What does not work is the selected tab. You head over
to the [ui-router
documentation](https://ui-router.github.io/guide/ng1/route-to-component#break-out-more-components),
again, and find the answer in the example. The
`ui-router` provides a `ui-sref-active` attribute, the
value of which gets applied as a CSS class name to the
element that it's on for the `<a>` tag that has the
`ui-sref`. You change the `main-screen.component.js`
file to use the `ui-sref-active`.

```html
<header class="app-bar">
  <h1 class="app-bar-title">Timely</h1>
  <div class="app-bar-spacer"></div>
  <button class="button" ng-click="mainScreen.logout()">Sign out</button>
</header>
<nav class="tabs">
  <ul class="tabs-list">

    <!-- I promise that, when I become President,
         there will be a chicken in every pot and a
         ui-sref-active on ever <li>! -->
    <li class="tabs-item" ui-sref-active="tabs-item-selected"><a ui-sref="main.entries">Today's Work</a></li>
    <li class="tabs-item" ui-sref-active="tabs-item-selected"><a ui-sref="main.clients">Clients</a></li>
    <li class="tabs-item" ui-sref-active="tabs-item-selected"><a ui-sref="main.report">Report</a></li>
  </ul>
</nav>
<ui-view></ui-view>
```

You lean back in your Herman Miller and stretch your
arms and feel the glow of a job well done spread
across you. You whisper to yourself, "You did good,
kid."

## What Did You Do?

With the help of your friend Alberto and the
`ui-router` documentation, you discovered how to
use the nested states feature of `ui-router`. From
reading posts on Stack Overflow, you know this is
no small achievement! You learned how to

* declare nested states;
* transition to nested states; and,
* use a `<ui-view>` in the parent state to show the
  nested states.

You prettied up the navigation, as well, by adding
the `ui-sref-active` attribute to each of the `<li>`
tags that held the `<a>` tags that had the `ui-sref`
attribute on them.

Now, you want to get the application working by
showing data. To get the data, you know that you
should create services, so that's what you've decided
to do next.
