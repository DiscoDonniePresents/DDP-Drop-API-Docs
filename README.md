# DDP Drop API

This is a reference about the DDP Drop API which can be accessed at `http://mobile.discodonniepresents.com/wp-admin/admin-ajax.php?action=/mobile/v1/{{NAME_OF_ENDPOINT}}`. The API gets its data from Elasticsearch, so the documentation for Elasticsearch may, to some extend, also be used as a reference for queries to the API.

## Endpoint `events`

### Single event

* **URL:** `http://mobile.discodonniepresents.com/wp-admin/admin-ajax.php?action=/mobile/v1/events`
* **Method:** `GET`
* **Return:** a single event object
* **Params:**
    * `id`: the ID of the event to retrieve
    * `fields:` a JSON array of fields to include (default is all fields)

To query a specific event by something other than the ID, please use the parameters from [Multiple-Events](#multiple-events) below, limiting the response to only return a single event.

#### Examples

Return the event with ID 142137:

```
http://mobile.discodonniepresents.com/wp-admin/admin-ajax.php?action=/mobile/v1/events&id=142137
```

Return the event with ID 144579, but only include the "summary" and "start_date" fields:

```
http://mobile.discodonniepresents.com/wp-admin/admin-ajax.php?action=/mobile/v1/events&id=144579&fields=["summary","start_date"]
```

### Multiple events

* **URL:** `http://mobile.discodonniepresents.com/wp-admin/admin-ajax.php?action=/mobile/v1/events`
* **Method:** `GET`
* **Return:** an array of event objects
* **Params:**
    * `size`: an integer to limit the results to a specific number (default is 10)
    * `offset`: an integer to show results starting at a specific offset (default is 0)
    * `query`: a JSON object with a query to send to Elasticsearch (see [Elasticsearch Query DSL Documentation](https://www.elastic.co/guide/en/elasticsearch/reference/2.x/query-dsl.html) for more information); it should look like `{"bool":{"must":[]}}`, where the array in `"must"` should contain the terms to filter by (default is no specific query)
    * `sort`: a JSON array containing sort objects ordered by sorting priority (see [Elasticsearch Sort Documentation](https://www.elastic.co/guide/en/elasticsearch/reference/2.x/search-request-sort.html) for more information) (default is sort by `start_date` descending)
    * `fields`: a JSON array of fields to include for each object (default is all fields)

#### Examples

Return 5 events by promoter "NightCulture" that happen in the state of "Texas":

```
http://mobile.discodonniepresents.com/wp-admin/admin-ajax.php?action=/mobile/v1/events&size=5&query={"bool":{"must":[{"term":{"promoters.summary":"NightCulture"}},{"term":{"venue.address.state.summary":"Texas"}}]}}
```

Return 20 events by artist "Carnage" at venue "Stereo Live" and sort them by the name of the tour:

```
http://mobile.discodonniepresents.com/wp-admin/admin-ajax.php?action=/mobile/v1/events&size=20&query={"bool":{"must":[{"term":{"artists.summary":"Carnage"}},{"term":{"venue.summary":"Stereo Live"}}]}}&sort=[{"tour.summary":"asc"}]
```

Return events that happen later than February 24th, 2016 at 8AM and sort them by distance (closest events first) from a specific latitude/longitude (secondary sort by the date, ascending):

```
http://mobile.discodonniepresents.com/wp-admin/admin-ajax.php?action=/mobile/v1/events&query={"bool":{"must":[{"range":{"start_date":{"gt":"2016-02-24T08:00:00"}}}]}}&sort=[{"_geo_distance":{"order":"asc","unit":"mi","distance_type":"plane","venue.address.geo":{"lat":29.7630556,"lon":-95.3630556}}},{"start_date":"asc"}]
```
