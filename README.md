# Project 2 - Text Analysis
This is the template for the second project for ECON1680: Machine Learning, Text Analysis, and Economics. This project is intended to be an economic application of text analysis techniques. Over the course of the project, you should fill in the following sections of the Readme.md file below with your research question and motivation, your data sources, your methods, your results, and instructions for replciation. 

## Research Question
Can you reliably predict whether an article is about a male or female athlete based on the words in the article aside from gendered pronouns? And what is the difference between the words in each article if one exists?

## Data
7150 Articles from ESPN's news archives, from the NBA, men's college basketball, and women's college basketball (which includes WNBA articles) sections. The dates range from January 2016 to April 2022. These articles were filtered to only inlcude articles that were tagged as "stories", as opposed to "previews" or "recaps."

## Method
First I had to classify each article as either a 1, if it was about a woman, or 0 if it was about a man, for the logit-lasso model. I did this by counting the number of gendered pronouns in each article and classifying the article as a 1 if the count of female pronouns was higher. After doing this, I removed formatting characters (like “\n”), punctuation, capitalization, and stop words, including the gendered pronouns, to make sure they didn’t get overweighted in the classification later. 

Next, I tried predicting the gender of the subject of each article. For this, I first set up a document term matrix with Tfidf Vectorizer and made a separate vector with the correct categories. I then split the document term matrix and the category vector into training and testing sets and ran a logit-lasso regression. From here I checked the accuracy of the model and the coefficients of the most important words.

On the first iteration, some of the most important words were the names of authors, players, and teams in each league. To correct this, I changed my cleaning method to also eliminate the names of authors. There were too many players and team names to eliminate all of them, and usually they only made up a handful of the top twenty most important words. For my third and final iteration, I predicted the gender categories based on the stemmed words instead of just the clean text to combine the same words with different endings (like “shooting” and “shooter”).


## Results
In the first iteration (Table 1), the logit-lasso regression predicted the correct gender labels with 93.7% accuracy. Three of the top four most important words for predicting men’s articles were “trade”, “playoffs”, and “debate”. This means that many more articles about men’s basketball used these words than women’s articles did. The most important words for women’s articles were author names, with the top three non-names being “pleads”, “nonphysical”, and “pointer”. 

In the second iteration (Table 2) with all the author names removed, the model predicted the labels correctly with 94.4% accuracy. “Playoffs” and “debate” remained among the top six words for men, but the words “anniversary”, “deep” and “blunders” moved up to rank two through four. “Trade” no longer made the list, possibly because part of the word got scraped during this round of cleaning. For the women’s articles, the top non-name words were “heroics”, “pleads”, and “nonphysical”, and they all ranked in the top four most important words overall for women.

On the third iteration (Table 3), the model predicted the labels correctly with 93.7% accuracy. “Playoff” and “debate” remained among the top 20 for men, but moved down quite a bit. “Anniversary”, “blunder” and “deep” were still some of the most important words, but “wife” also became very important. For women, “instruct”, “shoot”, and “scorer” moved up to be in the top ten most important words, along with “heroics” and “pleads”.

Overall, these results suggest that there are some clear differences in what writers write about women versus men other than the named entities. For men, the importance of the words “playoffs”, “debate”, “anniversary” and “trade” shows that men’s basketball is talked about in a way that allows for continuous discussion so that it never leaves the public eye. The NBA playoffs last two months and starts getting media hype when the All-Star break ends in February. After the season, media attention turns toward trades and free-agent signings which can happen until the trade deadline in the following February. On top of that, anniversaries allow for prolonging past storylines into the present, where they can become even more popular. Meanwhile, there are constantly mainstream debates about different aspects of basketball, such as who is the best player, shooter, or defender of all time. 

On the women’s side, the most important words still include positive adjectives like “heroics” or “scorer”, but there aren’t any words that allow for continuous debate which is what keeps the attention of fans. Any player’s feats in one game or series or even season can be described as heroic, but without the right media structure, these stories will be quickly forgotten among hundreds of others. 

Another observation is that the importance of the word “blunders” for men may be evidence of writers trying to avoid their unconscious biases, but overcompensating. I think this causes writers to be more willing to write critically of a male athlete’s on-court performance, as opposed to a female athlete’s performance.


## Replication Instructions
First get the urls for each of the three ESPN archive pages, which all follow the same format with only the month and year changing. You should loop through all the months in the year and all the years from 2016 to 2022, but stopping before May 2022. Then you should scrape all the urls from each of the resulting pages. For the women's pages, you should scrape the text, author, and date from all of the articles. For the men's articles, you should select a random sample of the same size as the women's article list length, using 1680 as the random seed. 

Once the articles are scraped and stored in a dataframe, label the gender of each article by first looking at the url to see if it contains wither "WNBA" or "womens, or "NBA", or "mens" to label accordingly. If the URL contains neither, then label it based on the count of gendered pronouns in the article. Next perfrom a round of basic cleaning to remove punctuation, captials, formatting characters, numbers and stopwords. Then vectorize the clean text using Tfidf vectorizer and run a logit lasso regression on the vectorized text with the gender labels as the classification labels. 

This should result in many authors being present in the women's top 20 most important words. To fix this, do another round of cleaning and remove author names by creating a set of author name tokens from the author column of the dataframe. Then loop through the set and remove each token from the clean text. Also in this round, create a new column of text with the stemmed words.

Next run two more logit-lasso regressions, one on the new clean text without the authors, and one on the stemmed text and print the top 20 coefficients for each gender. Note: for each train test split, the random seed should be 1680.
