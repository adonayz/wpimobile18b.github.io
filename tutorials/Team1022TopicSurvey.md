id: ts1022
#Topic Survey: Mobile Image Cropping - by Team 1022

###by Krysta Murdy & Sylvia van der Weide

This tutorial will allow you to implement an image cropping feature starting from your Project 2 code.


##Step 1: Adding Dependencies

In app build.gradle, locate the dependencies section and add: `api 'com.edmodo:cropper:1.0.1'`


##Step 2: UI Construction

In activity_main.xml...

1. Add the following code:
```xml
<com.edmodo.cropper.CropImageView
android:id="@+id/CropImageView"
android:layout_width="wrap_content"
android:layout_height="wrap_content" />
```

2. Edit the existing *ImageView* so that it starts off invisible by editing its properties in the "Attributes" bar.

3. Add a button to your UI that will eventually be your "crop" button.


###Step 3: Implement Cropping Functionality

In MainActivity.java...

1. In your existing `onActivityResult()`, replace the *ImageView* with a *CropImageView*:
	`CropImageView cropImageView = (CropImageView) findViewById(R.id.CropImageView);`

2. Create a function for your "crop" button. Here it is called `onClickCropButton()`.
In this function, retreive the image bitmap from the *CropImageView* object using `cropImageView.getCroppedImage();`

3. Set this image to your *ImageView* object using `setImageBitmap(<croppedImageBitmap>)`.
*Note: <...> indicate psuedocode

4. Set the visibilities of the *ImageView* to visible and the *CroppedImageView* to invisible.
	`setVisibility(View.VISIBLE);` or `setVisibility(View.INVISIBLE);`

**For more information, please see https://github.com/edmodo/cropper/wiki.

##THE END :)