# Review the Time Entry Page

Oh, my Goodness, you found bugs in your code. That's
*crazy*! But, it happens, and you worked through it
without an issue. You look at the completed page and
really like how quickly it responds to your inputs
plus the added feature of hiding the "START" button
when the person using Timely hasn't yet selected a
client.

## `time-entries.component.html`

You look back over the work you did, starting with the
`time-entries.component.html` file. You recognize that
you have started to find it easier to read, understand,
and use the `ng-` stuff in AngularJS. That pleases you.

```html
<!-- Show a message if there are no clients -->
<h1 ng-if="timeEntries.clients.length === 0"
    class="standalone-message">
  You must first create some clients.
</h1>

<!-- Show the table if there are clients -->
<div ng-if="timeEntries.clients.length > 0">
  <table class="data-table">
    <thead>
      <tr>
        <th class="data-table-main-column">Client</th>
        <th class="data-table-numeric">Start</th>
        <th class="data-table-numeric">End</th>
      </tr>
    </thead>
    <tbody>

      <!-- Loop over each row of data from the server -->
      <tr ng-repeat="row in timeEntries.data">
        <td class="data-table-main-column">
          {{ row.client.name }}
        </td>

        <!-- Format the times using the date filter -->
        <td class="data-table-numeric">
          {{ row.fromTime | date:"h:mm a" }}
        </td>

        <td ng-if="row.toTime"
            class="data-table-numeric">
          {{ row.toTime | date:"h:mm a" }}
        </td>
        <td ng-if="!row.toTime"
            class="data-table-numeric">
          <button class="button-primary"
                  ng-click="timeEntries.complete(row.id)">
            Finish
          </button>
        </td>
      </tr>
      <tr>
        <td class="data-table-main-column">
          <div class="drop-down-holder">
            <div class="drop-down-decorator">
              <i class="material-icons">arrow_drop_down</i>
            </div>
            <select class="full-width"
                    name="client.id"
                    id="clientId"
                    required
                    ng-model="timeEntries.selectedClient"
                    ng-options="client.name for client in timeEntries.clients">
              <option value=""></option>
            </select>
          </div>
        </td>
        <td class="data-table-numeric">

          <!-- Use ng-show to only show the button when
               the person has selected a client from
               the drop-down. -->
          <button ng-show="timeEntries.selectedClient"
                  ng-click="timeEntries.createNewEntry()"
                  class="button-cta">
            Start
          </button>
        </td>
      </tr>
    </tbody>
  </table>
</div>
```

## `time-entries.component.js`

This is the most complex component that you've written
to date, and you feel good about it. It seems lean and
mean.

```javascript
class TimeEntriesController {
  constructor(clientsData, timeEntriesData) {
    this.clientsData = clientsData;
    this.timeEntriesData = timeEntriesData;
  }

  $onInit() {
    this.clientsData
      .getAll()
      .then(clients => this.clients = clients);
    this.timeEntriesData
      .getAll()
      .then(data => this.data = data);
  }

  complete(entryId) {
    this.timeEntriesData
      .complete(entryId)
      .then(completedEntry => {
        let index = this.data.findIndex(e => e.id === entryId);
        this.data[index] = completedEntry;
      });
  }

  createNewEntry() {
    this.timeEntriesData
    .create(this.selectedClient.id)
    .then(data => {
      this.data = data;
      this.selectedClient = null;
    });
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

## `time-entries-data.service.js`

And, here's where you found the bugs, in the data
service, a misspelling and a wrong payload creation.
You ruefully shake your head at the inspirations that
caused you to write that silly code.

```javascript
class TimeEntriesDataService {
  constructor($http) {
    this.$http = $http;
  }

  getAll() {
    return this.$http
      .get('/api/entries')
      .then(response => response.data);
  }

  complete(id) {
    return this.$http
      .post('/api/entries/completions', { id })

      // Spell "response" correctly, next time!
      .then(repsonse => repsonse.data);
  }

  create(clientId) {
    return this.$http

      // Create the payload { id: clientId }, silly!
      .post('/api/entries', { client: { id: clientId } })
      .then(response => response.data);
  }
}

angular
  .module('app')
  .factory('timeEntriesData', [
    '$http',
    ($http) => new TimeEntriesDataService($http)
  ]);
```

## The Spring Template

And, you replaced all of the content in
`entries/index.html` with the usual little snippet.

```handlebars
{{> common/spa }}
```

## What Did You Do?

You practiced all of the skills that knowledge that
you've learned! And, you know that there's just one
more to go and you're done!
