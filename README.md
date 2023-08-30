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
            "type": "date",
            "format": "yyyy-MM-dd"
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
          }
        }
      }
      ```
* open the index
    - POST http://localhost:9200/title/_open

* import documents
    - POST http://localhost:9200/title/_doc
    ```
    {
      "start_date": "1994-01-01",
      "country": "us",
      "genres": ["drama"],
      "is_free": true,
      "total_view_count": 2791,
      "rating": 9.3,
      "title": "The Shawshank Redemption",
      "casts": [{"id": "00001","name": "Tim Robbins"}, {"id": "00002","name": "Morgan Freeman"}],
      "view_count_by_years": [{"year": "1995","view_count": "789"}, {"year": "1996","view_count": "6666"}],
      "cover_image_urls" { "tv": "http://hello.com/the_shawshank_redemption/tv.jpg", "app": "http://hello.com/the_shawshank_redemption/app.jpg", , "web": "http://hello.com/the_shawshank_redemption/web.jpg"},
      "create_date": "2023-08-31"
    }
    ```

