# Meilisearch 

Meilisearch is an open source lighting-fast search engine

## Run Locally

- Clone repo

- Run `docker-compose up`

Meilisearch will listen for requests at port 7700

## Meilisearch Architecture

Meilisearch stores data in indexes (synonymous to collections in MongoDB / tables in SQL). An index has one primary key

Indexes are made up of documents (synonymous to records) which are objects consisting of one or more fields

```json
{
    "attribute":"value"
}
```
## Adding Data to Meilisearch

Data needs to be stored in Meilisearch for it to be searched.

Download [movies data](https://docs.meilisearch.com/movies.json) as JSON

### 


```bash
curl \
>   -X POST 'http://127.0.0.1:7700/indexes/movies/documents' \
>   -H 'Content-Type: application/json' \
>   --data-binary @movies.json
```

response

```json
{
    "uid":0,
    "indexUid":"movies",
    "status":"enqueued",
    "type":"documentAddition",
    "enqueuedAt":"2022-03-28T06:36:45.610780527Z"
}
```

Status `enqueued` means that the json file has been queued for processing.

To check the progress of this task send a `GET` request to `/indexes/movies/tasks/:uid`

`uid` param is from the previous response

```bash
curl \
>   -X GET 'http://localhost:7700/indexes/movies/tasks/0'

```

response

```json
{
    "uid":0,
    "indexUid":"movies",
    "status":"succeeded",
    "type":"documentAddition",
    "details":
        {
            "receivedDocuments":19547,
            "indexedDocuments":19546
        
        },
    "duration":"PT20.144687288S",
    "enqueuedAt":"2022-03-28T06:36:45.610780527Z",
    "startedAt":"2022-03-28T06:36:45.799672624Z",
    "finishedAt":"2022-03-28T06:37:05.944359912Z"
}
```

status `succeeded` means that the data has been added to Meilisearch and indexed. Now you can search.

`POST /indexes/movies/search`

request body
```json
{
    "q":"botman"
}
```
using cURL
```bash
curl \
  -X POST 'http://127.0.0.1:7700/indexes/movies/search' \
  -H 'Content-Type: application/json' \
  --data-binary '{ "q": "botman" }'
```

response
```json
{
    "hits":[
        {
            "id":"4786",
            "title":"Rudo & Cursi",
            "poster":"https://image.tmdb.org/t/p/w500/xFGuhPSe1mCRcOllLlJYoNUknNt.jpg",
            "overview":"Two brothers living a hard life of manual labor in rural Mexico  ... Suddenly, they find themselves living the high life of star athletes: fame, fortune, fast cars and beautiful women.",
            "release_date":1229644800,
            "genres":["Comedy","Drama"]
        },
        {
            "id":"5145",
            "title":"The Business",
            "poster":"https://image.tmdb.org/t/p/w500/1iAFzQctlfXLSQXep5ufW7qeDR5.jpg",
            "overview":"During the '80s, a young man named Frankie dreams of escaping London's South East region, ... under his wing. Working as Charlie's driver, Frankie is immersed in a world of fast cars and pretty women -- but all the excess could be his undoing.",
            "release_date":1125622800,
            "genres":["Documentary"]
        }
    ],
        "nbHits":112,
        "exhaustiveNbHits":false,
        "query":"fast cars",
        "limit":20,
        "offset":0,"
        processingTimeMs":7

}
```

Read more on [Meilisearch](https://www.meilisearch.com/)