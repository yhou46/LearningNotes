# ElasticSearch

## Basics

### Indexes
An index is the fundamental unit of storage in Elasticsearch.
To store a document, you add it to a specific index. To search, you target one or more indices. Elasticsearch searches all data within them and returns any matching documents.

- In ElasticSearch, index = table, document = row, field = column from a normal SQL database

- ES also has concept of "data stream" in parallel with "Index".

Index or data stream: Use a regular index when you need frequent updates or deletes. For append-only, time series data such as logs, events, and metrics, use a data stream instead, since data streams manage rolling indices automatically.

Summary
https://www.elastic.co/docs/manage-data/data-store

### Documents

Document in ES is a JSON object, just like a row in traditional SQL DB. Each field in Document must have a mapping.

When a document is created in a index(table), all of its fields are indexed based on their mappings.

Frequently used mappings are:
    - Text
    - Keyword


## How search works
Ref: https://www.elastic.co/docs/solutions/search/full-text/how-full-text-works

## Pros and cons
### Pros:
- Powerful full text search: it supports complex search
    - Searching on multiple fields
- Designed for read heavy workloads, not for write heavy
    - Update a document requires soft deleting existing one and add a new one.

### Cons:
- consume more memory sinch each property is indexed by default
- Not a good idea to use Elasticsearch as your database.
    - Have data consistency issue

## When it should be used
Ref: https://www.elastic.co/docs/deploy-manage/production-guidance/general-recommendations

- Requires complex search

- Usually used with another DB as source of truth. ES needs to be synced with the DB and used for query only

- Return top N result rather than return all result matching a criteria.
    - Paging for all results can be compute consuming

## How does the pagination work in ES?
Ref: https://www.elastic.co/docs/reference/elasticsearch/rest-apis/paginate-search-results

1. Use from/size (not recommended for deep search)
    - from 9000, size 10 returns 9001 to 9010 results matching the search criteria
    - Inefficient for deep search:
        - Each shard needs to get all matching results, sort it. Then ES needs to merge 0-9010 results from all shards and then get the 9001 to 9010 results. The rest of them is just discarded

1. Use search_after (Recommended for deep search)
    - Initial search request will return list of hits, each with a sort property indicate the records
    - Later request should give search_after with the last hit's sort value to make ES start from last hits
    - Saves efforts in the merge part: no need for each shard to send all 9010 records and do the merge, only need 10 records after the last item

    - If a refresh occurs between these requests, the order of your results may change, causing inconsistent results across pages. To prevent this, you can create a point in time (PIT) to preserve the current index state over your searches.


## Inverted index
Ref: https://www.elastic.co/blog/found-elasticsearch-from-the-bottom-up

An inverted index is a data structure used to store mapping from content, such as words or numbers, to its locations in a database, or in this case, documents.

Example: if you store list of books, inverted index stored word mapping to book_ids. Then when you search for books including any words, ES can quickly find it using inverted index.

```shell
# documents
Book ID: 12
content (mappign: text): lazy dog

Book ID: 53
content: (mappign: text): lazy days are best days

# Inverted index:
lazy: [12, 53]
dog: [12]
days: [53]
are: [53]
best: [53]
```

## Doc_values
Ref: https://www.elastic.co/docs/reference/elasticsearch/mapping-reference/doc-values

The doc_values field is an on-disk data structure that is built at document index time and enables efficient data access. It stores the same values as _source, but in a columnar format that is more efficient for sorting and aggregation.

It helps the problem: what is the price of a book when you only want to get the price column.

- No need to query entire document to get a single column field
- Used for sorting and aggregation

Example:
```shell
# Documents
{
    id: doc_1,
    city: "Seattle",
    address: "xxx street, Seattle, 9xxxx, WA"
    updatedAt: "xxx"
}

{
    id: doc_2,
    city: "Bothell",
    address: "xxx street, Bothell, 9xxxx, WA"
    updatedAt: "xxx"
}

{
    id: doc_3,
    city: "Redmond",
    address: "xxx street, Redmond, 9xxxx, WA"
    updatedAt: "xxx"
}

{
    id: doc_47,
    city: "Bothell",
    address: "xxx street, Bothell, 9xxxx, WA"
    updatedAt: "xxx"
}

# Doc_values for city
doc_1   → "Seattle"
doc_2   → "Bothell"
doc_3   → "Redmond"
doc_47  → "Bothell"

```