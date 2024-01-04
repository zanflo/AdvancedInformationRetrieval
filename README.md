
# Content Accuracy of the gpt4all-falcon model
Advanced Information Retrieval group project 2023/24   
Group 03 with members:  
Patrick Hanfstingl  
Florian Zanotti  
Jakob Zenz
___
___
___
___
# Project Idea
In this project we investigate whether an open source Large Language Model (LLM), in particular gpt4all-falcon, can deliver relevant results in terms of Information Retrieval.
___
# Implementation
Overview of the implementation:
1) Get a dataset containing the titles we use as queries and the corresponding documents, we chose a Wikipedia subset.
2) Give the LLM one query at a time as input and let it generate a document from scratch.
3) Merge the original documents and the documents generated by the LLM, so that the dataset contains an additional 50% of documents generated by the LLM.
4) Retrieve 50 to 100 documents using BM25 (to speed up retrieval)
5) Re-rank these documents using a transformer, specifically MonoBert
6) Sort out documents that have a very low relevance score, they are basically not relevant matches.
7) Now we get a corresponding mixed ranking of LLM and non-LLM documents, which directly translates to the performance of the LLM compared to human written wikipedia articles. Then we calculate some metrics/statistics about the quantity and rank of LLM documents compared to non-LLM documents.
__
## The Choice of the LLM
Gpt4all-falcon (https://gpt4all.io/index.html) has the following features
- Relatively small size at ~4 GB
- Requires only 8 GB of RAM
- Described as the fastest of all gpt4all models
- Has qualitative output that suits our task 
- Performs well on average in various benchmarks compared to other possible models
___ 
## The Choice of the Transformer
MonoBert (https://huggingface.co/castorini/monobert-large-msmarco) has the following features:
- MonoBert adapts Bert for relevance classification 
    - Bert is a pre-trained natural language processing model using a transformer architecture.
    - For more information on how MonoBert works, see the original paper at https://arxiv.org/pdf/1901.04085.pdf or a paper that briefly summarizes the functionality at https://arxiv.org/pdf/2010.06467.pdf, chapter 3.2.1.
- Open source and well known
- Easy to use 
- Performs very well on our validation data
___
## The Choice of the Data Set
We needed a dataset that had the following properties:
- Collection of general widely comprehensive knowledge
- some text we can use as querry to find 

Therfore we chose the Wikipedia summary dataset (https://github.com/tscheepers/Wikipedia-Summary-Dataset) which fullfills this features. Furthermore we use the title as query
___
## Validation of BM25 and MonoBert
### The Choice of the Validation Data Set
### Generation of the Subset
### Outcomes
## Findings
![Alternativer Text](out_2.png)
## Future Improvements
choose another data set for better results