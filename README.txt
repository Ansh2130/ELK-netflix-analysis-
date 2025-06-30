
Netflix ELK Stack Dashboard
===========================

Overview
--------
This project demonstrates the use of the ELK (Elasticsearch, Logstash, Kibana) Stack to visualize and analyze data from the Netflix Titles Dataset. The dataset includes TV Shows and Movies available on Netflix, with details like type, rating, country, release year, etc.

Technologies Used
-----------------
- Elasticsearch 9.0.3
- Logstash 9.0.3
- Kibana 9.0.3
- Dataset: Netflix Titles from Kaggle (converted to CSV)

Setup Instructions
------------------
1. Install ELK Stack
   - Download and extract Elasticsearch, Logstash, and Kibana.
   - Start Elasticsearch:
     bin/elasticsearch.bat
   - Start Kibana:
     bin/kibana.bat

2. Prepare the Dataset
   - Download the Netflix dataset from Kaggle (Excel format).
   - Save it as netflix_titles.csv in a path like:
     C:/elk stack/netflix_titles.csv

3. Create Logstash Config File
   Save the following as netflix.conf:

   input {
     file {
       path => "C:/elk stack/netflix_titles.csv"
       start_position => "beginning"
       sincedb_path => "NUL"
     }
   }

   filter {
     csv {
       separator => ","
       skip_header => "true"
       columns => ["show_id","type","title","director","cast","country","date_added","release_year","rating","duration","listed_in","description"]
     }

     mutate {
       remove_field => ["host"]
     }
   }

   output {
     elasticsearch {
       hosts => ["http://localhost:9200"]
       index => "netflix"
     }
     stdout {}
   }

4. Run Logstash
   bin/logstash.bat -f netflix.conf

5. Go to Kibana
   - URL: http://localhost:5601
   - Create an index pattern: netflix

6. Build Visualizations
   - Created pie charts and bar graphs for fields like:
     - type (TV Show / Movie)
     - rating distribution
     - country
     - release_year

7. Build Dashboard
   - Added all visualizations from the library into a single Kibana Dashboard.

Sample Elasticsearch Queries
----------------------------

1. Find Highly Rated Shows in UK and US
GET netflix/_search
{
  "query": {
    "bool": {
      "must": [
        { "terms": { "country.keyword": ["United States", "United Kingdom"] } },
        { "match": { "rating": "TV-MA" } }
      ]
    }
  }
}

2. Find Indian Movies Released After 2018
GET netflix/_search
{
  "query": {
    "bool": {
      "must": [
        { "match": { "country": "India" } },
        { "match": { "type": "Movie" } },
        { "range": { "release_year": { "gt": 2018 } } }
      ]
    }
  }
}

3. Find Shows (Not From India) In The "Drama" Genre
GET netflix/_search
{
  "query": {
    "bool": {
      "must": [
        { "match": { "listed_in": "Drama" } }
      ],
      "must_not": [
        { "match": { "country": "India" } }
      ]
    }
  }
}

Project Structure
-----------------
.
├── netflix.conf                  # Logstash configuration
├── netflix_titles.csv            # Dataset
├── README.txt                    # Project documentation

Dashboard Access
----------------
Open this in your browser (Kibana):
http://localhost:5601/app/dashboards#/view/your-dashboard-id

Total Time Spent
----------------
Approximately  6 hours

Author
------
Created by Ansh
