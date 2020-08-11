# Kimball Analytics

Analytics with the Kimball application are designed to use a predictable amount
of CPU/memory, without risking details about individual users.

*note: See your local installation `/api-docs/index.html` for your particular API specification as it may differ from what is documented here.*

## Definitions

ID/User ID - The "high cardinality" ID to track for an event.
Event Name - The action completed by the user
Goal - A predetermined event that you wish to track that will collect events these users have also completed.

*note: User IDs are tracked only on the first occurrence. A user that performs an event twice will only show up once in the analytics.*

## Submitting Analytic Events

### While requesting feature flags

This can be done by including a `user_id` and `feature` in the `user_obj` and `context_obj` fields of a feature flag request. [Example](https://github.com/getkimball/example-python-application/blob/76d0cce51ecf14bb4c36bc928471390be464f5f9/example.py#L22-L28).

### Sending individual analytic events

Individual analytic events can be sent to `/v0/analytics` with `event_name` and `user_id` properties in the POST request body.

```
{
  "event_name": "string",
  "user_id": "string"
}
```

## Goals

Goals events are created with the API that will collect other events from that user when the user completes a goal event. This allows [funnel analysis](https://en.wikipedia.org/wiki/Funnel_analysis) of your application by tracking the user events leading up the goal.


### Create a goal

POST a `goal_name` to `/v0/goals` to create the goal

```
{
  "goal_name": "string"
}
```

### Retrieving Analytic Data

Analytic data can be retrieved via GET `/v0/analytics` which will return a list of analytic events seen, and details about each event.

* `name` - The event that was seen
* `count` -  The number of unique user IDs for this event
* `event_counts` - Data will only be included if this is a goal event. A list of:
    * `events` - A list of events a user completed before triggering this goal
    * `count` - The number of unique user IDs with this combination of events

### Example


A online store tracking feature usage of their store leading up to the first sale by a user might have the events

* `Sign up`
* `Login`
* `Searched for a product`
* `Browsed for a product`
* `Direct link to product`

With the goal of

* `Completed purchase`

When a purchase is completed the events a user has completed will be stored with the goal. As multiple users complete purchases, the goal event data may include combinations such as:

* [ `Sign up`, `Login`, `Searched for a product`] - X occurrences
* [ `Sign up`, `Login`, `Browsed for a product` ] - Y occurrences
* [ `Direct link to a product` ] - Z occurrences
