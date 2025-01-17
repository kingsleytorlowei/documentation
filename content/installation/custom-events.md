---
title: "Custom Events"
metaTitle: "Custom Events"
metaDescription: "How to send domain-specific events and add additional intelligence to session recordings."
---

Custom events are great for adding more intelligence by recording domain-specific events alongside session replays. OpenReplay makes use of 2 types of events: functional (`event`) and technical (`issue`). All events are indexed for easy search, and sync'ed with session recordings.

## Functional Events

Use the `event` method to signal functional events such as *order completed* or *product added*. It takes 2 parameters: *name* (string) and *payload* (JSON object).

```js
tracker.event('product_added', 'shoes');
```

Functional events are indexed and makes looking for specific session recordings easier. If they were successfully received by OpenReplay, they will be available as filters in the omni-search bar.

![Functional Event](../static/custom-events-1.png#center)

## Technical Events

`issue` is used to send technical events, like errors, that may occur in your stack or other downstream systems. `issue` takes 2 parameters: *name* (string) and *payload* (JSON object).

```js
tracker.issue('payment_error', {code: 500, context: 42});
```

Technical events are shown in session replay under the *Events* tab in DevTools, and as annotations on the playback. They're also taken into account in Funnels for correlating conversion drops with technical issues.

![Technical Event](../static/custom-events-2.png#center)
