# Propaganda Classification

# Background on Existing Propaganda Classifiers
Most existing classifiers are modeled on data where text is labeled on the article level, and where an article is labeled as propaganda based only on what news outlet the article orginates from. This is dissatisfying because all news sources produce some propaganda and some non-propaganda. I fear datasets that classify propaganda on the news source level are missing the real linguistic markers of propaganda that surpass political party boundaries.

# My Goal
Create a propaganda classifier that can pick up on linguistic features of propaganda irrespective of the political bent or leaning of the source. 

# What is Propaganda?
Propaganda is messaging that undermines the public’s ability to evaluate the reasonableness of statements.

We can find a description of the type of rhetorical techniques that achieves this effect in Michiko Kakutani's  review of Jason Stanley's book How Propaganda Works:

“This is achieved by various time-tested means — by *appealing to the emotions in such a way that rational debate is sidelined* or short-circuited; by *promoting an insider/outsider dynamic* that pollutes the broader conversation with negative stereotypes of out-of-favor groups; and by eroding community standards of 'reasonableness' that depend on 'norms of mutual respect and mutual accountability.'”

My hope, is that by creating a classifier that identifies propaganda on the sentence-level, the model can learn some of these techniques rather than topics or ideas that are disagreeable or threatening to a particular group.

# The Data

I used a dataset of about 18,000 sentences from articles accross different news sources from the 2018 news cycle. Each sentence is labeled as propaganda or not-propaganda. Additionally, the propaganda-class sentences have additional labels concerning the propaganda sub-technique employed. These labels are given by researchers MIT Computer Science and Artificial Intelligence Laboratory (CSAIL) and the Qatar Computing Research Institute (QCRI), HBKU.

After cleaning and manipulating txt files into a dataframe, an observation has the following information:
Sentence text | Propaganda / Non-Propaganda | Propaganda-Type

# 






