# Datasets

## Getting Started

### Reading List

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

This prompt (in chatgpt) will generate training data for 

```
Create 10 sentences in English with temporal expressions in TimeML / TIMEX3 format.  Create a variety of different tepmoral expressins, e.g.

* absolute dates (e.g., 2025-07-01)
* relative dates (e.g., next Tuesday)
* year/month dates (e.g., 2024-12)
* recency references like "last Friday"

Assume that todays date is 28.7.2025.

Use this format:

We are leaving for vacation on <TIMEX3 tid="t1" type="DATE" value="2025-07-05">July 5th</TIMEX3>.
The seminar was held on <TIMEX3 tid="t2" type="DATE" value="2025-06-15">June 15th</TIMEX3>.
She will submit the report by <TIMEX3 tid="t3" type="DATE" value="2025-06-30">Monday</TIMEX3>.
I was born on <TIMEX3 tid="t4" type="DATE" value="1990-02-14">February 14, 1990</TIMEX3>.
```

Output:

```
The invitation was sent on <TIMEX3 tid="t55" type="DATE" value="2025-06-26">Thursday</TIMEX3>.
She’ll begin her new job on <TIMEX3 tid="t56" type="DATE" value="2025-07-08">July 8th</TIMEX3>.
We last met in <TIMEX3 tid="t57" type="DATE" value="2025-05">May</TIMEX3>.
He plans to retire in <TIMEX3 tid="t58" type="DATE" value="2030-01">January 2030</TIMEX3>.
The flight departs on <TIMEX3 tid="t59" type="DATE" value="2025-07-02">Wednesday</TIMEX3>.
She returned from Kenya on <TIMEX3 tid="t60" type="DATE" value="2025-06-21">June 21st</TIMEX3>.
The festival will take place in <TIMEX3 tid="t61" type="DATE" value="2025-09">September</TIMEX3>.
The contract starts on <TIMEX3 tid="t62" type="DATE" value="2025-07-01">Tuesday</TIMEX3>.
I enrolled in university in <TIMEX3 tid="t63" type="DATE" value="2012">2012</TIMEX3>.
The anniversary falls on <TIMEX3 tid="t64" type="DATE" value="2025-08-30">August 30th</TIMEX3>.
```
