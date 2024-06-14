# Send SMS without external services

##  Intro
Not everyone uses the Home Assistant Companion on their phones. But sometimes it would be nice to be able to send messages to those people. 

When researching options, I found several ways of sending either text messenges or use messenging services like Telegram. All of them seems too complex and relied on external services like Twillio. The disadvantage is the need to create a seperate phone number other people won't recognise and the dependency on and external cloud server. 

So I was looking for a way to send SMS text messages directly from my own phone unsing my own phone number. Here is how I got it to work

## Prerequisites
* This guide only applies to Android Phone. Sorry I don't own an iPhone and won't be looking for ways to make it work on those.
* You do need to be running the Home Assistant Companion App on your own phone. The people you send the messages to don't need to use this app.
* [Tasker](https://play.google.com/store/apps/details?id=net.dinglisch.android.taskerm&hl=nl). One of the most versatile apps out there and my go-to companion for years to tweak my phone and have repeating tasks automated. The free version should work fine, but the paid version will allow you to do so much more for just a small amount of money
* Make sure you have granted tasker the `Send Text Messages` permission

## Setting up tasker
Tasker has the option to send sext messages built in. All you need to do is create a task to send a text message and a profile that will run that task. 
I've already created both, so you can use them by importing the [tasker_sendsms.xml](https://github.com/Dixet/homeassistant_projects/blob/main/home%20assistant%20sms/tasker_sendsms.xml) file into tasker. Importing is done by long-pressing the bottom bar and choosing Import Project.

You can also create the profile and task easily yourself

### Creating the Tasker task
In tasker go to the Tasks tab and press the plus sign. Enter a name for your task, for instance "Send Text Message".
In the task details press the plus sign to create the first and only step of the task. In the list that pops up find and select `Send SMS` (you can find it under the `Phone` section, or just start typing SMS and it will be in the list)

![Screenshot of the Tasker SMS task](/home%20assistant%20sms/readme_media/tasker_sms_task.jpg)

In the screen that follows, prepare the number and text variables that you will send from Home Assistant. Enter the text _%number_ in the `Number` field and the text _smstext_ in the `Message` field. You can use any variable name you like, as long as you use the same names here as you will later on in Home Assistant. 

Press the back button twice to save the task and return to the home screen of Tasker

### Creating the Tasker profile
Now the power of Tasker comes to play. You will make a new profile that will listen to an intent. Yes, you can send intents to Tasker to make it perform whatever you want from whatever app you want. That is what we'll be doing here. 

Select the `PROFILES` tab and press the plus button to create a new Profile. Select the `Event` profile type. In the list that follows find the `Intent received` event. You can find it under `Sysem`, or just start typing "intent" and you'll find it. 


![Screenshot of the Tasker Intent profile](/readme_media/tasker_intent_profile.jpg)

In this screen, all you have to do is enter a value in the Action field. This is the name in the intent sent from Home Assistant Tasker will listen to. I chose _Send SMS_, but you can use anything you like. Just make sure you use the same name later on in Home Assistant. 

Press back twice to save the the profile. Tasker will show you a list of tasks to perform when the event is triggered. Choose the task you created before, _Send Text Message_

### Create a home assistant Script
The easiest way to send text messages is to create a script. That way you can re-use the same intelligence from your dashboards with the push of a button or from any automation. 

I've already created the script for you, so just copy the YAML from [scripts.yaml](https://github.com/Dixet/homeassistant_projects/blob/main/home%20assistant%20sms/scripts.yaml) into a new script. 

To do this go to you Autmations section in home assistant. On the `scripts` tab create a new script. Use the three-dot menu of the right and go to YAML mode. There just paste the YAML. 

What it does is easy, so you can create it manually if you want by following these steps

1. In the script editor use the three-dot menu on the right and choose "add fields". This will create a new section in your script where you can define input variables. 
   - Add a first variable called _phonenumber_. Make it a text variable (so you can enter international codes like +42) and required
   - Add a second variable called _textmessage_. This will contain the text you want to send, so make it text and required as well
2. Add an action of the notify type to send a message to your phone. Choose the `notify_you_phone_name` action.
   - In the action parameters type the text `command_broadcast_intent` in the message text. This will tell the companion app that you don't want to show a message but want to sent an intent to another app
   - In the data field enter the following:
     ```intent_package_name: net.dinglisch.android.taskerm
        intent_action: Send SMS
        intent_extras: number:{{phonenumber}},smstext:{{textmessage|urlencode}}:String.urlencoded```

The `intent_package_name` is the internal name of the Tasker app, so this will tell the Companion app to send the intent to Tasker. The `intent action` is the name Tasker will listen to and the value has to be exactly what you entered in the first step [Creating the Tasker task](https://github.com/Dixet/homeassistant_projects/tree/main/home%20assistant%20sms#creating-the-tasker-task). Finally, the `intent_extras` will contain the data that is exposed to the Tasker task. Remeber we entered `%number` and `smstext` in the first step? Those exact names, without the percentage-sign should be entered here, followed by respectively the `phonenumber` field and the `textmessage` field, both enclosed in double curly brackets to tell home assistant these are templates that need to be evaluated. The last part, `:String.urlencoded``should be appended so you can use reserved charatecters like a comma in the text. Otherwise the comma would mark the start of a new variable in this list. 

Now just save your script and start texting!

### Testing your script
Go to the developer tools to test your script. Select the `SERVICES tab end choose the service `script.send_sms_text` or whatever name you gave it. 
Enter your own phone number for testing purposes and a text message and press _call service_. Wait a few seconds and voilà, you've got a message!

Call the service
![Screenshot of the Send message service](https://github.com/Dixet/homeassistant_projects/blob/main/home%20assistant%20sms/readme_media/sms_call_service.png)

And see the result!
![Screenshot of the Send message service](https://github.com/Dixet/homeassistant_projects/blob/main/home%20assistant%20sms/readme_media/sms_text_screenshot.jpg)
