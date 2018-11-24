id: Project3Hannah


# Storing and Querying Images with Tag, Location, and Time Data in a Database - by Hannah Jauris
This tutorial assumes that you have completed both of these tutorials
and are able to combine parts of those projects as necessary:
https://wpimobile18b.github.io/tutorials/Camera/#0 (Camera tutorial)
https://wpimobile18b.github.io/tutorials/Database/#0 (Database tutorial)

This tutorial will redesign the database, format images to be able to be placed in the database, query entries from the database, and utilize a Cursor object to iterate through the returned entries.
We will adapt and create UIs which will allow users to take pictures and save them to a database, along with the time and location the image was taken and a tag identifying it as a Kitten or a Puppy picture. The user will then be able to view the images they have taken and filter them based on the Puppy/Kitten tags by querying the database.

## Redesign the Database
* First, increase the version number in LogEntryDbHelper, since we want the database to reload.
* We want to redesign the database to store location values(latitude, longitude, and altitude), an image, a timestamp, and a tag. Create, in LogEntry, constants for the column names for each of these.
* Update the creation query to include the new columns with their data types.
  * The three location values will all be doubles, and can be stored as REAL types.
  * The timestamp, also present in the database tutorial, is still stored as a REAL type.
  * The tag is stored as a TEXt type.
  * The image cannot be stored as it is. Processing will need to be done to get it in and out of the database. We will store it as a BLOB object.

## Setting up the UI
### Add to Database Screen
* The UI should allow users to take pictures, which are displayed on the device, as shown in the Camera tutorial. As I had done for Project 2 Part 2, this UI should be updated to display the timestamp and location where the image was taken, with supporting code.
  * The Timestamp should be stored in a Timestamp object. This will make it easier to store in the database and reformat when we perform queries on the database.
* Add a button to Add the image to the database. We will be connecting an onClickListener to this later.
* Also add two radio buttons in a RadioGroup: one labeled "Kitten" and one labeled "Puppy". The user can use these buttons to save their picture under the corresponding tag.
* Add another button labeled "View DB", which will be connected to an onClickListener to change this current view to another layout.
  * For this, I used two fragments, one for the "adding to database" screen described above and the other for the "view database" screen described below. This will require moving code from the MainActivity.java into the corresponding fragment code, and will not be covered in depth in this tutorial.
* For each button, except the radio buttons, add onClickListeners in the code, and assign them to their respective buttons in the onActivityCreated() Fragment event. 
  * The "Take picture" button will have functionality identical to the Camera tutorial, allowing the users to take pictures upon touching it.
  * The "Add to DB" button will call a function to add an image and its information to the database, which will be created later.
  * The "View DB" fragment should load the other Fragment and remove this fragment, using FragmentManager and FragmentTransaction.
### View Database Screen
* Create a new Fragment layout to view the database entries.
* Include a "Next" button and a "Return" button, and text fields for the timestamp and location.
* Also include space for an image to be displayed, as well as a RadioGroup containing three radio buttons: "Kittens", "Puppies", "All".
* For each button, *including* the radio buttons, add onClickListeners in the code, and assign them to their respective buttons in the onActivityCreated() Fragment event. 
  * The radio buttons will call a function to query the database, as will be described later.
  * The "Next" button will call a function to display the next entry returned from the query, also described alter in the tutorial.
  * The "Return" button should load the other Fragment and remove this fragment, using FragmentManager and FragmentTransaction.

## Formatting Data and Adding Entries to the Database
* When the user takes an image and wants to add it to the database, they should tap the radio button with the appropriate tag for their picture, then tap the "Add to DB" button. To create a database entry, create a `createDBEntry()` helper function and call it from the listener.
* In this function, we will need to process the data to get it to a state where it will be accepted by the database. We use `ContentValues` to store the column/value pairs, and `insert` these into a writable database as described in the Database tutorial.
### The Image
* The bitmap needs to be stored in a blob-friendly format. One such format is as a byte array.
* Store the bitmap in a variable when you receive it back from the camera. This will make it easier to access when we make the database entry. (Similarly, the timestamp and location values should also be stored in variables.)
* When making the database entry, we need to convert the bitmap to a byte array. Luckily, a method exists to do just that!
* First, create a new ByteArrayOutputStream. If our bitmap we stored when taking the picture isn't null, call `bitmap.compress` to put it into the ByteArrayOutputStream, passing the arguments `Bitmap.CompressFormat.JPEG, 100, <byteArrayOutputSteamName>`.
  * The first argument is the type of compression. Since we took a picture from the camera, we store it as a JPEG.
  * The second argument is the quality of the compression, with 0 being the smallest and 100 being the best. We want to use 100 to have the best image quality retained.
  * The final argument is the ByteArrayOutputStream object we just created. This will put the results of the compression in our variable.
* Finally, we `put` the column-value pair in our ContentValues object, calling `toByteArray()` on the ByteArrayOutputStream when we pass it in, so the correct data type will be passed in.

### The Location
* The location is made up of three values, latitude, longitude, and altitude, all doubles.
* To store them, we simply `put` each of these values into ContentValues with their respective column name, which should be of type REAL in the database.

### Timestamp
* This is the same as the Database tutorial. `put` it ContentValues with its respective column name.

### Kitten/Puppy Tag
* This is quite similar to putting the text into the database in the prerequisite tutorial.
* However, we do have to obtain the tag information from the button label. To do so, get a reference to the RadioGroup, then call `findViewById(radioGroup.getCheckedRadioButtonId());` to get the radio button currently selected. Using `getText()` and `toString()`, we can pass the string of that button's label to the tag column.
  
## Querying the Database
* When we transition to the database-viewing page, we need a way to get our entries. We do this through a query.
* First, create a helper function to make the query. Make it take a String parameter for the selection argument, which we will utilize when the user wants to filter the images.
  * This function will be called in four places:
    * At the end of the `onActivityCreated` method, since as soon as the fragment loads we want to get database information to show on our page. Pass in `null` in this call.
    * In the onClickListeners for the three radio buttons. Pass in `null`, "Kitten", and "Puppy" for the Any, Kittens, and Puppies buttons, respectively.
* In this helper function (I called it queryDB), get a LogEntryDbHelper from the previous tutorial and get a readable database from it.  We can use this database to call the `query` function, which will return a `Cursor` object.
* The first parameter is the name of the table. Get this by referencing the constant in LogEntry.
* The second parameter we need to pass in is an array of the columns we want returned from our query. Since we want to view all the information about our image, we will create the array with entries for the column names of the image, timestamp, altitude, longitude, latitude, and image fields. We do not need the tag field, as we will see shortly.
* The next parameter is a String representing the selection we want to make. This allows us to filter our query so we only get results that meet the selection. Create a string of the column name for the tag column appended with " = ?".
* The next parameter is the selectionArgs, a String array, which is what will be compared to the tag column name in the above string. The " = ?" allows us to sanitize the input to prevent SQL injection attacks, even though that shouldn't be possible for this tutorial. For now, make the only element in the selectionArgs array the selectionArg parameter passed in.
* The other three parameters provide fields for grouping by, having, and ordering by certain attributes. We aren't concerned with these, so we can pass them `null`.
* By passing in these variables we have constructed, we can now query the database! However, we are going to want to filter by the Kitten or Puppy tag only when the user has selected those radio buttons.
  * Add an `if` statement checking if the parameter selectionArg is null. If it isn't, we can make the query as described above. If it is, we will make an identical query, except the selection and selectionArgs parameters will mass in `null` instead of our variables.
* Create a class-wide variable `cursor` of type `Cursor`, and store the result of your query in that.
* Now that we have created the query and stored its results in the cursor, we want to read them! create a helper function `displayNextDBEntry()`, and call it at the end of this function.

## Using the Cursor to Read Query Results
* We will process the information the cursor offers us in `displayNextDBEntry()`. But first, we need to set up where this function will be called.
  * It should be called at the end of the `queryDB` function, as mentioned above. Since this handles the display, we want it to show something as soon as it gets results!
  * In the onClickListener for the "Next" button's `onClick` method, we also want to call `displayNextDBEntry()`, since the next button proceeds to the next image returned from the database.
* The cursor offers us a way to iterate across the results obtained from the database query. It starts in a position before the first entry in the database.
* We want this to move forward to the next available entry, which will be the first entry the first time calling it. But what if we ware at the end of the results? We want to loop back to the beginning. In an if statement, check `cursor.moveToNext()`. Not only does this progress the pointer, it returns true if it points to an actual database entry. If it is true, it has moved to point at a legitimate entry. If not, we have run off the end of the results, and should call `cursor.moveToFirst()` to return it to the first valid entry.
* First, let's get the image and reformat it from a database-friendly format to a display-friendly format.
  * To get information from a database entry from the cursor, we need to know what data type it will be. Ours is a BLOB for the image, so we will call `cursor.getBlob` and store it in a byte array. The parameter for `getBlob` is the index for the column of the image.
    * We can get that by calling `cursor.getColumnIndex` and passing in the column name as defined in LogEntry.
  * Now that we have the data from the database, we want to restore it to a bitmap. Get a reference to the ImageView to display the image and call `setImageBitmap`, passing in a call to `BitmapFactory.decodeByteArray`.
    * `decodeByteArray` takes three parameters. The first is the byte array we stored the data from the database in. The second is the offset from which to start reading. Since we want to read th entire thing, we pass in 0. The last parameter is how much of the array to read. Since, again, we want the whole thing, pass in the length of the byte array.
* The process for extracting data is similar for the other columns. For the location doubles, call `cursor.getDouble`, with the parameter of the corresponding column index, obtained in the same way as for the image. Display these in their TextView as you desire.
* For the Timestamp, we use a call to `cursor.getLong` with the same parameters as the other get functions. We want to pass this value into the constructor of a Timestamp object to process that value and display a timestamp that users will be able to understand.

## Conclusion
* Once we have displayed all of the contents, that's it! Now we just have to test.
* You should be able to take pictures, tag them as a Kitten or Puppy image, and add them to the database along with info about when and where it was taken.
* You should also be able to navigate to another screen where you can access and view the information on the database, with it cycling through the entries, and immediately reloading to display only images with the selected tag.

## References:
* https://developer.android.com/guide/topics/ui/controls/radiobutton (If you don't know how to use radio buttons)
* https://developer.android.com/reference/android/database/Cursor (Very helpful for figuring out how to access various columns and how to get the next entry)
* https://developer.android.com/training/data-storage/sqlite#java (Helpful for how to create queries)
* https://developer.android.com/reference/android/graphics/Bitmap#compress(android.graphics.Bitmap.CompressFormat,%20int,%20java.io.OutputStream) (for putting the bitmap image into a blob format)
