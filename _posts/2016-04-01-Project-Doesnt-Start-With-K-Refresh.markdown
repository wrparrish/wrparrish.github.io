---
layout: post
title:  "Getting back underway with doesnt start with K"
date:   2016-04-04 16:30:47
categories: kotlin
featured: true
comments: true

---
Welcome back!
The time has come to define ( perhaps redefine ) the scope of our intended project. The original intent was to leverage the Rotten Tomatoes API to generate a reasonable starter application in Kotlin. The intended goals were a application that made network calls, parsed json, and used the data to populate some sort of adapter view. The Rotten Tomatoes API now requires registering for, and getting approval to use, an API key to access their data.

Rather than introduce that burden of responsibility for anyone that was interested in following along,  we will instead use the [SeatGeek API](http://platform.seatgeek.com/) which gives us the benefits of a well organized and documented open API for our use. 

Our goals for version 1 of the project can be summarized as follows:

    1. Identify and call an appropriate SeatGeek endpoint.
    2. Set up the corresponding Data class
    3. Define the relationship between JSON results and Data Class.
    4. Populate UI

### Identify and call an appropriate SeatGeek endpoint.
For the sake of this project,  i believe  SeatGeeks Event class, and endpoint to be a good fit for our needs.  It will allow us to build our rudimentary initial version, as well as allow for additional complexity and interactions, as we iterate over the project.

The Url we will be targetting initially is 'http://api.seatgeek.com/2/events?geoip=true'
One of the nice features of the SeatGeek API is the ability to append an optional argument for geoip.  When set to true, the API will provide events related to the location resolved from the IP the call originates from. This is a handy feature to improve the quality of our results on the initial iteration of the project,  until we can dive into some of the more native location based services.

One of the many convenience features kotlin provides developers,  is a handy abstraction for opening a URL Connection and receiving the response as a string. The implementation of this abstraction is as follows:

{% highlight kotlin %}
  val jsonResponseString = URL(url).readText()
{% endhighlight %}

While behind the scenes we are still making use of an HttpUrlConnection and a BufferedReader,  it's nice to be able avoid the boilerplate setup involved. In order to keep the call off of the main thread we can make us of another convenience feature provided by the language.

{% highlight kotlin %}
async{
      val jsonResponseString = URL(url).readText()
   uiThread { Toast.makeText(context, "Request performed", Toast.LENGTH_LONG).show() }
        }

{% endhighlight %}

This allows us to easily declare the work we want done on a background thread, and yet still provides an easy way to post the results to the UI Thread.  Its comparable to the AsyncTask in Android,  but saves us the boilerplate of Callbacks.

However, for this project i wanted to explore some of the libraries being implemented in the Kotlin community,  and stretch my legs beyond the typical OkHttp / Retrofit stack that i use at work. To that end i have decided to implement [Fuel](https://github.com/kittinunf/Fuel) for the projects networking. You can read all about it, at the link provided.   An implementation of its standard network call when targetting our selected endpoint is as follows:

{% highlight kotlin %}

        async {

          url.httpGet().responseJson { request, response, either ->
                //do something with response
                when (either) {
                    is Either.Left ->{
                    Log.e(javaClass.simpleName , response.toString())
                    
                    }

                    is Either.Right -> {
                        Log.d(javaClass.simpleName, response.toString())

                    }

                }
            }

            uiThread { Toast.makeText(context, "Request performed", Toast.LENGTH_LONG).show() }
        }

{% endhighlight %}

As the documentation states 'either is a functional style data structure that represents data that contains either left or right but not both'.  This makes it well suited to handle the usecase of representing success or failure. Indeed,  it used  for that purpose within the library. It follows a convention of  Left  being an "error"  and Right indicating "success".

The above function returns us the JSON we are looking for.  In the interest of brevity, i have included only the structure of a single Event JSON Object from the response data.  



{% highlight json %}
     {
            "links": [],
            "id": 2796833,
            "stats": {
                "listing_count": null,
                "average_price": null,
                "lowest_price_good_deals": null,
                "lowest_price": null,
                "highest_price": null
            },
            "title": "Promised Land Sound",
            "announce_date": "2015-09-03T00:00:00",
            "score": 0.4543,
            "date_tbd": false,
            "type": "concert",
            "datetime_local": "2015-11-10T18:30:00",
            "visible_until_utc": "2015-11-11T03:30:00",
            "time_tbd": false,
            "taxonomies": [
                {
                    "parent_id": null,
                    "id": 2000000,
                    "name": "concert"
                }
            ],
            "performers": [
                {
                    "stats": {
                        "event_count": 3
                    },
                    "name": "Promised Land Sound",
                    "short_name": "Promised Land Sound",
                    "url": "https://seatgeek.com/promised-land-sound-tickets",
                    "type": "band",
                    "image": null,
                    "home_venue_id": null,
                    "primary": true,
                    "score": 0.45332,
                    "images": {},
                    "slug": "promised-land-sound",
                    "taxonomies": [
                        {
                            "parent_id": null,
                            "id": 2000000,
                            "name": "concert"
                        }
                    ],
                    "has_upcoming_events": true,
                    "id": 277325
                }
            ],
            "url": "https://seatgeek.com/promised-land-sound-tickets/new-york-new-york-mercury-lounge-2015-11-10-6-30-pm/concert/2796833/",
            "created_at": "2015-09-03T00:00:00",
            "venue": {
                "city": "New York",
                "name": "Mercury Lounge",
                "extended_address": "New York, NY 10002",
                "url": "https://seatgeek.com/venues/mercury-lounge/tickets/",
                "country": "US",
                "display_location": "New York, NY",
                "links": [],
                "slug": "mercury-lounge",
                "state": "NY",
                "score": 0.45094,
                "postal_code": "10002",
                "location": {
                    "lat": 40.7223,
                    "lon": -73.9867
                },
                "address": "217 East Houston Street",
                "timezone": "America/New_York",
                "id": 327
            },
            "short_title": "Promised Land Sound",
            "datetime_utc": "2015-11-10T23:30:00",
            "general_admission": true,
            "datetime_tbd": false
        }

{% endhighlight %}

From this data,  we will be able to create the Data classes that will allow us to represent the Events, and its associated values for use within the API.  We will do this in part two, along with preparing the RecyclerView and its supporting cast,  to display this information for us.



    


    


[jekyll]:      http://jekyllrb.com
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-help]: https://github.com/jekyll/jekyll-help
