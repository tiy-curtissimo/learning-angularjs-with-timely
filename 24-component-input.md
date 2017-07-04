# A Refactoring: Component Input

Something's been bothering you. All these components
that you've made are really special-purpose, specific
to Timely and nothing else. You figure some components
can be re-usable little blocks of code and behavior so
that you don't have to be bothered with details. You
figure you can hide complexity behind some custom tag
and never have to worry about it, again.

You look at Timely and, while it's pretty spiffy, you
notice the custom dropdown that Michaela has used on
the time entry page and the report page. Your use of
it on the report page looks like this.

```handlebars
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
```

This seems like the kind of HTML that you could put
behind a component and, then, not have to worry about
making sure you get the classes and structure correct
all the time.

## What's In a Dropdown?

In the case of this dropdown, you see a bunch of
markup, the collection to loop over
(`report.clients`),
the name of the property to display as text
(`client.name`), and the `ng-model` binding to handle
the two-way binding.

*Hunh*, you think, *can I use two-way binding on a
custom component?*

You go to [duckduckgo.com](https://duckduckgo.com) and
start doing some searching for how to apply `ng-model`
to a custom component. You find this great video
[Experimenting With ngModel And ngModelController In
AngularJS](https://vimeo.com/116336978) from Ben
Nadel. Unfortunately, it uses the lower-level
directive functionality of AngularJS, something you
don't want to go into because that could just get a
little *messy*.

Instead, you find the [Component-based application
architecture](https://docs.angularjs.org/guide/component#component-based-application-architecture)
documentation, again, and see the item in the bulleted
list that starts

> Components have a well-defined public API - Inputs
> and Outputs

That sounds about right. You can get data into your
custom dropdown component and send stuff back out. You
decide that this is the way to approach the problem
rather than trying to do the directive route and learn
a bunch more stuff that may distract you from your
goal. You make a note to go back and look at
[directives](https://docs.angularjs.org/guide/directive)
in the future.

## Stubbing Out the Component

You go through the motions of making a new component:

* create a directory named `fancy-drop-down` under the
  `app` directory;
* create a JavaScript file
  `fancy-drop-down.component.js` in the directory and
  register the component, create a controller
  `class` for it, and set the controller alias to
  `dropDown`;
* create an HTML file
  `fancy-drop-down.component.html` in the directory
  and copy over the relevant HTML; and,
* replace the HTML in `report.component.html` with
  `fancy-drop-down`; and,
* register the new component in the `common/spa.html`
  file.

You have some files, now, files that look like these.

```javascript
class FancyDropDownController {
}

angular
  .module('app')
  .component('fancyDropDown', {
    templateUrl: '/app/fancy-drop-down/fancy-drop-down.component.html',
    controller: [() => new FancyDropDownController()],
    controllerAs: 'dropDown'
  });
```

```html
<div class="drop-down-holder single-row-chooser-main">
  <div class="drop-down-decorator">
    <i class="material-icons">arrow_drop_down</i>
  </div>

  <!-- Get the <select> in here, somehow -->

</div>
```

After moving that over, you realize the layout is all
messed up, so you go poke in the `site.css` and see
that the `single-row-chooser-main` CSS class provides
width to the dropdown, so you put that on the
`<fancy-drop-down>` element in the
`report.component.html`.

```handlebars
<h1 ng-if="report.clients.length === 0" class="standalone-message">
  You must first create some clients.
</h1>
<div ng-if="report.clients.length > 0">
  <div class="single-row-chooser">

    <!-- You're so fancy, you should be in Hollywood! -->
    <fancy-drop-down class="single-row-chooser-main">

      <!-- How to get this <select> inside the
           component's markup? -->
      <select class="full-width" name="id" id="clientId" required
              ng-model="report.selectedClient"
              ng-options="client.name for client in report.clients">
        <option value=""></option>
      </select>
    </fancy-drop-down>

    <button ng-disabled="!report.selectedClient"
            ng-click="report.generate()"
            class="button">Generate report</button>
  </div>
  <table class="data-table" ng-if="report.data">
    <thead>
      <tr>
        <th class="data-table-main-column">Month</th>
        <th class="data-table-numeric">Hours worked</th>
      </tr>
    </thead>
    <tbody>
      <tr ng-repeat="row in report.data">
        <td class="data-table-main-column">{{ row.monthName }} {{ row.year }}</td>
        <td class="data-table-numeric">{{ row.numberOfHoursWorked }}</td>
      </tr>
    </tbody>
  </table>
</div>
```

Well, that's a lot to *grok*. You stare at the screen
for what feels like 20 minutes lost in thought about
what to do next. It looks ... huge, impossible, like
it'll take a Sisyphean effort to finish it....

*One step at a time*, you remind yourself. *Hello,
duckduckgo*.

## Getting the `<select>` to Render.

You decide to try to figure out how to pass the
`<option>` tags from the parent template into the
`<fancy-drop-down>` component. You try a myriad of
search terms, most of them not giving you what you
want. You start to get frustrated because, it should
not be this hard.

* "angularjs component pass through"
* "angularjs component include child components"
* "angularjs insert child elements"
* "angularjs component innerhtml"
* "angularjs container component"

Then, you run across something called "transclusion"
in that last search. You want to just get up and walk
away from it all. "Transclusion"?!?! Seriously? You
search for that and, sure enough, right there on the
component configuration object is a `transclude`
property that enables this feature. There's also an
`ng-transclude` attribute for where stuff goes.

You push yourself away from the desk and lean back in
your chair. At this moment, at this very moment, you
wonder if AngularJS is the right choice for this
project. You wonder to yourself why the authors of the
framework would create such an obtuse name for such an
important feature.

You momentarily wonder if continuing with this would
just create a sunk cost fallacy for your behavior. You
could give what you have to Michaela and tell her she
can figure it out and just continue using a
spreadsheet to keep your work.

After rubbing your eyes and working the tension from
your shoulders, you decide to continue with it, with
this stupid "transclusion" bit.

You modify your component files to include these newly
found dumbly named things and, everything works like
magic.

```javascript
class FancyDropDownController {
}

angular
  .module('app')
  .component('fancyDropDown', {
    templateUrl: '/app/fancy-drop-down/fancy-drop-down.component.html',
    controller: [() => new FancyDropDownController()],
    controllerAs: 'dropDown',

    // Transclude this...
    transclude: true
  });
```

```html
<div class="drop-down-holder single-row-chooser-main">
  <div class="drop-down-decorator">
    <i class="material-icons">arrow_drop_down</i>
  </div>

  <!-- I got some transclude for you, AngularJS! -->
  <div ng-transclude></div>
</div>
```

Hopefully, the rest of this isn't as dumb.

## In Which You Discover That Yes, It's Just as Dumb

You decide to parameterize the icon that the dropdown
uses because, well, it is **fancy**, after all. You
want the `<fancy-drop-down>` to show the drop down
arrow unless the HTML specifies differently. So

`<fancy-drop-down>...</fancy-drop-down>`

shows the arrow while

`<fancy-drop-down icon-name="pets">...</fancy-drop-down>`

shows a paw print instead of an arrow.

You go to the input and output section of the
components page of the Developer's Guide. There it
says that you have to specify *binding* and use some
symbol based on what kind of value you want to bind
to.

| Symbol | Kind of binding               |
|--------|-------------------------------|
| @      | A plain old string            |
| <      | One-way binding to a variable |
| =      | Two-way binding to a variable |

Well, you just want a string, so you decide to use the
@ symbol. You have to use the correct case for the
name of the property just like you did with the
component name, camel case in JavaScript, kebab case
in HTML.

You add the binding to the component configuration
object and a default value in the `$onInit` method.

```javascript
class FancyDropDownController {
  $onInit() {

    // The default icon to use is the boring one.
    this.iconName = 'arrow_drop_down';
  }
}

angular
  .module('app')
  .component('fancyDropDown', {
    templateUrl: '/app/fancy-drop-down/fancy-drop-down.component.html',
    controller: [() => new FancyDropDownController()],
    controllerAs: 'dropDown',
    transclude: true,

    // Create a binding for the component
    bindings: {

      // An input binding that accepts a plain old
      // string in the HTML
      iconName: '@'
    }
  });
```

Sure enough, that looks the same that it did before,
which is what you wanted.

![timely - report
page](https://tiy-corp-train.github.io/newline-media/learning-angularjs-with-timely/empty-report-page.png)

Then, you change the HTML to try out the paw print,
just on a lark, to see what that looks like.

```html
<!-- Make it pet friendly, not just for service animals. -->
<fancy-drop-down class="single-row-chooser-main" icon-name="pets">
  <select class="full-width" name="id" id="clientId" required
          ng-model="report.selectedClient"
          ng-options="client.name for client in report.clients">
    <option value=""></option>
  </select>
</fancy-drop-down>
```

![timely - report
page](https://tiy-corp-train.github.io/newline-media/learning-angularjs-with-timely/empty-report-page-with-paw-print.png)

That worked. But, you feel that weird binding language
is pretty stupid, using @, <, and = rather than
*words* or something.

You see that if you needed to pass the value of some
variable or some complex object instead of just a
string, you'd need to use the '<' symbol.

The documentation says to stay away from '=', so you
probably will.

## What Did You Do?

Well, mainly, you got frustrated by made-up words and
silly symbols. But, AngularJS works as advertised. You
realize, though, that you're going to have to practice
saying that word enough so that it becomes part of
your everyday speech so you can talk to others about
it.

"Transclusion."

"Transcloooooosion."

"Transclusion."

And, you'll have to keep those notes around to
remember the difference between '<', '@', and '=' for
input bindings.

«sigh»

Oh well.

You use your new component to replace the one on the
time entry screen, too. Yay, `<fancy-drop-down>`! You
may have some weird on the inside but, from the
outside, you're beautiful.
