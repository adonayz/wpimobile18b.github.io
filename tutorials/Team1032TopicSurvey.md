id: ts1032

# Designing with React Native - by Team 1032
#### By Ryan Racine, and Isaac Woods
This tutorial will introduce you developing with react native.
We will be recreating the app from project 1, because it is quite simple.

## Step 0: Pre-Setup check
Before we can start with react-native, we have to make sure we have Node.js.
If you don't have Node.js installed, install it from [here](https://nodejs.org/en/).
If you do have Node.js installed, ensure that it is at least version 8.3. 
You can use the command `node --version` to check.
If it is not at least v8.3 you can upgrade using the same installer found at the above link.

## Step 1: Getting Started
We are going to start by setting up the react native CLI. More info can be found  [here](https://facebook.github.io/react-native/docs/getting-started)

Basically what we need to do is:
1. Run this command in command prompt: `npm install -g react-native-cli`
2. Ensure you have android studio and android sdk
3. Set up ANDROID_HOME variable to be the path to the sdk folder (likely in `<USER>\Appdata\Local\android\`)
4. Initialize your application project folder by running this command in command prompt: `react-native init <PROJECT_NAME>`
5. In command prompt, switch to the project folder (`cd <PROJECT_NAME>`), then run the server with `react-native start`

To open the project in Android Studio, you can open the `<PROJECT_NAME>/android` folder as the android studio project.

Now that we have a project, you should add the some pictures (and the assets folder). 
You can copy the folder from you project 1, if you still have it.
There should also be an example assets folder included with this codelab.

To run the app, we must first run an AVD, either using adb through terminal/command line, or through Android Studio. We can then build the app, again either using a CLI or Android Studio.

This should push the app to your AVD.

## Step 2: Editting the App.js
The app.js contains everything that is sent to the client-side (the app) so all the coding is done in here.
After editing app.js, you can reload your app by pressing `r` twice in the AVD.
The `render()` function returns what you want the app to display, it will start pre-generated with a basic view and some text pieces.

### Button
We can remove the text elements replace them with a button that will control the image switching.
To make the button show up just above the bottom we create a view with a style for it:

```JS
style={{
    flex:1,
    flexDirection: 'column',
    justifyContent: 'flex-end',
    alignItems:'center',
    marginBottom: 16,
   }}
```

Here we are passing the button element a prop named "style", which the button will automatically parse as indicatiors for how it should look. (The styling is similar to CSS styling).
This will ensure it is centered horizontally.


This button will also need a title (ex. "Change Picture") and a onPress function (ex. onPressEvent).
These should also be passed in as props.
 
``` JS
title={'Change Picture'},
onPress={this.onPressEvent},
```

Now above the render function but still inside the App class define onPressEvent:

``` JS
onPressEvent = () => {
    // Pick random next pic
    // Set the picture
}
```

We will fill out this function later.

### Image
Now we also want to add a place for the Image to go. 
We will create a `<Image... />` element and pass it these props for styling:

``` JS
style={{
    flex: 1,
    alignSelf: 'stretch',
    width: undefined,
    height: undefined,
   }}
resizeMode="contain"
```

This allows the image to change its shape to fit the screen width.
The `resizeMode` stops it from exceeding the size of the screen (its container).

### State
For that data that is going to change, we need to use the `state`. 
State should hold all dynamic variables. 
Everything else in the component (the class we are working in), should be purly functional.
State is usually initilized in the constructor (which we will create) and then updated with `this.setState()`.
Lastly, state is stored as a simple JSON object.

The image will need a source to display. We will have to use the `require()` function to load an image from a file.
We don't want to always load the same image though so we have to save a few things to the state.

In the state we will store all the images we have (with the `require()` function) to avoid conflicts with passing it a non-constant string.
We will also store the index of the picture it is currently on.

``` JS
constructor(props) {
    super(props);
    this.state = {
        images: [
            require("./android/app/src/main/assets/kitten_pictures/kittenPic1.jpg"),
            require("./android/app/src/main/assets/kitten_pictures/kittenPic2.jpg"),
            require("./android/app/src/main/assets/kitten_pictures/kittenPic3.jpg"),
            require("./android/app/src/main/assets/kitten_pictures/kittenPic4.jpg"),
            require("./android/app/src/main/assets/kitten_pictures/kittenPic5.jpg"),
        ],
        curPic: 0,
    };
}
```

Be sure to have correct paths and filenames to your files.

Now that we have a state we need to tell the image to get its source from the state using `source={this.state.images[this.state.curPic]}`

### Button Functionality
Now that the image is all set we can fill in the functionality for the button we stubbed out.

We will simply get the next picture in the list on each button press.
To get the next picture, we can get the current index, add 1, and mod by the length of the list, pretty simple. 
However, to get the image that is displayed to update, we need to change the value of `curPic` in the state.
To update the value in state, we have to use `setState(...)`, passing it a JSON with entries for what entries in state we want to update.
``` JS
this.setState({
    curPic: this.state.curPic = (this.state.curPic + 1) % this.state.images.length,
});
```

## Summary
We have now (hopefully) created a functioning react app. 
If you are interested in learning how to make this app more complicated than a single component (class), you can learn more [here](https://reactjs.org/docs/components-and-props.html#composing-components).
