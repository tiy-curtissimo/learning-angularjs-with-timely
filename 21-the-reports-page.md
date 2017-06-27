# The Reports Page

You have everything you want to start building some of
the *real* pages in the application. You decide to
tackle the page that shows reports.

## Reviewing the Report Screen

You check your notes and remember that the report
screen has a drop down with a list of the logged-in
person's clients, a button to kick off the report
generation, and the actual report.

![timely - report
page](https://tiy-corp-train.github.io/newline-media/learning-angular-with-timely/report.png)

You decide to tackle this in phases:

1. Use the client data service to get the person's
   clients.
1. Show an informative message if the person using
   Timely has no clients.
1. Show the client dropdown if the person has some
   clients and a disabled button.
1. Enable the button when the person selects a client.
1. React to the button click and get report data.
1. Show the report data.

## Inject and Use the Client Data Service

You open `report.component.js` and tell AngularJS that
you want the client data service provided for the
controller.

```javascript
class ReportController {
  constructor(clientsData) {
    this.clientsData = clientsData;
  }
}

angular
  .module('app')
  .component('report', {
    templateUrl: '/app/report/report.component.html',
    controllerAs: 'report',
    controller: [
      'clientsData',
      (clientsData) => new ReportController(clientsData)]
  });
```

Now that your component has a data service to get
client data, you want to use it.

In your reading, yesterday, you remember something
about component lifecycles in the AngularJS Developer's
Guide. You do a quick search and find it on the
*Components* page in the [Component-based application
architecture](https://docs.angularjs.org/guide/component#component-based-application-architecture)
section. There, you read:

> **Components have a well-defined lifecycle** \
> Each
> component can implement "lifecycle hooks". These are
> methods that will be called at certain points in the
> life of the component. The following hook methods can
> be implemented:
>
> * `$onInit()` - Called on each controller after all
>   the controllers on an element have been constructed
>   and had their bindings initialized (and before the
>   pre & post linking functions for the directives on
>   this element). This is a good place to put
>   initialization code for your controller.
> * ... more ...

That `$onInit()` looks like the right place to use the
client data service to get the client data. You
implement that method on the `ReportController` and
save the returned client data to a property named
clients.

```javascript
class ReportController {
  constructor(clientsData) {
    this.clientsData = clientsData;
  }

  // Hello, initializer!
  $onInit() {
    this.clientsData
      .getAll()
      .then(clients => this.clients = clients);
  }
}
```

You think it'd be nice to see what that returns, so you
open `report.component.html` and replace its content
with

```handlebars
{{ report.clients }}
```

You open a new browser window, type in
[http://localhost:5000](http://localhost:5000) and log
in using the username "maria" with password "maria".
Then, you click on the "REPORT" tab and see the result
of the data passed back from the client.

```json
[
  {"id":22,"name":"Allie's Sweets","isActive":true},
  {"id":21,"name":"Bret's Refrigeration","isActive":true},
  {"id":20,"name":"Zantina Unlimited","isActive":true}
]
```

That's awesome!

You click the "SIGN OUT" button, refresh the page, and
log back in using the username "hector" and the
password "hector". You click ont he "REPORT" tab and
see an empty list returned.

```json
[]
```

## Show an Informative Message About Not Having Clients

If "hector" doesn't have any clients, he should see the
helpful message that he has no clients. To see what
Michaela used in her template, you open the
`report/index.html` file under the `templates` section.
On line 30, you see where she displays the message for
someone that has no clients. You copy the HTML and
replace the content of `report.component.html` with it.

```html
<h1 class="standalone-message">
  You must first create some clients.
</h1>
```

You only want that message to appear if there are no
clients. To do conditional display like that, you
recall that AngularJS provides the `ng-if` attribute
directive thing which works kind of like a JavaScript
`if` statement. You use the `ng-if` to show the message
only when their are no clients.

```html
<h1 ng-if="report.clients.length === 0" class="standalone-message">
  You must first create some clients.
</h1>
```

When you look at the report page for "hector", you see

![timely - AngularJS showing no clients](https://tiy-corp-train.github.io/newline-media/learning-angular-with-timely/ng-if-on-report-screen.png)

and when you look at it for "maria", you see nothing.
The `ng-if` works as advertised! You thank the
AngularJS Overlords and move on to your next story.

## Show a Client Dropdown and a Disabled Button

You go back into Michaela's HTML and find the part that
shows the chooser from lines 36 - 54 in
`report/index.html`. You grab that stuff and put it
inside a `<div>` so you can control it with an `ng-if`.

```html
<h1 ng-if="report.clients.length === 0" class="standalone-message">
  You must first create some clients.
</h1>
<div ng-if="report.clients.length > 0">
  <div class="single-row-chooser">
    <div class="drop-down-holder single-row-chooser-main">
      <div class="drop-down-decorator">
        <i class="material-icons">arrow_drop_down</i>
      </div>
      <select class="full-width" name="id" id="clientId" required>
        <option value=""></option>
        {{#clients}}
          {{#isSelected}}
            <option value="{{ id }}" selected>{{ name }}</option>
          {{/isSelected}}
          {{^isSelected}}
            <option value="{{ id }}">{{ name }}</option>
          {{/isSelected}}
        {{/clients}}
      </select>
    </div>
    <button class="button-cta">Generate report</button>
  </div>
</div>
```

Now, you need to change it to use AngularJS mechanisms
rather than `mustache` mechanisms to populate the
`<select>` element. You do a quick
[duckduckgo.com](http://duckduckgo.com) search and find
that AngularJS has the `ng-options` attribute
directive. You go to its [documentation
page](https://docs.angularjs.org/api/ng/directive/ngOptions)
and learn that you can use a statement like "*label*
**for** *value* **of** *array*" to populate the
`<option>`s for the `<select>` element. You also see
that the documentation says that you can leave an empty
`<option>` in there, if you want to. So, you choose to
do that.

You see that the documentation states that you have to
have an `ng-model` on the `<select>`, too, for
`ng-options` to work. So, you add that on there, too,
and set it to a property named `selectedClient` which
seems like a good name.

```html
<h1 ng-if="report.clients.length === 0" class="standalone-message">
  You must first create some clients.
</h1>
<div ng-if="report.clients.length > 0">
  <div class="single-row-chooser">
    <div class="drop-down-holder single-row-chooser-main">
      <div class="drop-down-decorator">
        <i class="material-icons">arrow_drop_down</i>
      </div>

      <!-- Hello, ng-model to bind a value!
           Hello, ng-options to make <option>s! -->
      <select class="full-width" name="id" id="clientId" required
              ng-model="report.selectedClient"
              ng-options="client.name for client in report.clients">
        <option value=""></option>
      </select>

    </div>
    <button disabled class="button">Generate report</button>
  </div>
</div>
```

## Enable the Button When Client is Selected

You realize that this is like the disabling of the
button on the login form. Except, instead of a string,
you will enable or disable the button based on the
length of a string value, you will base it on whether
the `selectedClient` property has a value.

Because your JavaScript-fu is strong, you change that
`disabled` property on the button to an `ng-disabled`
property and set it equal to
`not-falsey-value-of-selectedClient`.

```html
<h1 ng-if="report.clients.length === 0" class="standalone-message">
  You must first create some clients.
</h1>
<div ng-if="report.clients.length > 0">
  <div class="single-row-chooser">
    <div class="drop-down-holder single-row-chooser-main">
      <div class="drop-down-decorator">
        <i class="material-icons">arrow_drop_down</i>
      </div>
      <select class="full-width" name="id" id="clientId" required
              ng-model="report.selectedClient"
              ng-options="client.name for client in report.clients">
        <option value=""></option>
      </select>
    </div>

    <!-- Button on when selectedClient is truthy.
         Button off when selectedClient is falsey. -->
    <button ng-disabled="!report.selectedClient" class="button">Generate report</button>
  </div>
</div>
```

## Getting the Report When the Button Gets Clicked

You want to capture the click of the "GENERATE REPORT"
button, but you are having a brain fart. You can't
remember how to do it, but you remember doing it for
the "SIGN OUT" button. You open the
`main-screen.component.html` file and see that it's
`ng-click`.

`ng-click`! That's right!

You set up the `<button>` to call a method named
`generate` on the controller.

```html
      </select>
    </div>

    <!-- Call generate() on the controller when clicked! -->
    <button ng-disabled="!report.selectedClient"
            ng-click="report.generate()"
            class="button">Generate report</button>
  </div>
</div>
```

You open the corresponding `.js` file for the
component, `report.component.js`, and add the
`generate()` method.

You realize you've come to the point where you need
AngularJS to give you the report data service. You
change the registration to ask for it and take care of
saving it in the constructor.

```javascript
class ReportController {
  constructor(clientsData, reportData) {
    this.clientsData = clientsData;
    this.reportData = reportData;
  }

  $onInit() {
    this.clientsData
      .getAll()
      .then(clients => this.clients = clients);
  }

  generate() {

  }
}

angular
  .module('app')
  .component('report', {
    templateUrl: '/app/report/report.component.html',
    controllerAs: 'report',
    controller: [
      'clientsData',
      'reportData',
      (clientsData, reportData) => new ReportController(clientsData, reportData)]
  });
```

You look back at the report data service to see how you
defined it. You see that you need to call the `create`
method on it and pass the `id` property of the client.

You add that to the `generate` method and store the
data you get from the server in a property named
`data`.

```javascript
  generate() {
    this.reportData
      .create(this.selectedClient.id)
      .then(data => this.data = data);
  }
```

## Showing the Report Data

You go back to `report.component.html` and dump the
value of the data onto the page by adding `{{
report.data }}` at the end of the HTML. You select a
client, click the "GENERATE REPORT" button, and JSON!

Under the default seed data, *maria*'s report for the
client Zantina Unlimited returns this data.

```json
[
  {"month":1483250400852,"monthName":"January","numberOfHoursWorked":22,"year":"2017"},
  {"month":1485928800852,"monthName":"February","numberOfHoursWorked":13,"year":"2017"},
  {"month":1488348000852,"monthName":"March","numberOfHoursWorked":6,"year":"2017"},
  {"month":1491022800852,"monthName":"April","numberOfHoursWorked":3,"year":"2017"},
  {"month":1493614800852,"monthName":"May","numberOfHoursWorked":2,"year":"2017"},
  {"month":1496293200852,"monthName":"June","numberOfHoursWorked":1,"year":"2017"}
]
```

You delete the `{{ report.data }}` from the page and
open Michaela's `report/index.html` file, again. In
that file, you see that lines 58 - 73 contain the HTML
for the table. You copy and paste that into
`report.component.html` as the last child of the
`<div>` that shows stuff only when the user has
clients.

```html
<h1 ng-if="report.clients.length === 0" class="standalone-message">
  You must first create some clients.
</h1>
<div ng-if="report.clients.length > 0">
  <div class="single-row-chooser">
    <div class="drop-down-holder single-row-chooser-main">
      <div class="drop-down-decorator">
        <i class="material-icons">arrow_drop_down</i>
      </div>
      <select class="full-width" name="id" id="clientId" required
              ng-model="report.selectedClient"
              ng-options="client.name for client in report.clients">
        <option value=""></option>
      </select>
    </div>
    <button ng-disabled="!report.selectedClient"
            ng-click="report.generate()"
            class="button">Generate report</button>
  </div>

  <!-- Hello, report table!!! I am soooo close! -->
  <table class="data-table">
    <thead>
      <tr>
        <th class="data-table-main-column">Month</th>
        <th class="data-table-numeric">Hours worked</th>
      </tr>
    </thead>
    <tbody>
      {{#report}}
        <tr>
          <td class="data-table-main-column">{{ monthName }} {{ year }}</td>
          <td class="data-table-numeric">{{ numberOfHoursWorked }}</td>
        </tr>
      {{/report}}
    </tbody>
  </table>
</div>
```

The `{{#report}}` and `{{/report}}` stuff is how
`mustache` does looping, so you delete that and
re-indent the HTML so it looks right.

```html
  <!-- Hello, report table!!! I am soooo close! -->
  <table class="data-table">
    <thead>
      <tr>
        <th class="data-table-main-column">Month</th>
        <th class="data-table-numeric">Hours worked</th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <td class="data-table-main-column">{{ monthName }} {{ year }}</td>
        <td class="data-table-numeric">{{ numberOfHoursWorked }}</td>
      </tr>
    </tbody>
  </table>
</div>
```

You only want the table to appear if you have data to
show, so you put an `ng-if` on the `<table>` to only
show if you have data.

```html
  <table class="data-table" ng-if="report.data">
```

In AngularJS, you've already seen that you are supposed
to use the `ng-repeat` attribute directive when you
want things to repeat. You want it to repeat rows of
the table, specifically, the `<tr>`s inside the
`<tbody>`. You add the `ng-repeat` and fix the output
in the `<td>`s to reference the looping variable.

```html
<tbody>

  <!-- Repeat until you're done, little rows of data! -->
  <!-- Each entry in the data will be put into the row
       variable. -->
  <tr ng-repeat="row in report.data">

    <!-- You reference the row variable, here, to get
         the values onto the page! -->
    <td class="data-table-main-column">{{ row.monthName }} {{ row.year }}</td>
    <td class="data-table-numeric">{{ row.numberOfHoursWorked }}</td>
  </tr>
</tbody>
```

Now that you have it all finished, you go back to
Michaela's `report/index.html`, delete the content, and
replace it with

```handlebars
{{> common/spa }}
```

BECAUSE YOU'RE DONE!

## What Did You Do?

Oh, my, you did so much!

* You used your custom data services by having
  AngularJS inject them into your component.
* You used the component lifecycle method `$onInit` to
  populate initial information from the client data
  service.
* You used `ng-if` to show and not show HTML based on
  the value of properties of the controller.
* You used `ng-options` and `ng-model` on a `<select>`
  to bind the selected value to a property on the
  controller.
* You used the selected value to call a data service.
* You used the `ng-repeat` to show data from the data
  service.

That's an entire AngularJS education on one page! Wow!

You take a deep breath and decide to take a small
break. You look over at your goldfish whose eyes seem
to tell you *good job!*
