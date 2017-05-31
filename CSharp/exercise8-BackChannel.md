# Exercise 8: [Title] (C#)

## Introduction

[intro about back channel, events and ways to interact with bots]

Inside [this folder](./exercise8-BackChannel) you will find a solution with the code that results from completing the steps in this exercise. You can use this solution as guidance if you need additional help as you work through this exercise. Remember that for using it, you first need to build it by using Visual Studio and complete the placeholders of the LUIS Model and Azure Search Index name and key in Web.config.

## Prerequisites

The following software is required for completing this exercise:

* [Visual Studio 2017 Community](https://www.visualstudio.com/downloads/) or higher
* An [Azure](https://azureinfo.microsoft.com/us-freetrial.html?cr_cc=200744395&wt.mc_id=usdx_evan_events_reg_dev_0_iottour_0_0) subscription
* An account in the [LUIS Portal](https://www.luis.ai)
* The [Bot Framework Emulator](https://emulator.botframework.com/)

## Task 1: [Enable BackChannel]

[steps to get the secret backchannel key]

[replace the `default.htm` with [this](../assets/csharp-backchannel/default.htm) and put your secret keys]

[in `RootDialog` add `SendSearchToBackchannel` method and call it from `SubmitTicket`]

[maybe, show the code with the Observable subscription in  `default.htm` (L67-73)]

## Task 2: Test [Event to BackChannel]

[use the web chat]

[type _my computer is not working_]

[see the results]

## Task 3: [Update to sent messages to user]

[add `cursor: pointer;` in `results h3`'s `style` in  `default.htm` (bellow L28)]

[add `showDetailsOf` event to h3 titles `createSeachResult` in  `default.htm`]

[add the `HandleEventMessage` in `MessagesController` ]

    ```CSharp
    public class MessagesController : ApiController
    {
        private readonly AzureSearchService searchService = new AzureSearchService();

    ...
    ```

    ```CSharp
    private async Task HandleEventMessage(Activity message)
    {
        if (string.Equals(message.Name, "showDetailsOf", StringComparison.InvariantCultureIgnoreCase))
        {
            var searchResult = await this.searchService.SearchByTitle(message.Value.ToString());
            string reply = "Sorry, I could not find that article.";

            if (searchResult != null && searchResult.Value.Length != 0)
            {
                reply = searchResult.Value[0].Text;
            }

            // return our reply to the user
            Activity replyActivity = message.CreateReply(reply);

            ConnectorClient connector = new ConnectorClient(new Uri(message.ServiceUrl));
            await connector.Conversations.ReplyToActivityAsync(replyActivity);
        }
    }
    ```

[change `Post` method to handle Events]

    ```CSharp
    public async Task<HttpResponseMessage> Post([FromBody]Activity activity)
    {
        if (activity.Type == ActivityTypes.Message)
        {
            await Conversation.SendAsync(activity, () => new RootDialog());
        }
        else if (activity.Type == ActivityTypes.Event)
        {
            await this.HandleEventMessage(activity);
        }
        else
        {
            this.HandleSystemMessage(activity);
        }

        var response = Request.CreateResponse(HttpStatusCode.OK);
        return response;
    }
    ```

## Task 4: Test [BackChannel Event to conversation]

[follow the same workflow from the last test]

[click in any title]

[see the magic]

## Further Challenges

[TBD]