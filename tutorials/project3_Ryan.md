id: Project3Ryan

# Integrating User Table That Stores User Preferences Like Language -- By Ryan Racine
#### By Ryan Racine

## Step 1: Editing Layout
We will need to create a new toggle button to switch the launguage choice. 
This text view will go in the top right of the screen

``` java
    <ToggleButton
        android:id="@+id/langSwitch"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginTop="8dp"
        android:layout_marginEnd="8dp"
        android:onClick="onLangSwitch"
        android:text="Language"
        android:textOff="English"
        android:textOn="Spanish"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintTop_toTopOf="parent" />
```

## Step 2: Adding a User Table
We already have the Database, its helper and the first table contract from the previous tutorial but now we will want to add another table to this database.
We will start by adding a new java class for the user table's contract. This tables will have two columns, one to store the username and the other to 
store the user's laungauge preference:

``` java
public class UserContract {
    private UserContract(){}

    public static class UserEntry implements BaseColumns {
        public static final String TABLE_NAME = "users";

        public static final String COLUMN_NAME_USER = "user";
        public static final String COLUMN_NAME_LANGUAGE = "language";
    }
}
```

Now that we've made the table's contract we need to add it to the helper. Start by adding the SQL for creating and deleting the table into the helper class:

``` java
    private static final String SQL_CREATE_USER =
            "CREATE TABLE " + UserContract.UserEntry.TABLE_NAME + " (" +
                    UserContract.UserEntry._ID +  " INTEGER PRIMARY KEY," +
                    UserContract.UserEntry.COLUMN_NAME_USER + " TEXT," +
                    UserContract.UserEntry.COLUMN_NAME_LANGUAGE + " TEXT)";

    private static final String SQL_DELETE_USERS =
            "DROP TABLE IF EXISTS " + UserContract.UserEntry.TABLE_NAME;
```

To get the helper to actually run these we must add them to the onCreate and onUpgrade functions:

``` java    
    public void onCreate(SQLiteDatabase db) {
        ...
        db.execSQL(SQL_CREATE_USER);
    }

    public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion){
        ...
        db.execSQL(SQL_DELETE_USERS);
        ...
    }
```

(Don't forget that youll have to delete the app to reset the database and have it recreate itself)

## Step 3: Editting the Main Activity
Now that we have all the database helper stuff set up, we can integrate it into the app itself.

First lets get the language switch toggle all set. For this we will declare a variable for the `ToggleButton` at the top of the class (`private ToggleButton mLangSwitch;`). 
We can get the button from the layout inside the `onCreate()` with (`mLangSwitch = (ToggleButton) findViewById(R.id.langSwitch);`). The last big thing we have to do for the ToggleButton
is to add a onSwitch function (you'll notice we already linked it to the ToggleButton in the view above). This function will first get the current language from the database, then
determine what the next language should be and set the language to be that:

``` java
    public void onLangSwitch(View v) {
        String oldLang = getCurLang();
        String curLang = (oldLang.equals("English")) ? "Spanish" : "English";
        setCurLang(curLang);
    }
```

Youll notice we use two functions her we have not yet defined (`getCurLang()` and `setCurLang()`), we will define these to functions next.

`getCurLang()` will be in charge of querying the database to find the current user's preferred language and returning it.
First it will need to get a readable database from the helper. Then it will query the Users table in this database and pull out all entries with the same username as the current user.
Then it will try to pull out the language from the one entry it found. If it didn't find any users with that username or anything else went wrong, it will return a failure.

``` java
    public String getCurLang(){
        // Access database
        SQLiteDatabase db = mDbHelper.getReadableDatabase();
        Cursor lang = db.query(UserContract.UserEntry.TABLE_NAME, null,UserContract.UserEntry.COLUMN_NAME_USER +"='" + user + "'", null, null, null, null);
        try{
            lang.moveToFirst();
            String s=lang.getString(lang.getColumnIndex(UserContract.UserEntry.COLUMN_NAME_LANGUAGE));
            return s;
        }
        catch(Exception e){
            // Failed so return failure
            return "Fail";
        }
    }
```

Now that getting the current users language preference is all set we need to be also able to set this user's preference. We will do this with the `setCurLang()` function.
There are four main parts to this function. First we have to access the database. Then we have to create the entry we want to insert or update the database with. 
Then we have to insert or update the database, depending on if the user is already in the database or not. Lastly we should update the view so all the default tet is in the preferred language.
 
``` java 
    public void setCurLang(String lang){
        // Access Database
        SQLiteDatabase db = mDbHelper.getReadableDatabase();

	// Create contentValues
        ContentValues mContentValues = new ContentValues();
        mContentValues.put(UserContract.UserEntry.COLUMN_NAME_USER, user);
        mContentValues.put(UserContract.UserEntry.COLUMN_NAME_LANGUAGE, lang);
     	
	// If not already in the database, insert instead of update
	long numEntries = DatabaseUtils.queryNumEntries(db, UserContract.UserEntry.TABLE_NAME);
        if (numEntries == 0) {
            sqlDb.insert(UserContract.UserEntry.TABLE_NAME, null, mContentValues);
        }
        else {
            // If already exists
            sqlDb.update(UserContract.UserEntry.TABLE_NAME,  mContentValues, UserContract.UserEntry.COLUMN_NAME_USER +"='" + user + "'", null);
        }

        // Update view
        if (lang.equals("English")){
            if(mSubmitButton.getText().toString().equals(DefButtonTxtSpn) || mSubmitButton.getText().toString().equals("?"))
                mSubmitButton.setText(DefButtonTxtEng);
            if(mEditText.getText().toString().equals(DefTextViewTxtSpn) || mEditText.getText().toString().equals("?"))
                mEditText.setText(DefTextViewTxtEng);
        }
        else{
            if(mSubmitButton.getText().toString().equals(DefButtonTxtEng) || mSubmitButton.getText().toString().equals("?"))
                mSubmitButton.setText(DefButtonTxtSpn);
            if(mEditText.getText().toString().equals(DefTextViewTxtEng) || mEditText.getText().toString().equals("?"))
                mEditText.setText(DefTextViewTxtSpn);
        }
    }
```

A coupe things you'll notice about this code is that we haven't set the default user's username, we havent defined variables
for Default textview and button in either launguage, and we also never set these elements to display "?".

So first lets add these variables to the beginning of our `MainActivity` class:

``` java
public class MainActivity extends AppCompatActivity {
    ...
    String user = "USER1";
    String DefButtonTxtEng = "SUBMIT";
    String DefButtonTxtSpn = "ENVIAR";
    String DefTextViewTxtEng = "Enter Text Here.";
    String DefTextViewTxtSpn = "Introducir Texto Aqu�.";
    ...
}
```

The next thing we are going to want to square away is setting the text for the TextView and the Submit Button at the beginning.
We dont want them to show either language until it knows which language the user uses so in the `onCreate()` function we will set them both to show "?" 
(this is what that check above is used for):

``` java 
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        ...
        mEditText.setText("?");
        mSubmitButton.setText("?");
        ...
    }
```

To prevent the user from seeing our confusion we want to update this text after the database is initialized. So in the `onCreate()` after the 
database is initialized we should set the current language to be what is stored in the database (keep in mind set current language takes care of all the View
updates for us). First we will check that it has a stored language. If it does, great we can just set it to be the same launguage (and update the toggle button).
If it doesn't then we will start the user out by setting their language to English.

``` java
    @Override
    protected void onCreate(Bundle savedInstanceState){
        ...
        String langStatus = getCurLang();
        if(langStatus.equals("Fail"))
            setCurLang("English");
        else {
            setCurLang(langStatus);
            if(langStatus.equals("Spanish"))
                mLangSwitch.setChecked(true);
        }
        ...
    }
```

The last thing we want to do is reset the text in the TextView to default of whichever laungage is being used inside `onSubmitButtonPressed()`:

``` java
    public void onSubmitButtonPressed(View v){
        ...
        String langStatus = getCurLang();
        if(langStatus.equals("Spanish"))
            mEditText.setText(DefTextViewTxtSpn);
        else
            mEditText.setText(DefTextViewTxtEng);
        ...
    }
```

