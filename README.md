# RestClientTemplate

## Overview

RestClientTemplate is a skeleton Android project that makes writing Android apps sourced from OAuth JSON REST APIs as easy as possible. This skeleton project
combines the best libraries and structure to enable quick development of rich API clients. The following things are supported out of the box:

 * Authenticating with any OAuth 1.0a or OAuth 2 API
 * Sending requests for and parsing JSON API data using a defined client
 * Persisting data to a local SQLite store through an ORM layer
 * Displaying and caching remote image data into views

The following libraries are used to make this possible:

 * [scribe-java](https://github.com/fernandezpablo85/scribe-java) - Simple OAuth library for handling the authentication flow.
 * [Android Async HTTP](https://github.com/codepath/AsyncHttpClient) - Simple asynchronous HTTP requests with JSON parsing
 * [codepath-oauth](https://github.com/thecodepath/android-oauth-handler) - Custom-built library for managing OAuth authentication and signing of requests
 * [Glide](https://github.com/bumptech/glide) - Used for async image loading and caching them in memory and on disk.
 * [Room](https://developer.android.com/training/data-storage/room/index.html) - Simple ORM for persisting a local SQLite database on the Android device

## Usage

### 1. Configure the REST client

Open `src/com.codepath.apps.restclienttemplate/RestClient.java`. Configure the `REST_API_INSTANCE` and`REST_URL`.

For example if I wanted to connect to Twitter:

```kotlin
// RestClient.kt
class RestClient(context: Context) : OAuthBaseClient {
    companion object {
        val REST_API_INSTANCE = TwitterApi.instance()

        const val REST_URL = "https://api.twitter.com/1.1"

        const val REST_CONSUMER_KEY =
            BuildConfig.CONSUMER_KEY // Change this inside apikey.properties

        const val REST_CONSUMER_SECRET =
            BuildConfig.CONSUMER_SECRET // Change this inside apikey.properties

        // ...constructor and endpoints
    }

}
```

Rename the `apikey.properties.example` file to `apikey.properties`.   Replace the `CONSUMER_KEY` and `CONSUMER_SECRET` to the values specified in the Twitter console:

CONSUMER_KEY="adsflfajsdlfdsajlafdsjl"
CONSUMER_SECRET="afdsljkasdflkjsd"

Next, change the `intent_scheme` and `intent_host` in `strings.xml` to a unique name that is special for this application.
This is used for the OAuth authentication flow for launching the app through web pages through an [Android intent](https://developer.chrome.com/multidevice/android/intents).

```xml
<string name="intent_scheme">oauth</string>
<string name="intent_host">codepathtweets</string>
```

Next, you want to define the endpoints which you want to retrieve data from or send data to within your client:

```kotlin
// RestClient.jt
fun getHomeTimeline(page: Int, handler: JsonHttpResponseHandler) {
  val apiUrl = getApiUrl("statuses/home_timeline.json")
  val params = RequestParams()
  params.put("page", String.valueOf(page))
  client.get(apiUrl, params, handler)
}
```

Note we are using `getApiUrl` to get the full URL from the relative fragment and `RequestParams` to control the request parameters.
You can easily send post requests (or put or delete) using a similar approach:

```kotlin
// RestClient.kt
fun postTweet(body: String, handler: JsonHttpResponseHandler) {
    val apiUrl = getApiUrl("statuses/update.json")
    val params = RequestParams()
    params.put("status", body)
    client.post(apiUrl, params, handler)
}
```

These endpoint methods will automatically execute asynchronous requests signed with the authenticated access token. To use JSON endpoints, simply invoke the method
with a `JsonHttpResponseHandler` handler:

```kotlin
// SomeActivity.kt
val client = RestApplication.getRestClient()
client.getHomeTimeline(object : JsonHttpResponseHandler() {
    override fun onSuccess(statusCode: Int, headers: okhttp3.Headers, json: JSON) {
        Log.i(TAG, "onSuccess!")
        // parse json response here
    }

    override fun onFailure(
        statusCode: Int,
        headers: okhttp3.Headers,
        response: String,
        throwable: Throwable
    ) {
        Log.i(TAG, "onFailure!", throwable)
    }

})
```

Based on the JSON response (array or object), you need to declare the expected type inside the OnSuccess signature i.e
`public void onSuccess(JSONObject json)`. If the endpoint does not return JSON, then you can use the `AsyncHttpResponseHandler`:

```kotlin
val client = RestApplication.getRestClient()
client.getSomething(object : JsonHttpResponseHandler() {
    override fun onSuccess (statusCode: Int, headers: Headers, json: JSON) {
        System.out.println(json)
    }
});
```
Check out [Android Async HTTP Docs](https://github.com/codepath/AsyncHttpClient) for more request creation details.

### 2. Define the Models

In the `src/com.codepath.apps.restclienttemplate.models`, create the models that represent the key data to be parsed and persisted within your application.

For example, if you were connecting to Twitter, you would want a Tweet model as follows:

```kotlin
// models/Tweet.kt
package com.codepath.apps.restclienttemplate.models

import androidx.room.ColumnInfo
import androidx.room.Embedded

import androidx.room.Entity
# Project 2 - *Name of App Here*

**Name of your app** is an android app that allows a user to view his Twitter timeline. The app utilizes [Twitter REST API](https://dev.twitter.com/rest/public).

Time spent: **X** hours spent in total

## User Stories

The following **required** functionality is completed:

- [x] User can **sign in to Twitter** using OAuth login
- [x]	User can **view tweets from their home timeline**
  - [x] User is displayed the username, name, and body for each tweet
  - [x] User is displayed the [relative timestamp](https://gist.github.com/nesquena/f786232f5ef72f6e10a7) for each tweet "8m", "7h"
- [x] User can refresh tweets timeline by pulling down to refresh

The following **optional** features are implemented:

- [ ] User can view more tweets as they scroll with infinite pagination
- [ ] Improve the user interface and theme the app to feel "twitter branded"
- [ ] Links in tweets are clickable and will launch the web browser
- [ ] User can tap a tweet to display a "detailed" view of that tweet
- [ ] User can see embedded image media within the tweet detail view
- [ ] User can watch embedded video within the tweet
- [ ] User can open the twitter app offline and see last loaded tweets
- [ ] On the Twitter timeline, leverage the CoordinatorLayout to apply scrolling behavior that hides / shows the toolbar.

The following **additional** features are implemented:

- [ ] List anything else that you can get done to improve the app functionality!

## Video Walkthrough

Here's a walkthrough of implemented user stories:

<img src='http://i.imgur.com/link/to/your/gif/file.gif' title='Video Walkthrough' width='' alt='Video Walkthrough' />

GIF created with [LiceCap](http://www.cockos.com/licecap/).

## Notes

Had a hard time understanding the recyclerview and the adapter's connection. Beside that, everything was understandable and usage of API.

## Open-source libraries used

- [Android Async HTTP](https://github.com/codepath/CPAsyncHttpClient) - Simple asynchronous HTTP requests with JSON parsing
- [Glide](https://github.com/bumptech/glide) - Image loading and caching library for Android

## License

    Copyright [yyyy] [name of copyright owner]

    Licensed under the Apache License, Version 2.0 (the "License");
    you may not use this file except in compliance with the License.
    You may obtain a copy of the License at

        http://www.apache.org/licenses/LICENSE-2.0

    Unless required by applicable law or agreed to in writing, software
    distributed under the License is distributed on an "AS IS" BASIS,
    WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
    See the License for the specific language governing permissions and
    limitations under the License.
