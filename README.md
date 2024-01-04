# Content Accuracy of the gpt4all-falcon model
Advanced Information Retrieval group project 2023/24   
Group 03 with members:  

**Patrick Hanfstingl**  
**Florian Zanotti**  
**Jakob Zenz**
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
___

## Choosing the LLM
Gpt4all-falcon (https://gpt4all.io/index.html) has the following features
- Relatively small size at ~4 GB
- Requires only 8 GB of RAM
- Described as the fastest of all gpt4all models
- Has qualitative output that suits our task 
- Performs well on average in various benchmarks compared to other possible models
___ 

## Choosing the Transformer
MonoBert (https://huggingface.co/castorini/monobert-large-msmarco) has the following features:
- MonoBert adapts Bert for relevance classification 
    - Bert is a pre-trained natural language processing model using a transformer architecture.
    - For more information on how MonoBert works, see the original paper at https://arxiv.org/pdf/1901.04085.pdf or a paper that briefly summarizes the functionality at https://arxiv.org/pdf/2010.06467.pdf, chapter 3.2.1.
- Open source and well known
- Easy to use 
- Performs very well on our validation data
___

## Choosing the Dataset
We needed a dataset with the following characteristics:
- A collection of general, broad knowledge so that we could test the general performance of gpt4all-falcon.
- Has queries or some text that we could use as queries
- Not too large, as we have very limited resources

A suitable dataset is the Wikipedia summary dataset (https://github.com/tscheepers/Wikipedia-Summary-Dataset). This contains many summaries of Wikipedia pages that are short in length and therefore do not require too many resources. It contains general, broad knowledge. It also has an extra column that contains the titles that we can use as queries.

This dataset still contains too many rows for our capabilities, so we created a subset of it by using only documents that contain the word 'sport' in them. So we basically have a subset with a sports theme.
___
## Validation of BM25 and MonoBert
It is important to validate the implementations of BM25 and monoBert that we use without gpt4all-falcon, so that we can exclude these two as interfering factors in our test. If BM25 and monoBert perform well in the validation, the results are only influenced by the performance of gpt4all-falcon. With this validation we can draw some conclusions about the performance of gpt4all-falcon.

### Choice of validation dataset
The dataset we use must have the following characteristics:
- Similar content to the dataset we use for testing
- Ground truths such as query relevancies (qrels)
- Cannot be too large because of limited resources

The dataset we have chosen that fulfills these criteria is WikIR1k (https://ir-datasets.com/wikir.html). This dataset contains Wikipedia articles with different purpose knowledge, which is identical to the dataset we use for testing. This dataset also contains queries and the corresponding qrels

Note that this set cannot be used to generate the LLM answers, as the queries given are only excerpts from the documents themselves and not topics that an LLM can give answer to.

### Generation of the Subset
The set is too large for our resources, so we took a subset of it. The creation of the subset is worth mentioning. We took all documents containing the word sport. We then added the corresponding queries that we found through qrels to the subset of queries. Then we took all the documents that were a ground truth of the queries in the queries subset and added them to the documents' subset. This ensures that it is possible for BM25 and monoBert to get a perfect match, but we do not falsify the outcome.

### Outcome
TODO
___
## Findings
To evaluate the relevance of LLM-generated content in information retrieval we choose 3 different approaches:

1. The relation between the amounts of LLM generated and wiki documents in the top results
2. The position of the first retrieved LLM and wiki document in the top results
3. The average position of LMM and wiki documents in the top results

All these tests were run twice, once evaluating the first 5 documents retrieved for each of the 100 queries after reranking them using BERT, ignoring if they are deemed relevant enough, and another time by evaluating all the relevant documents retrieved.

Please, note that these results were achieved by using a subset of the wikipediaset containing only 1000 sports articles. We used 100 different queries, each retrieving the 10 most relevant documents, on which these results are based. Refer to the section "Further Improvements"  for more details on how to extend/improve these results.

### 1. The relation between the amounts of LLM generated and wiki documents in the top results
![alt text](/plots/relevance_percent_top_x.png)
![alt text](/plots/relevance_percent_relevant.png)

These two graph show a percentbased representation of the amount of LLM-generated documents and Wikipedia documents retrived by our IR system.

From these two plots we conclude, tath the LLM-generated documents do indeed hold valuable and correct information as they make up a little less than 50% of all documents.
Looking closer at plot 2 however, the Wikipedia articles answer the queries a little better in general as they are retrieved a bit more often.

### 2. The position of the first retrieved LLM and wiki document in the top results
![alt text](/plots/first_index_top_x.png)
![alt text](/plots/first_index_relevant.png)

These two graphs show the position of the first document of one type in the list of retrieved documents. Please not that index 0 denotes, that no document of this type was found within the relevant answers.

The thesis of method one is further underlined by looking at these two plots. Here we can see the two best fitting documents for a given query normally alternate between being LLM generated or from Wikipedia.
The wikipedia document being in the top results is to be expected as the query used is the title of the articles. This further supports our theses, since also the LLM generated document (generated using the same title) shows up here. In order for this to happen, the generated content has to state fatcts about the chosen topic. If the AI  where to produce "random" answers, they would not show up anywhere near the top.

### 3. The average position of LMM and wiki documents in the top results
![alt text](/plots/avg_position_top_x.png)
![alt text](/plots/avg_position_relevant.png)

These two graphs show the average position of the documents separated again by type.

Evaluating these graphs we conclude the LLM generates correct and meaningful data as the top results seem to alternate between both human and AI-generated content.
We can interpret the content of these graphs as a quality metric.

## Future Improvements
Although our results are significant, there are a few things that could be improved:
- Select a different dataset for testing. MonoBert ranks the documents according to the semantics of the queries. The results of the findings could probably be more specific if the queries were semantically more meaningful/detailed.
- Get more resources to:
    - use larger datasets to exclude outliers
    - test the model not only on sports documents but also on general knowledge.
