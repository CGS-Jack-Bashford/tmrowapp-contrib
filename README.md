# tmrowapp-contrib [![Slack Status](http://slack.tmrow.com/badge.svg)](http://slack.tmrow.com) [![CircleCI](https://circleci.com/gh/tmrowco/tmrowapp-contrib.svg?style=shield)](https://circleci.com/gh/tmrowco/tmrowapp-contrib)

Welcome to the open-source repository of the Tomorrow app!👋

## What is the Tomorrow app?
The [Tomorrow](https://www.tmrow.com) app automatically calculates your carbon footprint by connecting to other services and apps in your life and translating activities from these apps and activities to greenhouse gas emissions.

The app is private-by-design: data will stay on device, unless the user explicitly gives consent. This code is maintained by [Tomorrow](https://www.tmrow.com).

Feel free to watch [the presentation](https://www.youtube.com/watch?v=keOPXD-ojWY) our Founder Olivier gave to the CopenhagenJS meetup, explaining what a JavaScript developer can do to combat climate change. If you have any question, want early access to the app or just want to hang out with people fighting climate change with code, join [our Slack community](https://slack.tmrow.com).

## Structure of this repository

- `./co2eq`: carbon models
- `./integrations`: contains all integrations
- `./integrations/img`: contains all integration logos
- `./playground`: source code of the playground
- `./definitions.js`: constant definitions


## How can I help?
You can help by:

- Helping us find, add and improve integrations to 3rd party services
- Helping us find, add and improve our Life Cycle Assesment / Carbon footprint data
- Giving us ideas, feedback and reporting bugs

### Integrating purchases and activities
[We wrote a little article about what we believe makes a great integration](https://tmrow.slite.com/api/s/note/8LLSWazeBZZyS4BEQiLTnJ/What-makes-a-great-integration-for-Tomorrow).
However, that shouldn't stop you from doing an integration which is interesting and useful to you! Our only requirement is that it can be quantified in greenhouse gas emissions.

Integrations can rely on an API or even on scrapers if necessary.

#### Suggesting an integration
Here is the list of current 3rd party integrations:
- ✈️ Tripit (tracks most airlines!)
- ✈️ Ryanair (contributor:[lauvrenn](https://github.com/lauvrenn))
- ✈️ Wizzair (contributor:[lauvrenn](https://github.com/lauvrenn))
- 🚂 Rejsekort
- ⚡ Sense (contributor:[snarfed](https://github.com/snarfed))
- ⚡ Linky (contributor:[bokub](https://github.com/bokub))
- ⚡ Barry
- ⚡ Ørsted
- 🚗 Renault Zoé
- 🚗 Tesla Cockpit
- 🚗 Uber (contributor:[willtonkin](https://github.com/willtonkin))
- 🚗 Automatic (contributor:[lauvrenn](https://github.com/lauvrenn))

You can [suggest a new integration here](https://github.com/tmrowco/tmrowapp-contrib/issues/new).

#### Coding or debugging a new integration

If you don't have an idea on your own or prefer to debug an integration, you can find integration suggestions and bugs in [the issues](https://github.com/tmrowco/tmrowapp-contrib/issues).

To make it easy for anyone to help out, a development playground is available:

From the `playground` folder, run `yarn` to install dependencies, then run `yarn serve` to start the playground and point your browser to [localhost:3000](http://localhost:3000) to get started.

#### How to make an integration work
An integration gathers activities from a 3rd party datasource.
To this end, 3 async methods need to be exported:

```javascript
async function connect(requestLogin, requestWebView) {
  const { username, password } = await requestLogin();
  // ...
  return newState;
}
async function collect(state = {}, logger) {
  // ...
  return { activities, state: newState };
}
async function disconnect() {
  // ...
  return newState;
}
```

The `connect` method is used to ask for user credentials (OAuth flows are also supported).
The `collect` method is called periodically (typically every few hours) to fetch new activities.
As the methods are pure, and to avoid re-asking the user for credentials everytime the `collect` method is called, a `state` object can be used to persist information (such as password, tokens..) across `collect`s.

Activities require a certain formatting:

##### Transportation activity formatting
```javascript
{
  id, // a string that uniquely represents this activity
  datetime, // a javascript Date object that represents the start of the activity
  durationHours, // a floating point that represents the duration of the activity in decimal hours
  distanceKilometers, // a floating point that represents the amount of kilometers traveled
  activityType: ACTIVITY_TYPE_TRANSPORTATION,
  transportationMode, // a variable (from definitions.js) that represents the transportation mode
  carrier, // (optional) a string that represents the transportation company
  departureAirportCode, // (for plane travel) a string that represents the departure airport, IATA code
  destinationAirportCode, // (for plane travel) a string that represents the final destination airport, IATA code
  departureStation, // (for other travel types) a string that represents the original starting point
  destinationStation, // (for other travel types) a string that represents the final destination
}
```
##### Electricity consumption activity formatting
```javascript
{
  id, // a string that uniquely represents this activity
  datetime, // a javascript Date object that represents the start of the activity
  durationHours, // an integer that represents the duration of the activity
  activityType: ACTIVITY_TYPE_ELECTRICITY,
  energyWattHours, // a float that represents the total energy used
  hourlyEnergyWattHours, // (optional) an array of 24 floats that represent the hourly metering values
  locationLon, // the location of the electricity consumption
  locationLat, // the location of the electricity consumption
}
```

### Adding or updating Life Cycle Assessment / Carbon Footprint of purchases and activities

Our current models and Life Cycle assessments are accessible [here](https://github.com/tmrowco/tmrowapp-contrib/tree/master/co2eq). If you know better sources, please contribute with your knowledge.

### Giving ideas, features requests or bugs

Please [add an issue here](https://github.com/tmrowco/tmrowapp-contrib/issues/new) or directly in the app.
