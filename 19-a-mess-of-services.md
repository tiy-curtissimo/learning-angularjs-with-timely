# Services! Services! Services!

You decide that before you get on with the creation of
the child states for entering time, managing clients,
and generating reports that you want to write the data
services for each of those states. To do this, you
need to look at Michaela's code.

Inside the `com.theironyard.timeliness.controllers`
package, you see the API controllers that she's
created for just such a need.

![timely - list of API controllers](https://tiy-corp-train.github.io/newline-media/learning-angular-with-timely/api-controllers.png)

You decide to tackle the API for time entries, first.

## Designing the Data Service for Time Entries

You open the `TimeEntriesApiController.java` file and
look at the mappings and methods of the controller
class.

```java
@RestController
@RequestMapping("/api/entries")
public class TimeEntriesApiController {

  // Constructor and private fields

  @GetMapping("")
  public List<WorkSpan> getEntryForm(Authentication auth, Model model) {
    // Stuff
  }

  @PostMapping("/completions")
  public WorkSpan completeWorkSpan(Authentication auth, @RequestBody WorkSpan span) {
    // More stuff
  }

  @PostMapping("")
  public List<WorkSpan> startWorkSpan(Authentication auth, @RequestBody WorkSpan span) {
    // Java, java, java
  }

}
```

From the code, you note the following API.

| Method | Path                     | Return value           | Parameter               |
|--------|--------------------------|------------------------|-------------------------|
| GET    | /api/entries             | a list of `WorkSpan`s  |                         |
| POST   | /api/entries/completions | a completed `WorkSpan` | a `WorkSpan`            |
| POST   | /api/entries             | a list of `WorkSpan`s  | a new `WorkSpan` to add |

You also go look at this `WorkSpan` class that gets
referenced in this API. Ignoring the JPA annotations,
you see the following data.

```java
public class WorkSpan {

  private Long id;

  private TimeWatcher watcher;

  private Client client;

  private Date fromTime;

  private Date toTime;

  // Constructors
  // Getters and setters

}

```

Playing around with Timely, you know that it sets the
`fromTime` to the current time when the `WorkSpan`
gets created. You also know that the `toTime` gets set
when you call the `/api/entries/completions` ReST
endpoint. JPA handles the `id` and the authentication
should handle the `watcher` fields. Armed with this
knowledge, you scratch down a design for a time
entries data service.

| Method   | Parameter                | Return value if successful             |
|----------|--------------------------|----------------------------------------|
| getAll   |                          | a list of time entries for the day     |
| complete | the id of the time entry | the updated time entry                 |
| create   | the id of the client     | the list of all the day's time entries |

## Coding the Time Entries Service

You create a new directory named `time-entries-data`
under the `app` directory and create a file in it
named `time-entries-data.service.js`.

![timely - create the time-entries-data.service.js file](https://tiy-corp-train.github.io/newline-media/learning-angular-with-timely/create-time-entries-data.service.js-file.png)

Then, you *immediately* add it to the `common/spa.html`

This data service will rely on the `$http` service, so
you write in the familiar boilerplate code that goes
into the AngularJS files.

```javascript
class TimeEntriesDataService {
  constructor($http) {
    this.$http = $http;
  }
}

angular
  .module('app')
  .factory('timeEntriesData', [
    '$http',
    ($http) => new TimeEntriesDataService($http)
  ]);
```

### Implementing the `getAll` Method

You start off easy enough writing the `get` portion of
the `getAll` method in the `TimeEntriesDataService`
class. But, you find yourself asking, "After invoking
the `get` method of the `$http` object, what is this
thing actually returning?"

```javascript
class TimeEntriesDataService {
  constructor($http) {
    this.$http = $http;
  }

  getAll() {
    return this.$http
      .get('/api/entries')
      // now what?
  }
}
```

You head over to the [AngularJS $http
documenatation](https://docs.angularjs.org/api/ng/service/$http)
and see right under the "General usage" section that

> the response object has these properties:
> * **data** – {string|Object} – The response body
>   transformed with the transform functions.
> * **status** – {number} – HTTP status code of the
>   response.
> * **headers** – {function([headerName])} – Header getter
>   function.
> * **config** – {Object} – The configuration object that
>   was used to generate the request.
> * **statusText** – {string} – HTTP status text of the
>   response.

That **data** property looks pretty promsing, but
what's up with the part about "the transform
functions"? You search the documenation and, further
down the page, you find the section "Transforming
Requests and Responses". In there, you read

> If the `Content-Type` is `application/json` or the
> response looks like JSON, deserialize it using a
> JSON parser.

Well, you figure that since the `@RestController` in
Spring serializes its data as `application/json`, that
`data` property on the response must be the list of
time entries that you want!

You finish implementing the `getAll` method with that
knowledge.

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
}

angular
  .module('app')
  .factory('timeEntriesData', [
    '$http',
    ($http) => new TimeEntriesDataService($http)
  ]);
```

### Implementing the `complete` Method

You know that you need to send at least the `id` of
the time entry to Michaela's service so that it knows
which time entry to mark complete and set an end time
for that time entry. With that knowledge, you knock
out the `complete` method of your data service.

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
      .then(repsonse => response.data);
  }
}
```

### Implementing the `create` Method

You refer to your design table and see that you need
to send the client id to Michaela's RESTful service
to create a new time entry. Feeling good about how
quickly you're knocking out the code, you drive it
home with the following implementation.

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
      .then(repsonse => response.data);
  }

  create(clientId) {
    return this.$http
      .post('/api/entries', { client: { id } })
      .then(response => response.data);
  }
}
```

## What Did You Do?

In this lesson, you worked in more than just the
front-end, you did some full-stack development! You

* read an existing Java controller;
* extracted the API from the controller;
* designed a data service; and,
* investigated the `$http` service to implement your
  data service.

You remind yourself, though, there are two more
data services to go...
