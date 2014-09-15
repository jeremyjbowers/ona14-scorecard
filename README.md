# ONA14 Panel Scorecards
This app allows panel attendees to score their panel in real-time. Other features include:
* Real-time updating for ongoing panels
* Highest-scoring panels
* Global activity view shows scores as they are received

## Getting Started
0a. Assume your mac is [set up like this](http://blog.apps.npr.org/2013/06/06/how-to-setup-a-developers-environment.html)
0b. Export environment variables that Jeremy sent to you. You can do this by editing your `~/.bash_profile` and then running `source ~/.bash_profile` after you have saved it.

```
git clone git@github.com:jeremyjbowers/ona14-scorecard.git && cd ona14-scorecards
pip install -r requirements.txt
./app.py
```

## Architecture
### Server-side
* There is a lightweight flask app that receives POSTed JSON documents representing a single scorecard.
* Each document is stamped with a time and the UUID of the user who generated it as well as a score.
* If we decide to do web sockets, the flask app emits an update each time a score is processed.
* If we decide to poll, a long-running python daemon will write a new JSON file every 15 seconds to S3.
* We'll store documents in [Elasticsearch](http://www.elasticsearch.org/guide/en/elasticsearch/client/python-api/current/).

### Client-side
* We'll use Pym.js to responsively embed an iframed HTML document stored on S3 into our Wordpress-based CMS.
* If we're using web sockets, the client will connect and update the score based on query string parameters passed by Pym from the parent to the child page.
* If we're using polling, the client will AJAX in the correct JSON file from S3 based on query string parameters passed by Pmy from the parent to the child page.

### App Architecture
* We'll want one route for receiving POSTed JSON for storing in Elasticsearch.
* We'll want one route for each panel that might just read JSON from Elasticsearch.
* We'll want one route for the "global update" page that can read JSON from Elasticsearch.
