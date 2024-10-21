

# The MLE Challenge solution by [Pablo Dávila Herrero](https://www.linkedin.com/in/pablo-davila-herrero/ "LinkedIn")

- [The MLE Challenge solution by Pablo Dávila Herrero](#the-mle-challenge-solution-by-pablo-dávila-herrero)
  - [Environment setup](#environment-setup)
  - [Challenge 1 - Refactor DEV code](#challenge-1---refactor-dev-code)
  - [Challenge 2 - Build an API](#challenge-2---build-an-api)
  - [Challenge 3 - Dockerize your solution](#challenge-3---dockerize-your-solution)


## Environment setup

This code has been tested for Python 3.12.1. It is recommended to create and activate a virtual environment:

```Bash
# Linux instructions
python -m venv .venv
source ./.venv/bin/activate
```

```powershell
# Windows instructions
python3 -m venv .venv
.\.venv\Scripts\activate
```

You may install all the required dependencies as follows:

```Bash
python -m pip install -r requirements.txt
```


## Challenge 1 - Refactor DEV code

In order to preprocess the provided dataset and train the model I have created new scripts in the [/lab](./lab) directory:

  - [/lab/data/preprocess_ny.py](./lab/data/preprocess_ny.py) is used to manually preprocess the dataset storing the result in [/data](./data)
  - [/lab/models/train_ny_classifier.py](./lab/models/train_ny_classifier.py) is used to manually train a `RandomForestClassifier` model. The trained model is saved as a pickle file in [/models](./models).

These scripts have been optimized for performance by leveraging pandas column-wise operations (faster than iterating with Python directly). I have also prevented data replication through the use of the `inplace=True` parameter and changed the data storage format to parquet.

All the parameters used to determine the behaviour of the scripts have been defined as module-level constants.


## Challenge 2 - Build an API

The code for this challenge can be found in the [/api](./api) directory. I have used [FastAPI](https://fastapi.tiangolo.com/) to create a basic API that uses the trained model in [/api/models](./api/models/) to provide predictions for new listings.

You may run the API for local development with the following command:

```Bash
fastapi dev .\api\main.py
```

**Note**: This requires you to copy the trained model to the [/api/models](./api/models/) directory.

The API can be accessed through the default port (8000) and counts with the following endpoints:

  - `/`: Health check. If successful, it will always return a json with `{"status": "ok"}`
  - `/estimate`: Predict the price category of a listing based on its features
  - `/docs`: Interactive API documentation using [Swagger](https://swagger.io/)

ML models are encapsulated by classes in [/api/estimators](./api/estimators/), while API entity schemas are defined in [/api/schemas](./api/schemas).

You can find the unit tests for this API in [/tests/api_test.py](./tests/api_test.py).


## Challenge 3 - Dockerize your solution

An alternative way to run the API is by using Docker. The most forward way to do so is by using `docker-compose`:

```Bash
docker compose up -d
```

The Dockerfile copies the requirements.txt file before copying the API code, allowing developers to reuse intermediate build steps when code depencencies have not been changed.

A container created with the docker-compose.yml will use the port 80, as well as models in the [/models](./models) directory. Containers will restart if an unexpected error occurs.
