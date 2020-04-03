# Propaganda Classification

## Background on Existing Propaganda Classifiers
Most existing classifiers are modeled on data where text is labeled on the article level, and where an article is labeled as propaganda based only on the news outlet the article orginates from. This is dissatisfying because all news sources produce some propaganda and some non-propaganda. I fear datasets that classify propaganda on the news source level are missing the real linguistic markers of propaganda that surpass political party boundaries.

## My Goal
Create a propaganda classifier that can pick up on linguistic features of propaganda irrespective of the political bent or leaning of the source. 

## What is Propaganda?
Propaganda is messaging that undermines the public’s ability to evaluate the reasonableness of statements.

We can find a description of the type of rhetorical techniques that achieves this effect in Michiko Kakutani's  review of Jason Stanley's book How Propaganda Works:

“This is achieved by various time-tested means — by *appealing to the emotions in such a way that rational debate is sidelined* or short-circuited; by *promoting an insider/outsider dynamic* that pollutes the broader conversation with negative stereotypes of out-of-favor groups; and by eroding community standards of 'reasonableness' that depend on 'norms of mutual respect and mutual accountability.'”

My hope, is that by creating a classifier that identifies propaganda on the sentence-level, the model can learn some of these techniques rather than topics or ideas that are disagreeable or threatening to a particular group.

## The Data

I used a dataset of about 18,000 sentences from articles accross different news sources from the 2018 news cycle. Each sentence is labeled as propaganda or not-propaganda. Additionally, the propaganda-class sentences have additional labels concerning the propaganda sub-technique employed. These labels are given by researchers MIT Computer Science and Artificial Intelligence Laboratory (CSAIL) and the Qatar Computing Research Institute (QCRI), HBKU.

After cleaning and manipulating txt files into a dataframe, an observation has the following information:
Sentence text | Propaganda / Non-Propaganda | Propaganda-Type

### A Preliminary Look at the Data:
#### Class Distribution of Propaganda and Non-Propaganda Sentences
![Screen Shot 2020-01-22 at 8 12 58 PM](https://user-images.githubusercontent.com/52469561/74769913-40be4600-5259-11ea-899d-04f89c502dee.png)
We will need to account for class imbalance in the modelling stage.

#### Class Distribution for Propaganda Sub-Types
![Screen Shot 2020-01-22 at 8 20 47 PM](https://user-images.githubusercontent.com/52469561/74770032-6f3c2100-5259-11ea-8db5-7dcbdbba17e7.png)

Since there is also a class imbalance for sub-types of propaganda, we may be concerned moving forward that the model is better at classifying over-represented techniques.

## Feature-Engineering
Since we only have text-data, we need to convert this text into numerical representations to input into model.
I have engineered three types of features:

1. meta-features: 
    -grammatical: word count, avg word length, proportion of different parts of speech
    -sentiment analysis
    -biased-word count
2. TFIDF: looking at the rarity or the importance of a token when it comes up in a sentence
3. BERT sentence embeddings: semantics, or meaning, or the sentence.

## Exploring Relevance of Different Kinds of Features

I began by looking at the engineered meta-features, comparing the grammars of propagandistic and non-propagandistic sentences. Overall, there was no significant difference between the two classes. However, propaganda sentences seem to have, on average, a higher word count and lower sentiment score.
![Screen Shot 2020-01-22 at 8 27 32 PM](https://user-images.githubusercontent.com/52469561/74770606-921b0500-525a-11ea-9e3e-994c777651c6.png)
![Screen Shot 2020-01-22 at 8 27 12 PM](https://user-images.githubusercontent.com/52469561/74770612-93e4c880-525a-11ea-8fbd-f512083fd696.png)


When exploring the actual tokens in the sentences, I found that almost all of the top-100 tokens overlapped in both classes. This is not surprising given that all sentences were taken from the 2018 news cycle and are thus, whether propagandistic or non-propagandistic, mostly about the same topics. This suggests that TFIDF may not be a very useful indicator of propaganda.

Here are the top words accross all sentences, and the unique set of words from the top 100 tokens in the propaganda and non-propaganda classes:
![Screen Shot 2020-01-22 at 9 01 05 PM](https://user-images.githubusercontent.com/52469561/74770808-fa69e680-525a-11ea-99a8-303565db91b8.png)
![Screen Shot 2020-02-18 at 2 29 10 PM](https://user-images.githubusercontent.com/52469561/74770859-17061e80-525b-11ea-8f48-a29c0bd6ed2a.png)

We can see above that of the distinct words for each class, propaganda sentences seem to have more references to social categories and ideals, wheras the non-propaganda sentences have more 'journalistic' words. This alligns with our expectations since one of the most prevelent propaganda sub-techniques in the dataset is name-calling and labelling.

Overall, because the grammers and words do not seem to be significantly different in propaganda sentences, getting a semantic representation of the sentences, where contextual meaning is accounted for, might prove more fruitful. For the sentence embeddings I used hugging face's BERT pre-trained sentence embeddings.

## Models
### Criteria:
I optimized the F1 Score for the Propaganda Class. Because of the class-imbalance problem the model has a harder time learning to identify the under-represented class (propaganda), even when balancing the class_weights in the hyperparameters. To address this, I decided to prioritize recall for the propaganda class without overly compromising precision.

### Process:
I optimized 4 different models with different inputs:
model 1: meta-features
model 2: optimized tfidf
model 3: sentence embeddings
model 4: stacked model with optimized logistic regression outputs of preceeding 3 models as inputs
![Screen Shot 2020-02-18 at 2 44 14 PM](https://user-images.githubusercontent.com/52469561/74772024-3bfb9100-525d-11ea-8c17-a7c5454a4d1d.png)

I optimized different classification algorithms for each model input using random search. Other than model 1, all other models performed best with a logistic regression.

### Model Performance:
![Screen Shot 2020-02-18 at 2 44 26 PM](https://user-images.githubusercontent.com/52469561/74772044-44ec6280-525d-11ea-8d8b-6e12f02913c8.png)

Ultimately, model 3, the model that only took in sentence embeddings as input performed the best. Its F1 Score is on par with the researchers' score. However, I decided to make a different trade-off between recall and precision than they did.

## Examples Using Front-End

![Screen Shot 2020-01-23 at 7 28 57 AM](https://user-images.githubusercontent.com/52469561/74772341-cba13f80-525d-11ea-9a47-7fbe73c40a09.png)
![Screen Shot 2020-01-23 at 7 29 23 AM](https://user-images.githubusercontent.com/52469561/74772344-cd6b0300-525d-11ea-9cb9-0cd740b931dc.png)
![Screen Shot 2020-01-23 at 7 30 07 AM](https://user-images.githubusercontent.com/52469561/74772349-ce9c3000-525d-11ea-8165-c8bf55091f7c.png)

## Next-Steps
- While exploring the data, I found some complications with the labelling when it came to instances of reported speech. Moving forward, I would llike to think of how to deal with labelling of sentences where propaganda appears within quotations. It would seem that there are cases where the sentence as a whole shouldn't be classified as propaganda.
- I would like to not just improve the current measures I employed to address class-imbalance between the propaganda and non-propaganda classes, but also within the propaganda sub-classes. I imagine that this model is better and classifying those more prevelent techniques. 
- I would also love to further explore the labelling bias. 
- I think there may be other meta-features that rely on sub-classification tasks that may be useful in identifying propaganda. For example, speech acts help us understand what a speaker is trying to do with her words. Reporting is a different action than persuading, commanding, or subordinating. It may be the case that these latter actions may be propaganda-indicators.





