id: ts1051
author: Georgie Cromwell, Benjamin Nickerson, Robert Scalfani 
summary: CS4518 Topic Survey: Amazon Lex
categories: common
environment: markdown 
status: draft 

# Amazon Lex Tutorial - By Team1051 
##Requirements
*Note: Microphone does not always work with the AVD emulator (does not clearly pick up audio). You should use a physical android device to test this project.*

##Project Setup
1. Create an empty Android Project and switch to the Project View and Navigate to App/Src/Main/Res/ and create a new Android Resource Directory with the resource type as raw. Name this new directory 'raw'.

2. Create an AWS account if you don't already have one and then go to the "mobile hub" at https://console.aws.amazon.com/mobilehub/home?#/createwizard/?app=android

3. In the "Create a Project" step of the mobile hub, enter in the name of your project and click next.

4. Click "Download Cloud Config" to get the awsconfiguration.json file and put into the new 'raw' directory.

5. In your code, Insert the following code into the AndroidManifest.xml file outside of the application tag:

    ```java
    <uses-permission android:name="android.permission.INTERNET"/>
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
    ```

5. And add the following dependencies to your app/build.gradle and sync:

    ```java
    dependencies {
        implementation ('com.amazonaws:aws-android-sdk-mobile-client:2.7.+@aar') { transitive = true }
    }
    ```

##Connecting Amazon Lex to Android
6. Add this code inside your main activity class and when prompted press 'alt-enter' to automatically add the two necessary import statements.

    ```java
    import com.amazonaws.mobile.client.AWSMobileClient;
    
      public class YourMainActivity extends Activity {
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
    
            AWSMobileClient.getInstance().initialize(this, new AWSStartupHandler() {
                @Override
                public void onComplete(AWSStartupResult awsStartupResult) {
                    Log.d("YourMainActivity", "AWSMobileClient is instantiated and you are connected to AWS!");
                }
            }).execute();
    
            // More onCreate code ...
        }
      }
      ```
  
7. Run, and check in Logcat to make sure AWS connected successfully. You can 'ctrl-f' for 'AWSMobileClient' to find it. You should be able to see a connection successful statement. 

##Adding Amazon Lex Chat Bot
8. Navigate back to the AWS mobile hub and select your project that you created in step three (it should be listed). Scroll down and choose to add a conversation bot with the type of 'Make an Appointment'

9. Fill in the bot name and the other blanks and click create new bot.

10. Now return to the Project Details page and click integrate (next to the 'Backend Features' are working on) to integrate the chat bot with the app.

11. Now, download the new awsconfiguration.json file by click 'Download Cloud Config' with the new lex connection and replace the old one in the 'raw' directory in your project.

12. Add in to the app/build.gradle add in to the dependences and sync:

     ```java
    implementation ('com.amazonaws:aws-android-sdk-lex:2.7.+@aar') { transitive = true }
    ```
13. Place these imports into your main activity:

    ```java
    import com.amazonaws.mobileconnectors.lex.interactionkit.Response;
    import com.amazonaws.mobileconnectors.lex.interactionkit.config.InteractionConfig;
    import com.amazonaws.mobileconnectors.lex.interactionkit.ui.InteractiveVoiceView;
    ```

14. Add the following code to your activity_main.xml layout to add the voice component:

    ```java
    <com.amazonaws.mobileconnectors.lex.interactionkit.ui.InteractiveVoiceView
    android:id="@+id/voiceInterface"
    layout="@layout/voice_component"
    android:layout_width="200dp"
    android:layout_height="200dp"/>
    ```

15. Add this line of code in your strings.xml file to set the region for your bot:

    ```java
    <string name="aws_region">us-east-1</string>
    ```

16. Add an init() function in your main activity and a call to this new init() function in onCreate(). Be sure to read the comments and replace fields with your bot's name and your bot's alias that you defined earlier!

    ```java
    public void init(){
            InteractiveVoiceView voiceView =
                    (InteractiveVoiceView) findViewById(R.id.voiceInterface);
    
            voiceView.setInteractiveVoiceListener(
                    new InteractiveVoiceView.InteractiveVoiceListener() {
    
                        @Override
                        public void dialogReadyForFulfillment(Map slots, String intent) {
                    Log.d("INTENT",intent);
                            Log.d("READY", String.format(
                                    Locale.US,
                                    "Dialog ready for fulfillment:\n\tIntent: %s\n\tSlots: %s",
                                    intent,
                                    slots.toString()));
                        }
    
                        @Override
                        public void onResponse(Response response) {
                            Log.d("RESPONSE", "Bot response: " + response.getTextResponse());
                        }
    
                        @Override
                        public void onError(String responseText, Exception e) {
                            Log.e("ERROR", "Error: " + responseText, e);
                        }
                    });
    
            voiceView.getViewAdapter().setCredentialProvider(AWSMobileClient.getInstance().getCredentialsProvider());
    
            //replace parameters with your botname, bot-alias
            voiceView.getViewAdapter()
                    .setInteractionConfig(
                            new InteractionConfig("YOUR-BOT-NAME","$LATEST"));
    
            voiceView.getViewAdapter()
                    .setAwsRegion(getApplicationContext()
                            .getString(R.string.aws_region));
        }
      ```
    
17. Your bot is now fully integrated with your application. Now you're going to want to recieve output from the bot when you speak to it. To do this, create a TextView in your layout xml file and add the following line after "Log.d("RESPONSE", "Bot response: " + response.getTextResponse());" in your MainActivity.java
       
    ```java
    TextView mResponseView = findViewById(R.id.responseView);
    mResponseView.setText( response.getTextResponse() );
    ```

18. Now that your bot is fully set up with your project, you're going to want to edit the responses that your bot will give to certain prompts. To do this, navigate back to your Lex Project in the AWS mobile hub. In the second row of icons, click the purple "Converstaional Bots" button. Then click "edit" on the bot you'd like to edit.

19. In the new page that you're directed to, go ahead and edit the different fields and responses to custom tailor your bot.

##Summary
*Now you have a working conversation bot integrated with your Android Studio Project*
Please follow this [link](https://aws.amazon.com/lex/) for more information on Amazon Lex. The link can provide different functionalities to leverage with this technology. 
