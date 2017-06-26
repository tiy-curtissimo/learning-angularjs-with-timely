# Review Pretty the Header

You did a great job at prettying the header! Here's
one way you could have done it.

In `main-screen.component.html`:

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
```
