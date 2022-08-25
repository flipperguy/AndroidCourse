# Joke Lab

## Description

This lab will use the Retrofit library to make HTTP requests to the Chuck Norris joke database

### Notes

Chuck Norris Joke Database API

http://www.icndb.com/api/

API Call

http://api.icndb.com/jokes/random?limitTo=[nerdy]

Retrofit Documentation

https://square.github.io/retrofit/

# Create a JokeActiviy classs

Use the wizard to create a new Empty Activity names `JokeActivity` which will call the joke api and display the returned random joke.

# Create layout for Joke Activity

Add view elements to contain the joke text and to initiate the call.  

Following is a simple layout you can use or feel free to be more creative.

```
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".JokeActivity">

    <TextView
        android:id="@+id/joke_content"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:inputType="textMultiLine"
        android:textSize="24sp"
        android:layout_margin="@dimen/big_padding"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintEnd_toEndOf="parent"/>

    <Button
        android:id="@+id/joke_button"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/get_new_joke"
        android:layout_marginBottom="@dimen/big_padding"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintEnd_toEndOf="parent"/>
    
</androidx.constraintlayout.widget.ConstraintLayout>
```

# Wire the layout views in the activity

In `JokeActivity` wire up the view elements and create stub methods to call the api and populate the text view.

```
    TextView jokeContent;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_joke);

        jokeContent = findViewById(R.id.joke_content);

        Button jokeButton = findViewById(R.id.joke_button);
        jokeButton.setOnClickListener( view -> {
            Log.i("JokeActivity","clicked joke button");
            getNewJoke();
        });
    }

    private void getNewJoke() {
        makeJokeCall();
    }

     private void makeJokeCall() {
        jokeContent.setText("This is a new joke. The text is really long and should wrap.");
     }
```

Run the app the verify that the views are wired correctly.

# Add the Retrofit dependencies

Update the `build.gradle` file.

```
implementation 'com.squareup.retrofit2:retrofit:2.9.0'
implementation 'com.squareup.retrofit2:converter-gson:2.9.0'
implementation 'com.squareup.okhttp3:okhttp:4.7.2'
```

# Create a `joke` package to contain joke related classes


# Create Model classes a Joke item

This will be used to serialize the JSON returned by the request and turn it into a Java object.

Examine the response JSON to determine which model classes you need and what properties they should have.

```
{
  "type": "success",
  "value": {
    "id": 451,
    "joke": "Chuck Norris writes code that optimizes itself.",
    "categories": [
      "nerdy"
    ]
  }
}
```

One approach is to use two models, a response model and a value model contained in the response.

Create a response model called `JokeResponseModel`

```
public class JokeResponseModel {

    @SerializedName("type")
    private String type;

    @SerializedName("value")
    private JokeValueModel value;

    public String getType() {
        return type;
    }

    public JokeValueModel getValue() {
        return value;
    }
}
```

Create a value model called `JokeValueModel`

```
public class JokeValueModel {

    @SerializedName("id")
    private int id;

    @SerializedName("joke")
    private String joke;

    @SerializedName("catgories")
    private ArrayList<String> categories;

    public int getId() {
        return id;
    }

    public String getJoke() {
        return joke;
    }

    public ArrayList<String> getCategories() {
        return categories;
    }
}
```

# Create joke API class

Create a java class in the `joke` package named `JokeApi`.  This interface defines the http calls that the app can make.

```
public interface JokeApi {

    String BASE_URL = "http://api.icndb.com/jokes/";

    @GET("random?limitTo=[nerdy]")
    Call<JokeResponseModel> getJoke();
}

```

# Create Retrofit Client

Create a java class in the `joke` package named `Retrofit Client`.  This object will be used to make the HTTP connection.

It acts as a wrapper around the native java Http client in Android.

```
public class RetrofitClient {

    private static RetrofitClient instance = null;
    private JokeApi myApi;

    private RetrofitClient() {
        Retrofit retrofit = new Retrofit.Builder().baseUrl(JokeApi.BASE_URL)
                .addConverterFactory(GsonConverterFactory.create())
                .build();
        myApi = retrofit.create(JokeApi.class);
    }

    public static synchronized RetrofitClient getInstance() {
        if (instance == null) {
            instance = new RetrofitClient();
        }
        return instance;
    }

    public JokeApi getMyApi() {
        return myApi;
    }
}
```


# Call the API from the `JokeActivity`

```

    private void makeJokeCall() {
        Call<JokeResponseModel> call = RetrofitClient.getInstance().getMyApi().getJoke();
        call.enqueue(new Callback<JokeResponseModel>() {

            @Override
            public void onResponse(Call<JokeResponseModel> call, Response<JokeResponseModel> response) {
                Log.i("JokeActivity", "Raw: " + response.raw());
                Log.i("JokeActivity", "Message: " + response.message());
                JokeResponseModel jokeResponseModel = response.body();
                jokeContent.setText(jokeResponseModel.getValue().getJoke());
            }

            @Override
            public void onFailure(Call<JokeResponseModel> call, Throwable t) {
                Log.e("JokeActivity", "Error: " + t.getLocalizedMessage());
                Toast.makeText(getApplicationContext(), "An error has occured", Toast.LENGTH_LONG).show();
            }

        });
    }
```

# Add network permission to manifest

In `AndroidManifest.xml` add the permission for accessing the internet.  Without this permission Android will throw a security exception.

Add this tag as a child of the 'manifest' tag.

```
<!--Internet Permission-->
<uses-permission android:name="android.permission.INTERNET" />
```

# Allow for clear text transmission

In `AndroidManifest.xml` in the "application" tag add an attribute to allow clear text over http.  Android prefers https with encryption.

```
android:usesCleartextTraffic="true"
```

# Additional features

Here are some ideas for additional features

- Fetch a list of jokes
- Save jokes in a local database
- Display jokes in a recycler view