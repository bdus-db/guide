---
title: API
---

Bradypus is shipped with a read-only JSON Rest API that makes it very easy to 
query and retrieve informations programmatically.

The API is versioned, and the current version is v2. Both v1 and v2 are
available, but if yiu are planning to create applications that make use
of this feature, please consider seriously to use v2, since the previous
version be deprecated very soon.

The API endpoint is available at the `api/` relative URL, eg.:
`https://db.bradypus.net/api/`.

{: .callout-block .callout-block-warning}
The API function must be activated in the main app configuration file in order for the API to work. The API should run as a specific user of the database

Foreach API call the **application**  and a set of **parameters** should be provided in the URL in the form: `https://{base-url}/api/{app-name}/{table-name}?parameters`, eg.: `https://db.bradypus.net/api/test/sites?parameters`

### Available parameters:
- `app` (GET, string, required): a valid app
- `table-name` (GET, string, required): referenced table id
- `verb` (GET, string, required, one of: read, search, inspect): the action the API should run.
At present the following values are available: **read**: will return full structured data for a record,
 **search**: will return a search result. Each `verb` value requires one or more additional parameters
 and **inspect**: will return the list of fields of the referenced table and that of relative plugins

- `id` (GET, int, required for `verb` **read**): the database id of the record to be rendered.

- `type` (GET, string, required for `verb` **search**): type of search to perform.
The available types are: **all**, **recent**, **sqlExpert**, **fast**, **id_array**, **encoded**.

- `limit`(GET, int, optional for `type` **recent**, default: 20): number of total most recent records to return

- `querytext` (GET, string, required for `type` **sqlExpert**): Query (Where statement) to execute
- `join` (GET, string, optional for `type` **sqlExpert**): JOIN statement, if needed
- `fields` (GET, array, optional for `type` **sqlExpert**): array of fields (field id => field label) to require from the database

- `string` (GET, string, required for `type` **fast**): string to search in the database

- `id` (GET, array, required for `typt` **id_array**): array of id to require from the database

- `q_encoded` (GET, string, required for `type` **encoded**): base64_encoded query (Where statement) to execute
- `join` (GET, string, optional for `type` **sqlExpert**): JOIN statement, if needed
- `fields` (GET, array, optional for `type` **sqlExpert**): array of fields (field id => field label) to require from the database

- `records_per_page` (GET, int, optional for `type` **search**, default: 30): maximum number of records per page
- `total_rows` (GET, int, optional): Total number of results for query; if not given will be calculated (used to limit database access)
- `page` (GET, int, optional for `type` **search**, default: 1): Number of page to require
- `fullRecords` (GET, boolean, optional, default false): if true returned records will be complete with all information
- `geojson` (GET, boolean, optional, default false): if true valid geojson (without header) will be returned. It is mandatory that a field named geometry exists, containing WKT data.




## Examples

- Show metadata (field list) for table finds: https://db.bradypus.net/api/ghazni/finds?verb=inspect
- Show metadata (full configuration) for all application: https://db.bradypus.net/api/ghazni/all?verb=inspect
- Show record with id #1: https://db.bradypus.net/api/ghazni/finds?verb=read&id=1
- Get all records from database:
  - first page (no page parameter): https://db.bradypus.net/api/ghazni/finds?verb=search&type=all
  - first page (with page parameter): https://db.bradypus.net/api/ghazni/finds?verb=search&type=all&page=1
  - third page: https://db.bradypus.net/api/ghazni/finds?verb=search&type=all&page=3
- Get most recently entered records:
  - default number (20) of records: https://db.bradypus.net/api/ghazni/finds?verb=search&type=recent
  - custom number (eg. 30) of records: https://db.bradypus.net/api/ghazni/finds?verb=search&type=recent&limit=30
  - custom number (eg. 30) of records, page 2: https://db.bradypus.net/api/ghazni/finds?verb=search&type=recent&limit=30&page=2
- Search a string in anywhere:
  - Search for word **Figurine**: https://db.bradypus.net/api/ghazni/finds?verb=search&type=fast&string=Figurine
  - Search for word **Figurine** and get the second page: https://db.bradypus.net/api/ghazni/finds?verb=search&type=fast&string=Figurine&page=2
- Get a list of records by listing their ids: https://db.bradypus.net/api/ghazni/finds?verb=search&type=id_array&id[]=1&id[]=2
- Execute a custom defined SQL query on the server (all edit actions will be rejected), eg. get records with inventory number  bigger than 10 > SQL: `` `inv_no` > 10 `` > base64_encode'd: `YGludl9ub2A+MTA=` > Url encoded: `YGludl9ub2A%2BMTA%3D` > URl: https://db.bradypus.net/api/ghazni/finds?verb=search&type=encoded&q_encoded=YGludl9ub2A%2BMTA%3D


## Response JSON data structure

### JSON structure of for `verb` **search**
```javascript
{
    "head": { // head part of the document containing metadata
        "query_where": "", // (string) Where statement, plain
        "query_arrived": "", // (string) Full SQL text of the built query
        "query_encoded": "", // (string) base64_econcoded version of the executed query for further use
        "total_rows": "", // (int) total number of records found
        "page": "", // (int) current page number
        "total_pages": "", // (int) Total number of pages found
        "table": "", // (string) Full form of the queried table
        "stripped_table": "", // (string) Cleaned form (no app name and prefix) of queried table
        "table_label": "", // (string) Label of the table name
        "no_records_shown": "", // (int) Number of records shown in the current page
        "query_executed": "", // (string) Full SQL text of the executed query (with pagination limits and sorting)
        "fields": {} // (object) associative object list of fields with field id as key and field label as value
        }
    },
    "records": [ // array of objects records
        {
            "core": { }, // (object) associative object list with field name as key and field value as value
            "coreLinks": [], // (array of objects) array with associative object list with coreLinks
            "allPlugins": [], // (array of objects) array with associative object list with plugin data
            "fullFiles": [], // (array of objects) array with associative object list with file data
            "geodata": [], // (array of objects) array with associative object list with geo data
            "userLinks": [] // (array of objects) array with associative object list with user defined links
        },
        {...}
    ]
}
```

### Single record structure
```javascript
{
  "metadata": {
    "table": "", // referenced table name, complete with prefix
    "stripped_table": "" // referenced table name, without with prefix
    "table_label": "" // referenced table label
  }
  "fields": {}, // (object) associative object list of fields with field id as key and field label as value
  "core": { }, // (object) associative object list with fieldname as key and field value as value
  "coreLinks": [], // (array of objects) array with associative object list with coreLinks
  "backLinks": [], // (array of objects) array with associative object list with backLinks
  "allPlugins": [], // (array of objects) array with associative object list with plugin data
  "fullFiles": [], // (array of objects) array with associative object list with file data
  "geodata": [], // (array of objects) array with associative object list with geo data
  "userLinks": [] // (array of objects) array with associative object list with user defined links
}
```