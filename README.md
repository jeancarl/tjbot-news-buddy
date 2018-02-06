# A Talking TJBot

This lab will show the components of programming a TJBot to speak headlines using the Watson Text to Speech, Watson Discovery News, and Watson Speech to Text services.

## Text to Speech

In this section, we'll program the TJBot to speak.

1. Copy the following code into the code editor

	```
	var tj = new TJBot(["speaker"],
	  {
	    robot: {
	      gender: "female"
	    },
	    speak: {
	      language: "en-US"
	    }
	  },
	  {
	    text_to_speech: {
	      username: "--username--",
	      password: "--password--"
	    }
	  }
	);
	```

2. [Create a Watson Text to Speech service](https://ibm.biz/catalog-text-to-speech) in IBM Cloud.

3. Replace the values of `--username--` and `--password--` with the service credentials for the Watson Text to Speech service.

4. We've configured the TJBot to speak. Add the code below the instantiation of the TJBot object.

	```
	tj.speak("hello world");
	```

5. Click on the play icon to run the code. TJBot speaks the phrase *hello world* in English.

## Discovery News

In this section, we will use the Watson Discovery News service to search for headlines about our favorite sports team and have TJBot speak the first headline.

1. Add the following code above the instantiation of the TJBot object to use the [Watson Node.js SDK](https://ibm.biz/watson-node-sdk).

	```
	var DiscoveryV1 = require("watson-developer-cloud/discovery/v1");

	var discovery = new DiscoveryV1({
	  username: "--username--",
	  password: "--password--",
	  version_date: "2017-09-01"
	});
	```

2. [Create a Watson Discovery service](https://ibm.biz/catalog-discovery) in IBM Cloud.

3. Replace the values of `--username--` and `--password--` with the service credentials for the Watson Discovery service.

4. Add the following function below the instantiation of the TJBot object. When this function is called, it will get a headline from the Watson Discovery service and speak it out.

	```
	function speakTeamHeadlines(team) {
	  discovery.query({
     	 environment_id: "system",
        collection_id: "news",
        query: team,
        count: 1
     }, function(err, data) {
        tj.speak(data.results[0].title)
     });
	}
	```

5. Add a call to the function, with your favorite sports team as the argument.

	```
	tj.speak("hello world").then(() => {
		speakTeamHeadlines("san francisco giants");
	});
	```

## Speech to Text

In this section, we'll program TJBot to use the Speech to Text service to recognize voices and use what is heard to search for news.

1. Instruct TJBot that it has a microphone that it can use. Add `microphone` to the list of hardware.

	```
	var tj = new TJBot(["speaker","microphone"],
	  {
	    robot: {
	      gender: "female"
	    },
	```

2. [Create a Watson Speech to Text service](https://ibm.biz/catalog-speech-to-text) in IBM Cloud.

3. Under the credentials section in the code, add another property with the key speech_to_text and the object containing the username and password for the Speech to Text service.

	```
	var tj = new TJBot(["speaker"],
	  {
	    robot: {
	      gender: "female"
	    },
	    speak: {
	      language: "en-US"
	    }
	  },
	  {
	    text_to_speech: {
	      username: "--username--",
	      password: "--password--"
	    },
	    speech_to_text: {
	      username: "--username--",
	      password: "--password--"
	    }
	  }
	);
	```

4. Replace the values of `--username--` and `--password--` with the service credentials for the Watson Speech to Text service.

5. Update the speakTeamHeadlines with two additional method calls, to stop listening before speaking, and to resume listening after TJBot is finished speaking.

	```
	function speakTeamHeadlines(team) {
	  discovery.query({
        environment_id: "system",
        collection_id: "news",
        query: team,
        count: 1
     }, function(err, data) {
        tj.stopListening();
        tj.speak(data.results[0].title).then(() => {
	        tj.listen(processText);
	     });
     });
	}
	```

6. Add the processText function to handle the text that is processed.

	```
	function processText(text) {
		speakTeamHeadlines(text);
	}
	```

7. Change the first greeting to instruct TJBot to start listening after the introduction.

	```
		tj.speak("Hi. I'm T. J. Bot and I can search headlines for your favorite sports team. What is your favorite team?").then(() => {
			tj.listen(processText);
		});
	```
