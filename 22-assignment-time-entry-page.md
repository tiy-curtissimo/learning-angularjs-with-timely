# The Time Entry Page

![timely - the time entry page](https://tiy-corp-train.github.io/newline-media/learning-angularjs-with-timely/timely-work-1.png)

You see the time entry page and, all of a sudden,
everything seems to snap into place about how to build
it.

Like the reports page, you see you'll need the clients
data service and, of course, the time entries data
service.

You see that if the person has no clients, you'll want
to show a nice message telling them to go make some
clients.

You see that, when the component initializes, you want
the component to use the client service and the time
entries data service to get the list of clients and
list of entries, respectively.

You see that, if a time entry has no end time, that you
will want to show a button and that, when clicked, will
call a method on the controller that will call the
`complete` method fo the time entries service with the
`id` value of the time entry.

You see that, after rendering all the entries for the
day, you will want to create a dropdown and a start
button that should work a lot like the report screen.
When the person clicks the start button, then it should
call a method on the controller that will call the
`create` method of the time entry service.

A little `ng-if`, a sprinkle of `ng-repeat`, a dash
of `ng-click` and you think you can get this worked
out in no time!

## Hints, If You Need Them

You can't get the data services into the time entry
component?

[reveal]
You need to do some dependency injection.

```javascript
class TimeEntriesController {
  constructor(clientsData, timeEntriesData) {
    this.clientsData = clientsData;
    this.timeEntriesData = timeEntriesData;
  }
}

angular
  .module('app')
  .component('timeEntries', {
    templateUrl: '/app/time-entries/time-entries.component.html',
    controllerAs: 'timeEntries',
    controller: [
      'clientsData',
      'timeEntriesData',
      (clientsData, timeEntriesData) => new TimeEntriesController(clientsData, timeEntriesData)]
  });
```
[/reveal]

Forgot how to show something based on a condition?

[reveal]
Use `ng-if`: <a href="https://docs.angularjs.org/api/ng/directive/ngIf">https://docs.angularjs.org/api/ng/directive/ngIf</a>
[/reveal]

Forgot how to handle a button click?

[reveal]
Use `ng-click`: <a href="https://docs.angularjs.org/api/ng/directive/ngClick">https://docs.angularjs.org/api/ng/directive/ngClick</a>
[/reveal]

Forgot how to loop over a list?

[reveal]
Use `ng-repeat`: <a href="https://docs.angularjs.org/api/ng/directive/ngRepeat">https://docs.angularjs.org/api/ng/directive/ngRepeat</a>
[/reveal]
