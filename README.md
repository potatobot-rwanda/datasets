# Datasets

## Getting Started

### Reading List

[LLM Prompting](https://www.promptingguide.ai/)
* Read first section (Introduction and all sub-chapters under Introduction)
* Read [Few shot prompting](https://www.promptingguide.ai/techniques/fewshot)

TimeML standard (especially the TimeX3 standard for temporal expressions)
* [TimeML Website](https://timeml.github.io/site/index.html)
* [TimeX3 specifications](https://timeml.github.io/site/publications/timeMLdocs/timeml_1.2.1.html#timex3)

## Data Formats

We use a custom data format for our training data:

```
<TIMEX3 tid="t313" type="DATE" value="2025-05-30">four weeks ago</TIMEX3>, I planted <POTATO>Ndamira</POTATO> potateos in my field in <LOCATION>Nyamata</LOCATION>.
```

This example contains annotations for all entity types LOCATION, POTATO and TIMEX3.

## Repository Overview

```
├── README.md               # This help file
├── data                    # data directory
│   └── english_examples    # english data
│       ├── eng_dataset.csv # data format to send to the AI models team
│       └── eng_dataset.xml # data generation format
├── requirements.txt
└── src
    └── convert_xml_to_bio.py # convert from the xml data format to the csv dat format
```

## Forkflow

### Workflow overview

1. Create NER data
* Prompt engineering to create prompts that generate high quality data for all three entity classes (location, potato and temporal expressions)
* Generate data for all three entitiy classes, individual and joint with LLMs

2. Annotate NER data
* Develop NER data annotation scheme
* Manually annotate smaller sample (e.g., 100 samples)
* Analyse data quality

3. Develop data for location linking 
* Develop data generation scheme
* Develop data annotation schem
* Analyse data quality


### Generate NER data

I used this prompt to develop my prompt:

```
Generate 5 conversations between a chatbot and a Rwandan farmer. The farmer calls the chatbot because he wants to know when to spray his potatoes. The chatbot asks four questions to the user:

* When did you last spray your potatoes?
* When did you plant your potatoes?
* Where is your farm located?
* Which potato variety do you plant?

Here are some more instructions:
* Assume that today is 28.6.2025
* The chatbot always starts the conversation
* You do not need to create the whole conversation. You need to create at least one chatbot utterance and one user response.
* These are possible potato varieties: Red Bliss, New Potatoes, Fingerling, French Fingerling, Russian Banana, Rose Finn Apple, Austrian Crescent, La Ratte, German Butterball
* The user answers only with the information he was asked for.
* In the user message, annotate the necessary information:
    * last spray date: as TIMEX3 (TIMEML). add a tag option="LAST_SPRAY_DATE"
    * potato plant date: as TIMEX3 (TIMEML). add a tag option="PLANT_DATE"
    * wrap the location with a <LOCATION> tag
    * wrap the potato variety with a <POTATO> tag

Here are examples:

CONVERSATION

Chatbot: Hello, I can tell you when to spray your potatoes. When did you last spray?
User: I last sprayed my potatoes <TIMEX3 type="DATE"  option="LAST_SPRAY_DATE" value="2025-06">in June</TIMEX3>.

CONVERSATION

Chatbot: Where is your farm located?
User: In <LOCATION>Musanze</LOCATION>.
Chatbot: When did you last spray your potatoes?
User: I last sprayed my <POTATO>French Fingerlings</POTATO> <TIMEX3 tid="t171" type="DATE" option="LAST_SPRAY_DATE" value="2025-04">two months ago</TIMEX3>

CONVERSATION

Chatbot: Chatbot: Hello! I can help you spray at the right time. When did you last spray your potatoes?
User: <TIMEX3 type="DATE" value="2025-05-15" option="LAST_SPRAY_DATE">About six weeks ago</TIMEX3>.
Chatbot: When were they planted?
User: <TIMEX3 type="DATE" value="2025-05-15" option="PLANT_DATE">About six weeks ago</TIMEX3>.

CONVERSATION

Chatbot: Hello! I can help you determine the right time to spray. When did you last spray your potatoes?
User: I last sprayed my <POTATO>German Butterball</POTATO> potatoes <TIMEX3 tid="t301" type="DATE" option="LAST_SPRAY_DATE" value="2025-06-12">about two weeks ago</TIMEX3>. 
```

Then, I used this prompt multiple times to generate training data

```
Great! Please generate another 5000 examples in CSV format. 
```