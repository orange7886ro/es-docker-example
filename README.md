# es-docker-example

* Elastic search
    - version: 7.10.1
    - cluster: 2 nodes
    - port: 9200

* kibana
   - version: 7.10.1
   - port: 5601



### Remember to set up virtual memory

If your ES has the problem like `max virtual memory areas vm.max_map_count [65530] is too low`
Please set your virtual memory limitation with below

```
sysctl -w vm.max_map_count=262144
```

### Setup ES
* create a index called `title_{today}` and add alias `title`

* close the index to update the settings and mapping
    - POST http://localhost:9200/title/_close
    - PUT http://localhost:9200/title/_settings
        ```
        {
          "analysis": {
              "analyzer": {
                  "kstem_standard_token_analyzer": {
                      "type": "custom",
                      "tokenizer": "standard",
                      "filter": ["lowercase", "kstem"]
                  },
                  "ngram_standard_token_analyzer": {
                      "type": "custom",
                      "tokenizer": "standard",
                      "filter": ["lowercase", "ngram"]
                  },
                  "keyword_token_analyser": {
                      "type": "custom",
                      "tokenizer": "keyword",
                      "filter": "lowercase"
                  },
                  "whitespace_token_analyser": {
                      "type": "custom",
                      "tokenizer": "whitespace",
                      "filter": "lowercase"
                  }
              }
          }
      }
      ```
    - PUT http://localhost:9200/title/_mapping
      ```
      {
        "properties": {
          "start_date": {
            "type": "date"
          },
          "country": {
            "type": "keyword"
          },
          "genres": {
            "type": "keyword"
          },
          "is_free": {
            "type": "boolean"
          },
          "total_view_count": {
            "type": "integer"
          },
          "rating": {
            "type": "float"
          },
          "title": {
            "type": "text",
            "fields": {
              "kstem": {
                "type": "text",
                "analyzer": "kstem_standard_token_analyzer"
              },
              "ngram": {
                "type": "text",
                "analyzer": "ngram_standard_token_analyzer"
              },
              "whitespace": {
                "type": "text",
                "analyzer": "whitespace_token_analyser"
              },
              "keyword": {
                "type": "text",
                "analyzer": "keyword_token_analyser"
              }
            }
          },
          "casts": {
            "type": "nested",
            "properties": {
              "id": {
                "type": "keyword"
              },
              "name": {
                "type": "text"
              }
            }
          },
          "view_count_by_years": {
            "type": "nested",
            "properties": {
              "year": {
                "type": "integer"
              },
              "view_count": {
                "type": "integer"
              }
            }
          },
          "cover_image_urls": {
            "properties": {
              "tv": {
                "type": "keyword"
              },
              "app": {
                "type": "keyword"
              },
              "web": {
                "type": "keyword"
              }
            }
          },
          "create_date": {
            "type": "date"
          }
        }
      }
      ```
* open the index
    - POST http://localhost:9200/title/_open

* import documents
    - POST http://localhost:9200/title/_doc

