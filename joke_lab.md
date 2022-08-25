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

# Create layout for Joke Activity

this is step 1

```
lsflsdjfsf
```

# Wire the layout views in the actiity


# Add the Retrofit dependencies

Update the `build.gradle` file.

```
implementation 'com.squareup.retrofit2:retrofit:2.9.0'
implementation 'com.squareup.retrofit2:converter-gson:2.9.0'
implementation 'com.squareup.okhttp3:okhttp:4.7.2'
```

# Create a `joke` package to contain joke related classes


# Create a Model class for a Joke item

This will be used to serialize the JSON returned by the request and turn it into a Java object 

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

JokeValueModel

```
lsflsdjfsf
```

JokeValueModel

```
lsflsdjfsf
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