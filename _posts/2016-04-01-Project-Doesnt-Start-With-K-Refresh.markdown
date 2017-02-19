---
layout: post
title:  "Getting back underway with doesnt start with K"
date:   2016-04-13 16:30:47
categories: kotlin
featured: true
comments: true

---
Welcome back!

Revisiting our goals for version 1 of the project can be summarized as follows:

    1. Identify and call an appropriate SeatGeek endpoint.
    2. Set up the corresponding Data class
    3. Define the relationship between JSON results and Data Class.
    4. Populate UI

### Identify and call an appropriate SeatGeek endpoint.

{% highlight kotlin %}
     private val URL  = "https://api.seatgeek.com/2/events"
     private val COMPLETE_URL = "$URL?client_id=$clientId&geoip=$useGeoIp"
{% endhighlight %}

To fetch our events we will be using the /events endpoint exposed within the SeatGeek platform.  A recent change has required developers to use a client id  to access the api,  however it's a quick process to register for and obtain one.   To that end,  we are parameterizing the url used for the request with this client id, and a value representing  our desire to use the geoIP  feature of the api, to automatically give us back events relevant to our resolved location. 


### Set up the corresponding Data class

We are going to leverage the data class feature of kotlin to cut down on alot of the boilerplate for generating our response classes.  The structure of the event response call was visited in the last post,  a proper data class representing this structure is as follows:

{% highlight kotlin %}

data class Event(val links: List<Any>,
                 val id: Int,
                 val stats: Stats,
                 val title: String,
                 val announce_date: String,
                 val score: Double,
                 val date_tbd: Boolean,
                 val type: String,
                 val datetime_local: String,
                 val visible_until_utc: String,
                 val time_tbd: Boolean,
                 val taxonomies: List<Taxonomy>,
                 val performers: List<Performer>,
                 val url: String,
                 val created_at: String,
                 val venue: Venue,
                 val short_title: String,
                 val datetime_utc: String,
                 val datetime_tbd: Boolean,
                 val general_admission: Boolean)

{% endhighlight %}

We are keeping this tucked away in the [ServerClasses.kt](https://github.com/wrparrish/DoesntStartWithK/blob/master/app/src/main/java/com/ruthlessprogramming/doesntstartwithk/data/server/ServerClasses.kt)  file, along with the data classes representing the other pertinent types such as performer, taxonomy, and venue etc. There are interesting benefits to be gained from the way kotlin allows you to play with class structure within a file, as well as declaring fields and classes as top level items within a package. Currently i'm only using  that feature as a basic placeholder for some values to be used as parameters for the SeatGeek requests in the [NetParams.kt](https://github.com/wrparrish/DoesntStartWithK/blob/master/app/src/main/java/com/ruthlessprogramming/doesntstartwithk/data/server/NetParams.kt) file,  I'll describe these in more detail, with a more robust implementation in a future post.  


### Define the relationship between JSON results and Data Class

Part of the future planned evolution of this project, is a full bore [clean architecture](https://8thlight.com/blog/uncle-bob/2012/08/13/the-clean-architecture.html) rewrite.  In the interest of adhering to best practices, and to prepare for that eventual evolution,  it's important that we make a distinction between the objects that are the "entities" or core  objects of our project,  and those that are tied to an implementation detail such as a web service / 3rd party api.  To that end, when we build a UI,  we are going to make it reliant on objects that represent a constant feature of the applications intent,  we will make them reliant on our entities, and not the server responses.  This can be achieved through a simple mapping process, where web responses are used to generate  entity / domain model objects. A very basic implementation of our Event domain model object is as follows:

{% highlight kotlin %}

package com.ruthlessprogramming.doesntstartwithk.data.server

import  com.ruthlessprogramming.doesntstartwithk.domain.model.Event as ModelEvent

class ServerDataMapper {

    fun convertEventItemToDomain(event: Event) = with(event) {
       ModelEvent(event.id, event.title, event.url, event.performers.first().image)
    }

    fun convertEventResponseToDomain(result: EventResponse): List<ModelEvent> {
        return result.events.mapIndexed { i, event ->
             convertEventItemToDomain(event)
        }
    }

}

{% endhighlight %}

Here is where we make our associations between two kinds of Events. One is the model of the data structure provided by the seatgeek api that represents the idea of an event, and the other is a pared down version, that acts as our domain event object.   Of particular note is the following import

{% highlight kotlin %}    
import  com.ruthlessprogramming.doesntstartwithk.domain.model.Event as ModelEvent
{% endhighlight %}

this allows us to have two classes named Event,  but to refer to it with greater context and detail, through adjusting the class through the  "as x" feature.   When using the primary constructor on ModelEvent in the above code, we are really creating an object of  type Event, that lives in the domain model.  


### Populate the UI

We are able to generate a list of events by querying the previously mentioned url,  and once mapping them into our expected domain model format, we are ready to make them available in the UI.  Our basic implementation of an activity that lists events is as follows:

{% highlight kotlin %}

class EventListActivity : AppCompatActivity(){
    val recyclerView by lazy { findViewById(R.id.rv_movie_list) as RecyclerView }


    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.screen_event_list)
        setupRecView()
        getEventsFromApi()
    }


    fun setupRecView(){
        val layoutManager: LinearLayoutManager = LinearLayoutManager(applicationContext)
        val orientation: Int = LinearLayoutManager.VERTICAL
        layoutManager.orientation = orientation
        recyclerView.layoutManager = layoutManager

    }

    fun getEventsFromApi() {
        async() {
            val result = RequestEventsCommand().execute()
            runOnUiThread {
                val adapter: EventAdapter = EventAdapter(result)
                recyclerView.adapter = adapter
            }
        }
    }
}

{% endhighlight %}


This is fairly standard stuff and doesn't deviate much from the java implementation.  Of note however are the semantics around our reference to the recyclerview.  Kotlin properties must be initialized with a value, or null ( and thus be made nullable with the ? operator) and to avoid this cluttering our code,  we instead indicate that we want to initialize the recyclerview by lazy instantiation, and pass the require code to do so into a lambda.  This code wont be executed until the first time the recyclerview reference is used,  and we can be sure at that point that the reference is able  to be initialized with the correct value. 


### Wrap-up

With this implementation in place, we have achieved some of the initial alpha milestone goals of this project.   I wanted to get a kotlin project up and running, and start exploring some of the implementation details of using the language in an Android project, as well as begin to explore what the language could do for me and my peers.  While life distractions caused this to take longer than expected,  this is only the beginning of the intended goals for this project.  To follow is  a much more robust implementation that leverages a much more resilient, clean architecture,  and a deeper exploration of kotlins power.


[jekyll]:      http://jekyllrb.com
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-help]: https://github.com/jekyll/jekyll-help