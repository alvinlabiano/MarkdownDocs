# Recommender Engine Documentation
**Background:** We have developed two different recommendation engines, first one is User Article Recommender and second one is Market Basket Analysis. We have used the KNN Algorithm for both systems. 

## Source Code
The source code of both Reco Engines is available in Summit’s git repository. Here is the path of the same.
  - **Path:** https://github.com/Summit-x-Tuple/summit_recommander

## Model Training
As both Reco Engines are developed using KNN algorithm, we are using Python’s surprise library for the Implementation. Here is the path of the training module for both the engines.
- **User Article Recommender:** https://github.com/Summit-x-Tuple/summit_recommander/tree/master/uar_training
- **Market Basket Analysis:** https://github.com/Summit-x-Tuple/summit_recommander/tree/master/uar_training

### Training data Preparation
Training data for both algorithms is exactly the same. We are considering 3 fields in training data they are:
* Summit client ID
* Article Slug
* Time on Page

To control the size of the model and keep recommendations relevant we are training our models only for the last 6 month’s data. Here is the SQL to generate training data:

### Training with SQL
     WITH w1_ut AS (
     SELECT
           ut.summit_client_id
          ,ut.domain
          ,ut.slug
          ,ar.article_title
          ,ut.time_on_page
          ,ut.date_time
          ,cast(substr(ut.date_time, 1, 8) as bigint) date
    FROM archives.tf_articles ar
    JOIN google_analytics.tf_user_tracks_raw ut
    ON (ar.domain = ut.domain AND ar.article_title_id = ut.slug)
    )
    SELECT
    summit_client_
         ,concat(domain, '/', slug) slug
         ,time_on_page
    FROM w1_ut
    WHERE date >= 20200401 AND date <= 20200430
    LIMIT 10

In this SQL change the date range and domain accordingly. Run this SQL on Athena and download the data as a CSV file. Keep this training data CSV file in the training folder of both the Reco Engines and run the training script, it should generate the models.

## Recommendation Inference Module
In this module we generate the predictions/recommendations for the requested users.

User Article Recommender takes **summit_client_id** as input and it returns the slug of top 10 recommended articles by the model. 

On the other hand Market Basket model takes **article slug** as input and generates the recommended slug for the input. Here is the path of both inference modules.

* **UAR inference code:** https://github.com/Summit-x-Tuple/summit_recommander/blob/master/get_recommendation.py
* **MBA inference code:** https://github.com/Summit-x-Tuple/summit_recommander/blob/master/mba_inference.py

## API Server
We are using Python Flask to write our APIs and backend code. Here is the path for code of APIs and server code which responds to all user requests
* **Path:** https://github.com/Summit-x-Tuple/summit_recommander/blob/master/app.py

## ML API Server Architecture
This Server is developed using the Python Flask framework. We are using Nginx as a web server and gunicorn as an application server. We are using the supervisor process to manage the gunicorn server. Here is the architecture / block diagram of ML API Server.

![Test this shit]()





# New Features!

  - Import a HTML file and watch it magically convert to Markdown
  - Drag and drop images (requires your Dropbox account be linked)


You can also:
  - Import and save files from GitHub, Dropbox, Google Drive and One Drive
  - Drag and drop markdown and HTML files into Dillinger
  - Export documents as Markdown, HTML and PDF

Markdown is a lightweight markup language based on the formatting conventions that people naturally use in email.  As [John Gruber] writes on the [Markdown site][df1]

> The overriding design goal for Markdown's
> formatting syntax is to make it as readable
> as possible. The idea is that a
> Markdown-formatted document should be
> publishable as-is, as plain text, without
> looking like it's been marked up with tags
> or formatting instructions.

This text you see here is *actually* written in Markdown! To get a feel for Markdown's syntax, type some text into the left window and watch the results in the right.

### Tech

Dillinger uses a number of open source projects to work properly:

* [AngularJS] - HTML enhanced for web apps!
* [Ace Editor] - awesome web-based text editor
* [markdown-it] - Markdown parser done right. Fast and easy to extend.
* [Twitter Bootstrap] - great UI boilerplate for modern web apps
* [node.js] - evented I/O for the backend
* [Express] - fast node.js network app framework [@tjholowaychuk]
* [Gulp] - the streaming build system
* [Breakdance](https://breakdance.github.io/breakdance/) - HTML to Markdown converter
* [jQuery] - duh

And of course Dillinger itself is open source with a [public repository][dill]
 on GitHub.

### Installation

Dillinger requires [Node.js](https://nodejs.org/) v4+ to run.

Install the dependencies and devDependencies and start the server.

```sh
$ cd dillinger
$ npm install -d
$ node app
```

For production environments...

```sh
$ npm install --production
$ NODE_ENV=production node app
```

### Plugins

Dillinger is currently extended with the following plugins. Instructions on how to use them in your own application are linked below.

| Plugin | README |
| ------ | ------ |
| Dropbox | [plugins/dropbox/README.md][PlDb] |
| GitHub | [plugins/github/README.md][PlGh] |
| Google Drive | [plugins/googledrive/README.md][PlGd] |
| OneDrive | [plugins/onedrive/README.md][PlOd] |
| Medium | [plugins/medium/README.md][PlMe] |
| Google Analytics | [plugins/googleanalytics/README.md][PlGa] |


### Development

Want to contribute? Great!

Dillinger uses Gulp + Webpack for fast developing.
Make a change in your file and instantaneously see your updates!

Open your favorite Terminal and run these commands.

First Tab:
```sh
$ node app
```

Second Tab:
```sh
$ gulp watch
```

(optional) Third:
```sh
$ karma test
```
#### Building for source
For production release:
```sh
$ gulp build --prod
```
Generating pre-built zip archives for distribution:
```sh
$ gulp build dist --prod
```
### Docker
Dillinger is very easy to install and deploy in a Docker container.

By default, the Docker will expose port 8080, so change this within the Dockerfile if necessary. When ready, simply use the Dockerfile to build the image.

```sh
cd dillinger
docker build -t joemccann/dillinger:${package.json.version} .
```
This will create the dillinger image and pull in the necessary dependencies. Be sure to swap out `${package.json.version}` with the actual version of Dillinger.

Once done, run the Docker image and map the port to whatever you wish on your host. In this example, we simply map port 8000 of the host to port 8080 of the Docker (or whatever port was exposed in the Dockerfile):

```sh
docker run -d -p 8000:8080 --restart="always" <youruser>/dillinger:${package.json.version}
```

Verify the deployment by navigating to your server address in your preferred browser.

```sh
127.0.0.1:8000
```

#### Kubernetes + Google Cloud

See [KUBERNETES.md](https://github.com/joemccann/dillinger/blob/master/KUBERNETES.md)


### Todos

 - Write MORE Tests
 - Add Night Mode

License
----

MIT


**Free Software, Hell Yeah!**

[//]: # (These are reference links used in the body of this note and get stripped out when the markdown processor does its job. There is no need to format nicely because it shouldn't be seen. Thanks SO - http://stackoverflow.com/questions/4823468/store-comments-in-markdown-syntax)


   [dill]: <https://github.com/joemccann/dillinger>
   [git-repo-url]: <https://github.com/joemccann/dillinger.git>
   [john gruber]: <http://daringfireball.net>
   [df1]: <http://daringfireball.net/projects/markdown/>
   [markdown-it]: <https://github.com/markdown-it/markdown-it>
   [Ace Editor]: <http://ace.ajax.org>
   [node.js]: <http://nodejs.org>
   [Twitter Bootstrap]: <http://twitter.github.com/bootstrap/>
   [jQuery]: <http://jquery.com>
   [@tjholowaychuk]: <http://twitter.com/tjholowaychuk>
   [express]: <http://expressjs.com>
   [AngularJS]: <http://angularjs.org>
   [Gulp]: <http://gulpjs.com>

   [PlDb]: <https://github.com/joemccann/dillinger/tree/master/plugins/dropbox/README.md>
   [PlGh]: <https://github.com/joemccann/dillinger/tree/master/plugins/github/README.md>
   [PlGd]: <https://github.com/joemccann/dillinger/tree/master/plugins/googledrive/README.md>
   [PlOd]: <https://github.com/joemccann/dillinger/tree/master/plugins/onedrive/README.md>
   [PlMe]: <https://github.com/joemccann/dillinger/tree/master/plugins/medium/README.md>
   [PlGa]: <https://github.com/RahulHP/dillinger/blob/master/plugins/googleanalytics/README.md>
