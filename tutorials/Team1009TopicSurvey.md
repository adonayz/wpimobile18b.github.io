id: ts1009

# RecyclerView - by Team 1009




## Introduction



[RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView) is a flexible view structure in Android that allows a developer to easier and efficiently show a scrollable list. This view provides a limited window into a large data set without taking up too much memory or storage.

### **What are some uses of RecyclerView?**

* Handling the view of any dataset where the total number of objects is either unknown or unlimited.
* Efficiently handling and releasing memory when handles large datasets.
* Asynchronous background loading of multiple objects.
* Showing lists that are based on dynamic server-based data.

### **What you will build**

### **What you'll learn**

* How to use the RecyclerView

### **What you'll need**

* Some text editor or Android Studio.
* 10 minutes

This codelab is focused on RecyclerView Use. Non-relevant concepts and code blocks are glossed over and are provided for you to simply copy and paste.


## Getting set up



### **Create a New Project**

* Create a new project.

### **Adding in the required Dependencies**

* Navigate to the build.Gradle (app) and add the following dependency.

```
implementation 'com.android.support:recyclerview-v7:27.1.1'
```


## Setting up the XML Layouts



### **Create layout file for RecyclerView element**

* Create a new file called 'list_item.xml' in the layout folder (path -> app/src/main/res/layout)
* Create a TextView element in the new layout file. 
* RecyclerView will use this TextView to display strings from the string array.
* Assign the TextView the id "row_item"

```
<TextView
    android:id="@+id/row_item"
    android:layout_width="match_parent"
    android:layout_height="50dp"
    android:gravity="center"
    xmlns:android="http://schemas.android.com/apk/res/android"/>
```

### **Layout file for RecyclerView**

* Open activity_main.xml in the layout folder
* Add a RecyclerView element under the root xml tag (in this case LinearLayout)
* Assign the RecyclerView the id "item_list"
* Add a scrollbars attribute to the RecyclerView as shown in the example below

```
<android.support.v7.widget.RecyclerView
        android:id="@+id/item_list"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:scrollbars="vertical"
        />
```


## Writing the List Adapter



### An important part of a recycler view is a separate class called an Adapter.

### The adapter creates views for items and replaces the content of views that are no longer needed with new data.

* Create a new java class named MyAdapter
* For properties, give a private ArrayList of strings, named mDataset, and an int named listItemLayout
* Define a simple public constructor that takes an arraylist of strings and an int and sets mDataset and listItemLayout to the given values

### We need an inner class called a ViewHolder

* Create a public static inner class named MyViewHolder
* have it extend RecyclerView.ViewHolder
* Have it implement View.OnClickListener
* For properties, give a public TextView mTextView
* Define a constructor that takes a View itemView as an argument
* Call super(itemView)
* Set the onClickListener of itemView to this
* Set mTextView to itemView.findViewById, looking for the row_item we created 
* Override the onClick method so it gives a custom message

```
    public static class MyViewHolder extends RecyclerView.ViewHolder {
        public TextView mTextView;
        public MyViewHolder(View itemView){
            super(itemView);
            itemView.setOnCLickListener(this);
            mTextView = (TextView) itemView.findViewById(R.id.row_item);
        }
        Public void onClick(View view){
            Log.d("onclick", "onClick " + getLayoutPosition() + " " + mTextView.getText());
    }
```

### Now that we have our inner class defined, we can extend MyAdapter.

* MyAdapter should extend RecyclerView.Adapter, and it's type should be MyAdapter.MyViewHolder

```
    public class MyAdapter extends 
    RecyclerView.Adapter<MyAdapter.MyViewHolder>{
    private String[] mDataSet;
    Private int listItemLayout;
...
}
```

### Now that we have extended RecyclerView.Adapter, we must override the inherited methods to complete the Adapter class.

* Override getItemCount(), onBindViewHolder(), and onCreateViewHolder()
* getItemCount() is a simple method that returns the number of items in the data set as an int.

```
    @Override
    public int getItemCount(){
        return mDataSet.size();
    }
```

* onBindViewHolder is a void method that takes a ViewHolder (such as MyViewHolder)  and an int position. We set the text inside the Holder to the text of the mDataSet at the given position

```
    @Override
    public void onBindViewHolder(MyViewHolder holder, int position){
        holder.mTextView.setText(mDataSet.get(position));
    }
```

* The Final method we must override is onCreateViewHolder, which is of type MyAdapter.MyViewHolder. It takes a ViewGroup and an int viewType as arguments. When we want to create a ViewHolder, we create a textview from the current context and make a new MyViewHolder with the created textview as the argument.

```
    @Override
    public MyAdapter.MyViewHolder onCreateViewHolder(ViewGroup parent, int viewType){
        View v =  LayoutInflater.from(parent.getContext()).inflate(listItemLayout, parent, false);
        MyViewHolder vh = new MyViewHolder(t);
        return vh;
    }
```

### With these methods implemented, the Adapter class and its ViewHolder inner class are complete. 


## Setting up The MainActivity



### **In the onCreate() method**

* Create a String list or array and populate it.

```
// Initializing list
ArrayList<String> itemList = new ArrayList<String>();

// Populating list items
for(int i=0; i<100; i++) {
    itemList.add("Item " + i);
}
```

* Create an instance of the custom adapter you created
* Use R.layout.list_item as the first argument in the adapter constructor. This imports list_item.xml into the adapter
* Use the String list or array you created as the second argument for the adapter constructor.

```
// Creating adapter instance
        MyAdapter itemArrayAdapter = new MyAdapter(R.layout.list_item, itemList);
```

* Create a RecyclerView object and assign it to the RecyclerView element created in activity_main.xml (in this case R.id.recyclerView)

```
RecyclerView recyclerView = (RecyclerView) findViewById(R.id.item_list);
```

* Finally use .setAdapter() to import the custom adapter object into the RecyclerView object

```
recyclerView.setLayoutManager(new LinearLayoutManager(this));
recyclerView.setItemAnimator(new DefaultItemAnimator());
recyclerView.setAdapter(itemArrayAdapter);
```


## Testing



### **Test your application**

Test your application on you Android Device of AVD. Make sure your list is scrollable and that you can scroll to string 99. Show one of us you application before moving on and discussing the extra work.


## Summary



RecyclerView is an efficient tool for displaying portions of a much larger dataset without having to hold the entire dataset in memory. Although it requires the use of extra dependencies as well as building a custom adapter, the performance payoff is well worth it.

<aside class="special">

**Remember**: This tutorial has only taken you through the basic of using a RecyclerView. There are many other features out there that can make RecyclerView even more powerful.
</aside>

### **Extra Work**

If you completed this tutorial and have some extra time consider how you would complete or actually complete the following tasks.

* Instead of just String consider how one would display both a picture and a description list inside the RecyclerView.
* Instead of having the entire list beforehand, when and where would be the best location to make a request to get more of the list.
* Instead of having a set view for each shown object, how would one display a list of different views.
* How would one make this list searchable? (Hint. Lookup SearchView, how would this fit into the application you have written?).


