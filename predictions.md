# Kimball Predictions

Predications in the Kimball App use the [analytics](/analytics.md) data to generate [Bayesian conditional probabilities](https://en.wikipedia.org/wiki/Bayesian_statistics).

Predictions are available at `/v0/predictions` in your local installation.

Probabilities are returned in the response for goals that have at least 1 user that has completed an event before the goal. Goals with 0 users or users that have completed no other events are not included in the response.

## Example output

For a hypothetical set of data consisting of

Events:
  * Example Event A
  * Example Event B

Goals:
  * Example Goal Event

the prediction API might return:

```
{
  "goals":{
    "Example Goal Event": {
      "events":{
          "Example Event A": {"bayes": 0.5},
          "Example Event B": {"bayes": 0.25}
      }
    }
  }
}
```

This response displays the information:

* There is a 50% probability that users who complete `Example Event A` will complete `Example Goal Event`
* There is a 25% probability that users who complete `Example Event B` will complete `Example Goal Event`
