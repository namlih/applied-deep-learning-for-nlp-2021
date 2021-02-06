# Applied Deep Learning for NLP - Winter 2020/21

This repository consists of the codebase for *Prediction of Trump vs. Biden support with Sentiment Classification Model trained on YouTube Comments* project. 

Project is seperated into three parts:
1. [Model Creation](#model-creation) explains the model creation process which includes data preparation and model training
2. [API](#api) that includes fastAPI interface for serving the model
3. [Alexa skill](#alexa-skill) that takes the input from the user and uses fastAPI to predict if a given view is more likely to be of a Trump fan or a Biden fan.


### Model Creation

### API

<center><img src="skill_structure.png" width="500"></center>

BERT inference is fast on CPU and it is not possible to load a BERT model on an EC2 instance that comes with the AWS Free Tier. Therefore, API is served locally via `/checkpoliticalview/{view}` endpoint on the port 5000 rather than the alexa-hosted lambda function within alexa console. Since the skill also runs locally, it is trivial to send request using `http://0.0.0.0:5000` from the skill itself. The `view` parameter is taken from the request and fed into the pretrained BERT model to retrieve a prediction e.g. Biden.

The model class is stored in [SentimentClassifier.py](api/SentimentClassifier.py) which is basic BERT model with having a linear layer on top. The training is carried out in the [notebook file](/notebook/model%20training/sentiment_analysis_trump_biden.ipynb).

### Alexa Skill

This alexa skill uses a custom end-point rather than using the default lambda function provided in an Alexa-hosted-skill template. Hence, it is developped locally rather than using the Alexa skill console. With the help of [local_debugger.py](skill/local_debugger.py) file, it is possible to debug the alexa code and improve the development experience. 

When a custom endpoint is used, Alexa requires that endpoint having an https protocol. Luckily, `ngrok` is able to serve localhost with https. 

[en-US.json](skill/skill-package/interactionModels/custom/en-US.json) file describes the interaction model such as the intents, slots and the intenhandler names for them. 

The [lambda_function.py](skill/skill/lambda/lambda_function.py) contains all the logic that tells what to do in case of an intent. For instance, it makes requests to the endpoint served via fastAPI via [main.py](api/main.py) file.