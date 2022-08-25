# Joke Lab

## Description

This lab will use the Retrofit library to make HTTP requests to the Chuck Norris joke database

### Notes

Chuck Norris Joke Database API

http://www.icndb.com/api/

API Call

http://api.icndb.com/jokes/random?limitTo=[nerdy]

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

```
lsflsdjfsf
```