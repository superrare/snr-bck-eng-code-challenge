# Senior Backend Engineering Code Challenge

This should be taken as way to demonstrate your:
* How you structure projects
    * formatting, organization, linting, etc.
* Skills/understanding of TypeScript
* Skills/understanding of NestJS
* Integration with external APIs
* Databases
    * design, queries, migrations, etc.
* Caching
    * design, implementation, strategies, etc.


## The Task
The participant will build a NestJS application that:

1.	Accepts coordinates via a protected webhook (POST endpoint).
1.	Performs a transformation on the coordinates.
1.	Calls the Overpass API to get nearby landmarks.
1.	Saves these landmarks in a SQLite database.
1.	Caches the results.
1.	Exposes a GET endpoint that retrieves them from the cache (fallback to DB).

## NestJS + Overpass Code Challenge

### Overview & Requirements

#### 1. Protected Webhook Endpoint (POST /webhook)
*	Receives a JSON payload with lat and lng.
*	For example:

    ```json
    {
      "lat": 48.8584,
      "lng": 2.2945
    }
    ```


*	Be sure to have some validation in place and return proper HTTP status codes.
* Require `Authorization` header with a secret

#### 2.	Overpass API Call

*	Query the Overpass API at https://overpass-api.de/api/interpreter to find nearby tourist attractions within a 500m (or configurable) radius of the coordinates.
*	Example Curl command:
    ```
    curl -X POST \
       -H "Content-Type: text/plain" \
       --data '[out:json];
       (
         way["tourism"="attraction"](around:500,{{lat}},{{lng}});
         relation["tourism"="attraction"](around:500,{{lat}},{{lng}});
       );
    ```


*	Expect a JSON response containing ways or relations with metadata in the tags property (including name if available).

#### 3.	Store in SQLite
*	Parse the JSON response and store relevant data in a SQLite database.
    *	Perform some transformation on the data to simplify what is stored about landmarks
*	If nothing is found, store the original request coordinates.

#### 4.	Caching
*	Use some form of caching to store the landmarks that will be used for fetching.
* Implement a write-through cache.

#### 5. Data Retrieval Endpoint (GET /landmarks)
* Accepts query parameters lat and lng.
* Use your best judgement on how to design the data that is returned.

#### 6.	Error Handling & Validation
*	If the Overpass API is unreachable or returns an error, handle gracefully (e.g., return 500 with an error message).
*	Validate that lat and lng are valid floats in the expected range (-90 to 90, -180 to 180). Return 400 if invalid.

#### 7.	Testing
*	Demonstrate how to call the POST /webhook endpoint with sample coordinates (e.g., near the Eiffel Tower: 48.8584, 2.2945).
*	Show how to retrieve the landmarks using GET /landmarks?lat=48.8584&lng=2.2945.
*	Verify data is served from the cache after the first retrieval.

#### 8.	Submission
*	Provide instructions in a README on how to install dependencies, run the application, and test the endpoints with curl or a REST client.
*	Include the commands you used, for example:

### Create or migrate the SQLite DB (if needed)

```
npm run db:migrate
```

### Post some coordinates

```bash
curl -X POST -H "Content-Type: application/json" \
  -d '{"lat":48.8584,"lng":2.2945}' \
  http://localhost:3000/webhook
```

### Retrieve the landmarks

```bash
curl "http://localhost:3000/landmarks?lat=48.8584&lng=2.2945"
```
