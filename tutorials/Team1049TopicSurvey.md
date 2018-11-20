id:     ts1049

# CS4518 Team 1049 picture-in-picture tutorial

## Picture In Picture Tutorial (Overview)


This tutorial will be covering how to use Picture In Picture (PIP) mode for android. Additionally we will be covering the best practices of PIP throughout.

We will do the following:
-Setup the project
-Enable Picture In Picture
-Enter PIP mode when the user leaves the application / presses the home button
-Add parameters
-Disable and re-enable the UI Heading/Action Bar


![](https://i.imgur.com/Hb3pdPe.png)

Prerequisites
-Android Studio Installed
-Android device supporting API 27 or later
-Or AVD installed with API 27 or later



## Setup the project
This tutorial provides a startercode project folder. This project contains a simple video view that plays a cat video when the application launches. 


Start by opening this project in Android Studio.

## Enabling PIP
To use PIP for an activity we next must set it enabled in the android manifest. Navigate to the AndroidManifest.xml file and under the main activity add the following:
``` xml
<activity android:name=".MainActivity"
           android:resizeableActivity="true"
           android:supportsPictureInPicture="true"
           android:configChanges="screenSize|smallestScreenSize|screenLayout|orientation">
...
```


## Going to PIP Mode


Enabling PIP mode is relatively straightforward, but to do it following best practices requires some setup. We will demonstrate how to use PIP in this step through progressive stages to best explain design choices, however it is possible to implement all of these steps at once  


Inside the MainActivity.java file we can override a function that is activated when the user leaves the application. 
``` java
@Override
   protected void onUserLeaveHint() {
       super.onUserLeaveHint();
       ...
   }
```
Now whenever the user hits the home button for example we want our app to move to PIP mode.
Add this one line to the onUserLeaveHint function:
``` java
enterPictureInPictureMode();
```


![](https://i.imgur.com/PEcp6Gb.png)

Your application should look something like this when the home button is pressed. 
Android studio will inform you that this method is deprecated as of 8.0. That's because the newer version must accept a set of parameters when going to PIP. We will do this next.


## Adding Parameters
###Reorganizing our code
Because going to PIP might be used in different sections of the activity, we will put the setup code in its own function. Start by making a GoPIP() function in your MainActivity class and replacing the earlier deprecated method call within onUserLeaveHint() with the GoPIP() call, as well as putting the enterPictureInPictureMode() function to GoPIP().

Note that at this point, we could also add an onClick() function for a button to call this function, or have it called when the user swipes a certain length, but we will not cover these in this tutorial.

###Creating Parameters
Now that we have our function to launch PIP, we can look at Parameters.
The previous deprecated function now accepts an object called PictureInPictureParams, however before we get those we want to have a builder object to create it for us!


Within GoPIP(), give your activity a builder as a member variable, allowing it to be used throughout the class:
``` java
private final PictureInPictureParams.Builder mPictureInPictureParamsBuilder =
           new PictureInPictureParams.Builder();
```

We can immediately build our parameter set and go to PIP  using the build() function:
``` java
PictureInPictureParams params = mPictureInPictureParamsBuilder.build();
enterPictureInPictureMode(params);
``` 


![](https://i.imgur.com/V23v40M.png)


##Picture In Picture Aspect Ratio Handling
This is nice and all but surely Android forces us to use a param set for a reason. In our application we have a video view that we want to preserve when moving into PIP. Luckily one of the params that we can set is the aspect ratio of the PIP screen. First we can extract the aspect ratio into a Rational number object within GoPIP() before enterPictureInPictureMode(params) is called.:
``` java
VideoView videoview = (VideoView) findViewById(R.id.videoView);
Rational aspectRatio = new Rational(videoview.getWidth(), videoview.getHeight());
```
Next we can set the aspect ratio of the builder object to the Rational:
``` java
mPictureInPictureParamsBuilder.setAspectRatio(aspectRatio);
```


Build the project and run it, we can see the aspect ratio is in fact maintained but there is still a problem with the title bar remaining and covering our video. That will be our next task.


## Picture In Picture UI Handling
Android supplies a method that will be activated whenever we either go into or out of PIP. We can override this method in order to handle any changes in either code or UI that might happen during the change.
``` java
@Override
public void onPictureInPictureModeChanged (boolean isInPictureInPictureMode, Configuration newConfig) {
       ...
   }
```
If you notice, one of the function parameters is a boolean telling us whether we are going into or out of PIP. We can setup a simple conditional to divide the changes based on this bool.
``` java
if (isInPictureInPictureMode) {
...
} else {
...
}
```
Depending on what type of UI controls you need you can place them here, for us we just want to hide the title bar when we go to PIP, and re-show the title bar when PIP stops. We can add ``` getSupportActionBar().hide(); ``` to the top conditional and ``` getSupportActionBar().show(); ``` on the lower half.


Once again compile and run your application and check to see that the title bar disappears when entering PIP, and returns when leaving PIP.



![](https://i.imgur.com/Hb3pdPe.png)
## Good Practice
Some devices do not have the RAM needed to run picture in picture, so we want to check this before attempting to run anything. At the start of the GoPIP() function we can add a check to the package manager to see if the user can use PIP.
``` java
if(!getPackageManager().hasSystemFeature(PackageManager.FEATURE_PICTURE_IN_PICTURE)) {
           return;
}
```


## Additional PIP Params (Optional Challenge)
Aspect ratio is a simple example of a parameter for PIP mode. For more advanced application, we may want to add remote actions that will be run in the PIP mode. This is useful when we may want to control the media in our application from PIP. To add actions to the PIP use the following general concept:
``` java
final ArrayList<RemoteAction> actions = new ArrayList<>();
actions.add(new RemoteAction(icon, title, title, intent)); // Where you can customize your icon titles and intent to whatever you may need
mPictureInPictureParamsBuilder.setActions(actions);
```


We encourage you to go the extra step and attempt to add an action or listener that will trigger while the user is in PIP. This could be anything you want

Another optional challenge could be having a PIP app interact with another non-PIP app using intents There are many uses for PIP outside of just watching videos.

## Conclusion
In this tutorial you learned how to:
* Enable picture in picture mode for an activity
* Go to picture in picture mode when a user leaves the application
* Add parameters to the PIP call to maintain the aspect ratio
* Handle UI changes during a PIP transition
* Good practices of PIP


From here we hope you explore more actions and intents that can be added to a PIP param action list to provide a highly customized viewing experience for your application.