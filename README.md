# How Factors Influencing Crowd Engagement with News Differ Around the Globe
### _Anindro Bhattacharya_

### Project Blog Page: [https://ab20cs.github.io/projects/news_engagement/](https://ab20cs.github.io/projects/news_engagement/)

## Abstract
Social media has become a medium to facilitate crowd engagement with the news. Large fractions of the public now get their morning headlines by simply reading stories reposted by others on their social media feed (using platforms like Reddit). It is important to understand the mechanism by which news is popularized on social media in hopes that these platforms can adjust their algorithms and policies to limit the spread of misinformation. To gain insight into how news is popularized on Reddit, we investigated whether various factors (such as changing the header when sharing an article, negative sentiment in the shared article and the time of day at which the post was made) are correlated with increased engagement with the posts that share news articles. We analyzed how these trends differ between news subreddits from around the world to better understand if local culture may play a role in news engagement.

## Hypothesis
Changing the title of an article when sharing it on Reddit, the degree of negative sentiment in the news article being shared and the time of day at which a news article is posted on Reddit are all correlated with increased activity on the post.

## Research Questions
1. When posting a link to a news article on Reddit, does making the title of the post different from the original article title impact the activity on the post? If there is an influence, how does this influence differ from region-to-region? 
2. Is the sentiment of a news article shared on Reddit correlated with the activity on the post in subreddits around the world? If so, how does this relationship differ across subreddits?
3. Is the time of day at which a news article was shared on Reddit correlated with the activity on the posts?  

## Datasets Used
We used the [Python Reddit API Wrapper (PRAW)](https://praw.readthedocs.io/en/stable/index.html) which scrapes posts from any subreddit. To use access this data, one must have Reddit account and set up an "application for personal use" through the [Reddit Preferences - Apps page](https://www.reddit.com/prefs/apps). By passing the credentials for this app (i.e., client_id, client_secret, user_agent, username, password) into the API and calling the `subreddit.hot()` function, we can fetch up to 1000 of the top posts from a given subreddit. This fetched data is stored in a Pandas DataFrame. 

By passing the URLs through the `extract` function implemented in the [Goose3 library](https://goose3.readthedocs.io/en/latest/), we can extract necessary information about the article linked (including the article title and content). Any rows whose article is behind a paywall cannot be accessed by Goose3 and has thus been removed from our dataset.

## Methods
We used PRAW to scrape the [r/canadanews](https://reddit.com/r/canadanews), [r/usnews](https://reddit.com/r/usanews), [r/EUnews](https://reddit.com/r/EUnews) and [r/ausnews](https://reddit.com/r/ausnews).

### Analyzing Impact of Changing News Article Headline on Crowd Engagment
We used the Goose3 library to extract the original titles of the articles. To model title similarity, the embeddings of the original titles and their corresponding post titles were constructed using SentenceTransformer's `all-MiniLM-L6-v2` pre-trained model and the cosine similarity between these embeddings were computed. To visualize the distributions of the title similarity values, we constructed a pair of violin plots. One plot shows the distributions split by activity (i.e., one boxplot for posts with activity at or above the 99th percentile [“high” group] and another for posts with activity at or below the 50th percentile [“low” group]). T-tests and Bartlett tests were then used to determine whether there was a signicant difference in the mean and variance (respectively) of the title similarities between the "high" and "low" groups.

### Analyzing Impact of Article Sentiment on Crowd Engagement
We used the Python Goose3 `extract` function to fetch the body text of each article. After extraction, these texts were fed into [Python-SentiStrength](https://github.com/hykilpikonna/PySenti) which scored each article's sentiment on a scale of -5 (extremely negative) to +5 (extremely positive). Similar methods with violin plots, T-tests and Bartlett tests that were used with the headline changing analysis were used to analyze the impact of sentiment on engagement.

### Analyzing Impact of Time of Post on Crowd Engagement
In each dataset constructed, there is a column named `created` which holds the date/time stamp (in the UTC timezone, represented in Unix time) at which each post was made. The `fromtimestamp` function implemented in Python's `datetime` library was used to convert the Unix time to a readable datetime format. These UTC times were then converted to the local time zones of each subreddit analyzed. For regions where there are multiple time zones, the UTC times were converted to the middle time zone in the region (e.g., for Canada and the US, this was Central Standard Time (CST)). To visualize the trends throughout the day, we constructed line graphs by plotting the mean activity of all posts that were posted during each hour of the day.

## Conclusion
Through this study, we made several interesting discoveries. Contrary to our hypothesis (which had been accepted in a paper using data from a decade ago), there is no correlation between changing the title when linking an article on Reddit and activity levels on the post. This finding also underscores the importance of repeating such experiments over time. With the fast changes in the ways information is spread due to technology, the behaviours of users are also shifting. By repeating experiments, we can stay up-to-date on the current state of human behaviour as it relates to engagement with the news. We also discovered that on r/EUnews and r/ausnews, posts with higher activity link to articles with more negative sentiment than articles linked to by lower activity posts (which aligns with our hypothesis). This trend was not observed in r/canadanews and r/usnews, suggesting differences in the factors influencing news popularity among regions. Lastly, our study uncovered patterns which potentially imply that the local time at which posts are made have an influence on the level of activity on the post. 

In future studies, in order to verify if these results generalize to a broader scope, similar methodologies should be applied to more news subreddits as well as other social media platforms on which news is shared.

### Jupyter Notebook with Analysis: [news_engagement_analysis.ipynb](news_engagement_analysis.ipynb)
