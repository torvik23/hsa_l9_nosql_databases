# HSA L9: Elasticsearch Index & Autocomplete

## Overview
This is an example project to show how to create ES (Elasticsearch) index that will serve autocomplete needs with leveraging typos and errors (max 3 typos if word length is bigger than 7).

## Getting Started

### Preparation
1. Run the docker containers.
```bash
  docker-compose up -d
```

Be sure to use ```docker-compose down -v``` to cleanup after you're done with tests.

2. Generate 'vocabulary' index with the next command:
```
docker exec -it php-fpm php bin/console.php es:reindex
```

## Test scenario
#### 1. Word: `quick`. Length < 7. Mistakes in the word - 1.

Request:
```elasticsearch
{
  "suggest": {
    "word-autocomplete": {
      "text": "qvick",
      "completion": {
        "field": "word",
        "fuzzy": {
          "fuzziness": 0,
          "min_length": 7
        }
      }
    }
  }
}
```

Response:
```elasticsearch
{
  "took": 2,
  "timed_out": false,
  "_shards": {
    "total": 1,
    "successful": 1,
    "skipped": 0,
    "failed": 0
  },
  "suggest": {
    "word-autocomplete": [
      {
        "text": "qvick",
        "offset": 0,
        "length": 5,
        "options": []
      }
    ]
  }
}
```

#### 2. Word: `quicksilver`. Length > 7
##### 2.1 Mistakes in the word - 1.

Request:
```elasticsearch
POST /vocabulary/_search?pretty
{
  "suggest": {
    "word-autocomplete": {
      "text": "quiksilver",
      "completion": {
        "field": "word",
        "fuzzy": {
          "fuzziness": 1,
          "min_length": 7
        }
      }
    }
  }
}
```

Response:
```elasticsearch
{
  "took": 2,
  "timed_out": false,
  "_shards": {
    "total": 1,
    "successful": 1,
    "skipped": 0,
    "failed": 0
  },
  "suggest": {
    "word-autocomplete": [
      {
        "text": "quiksilver",
        "offset": 0,
        "length": 10,
        "options": [
          {
            "text": "quicksilver",
            "_index": "vocabulary",
            "_type": "_doc",
            "_id": "16400",
            "_score": 3,
            "_source": {
              "word": "quicksilver",
              "explanation": "The metal mercury; -- so called from its resemblance to liquid silver. Quicksilver horizon, a mercurial artificial horizon. See under Horizon. -- Quicksilver water, a solution of mercury nitrate used in artificial silvering; quick water."
            }
          },
          {
            "text": "quicksilvered",
            "_index": "vocabulary",
            "_type": "_doc",
            "_id": "20548",
            "_score": 3,
            "_source": {
              "word": "quicksilvered",
              "explanation": "Overlaid with quicksilver, or with an amalgam of quicksilver and tinfoil."
            }
          },
          {
            "text": "quicksilvering",
            "_index": "vocabulary",
            "_type": "_doc",
            "_id": "91599",
            "_score": 3,
            "_source": {
              "word": "quicksilvering",
              "explanation": "The mercury and foil on the back of a looking-glass."
            }
          }
        ]
      }
    ]
  }
}
```

##### 2.2 Mistakes in the word - 2.

Request:
```elasticsearch
{
  "suggest": {
    "word-autocomplete": {
      "text": "quiksillver",
      "completion": {
        "field": "word",
        "fuzzy": {
          "fuzziness": 2,
          "min_length": 7
        }
      }
    }
  }
}
```

Response:
```elasticsearch
{
  "took": 3,
  "timed_out": false,
  "_shards": {
    "total": 1,
    "successful": 1,
    "skipped": 0,
    "failed": 0
  },
  "suggest": {
    "word-autocomplete": [
      {
        "text": "quiksillver",
        "offset": 0,
        "length": 11,
        "options": [
          {
            "text": "quicksilver",
            "_index": "vocabulary",
            "_type": "_doc",
            "_id": "16400",
            "_score": 3,
            "_source": {
              "word": "quicksilver",
              "explanation": "The metal mercury; -- so called from its resemblance to liquid silver. Quicksilver horizon, a mercurial artificial horizon. See under Horizon. -- Quicksilver water, a solution of mercury nitrate used in artificial silvering; quick water."
            }
          },
          {
            "text": "quicksilvered",
            "_index": "vocabulary",
            "_type": "_doc",
            "_id": "20548",
            "_score": 3,
            "_source": {
              "word": "quicksilvered",
              "explanation": "Overlaid with quicksilver, or with an amalgam of quicksilver and tinfoil."
            }
          },
          {
            "text": "quicksilvering",
            "_index": "vocabulary",
            "_type": "_doc",
            "_id": "91599",
            "_score": 3,
            "_source": {
              "word": "quicksilvering",
              "explanation": "The mercury and foil on the back of a looking-glass."
            }
          }
        ]
      }
    ]
  }
}
```

##### 2.3 Mistakes in the word - 3.

Request:
```elasticsearch
{
  "suggest": {
    "word-autocomplete": {
      "text": "quksilve",
      "completion": {
        "field": "word",
        "fuzzy": {
          "fuzziness": 3,
          "min_length": 7
        }
      }
    }
  }
}
```

Response:
```elasticsearch
{
  "took": 2,
  "timed_out": false,
  "_shards": {
    "total": 1,
    "successful": 1,
    "skipped": 0,
    "failed": 0
  },
  "suggest": {
    "word-autocomplete": [
      {
        "text": "quksilve",
        "offset": 0,
        "length": 8,
        "options": [
          {
            "text": "quicksilver",
            "_index": "vocabulary",
            "_type": "_doc",
            "_id": "16400",
            "_score": 2,
            "_source": {
              "word": "quicksilver",
              "explanation": "The metal mercury; -- so called from its resemblance to liquid silver. Quicksilver horizon, a mercurial artificial horizon. See under Horizon. -- Quicksilver water, a solution of mercury nitrate used in artificial silvering; quick water."
            }
          },
          {
            "text": "quicksilvered",
            "_index": "vocabulary",
            "_type": "_doc",
            "_id": "20548",
            "_score": 2,
            "_source": {
              "word": "quicksilvered",
              "explanation": "Overlaid with quicksilver, or with an amalgam of quicksilver and tinfoil."
            }
          },
          {
            "text": "quicksilvering",
            "_index": "vocabulary",
            "_type": "_doc",
            "_id": "91599",
            "_score": 2,
            "_source": {
              "word": "quicksilvering",
              "explanation": "The mercury and foil on the back of a looking-glass."
            }
          }
        ]
      }
    ]
  }
}
```
