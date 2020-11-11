# Flask-VueJs-Template 🌶️✌

> A Flask + Vue.js sample application adapted from [this repository](https://github.com/gtalarico/flask-vuejs-template).

![Vue Logo](/docs/vue-logo.png "Vue Logo") ![Flask Logo](/docs/flask-logo.png "Flask Logo")

## Features

* Minimal Flask 1.0 App
* [Flask-RestPlus](http://flask-restplus.readthedocs.io) API with class-based secure resource routing
* Starter [PyTest](http://pytest.org) test suite
* [vue-cli 3](https://github.com/vuejs/vue-cli/blob/dev/docs/README.md) + yarn
* [Vuex](https://vuex.vuejs.org/)
* [Vue Router](https://router.vuejs.org/)
* [Axios](https://github.com/axios/axios/) for backend communication
* Sample Vue [Filters](https://vuejs.org/v2/guide/filters.html)

## Template Structure

The template uses Flask & Flask-RestPlus to create a minimal REST style API,
and let's VueJs + vue-cli handle the front end and asset pipline.
Data from the python server to the Vue application is passed by making Ajax requests.

### Application Structure

#### Rest Api

The Api is served using a Flask blueprint at `/api/` using Flask RestPlus class-based
resource routing.

#### Client Application

A Flask view is used to serve the `index.html` as an entry point into the Vue app at the endpoint `/`.

The template uses vue-cli 3 and assumes Vue Cli & Webpack will manage front-end resources and assets, so it does overwrite template delimiter.

The Vue instance is preconfigured with Filters, Vue-Router, Vuex; each of these can easilly removed if they are not desired.

#### Important Files

| Location             |  Content                                   |
|----------------------|--------------------------------------------|
| `/app`               | Flask Application                          |
| `/app/api`           | Flask Rest Api (`/api`)                    |
| `/app/client.py`     | Flask Client (`/`)                         |
| `/src`               | Vue App .                                  |
| `/src/main.js`       | JS Application Entry Point                 |
| `/public/index.html` | Html Application Entry Point (`/`)         |
| `/public/static`     | Static Assets                              |
| `/dist/`             | Bundled Assets Output (generated at `yarn build` |
| [`.github/workflows/build_and_deploy.yaml`](.github/workflows/build_and_deploy.yaml) | The deployment workflow |

## Installation

### Before you start

Before getting started, you should have the following installed and running:

- [X] NPM - [instructions](https://yarnpkg.com/en/docs/install#mac-stable)
- [X] Vue Cli 3 - [instructions](https://cli.vuejs.org/guide/installation.html)
- [X] Python 3
- [X] Pipenv (optional)

### Get started

* Clone this repository:

    ```bash
    git clone https://github.com/Azure-Samples/flask-vuejs-webapp.git
    ```

* Setup virtual environment, install dependencies, and activate it:

    ```bash
    python -m venv venv
    source venv/bin/activate  # or ".\venv\Scripts\activate" on Windows
    pip install -r requirements.txt
    ```

* Install JS dependencies

    ```bash
    npm install
    ```

### Development Servers

Run Flask API development server:

```bash
python run.py
```

From another tab in the same directory, start the webpack dev server:

```bash
npm run serve
```

The Vuejs application will be served from `localhost:8080` and the Flask API and static files will be served from `localhost:5000`. The dual dev-server setup allows you to take advantage of webpack's development server with hot module replacement. The proxy is configured in `vue.config.js` and is used to route the requests back to Flask's API on port 5000.

If you would rather run a single dev server, you can run Flask's development server only on `:5000`, but you have to build build the Vue app first and the page will not reload on changes. Run the commands below to build the Vue.js app and serve it on Python's dev server.

```bash
npm build
python run.py
```

## Production Server

This template is configured to be built with GitHub Actions and run on Azure App Service. You can take a look at the GitHub Actions [workflow file](.github/workflows/build_and_deploy.yaml) to see the build and deployment steps.

### JS Build Process

The Vue.js application is built and minified to the `dist/` folder. Once the application is deployed to App Service, Flask serves the static content via the Flask Blueprint in [`client.py`](app/client.py).

### Python Build Process

The GitHub Actions workflow builds and tests the Vue.js and Flask applications before deploying. If the tests pass successfully, the Flask app is deployed to the web app along with the minified Vue.js. Once deployed, the App Service build pipeline will re-install the Python dependencies so they are correctly installed for the runtime image.

### Production Sever Setup

The second job of the deployment workflow uses the Azure CLI to set some configuration settings on the web app. These are outlined below.

- `SCM_DO_BUILD_DURING_DEPLOYMENT`: When set to true, the app setting will force the SCM site to re-build the Flask application. This ensures that the Python libraries are installed properly for the runtime OS.
- `FLASK_ENV`: This app setting tells Flask which configuration to use.
- `startup-script`: This setting tells App Service how to initialize the application. In our case, we use gunicorn to serve the "app" object in [`app.py`](app/__init__.py). For this particular application, set the startup script to `gunicorn --bind=0.0.0.0 --timeout 600 app:app`.
