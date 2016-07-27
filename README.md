# Multi Tenancy with Slack API and Mule HTTP Connector
This project is an example to showing how to create a multi tenancy Mule Application with the HTTP Connector and using the Slack API

## Pre Requisites
### Slack API Credentials
Due that we are using the Slack API for this example, you will require Slack **User Id** and **User Secret**.

#### Create Slack Application
* Log in into Slack -> https://slack.com/signin
* Once logged into Slack go to https://api.slack.com/apps and create a new Slack Application.
* Fill all the required information: name, description, etc, etc. But ***NOT*** forget the **Redirect URL**: http://0.0.0.0:8081/redirect-url
* After created the new Slack App, go again to https://api.slack.com/apps/ and select your created application and select from the Left Menu **App Credentials**
* And finally in the **OAuth Information** block you will find your **User Id** and **User Secret** credentials.

### Demo usage
#### Configure credentials
Inside the project exist a file in the following path: `/src/main/app/mule-app.properties` and with this content:
```
slack.clientId=COMPLETE
slack.clientSecret=COMPLETE
```
In this file you will need to fill the copied credentials from the **Create Slack Application** section.

#### Start Application
##### In Anypoint Studio
Just Run project

##### In Mule Standalone
* Using a maven command line, go to the project root and execute `mvn clean package`
* In the `target` folder the `slack-oauth2-http-connector-and-multi-tenancy-1.0.0-SNAPSHOT.zip` will be created.
* Copy the ZIP file into the `mule-standalone-3.X.X/apps` folder
* Finally start the Mule server executing `./mule` from `mule-standalone-3.X.X/bin` folder.

#### Use Application
##### Application Endpoints
The application will contain the following endpoints:

| Endpoint | Query Params | Purpose |
| - | - | - |
| 0.0.0.0:8081/login | **userId**: ID of the user to bind with the new Slack login | This endpoint will redirect you to the Slack API login page|
| 0.0.0.0:8081/redirect-url |  | This endpoint is not planned to be used manually, this is the endpoint which Slack will use to grant the **Access Token**|
| 0.0.0.0:8081/auth.test | **userId**: ID of the user to test the Slack authentication| This will try to verify the authentication against Slack API of the given **userId**.

##### Step by Step
* Once the application is deployed correctly, hit `http://0.0.0.0:8081/login?userId=1`
* * This endpoint will redirect you to the Slack Login page, enter your credentials and login.
* * With the `userId` query param, we are telling to the HTTP Connector, that the generated **Access Token** should be binded with the identifier `1`.
* * If everything is executed correctly, after the Login the message `Successfully retrieved access token` will appear.
* Hit `0.0.0.0:8081/auth.test?userId=1` and you will respond with the following JSON:
```JSON
{
    "ok": true,
    "url": "https:\/\/mulesoft.slack.com\/",
    "team": "MuleSoft",
    "user": "esteban.wasinger",
    "team_id": "T0XXXXXXX",
    "user_id": "U0XXXXXXX"
}```
and in the application log:
`org.mule.api.processor.LoggerMessageProcessor: This is the Slack Access Token --> xoxp-222222222-33333333-444444444-555555555`
