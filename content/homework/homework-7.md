+++
date = "2016-11-15T18:04:43-08:00"
title = "Homework 7"

+++

# tl;dr

Write code to parse a JSON search result from the Github search API. I
recommend following [this tutorial](http://randomdotnext.com/retrofit-rxjava/).
[Here is a good
URL](https://api.github.com/search/repositories?sort=stars&order=desc&q=created:%3E2016-10-01&per_page=25)
for results that are a list of repos.


# Muy Largo

This week I want you to write a class that makes an HTTP GET request to a JSON
endpoint and parses the response to turn it into a Java object.

There are lots of ways you could go about doing this. I suggest using
[Retrofit](https://square.github.io/retrofit/) and
[RxJava](https://github.com/ReactiveX/RxJava). A good tutorial on how to use
these together can be found [here](http://randomdotnext.com/retrofit-rxjava/).
When I wrote my version of the app, I basically followed that.

## Aside: JSON Endpoints

JSON is an extremely common data serialization format. This means it is easy to
go from objects in code to something that can be written to disk (e.g. to save
as a file or send over the network) and then back into code. I won't give a
whole tutorial here, but it basically lets you use `[ ]` to create an array (or
a list of items), and `{ }` to define a map.

For example, go to
[api.github.com/users/srsudar/repos](https://api.github.com/users/srsudar/repos)
and look at the response. It is a blob of text. If you install a Chrome
extension [like this
one](https://chrome.google.com/webstore/detail/json-formatter/bcjindcccaagfpapjjmafapmmgkkhgoa?hl=en)
you can view the response with formatting and color. It is nice. At the time of
this writing, I see something like:

```json
[
  {
    "id": 18454181,
    "name": "android-primer",
    "full_name": "srsudar/android-primer",
    [ ... snip long code ... ]
  },
  {
    "id": 22605955,
    "name": "android-should-know",
    "full_name": "srsudar/android-should-know"
    [ ... snip long code ... ]
  },
  [ ... snip more items ... ]
]
```

This represents an array of repositories. We know it is an array because the
response starts and ends with `[ ]`. Each element in the array is an object
(because it starts with `{ }`) that has lots of properties. In this case I'm
only showing `id`, `name`, and `full_name`.

Many, many services expose JSON endpoints for you to consume and mess around
with. You can see the whole Github API description
[here](https://developer.github.com/v3/).


## What Does it Mean to Fetch JSON on Android?

There are several steps required to fetch JSON on Android:

1. Get a background thread: Because you can't execute network requests on the
   main thread, you'll need to do something to ensure you make the request on a
   background thread.
1. Issue the network request: Now you have to issue an HTTP GET request to your
   given endpoint.
1. Get the response as a String: HTTP requests respond with a message body. If
   you're talking to a JSON endpoint, this is interpreted as a String.
1. Parse the response: Once you get the response body as a String, you need to
   parse that String into a JSON object. If you're working with JavaScript,
   that is easy, as JSON is a first class citizen in JavaScript. However, since
   Java is a typed languaged, you need to go through an extra step of
   converting the response into Java obects. In our Github API demo above, this
   might mean a `Repo` object with an `int` field `id`, a `String` field
   `name`, and a `String` field `full_name`.

## Retrofit and RxJava

In class I gave an example using Retrofit and RxJava. Retrofit takes care of
steps 2, 3, and 4 for you. All you have to do is provide the `Repo` object (or
a similar object for a different JSON endpoint) and make the request.

You can use RxJava to handle step 1--making a request on a background thread.

Using the two of them together you can handle the steps 1-4 and use JSON on
Android. Neat.


## Goal this Week

The goal for this week is to write Android code that will talk to and parse the
Github API like the one above. This will be a lot of following code tutorials.
I recommend [this one](http://randomdotnext.com/retrofit-rxjava/).

I would start by following that tutorial exactly and querying that API. Then,
if you're following along with the GithubHotness application I'm creating for
the course, you'll want to update it to use the `/search/repositories` API to
query by date created and sort by the number of stars.

A full search URL might look like this:

```
https://api.github.com/search/repositories?sort=stars&order=desc&q=created:%3E2016-10-01&per_page=25
```


Note that that we are adding 'query parameters' to the URL. Query parameters
are the things following the `?` in the URL. They are key-value pairs. Each
pair is separated by `&`. So you could have something like
`example.com?foo=fooVal&bar=barVal`. Here we have two query parameters: `foo`
and `bar`.

In that URL we have `sort`, `order`, `q`, and `per_page`. Documentation on the
search API can be found [here](https://developer.github.com/v3/search/).

To pass these query parameters to Retrofit, you'll need to use the `@Query`
annotation. In my project I've wrapped them in something called
`GithubService`, which looks like the following:

```java
String SEARCH = "/search/repositories";

@GET(SEARCH)
Observable<SearchResponse> searchMostPopularRepos(
    @Query("sort") String sort,
    @Query("order") String order,
    @Query("q") String query,
    @Query("per_page") int perPage);
```

I'm returning a `SearchResponse`, not a list of items. If you navigate to the
[search url](https://api.github.com/search/repositories?sort=stars&order=desc&q=created:%3E2016-10-01&per_page=25) 
you'll see something like this:

```json
{
  "total_count": 1661015,
  "incomplete_results": false,
  "items": []
}
```

The endpoint isn't just giving you a list of repositories, it's actually giving
you some metadata (the `total_count` and `incomplete_results` properties) and
then the results themselves in the `items` property. You need to help Retrofit
know how to parse this. In my case, I care only about the repositories
themselves, so I just have a `List<Repo>` field with an annotation telling
Retrofit that this is called `items` in the response:

```java
public class SearchResponse {
  @SerializedName("items")
  private ArrayList<Repo> repos;

  public List<Repo> getRepos() {
    return repos;
  }

  public void setRepos(ArrayList<Repo> repos) {
    this.repos = repos;
  }

  @Override
  public String toString() {
    return "SearchResponse{" +
        "repos=" + repos +
        '}';
  }
}
```

Your assignment this week is to follow the tutorial, using the code samples
like I've provided here, to parse a list of repositories from a search result.
Ideally, you'd plug this into your `RecyclerView` from last week, but that
might be pushing it. For now, if you are just able to parse the results and log
them, that will be a good step.

If you get stumped, ask me or take a look at how I did it in the [GithubHotness
repo](https://github.com/srsudar/GithubHotness).
