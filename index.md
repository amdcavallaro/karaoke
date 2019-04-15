---
id: karaoke
summary: In this codelab, you'll build a Karaoke action using Actions on Google, DialogFlow, Cloud Functions and the YouTube API.
status: [draft]
authors: Amanda Cavallaro
categories: Assistant,GCP
tags: aogdevs,iwd19,wtm19
feedback link: https://github.com/amdcaruso/karaoke

---

# Google Assistant: Build your own karaoke

[Codelab Feedback](https://github.com/amdcaruso/karaoke)


## Introduction



Technology is the greatest equalizer of this century. You can build natural and rich conversational experiences, you can build a computer program that uses machine learning techniques to perform human-like conversations.

There are many use cases we can take advantage of the use of voice. In this codelab we will use Google Cloud Platform, the YouTube API and Actions on Google to create a karaoke action.

<aside class="special">

Looking for more? Check out the the official  [Actions on Google](https://developers.google.com/actions/extending-the-assistant) and  [Dialogflow](https://dialogflow.com/docs) documentation.
</aside>

### **What you will build**

### **What you'll learn**

* Build an Assistant action that consists of an Actions project, a Dialogflow agent with custom intents and entities, a webhook, and a Cloud Function.
* How to implement Action fulfillment using the Action on Google client library and the Dialogflow inline editor

### **What you'll need**

* A recent version of a browser of your choice, such as  [Chrome](https://www.google.com/chrome/).
* To be logged in your google account.


## Getting set up



### **Create a Dialogflow project**


Actions is how we name the apps for the assistant, we'll get started by creating a Dialogflow project

1. Open the  [Dialogflow Console](http://console.dialogflow.com).
2. Sign in with your Google account, if you haven't already
3. When you land on the Dialogflow page, check the box next to **Yes, I have read and accept the agreement** and click **Accept**. **(if it's the first time you're using Dialogflow)**
4. You should see the tool that resembles the image below, Dialogflow agent creation page. Name it  **karaoke** and click **CREATE**:  <img src="img/8dfd4dfc71ad9fb6.png" alt="8dfd4dfc71ad9fb6.png"  width="624.00" />


## Dialogflow: Build the conversation



### **Intent: Default Welcome Intent**

The welcome intent is the first message your users will get from your agent, usually a greeting. The welcome intent is also responsible for informing your users about their options and what they could do with your agent.

1. Click on intents
2. Click on "Default Welcome Intent" <img src="img/9df5b8bbc2aa30e3.png" alt="9df5b8bbc2aa30e3.png"  width="624.00" />
3. Scroll down to the **Responses **section
4. Click the **bin icon** to delete the default text responses <img src="img/584f65bfc02cac9.png" alt="584f65bfc02cac9.png"  width="624.00" />
5. Click on **ADD RESPONSES** > **Text response**, 
6. Type in the following:  *It's Karaoke time! What artist and song title are you searching for?* 
7. Now scroll all the way up and click **Save** in the top-right corner. 

You've just created the welcoming response to the first interaction of the users with your karaoke app.


## Songs entity



Entities are powerful tools used for keyword extraction. Let's create the songs entity. 

1. On the left menu click  *Entity* .
2. Click the blue button that says  *Create entity.* 
3. Name it  *song* .
4. Click the more (3 dots) button on the top right  <img src="img/9134a03fd34cae5d.png" alt="9134a03fd34cae5d.png"  width="624.00" />
5. Click  *Switch to raw mode* 
6. Paste the data below within the CSV tab

    "Hotel California","Hotel California"
    "Shooting Star","Shooting Star"
    "When I See You Smile","When I See You Smile"
    "No Matter What","No Matter What"
    "Walks Like A Woman","Walks Like A Woman"
    "No Sleep Till Brooklyn","No Sleep Till Brooklyn"
    "Loser","Loser"
    "In a Big Country","In a Big Country"
    "Rock Around the Clock","Rock Around the Clock"
    "AIN'T NO SUNSHINE","AIN'T NO SUNSHINE"
    "Cradle of Love","Cradle of Love"
    "Dancing With Myself","Dancing With Myself"
    "Eyes Without a Face","Eyes Without a Face"
    "My Life","My Life"
    "SHE'S ALWAYS A WOMAN","SHE'S ALWAYS A WOMAN"


7. Click save.
8. 🌟 You've created the  *song*  entity


## Artists entity



1. On the left menu click Entity.
2. Click the blue button that says Create entity.
3. Name it  *artist* .
4. Click the more (3 dots) button on the top right
5. Click Switch to raw mode
6. Paste the data below within the CSV tab

    "@sys.music-artist","@sys.music-artist"
    "Eagles","Eagles"
    "Supertramp","Supertramp"
    "Survivor","Survivor"
    "The Beach Boys","The Beach Boys"
    "The Beatles","The Beatles"
    "The Clash","The Clash"
    "The Doors","The Doors"
    "The Goo Goo Dolls","The Goo Goo Dolls"
    "The Offspring","The Offspring"
    "The Police","The Police"
    "The Pretenders","The Pretenders"
    "The Ramones","The Ramones"
    "The White Stripes","The White Stripes"
    "The Who","The Who"
    "Twisted Sister","Twisted Sister"
    "U2","U2"
    "Van Halen","Van Halen"
    "Whitesnake","Whitesnake"
    "ZZ Top","ZZ Top"

7. Click save.
8. 🌟 You've created the  *artist*  entity


## Intent: search



Let's create the intent that  parses user input for fields that are required by the Youtube Data API.

1. From the left-hand menu, click on the **+** icon by the **Intents** menu item. For the **Intent name** field, enter search.
2. In the **Training phrases** field click **Add Training Phrases**.
3.  In the Add user expression field enter **Backstreet boys - I want it that way **and hit **Enter**.
4. Select ** *Backstreet boys* ** and assign it the ** *@sys.artist * **entity.
5. Select ** *I want it that way * **and assign it the ** *@sys.song * **entity
6. Add more training phrases and assign the correct entities to the words. <img src="img/75f5448926077e54.png" alt="75f5448926077e54.png"  width="493.00" />
7. Now that your training phrases have been added, scroll down and expand the **Actions and parameters** section.
8. Now check the **REQUIRED** checkbox for the **artist** parameter. This tells Dialogflow to not trigger the intent until the parameter is properly provided by the user.
9. Now click on **Define prompts** for the number parameter (right-hand side) and provide a re-prompt phrase. Enter in Who sings it? in the prompt field and then click **Close**.  <img src="img/ce8e55f3a1a19125.png" alt="ce8e55f3a1a19125.png"  width="575.00" />
10. Now scroll to the top of the page and click **Save**.
11. Now scroll down to the **Fullfilment** section. 
12. Click the dropdown arrow and click **Enable fulfillment**. 
13. Then click the **Enable webhook call for this intent** slider: <img src="img/ba365cb2730dd15b.png" alt="ba365cb2730dd15b.png"  width="624.00" />
14. This tells Dialogflow to call your fulfillment to generate a response to the user instead of using Dialogflow's response feature.
15. In the **Responses** section right above fulfillment, click **ADD RESPONSE**. Then click on the Google Assistant tab.
16. Move the toggle for **Set this intent as end of conversation**. This tells Dialogflow to relinquish control back to the Google Assistant after your fulfillment returns a response to the user.  <img src="img/c5489f7bad23933e.png" alt="c5489f7bad23933e.png"  width="624.00" />
17. Then scroll to the top of the intent and click **SAVE** once more to save the entire intent.


## Youtube Data API



We will use the  [Youtube Data API v3](https://developers.google.com/youtube/v3/docs/search) for searching videos. You can find the methods supported in this link to better understand the queries used in our code.

### **Enable the API**

1. **Open the ** [GCP Console](https://console.cloud.google.com)
2. Open the Navigation menu and select **APIs & Services** > **Library**. 
3. In the search bar, type **Youtube Data API v3** and select it from the results page.
4. Click **Enable**.
5. Click the back button in your browser **twice** and ensure that your page resembles the following with the Youtube Data API enabled:

<img src="img/111500a0349b6062.png" alt="youtube-api.png"  width="624.00" />

### **Get an API Key**

To make Youtube Data API requests from your Cloud Function, you will need to login with your  [Google Account](https://www.google.com/accounts/NewAccount) to access the Google API Console, request an API key, and register your application.

1. Return to the  [GCP Console](https://console.cloud.google.com)
2. Open the Navigation menu.
3. From there, select **APIs & Services** > **Credentials**. 
4. Click on the **Create credentials** dropdown
5. Select **API Key**. You should see this input as below, with your generated key. <img src="img/af96b435786e7812.png" alt="api-key.png"  width="525.50" />
6. **Copy** the API key and save it somewhere for later use, (e.g a text editor or notepad). 
7. Click **Close**.


## Build the fulfillment webhook endpoint



When you set up your webhook, you pass information from a matched intent into a web service and get a result from it.

1. On the left menu click on: Fulfilment
2. Toggle on the  *Inline Editor(Powered by Cloud Functions for Firebase)* 
3. Copy the `index.js`  as seen below.

```
"use strict";

const {
  dialogflow,
  actionssdk,
  Image,
  Button,
  BasicCard,
  Table,
  Carousel
} = require('actions-on-google');

const functions = require('firebase-functions');
const app = dialogflow({ debug: true });

const API_KEY = "ADD_YOUR_API_KEY";

app.intent("search", (conv, {song, artist}) => {
  let query = `${artist} ${song}`;
  console.log(song);
  if (!song || song == 'undefined') {
    query = conv.query;
  }
  
  const url = "https://www.googleapis.com/youtube/v3/search?part=snippet&maxResults=5&q=" + encodeURIComponent(query + " karaoke|lyrics")+ "&type=video&order=relevance&videoCategoryId=10&key=" + API_KEY;

  console.log(url);
  const axios = require('axios');
    return axios.get(url)
        .then(response => {
          var output = JSON.stringify(response.data);
          var song_fields = response.data.items[1];
          return song_fields;
      }).then(output => {
                      var song_title = output.snippet.title;
          song_title = song_title.replace(/&amp;/g, '&');
          song_title = song_title.replace(/&quot;/g, '\"');
          var song_link = JSON.stringify(output.id.videoId);
          var song_thumbnail = output.snippet.thumbnails.high.url;
          conv.ask(`Finding your song...`);
      conv.ask(new BasicCard({
  title: `${song_title}`,
  buttons: new Button({
    title: `Let's sing`,
    url: `https://www.youtube.com/watch?v=${song_link.slice(1, -1)}`,
  }),
  image: new Image({
    url: song_thumbnail,
    alt: 'Song thumbnail'
  }),
  display: 'CROPPED',
}));
      conv.close(`${song_title}. Link: https://www.youtube.com/watch?v=${song_link.slice(1, -1)}. Enjoy! Bye`);
      });
});
exports.dialogflowFirebaseFulfillment = functions.https.onRequest(app);

```

1. Next, add your YOUTUBE API to the  `index.js` file (same as above):

```
const API_KEY = "ADD_YOUR_API_KEY";
```

1. Add the code snippet below to your  `package.json` file:

    {
     "name": "dialogflowFirebaseFulfillment",
      "description": "karaoke.",
      "version": "0.0.1",
      "author": "Amanda Cavallaro",
      "engines": {
        "node": "8"
      },
      "scripts": {
        "start": "firebase serve --only functions:dialogflowFirebaseFulfillment",
        "deploy": "firebase deploy --only functions:dialogflowFirebaseFulfillment"
      },
      "dependencies": {
        "actions-on-google": "^2.2.0",
        "firebase-admin": "^7.0.0",
        "firebase-functions": "^2.2.0",
        "dialogflow": "^0.8.0",
        "dialogflow-fulfillment": "^0.6.1",
        "axios": "0.16.2"
      }
    }

Check your Google Permission Settings

It's time to use the Actions simulator to begin testing your agent.

It is important to check that necessary permissions are enabled.

Visit the  [](https://myaccount.google.com/activitycontrols). 

Ensure that the following permissions are enabled:

* Web & App Activity
* Device Information
* Voice & Audio Activity

You can now **close** the Activity Controls page.

Test the action with the simulator

To test out your Action in the Actions console simulator:

1. In the  [](https://console.dialogflow.com/) left navigation, click on Integrations. 
2. Then, click on Google Assistant > Integration Settings.
3. Click Test to update your Actions project and load it into the Actions Console simulator. (If you see a ‘Check auto-preview setting' dialog, you can leave the ‘Auto-preview changes' option enabled, then click Continue.) <img src="img/e96ac29642231a05.png" alt="e96ac29642231a05.png" title=""Figure 3: Dialogflow Console (Testing your Action from Google Assistant Integration Settings.)""  width="624.00" />
4. To test your Action in the simulator, type "Talk to my test app" into the Input field and hit enter.

<img src="img/51f21d3f54b44d1d.png" alt="51f21d3f54b44d1d.png" title=""Figure 4: Launching the codelab Action with an explicit invocation in the simulator.""  width="624.00" />

Notice that when your Action's welcome intent is invoked, the Assistant responds with the greeting message and prompts the user to provide the music artist and song title.

Return to the Actions console. Then from the left-hand menu, under the TEST header click **Simulator**. You should now be on the following page:

<img src="img/10b7d49ec40d1157.png" alt="10b7d49ec40d1157.png"  width="508.00" />

Through the Actions simulator you are able test your action using an intuitive interface that lets you simulate hardware devices and their settings

Reply by type/ saying a music artist and a song such as Eagles - Hotel California. You should receive a similar output in the right-hand panel, which includes the song name, link, and thumbnail:

<img src="img/dbd26b4bd3b80917.png" alt="dbd26b4bd3b80917.png"  width="315.00" />

Try talking with your test app with new music artists and songs!


## Next Steps



Congratulations!

You built a karaoke action with Actions on Google.

#### What we've covered

* How to build a robust Google Assistant application
* How to use the firebase inline-editor and cloud functions
* How to handling Youtube Data API requests
* How to set up an Actions project using the Dialogflow and Actions Consoles.
* How to create a welcome intent so that users can start a conversation with your Action.
* How to create conversational responses using Dialogflow intents and a custom webhook.
* How to test your Action in real-time using the Actions Console simulator.
* How to declare your conversation grammar with Dialogflow intents

#### What's next

*  [actions.google.com](http://actions.google.com/): The official documentation site for Actions on Google.
*  [Actions on Google GitHub repo](https://github.com/actions-on-google/): Sample code and libraries.
*  [Dialogflow.com](https://dialogflow.com/): The official documentation site for Dialogflow.
*  [r/GoogleAssistantDev](https://www.reddit.com/r/GoogleAssistantDev/): The official Reddit community for developers working with the Google Assistant.

#### Credits

* This website was based on the qwiklabs: Google Assistant: Build a Youtube Entertainment App and Build Actions for the Google Assistant codelab 1.
*  [This](https://github.com/fivethirtyeight/data/blob/master/classic-rock/classic-rock-song-list.csv) data set was used for the songs and artists.


