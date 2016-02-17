# A reactive bot for Slack commands

## Introduction
This application demonstrates use of Mulesoft studio and ESB.
Main highlights:
* Integrates 4 services APIs 
 * slack command API, 
 * the weather API of www.wunderground.com,
 * Google geolocation API, and 
 * Yahoo stock quote API.
* No additional code, only studio elements and their configurations used.
* Proper port configuration for a cloudhub account.

### Selected scenario
Provide custom commands for Slack team channels, which help bringing into a channel on-demand information about:
- The weather for a given city.
- The stock information for a given stock.

### Technologies
Used:
* Vanilla Anypoint™ Studio, 
* Mule ESB’s graphical user interface (GUI), 
* Basic free CloudHub account.

## Running the scenario
The application runs on a free account in CloudHub. As such one needs to do very little to use it:

1. Go to `https://mulerson.slack.com/messages/general/`.
2. Use the demo user `reviewer` with password `mulesoft234`
3. Type `/muleweather London` in the chat and press Enter key. You may change London to any other city you like.
4. Expect to see the temperature in London.
5. Type `/mulequote TSLA` in the chat and press Enter key. You may provide any other stock symbol you like.
6. Expect to see the stock quote for TSLA symbol.

In the logs (local or CloudHub) one may see more detailed trace of what has happened in the application.

## Architecture

### Integrated services 
In this application we integrate 3 public services using their APIs.

The Slack service offers flexible team messaging. It offers customizable commands API, which can bring various external context at the finger tips for the user, right inside the channel.

I have selected to bring in Slack the following commands:
* Weather information - using the Weather Underground free REST API.
* Locate the country of a city - using the Google free geolocation API. Needed for the weather information.
* Stock quotes - using the Yahoo stock quote REST API.  

### Design
The app does not use any custom code - the generic components seemed sufficient.

* Used the HTTP connector to provide the bot service listening to particular requests from Slack.
* Used the Expression component to select significant properties from the HTTP request parameters and keep them into the `flowVars` map.
* Used a Choice flow control element to fork the logic into the weather command and all the rest.
* Used a Flow reference to structure the logic in readable blocks.
* Used a sub-flow scope element to provide a readable logic for the weather service call and response processing.
 * Used the HTTP connector to request the weather service.
 * Used the JSON to Object transformer to parse the JSON result
 * Used the Set Payload transformer to set the result payload, and used an expression therein to extract the value of interest, in this case the temperature in Celsium units.
 * Used the Logger component for diagnostic and audit purposes.

## Setting up a local project
### Run as mule application
1. Execute the project in Studio, or the command line using maven.
2. Make sure the machine can accept requests from the Internet (use public machine, or proxy, or tunnel, or load ballancer).
3. Make sure your firewall allows for the `http.port` value defined in the `mule-app.properties` file. 

Alternatively, deploy to your account in CloudHub. The deployer there will inject the proper listening port, in this case `80`.

### Slack setup
A slack team account offers custom command extension API.

1. One needs to set up a team and register with the service. 
2. Register two new custom integration of type Slash command. We support `/muleweather` and `/mulequote`.
3. Configure the URL that each command calls.

### Weather channel setup
I have selected the Weather Underground service because it offers its API for small scale use for free.

1. Setup an account.
2. Purchase the $0 option to get an API key.
3. Update the global property "wunderground.api.key" to contain the proper API key.

