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

![Screenshot of the Tasker SMS task](https://github.com/Dixet/homeassistant_projects/blob/main/home%20assistant%20sms/readme_media/tasker_sms_task.jpg)
