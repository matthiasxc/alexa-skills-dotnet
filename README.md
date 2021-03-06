# Alexa Skills SDK for .NET

[![Build status](https://ci.appveyor.com/api/projects/status/9r6nb0nlbykw5fh7?svg=true)](https://ci.appveyor.com/project/TimHeuer/alexa-skills-dotnet)

Alexa.NET is a helper library for working with Alexa skill requests/responses in C#.  Whether you are using the AWS Lambda service or hosting your own service on your server, this library aims just to make working with the Alexa API more natural for a C# developer using a strongly-typed object model.

# Some Quick Samples
Here are some *simple* examples of how to use this library assuming the default signature of the AWS Lambda C# function:
```csharp
public SkillResponse FunctionHandler(SkillRequest input, ILambdaContext context)
{
    // your function logic goes here
}
```
## Get the request type (Launch, Intent, etc)
You most likely are going to want to get the type of request to know if it was the default launch, an intent, or maybe an audio request.
```csharp
// check what type of a request it is like an IntentRequest or a LaunchRequest
var requestType = input.GetRequestType();

if (requestType == typeof(IntentRequest))
{
    // do some intent-based stuff
}
else if (requestType == typeof(Alexa.NET.Request.Type.LaunchRequest))
{
    // default launch path executed
}
```

## Get the intent and look at specifics
Once you know it is an IntentRequest you probably want to know which one (name) and perhaps pull out parameters (slots):
```csharp
// do some intent-based stuff
var intentRequest = input.Request as IntentRequest;

// check the name to determine what you should do
if (intentRequest.Intent.Name.Equals("MyIntentName"))
{
    // get the slots
    var firstValue = intentRequest.Intent.Slots["FirstSlot"].Value;
}
```

## Build a simple voice response
There are various types of responses you can build and this library provides a helper function to build them up.  A simple one of having Alexa tell the user something using SSML may look like this:
```csharp
// build the speech response 
var speech = new Alexa.NET.Response.SsmlOutputSpeech();
speech.Ssml = "Today is <say-as interpret-as=\"date\">????0922</say-as>.<break strength=\"x-strong\"/>I hope you have a good day.";

// create the response using the ResponseBuilder
var finalResponse = ResponseBuilder.Tell(speech);
return finalResponse;
```

## Build a simple response with a Card
In your response you can also have a 'Card' response, which presents UI to the Alexa companion app for the registered user.  Cards presently are simple and contain basically titles and plain text (no HTML :-().  To create a response with cards, you can use the ResponseBuilder:
```csharp
// create the speech response - cards still need a voice response
var speech = new Alexa.NET.Response.SsmlOutputSpeech();
speech.Ssml = "Today is <say-as interpret-as=\"date\">????0922</say-as>.";

// create the card response
var finalResponse = ResponseBuilder.TellWithCard(speech, "Your Card Title", "Your card content text goes here, no HTML formatting honored");
return finalResponse;

```
