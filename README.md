
# Analysing Ask Hacker News and Show Hacker News posts - Project (2)

In this project I am comparing Ask HN posts and Show HN posts to figure out which type of posts receive more comments on average and also if there is any relation to the amount of comments someone gets and the time they posted. Our data set is taken from Kaggles [Hacker News Posts](https://www.kaggle.com/hacker-news/hacker-news-posts).

Let's begin by reading our csv file.


```python
from csv import reader

# The Google Play data set #
opened_file = open("hacker_news.csv")
read_file = reader(opened_file)
hn = list(read_file)

# Printing to see what is in the first 5 rows
for i in hn[:5]:
    print(i)
    print("\n")
```

    ['id', 'title', 'url', 'num_points', 'num_comments', 'author', 'created_at']
    
    
    ['12224879', 'Interactive Dynamic Video', 'http://www.interactivedynamicvideo.com/', '386', '52', 'ne0phyte', '8/4/2016 11:52']
    
    
    ['10975351', 'How to Use Open Source and Shut the Fuck Up at the Same Time', 'http://hueniverse.com/2016/01/26/how-to-use-open-source-and-shut-the-fuck-up-at-the-same-time/', '39', '10', 'josep2', '1/26/2016 19:30']
    
    
    ['11964716', "Florida DJs May Face Felony for April Fools' Water Joke", 'http://www.thewire.com/entertainment/2013/04/florida-djs-april-fools-water-joke/63798/', '2', '1', 'vezycash', '6/23/2016 22:20']
    
    
    ['11919867', 'Technology ventures: From Idea to Enterprise', 'https://www.amazon.com/Technology-Ventures-Enterprise-Thomas-Byers/dp/0073523429', '3', '1', 'hswarna', '6/17/2016 0:01']
    
    


I can see the first row has the headers of the data so I will assign it in another variable.


```python
headers = hn[:1]
hn = hn[1:]

# Confirms header is gone
for i in hn[:5]:
    print(i)
    print("\n")
    
```

    ['12224879', 'Interactive Dynamic Video', 'http://www.interactivedynamicvideo.com/', '386', '52', 'ne0phyte', '8/4/2016 11:52']
    
    
    ['10975351', 'How to Use Open Source and Shut the Fuck Up at the Same Time', 'http://hueniverse.com/2016/01/26/how-to-use-open-source-and-shut-the-fuck-up-at-the-same-time/', '39', '10', 'josep2', '1/26/2016 19:30']
    
    
    ['11964716', "Florida DJs May Face Felony for April Fools' Water Joke", 'http://www.thewire.com/entertainment/2013/04/florida-djs-april-fools-water-joke/63798/', '2', '1', 'vezycash', '6/23/2016 22:20']
    
    
    ['11919867', 'Technology ventures: From Idea to Enterprise', 'https://www.amazon.com/Technology-Ventures-Enterprise-Thomas-Byers/dp/0073523429', '3', '1', 'hswarna', '6/17/2016 0:01']
    
    
    ['10301696', 'Note by Note: The Making of Steinway L1037 (2007)', 'http://www.nytimes.com/2007/11/07/movies/07stein.html?_r=0', '8', '2', 'walterbell', '9/30/2015 4:12']
    
    


## Finding Ask HN and Show HN posts

Now I'll like to sort out the posts based on the ones that have the title "Ask HN" or "Show HN".


```python
ask_posts = []
show_posts = []
other_posts = []

for row in hn:
    title = row[1]
    title_low = title.lower()
    
    if title_low.startswith("ask hn"): 
        ask_posts.append(row)
    elif title_low.startswith("show hn"):
        show_posts.append(row) 
    else:
        other_posts.append(row)
        
print("Number of posts in ask_posts:", len(ask_posts ))
print("Number of posts in show_posts:", len(show_posts))
print("Number of posts in other_posts:", len(other_posts))
```

    Number of posts in ask_posts: 1744
    Number of posts in show_posts: 1162
    Number of posts in other_posts: 17194


Let's print a few posts like the 3 first ones to check out ask_posts has correct values, for example.


```python
for i in ask_posts[:3]:
    print(i)
```

    ['12296411', 'Ask HN: How to improve my personal website?', '', '2', '6', 'ahmedbaracat', '8/16/2016 9:55']
    ['10610020', 'Ask HN: Am I the only one outraged by Twitter shutting down share counts?', '', '28', '29', 'tkfx', '11/22/2015 13:43']
    ['11610310', 'Ask HN: Aby recent changes to CSS that broke mobile?', '', '1', '1', 'polskibus', '5/2/2016 10:14']


## Finding the largest number of average comments in ask posts vs show posts

Let's find the average number of comments existing in ask_posts and show_posts and see which of the two type of posts have the largest average number of comments.


```python
# Finding average number of comments in ask_posts
total_ask_comments = 0
for row in ask_posts:
    total_ask_comments += int(row[4])
    
avg_ask_comments = total_ask_comments/len(ask_posts)
print(avg_ask_comments)
```

    14.038417431192661



```python
# Finding average number of comments in show_posts
total_show_comments = 0
for row in show_posts:
    total_show_comments += int(row[4])
    
avg_show_comments = total_show_comments/len(show_posts)
print(avg_show_comments)
```

    10.31669535283993


From the above results we can see that show posts receive less comments on average than ask posts. 

##  Calculating the amount of ask posts and total number of comments created per hour


```python
import datetime as dt

result_list = []
counts_by_hour = {}
comments_by_hour = {}

# Created_at column is in elemdent of index 6
# and number of comments is in column with index 4
for posts in ask_posts:
    result_list.append([posts[6], int(posts[4])])
                        
for row in result_list:
    row[0] = dt.datetime.strptime(row[0], "%m/%d/%Y %H:%M")
    if row[0].hour not in counts_by_hour:
        counts_by_hour[row[0].hour] = 1
        comments_by_hour[row[0].hour] = row[1]
    else:
        counts_by_hour[row[0].hour] += 1
        comments_by_hour[row[0].hour] += row[1]
        
```


```python
print("Number of ask HN posts created by hour ->", counts_by_hour)
print("\n")
print("Number of ask HN comments created by hour ->", comments_by_hour)
```

    Number of ask HN posts created by hour -> {0: 55, 1: 60, 2: 58, 3: 54, 4: 47, 5: 46, 6: 44, 7: 34, 8: 48, 9: 45, 10: 59, 11: 58, 12: 73, 13: 85, 14: 107, 15: 116, 16: 108, 17: 100, 18: 109, 19: 110, 20: 80, 21: 109, 22: 71, 23: 68}
    
    
    Number of ask HN comments created by hour -> {0: 447, 1: 683, 2: 1381, 3: 421, 4: 337, 5: 464, 6: 397, 7: 267, 8: 492, 9: 251, 10: 793, 11: 641, 12: 687, 13: 1253, 14: 1416, 15: 4477, 16: 1814, 17: 1146, 18: 1439, 19: 1188, 20: 1722, 21: 1745, 22: 479, 23: 543}


## Calculating the average number of comments for posts created during each hour of the day


```python
avg_by_hour = []

for row in comments_by_hour:
    avg_by_hour.append([row, comments_by_hour[row] / counts_by_hour[row]])

avg_by_hour
```




    [[0, 8.127272727272727],
     [1, 11.383333333333333],
     [2, 23.810344827586206],
     [3, 7.796296296296297],
     [4, 7.170212765957447],
     [5, 10.08695652173913],
     [6, 9.022727272727273],
     [7, 7.852941176470588],
     [8, 10.25],
     [9, 5.5777777777777775],
     [10, 13.440677966101696],
     [11, 11.051724137931034],
     [12, 9.41095890410959],
     [13, 14.741176470588234],
     [14, 13.233644859813085],
     [15, 38.5948275862069],
     [16, 16.796296296296298],
     [17, 11.46],
     [18, 13.20183486238532],
     [19, 10.8],
     [20, 21.525],
     [21, 16.009174311926607],
     [22, 6.746478873239437],
     [23, 7.985294117647059]]



## Printing the five highest average number of comments for posts created during each hour of the day


```python
swap_avg_by_hour = []

for row in avg_by_hour:
    swap_avg_by_hour.append([row[1], row[0]])

print(swap_avg_by_hour)
```

    [[8.127272727272727, 0], [11.383333333333333, 1], [23.810344827586206, 2], [7.796296296296297, 3], [7.170212765957447, 4], [10.08695652173913, 5], [9.022727272727273, 6], [7.852941176470588, 7], [10.25, 8], [5.5777777777777775, 9], [13.440677966101696, 10], [11.051724137931034, 11], [9.41095890410959, 12], [14.741176470588234, 13], [13.233644859813085, 14], [38.5948275862069, 15], [16.796296296296298, 16], [11.46, 17], [13.20183486238532, 18], [10.8, 19], [21.525, 20], [16.009174311926607, 21], [6.746478873239437, 22], [7.985294117647059, 23]]



```python
sorted_swap = sorted(swap_avg_by_hour, reverse = True)

print(sorted_swap)
```

    [[38.5948275862069, 15], [23.810344827586206, 2], [21.525, 20], [16.796296296296298, 16], [16.009174311926607, 21], [14.741176470588234, 13], [13.440677966101696, 10], [13.233644859813085, 14], [13.20183486238532, 18], [11.46, 17], [11.383333333333333, 1], [11.051724137931034, 11], [10.8, 19], [10.25, 8], [10.08695652173913, 5], [9.41095890410959, 12], [9.022727272727273, 6], [8.127272727272727, 0], [7.985294117647059, 23], [7.852941176470588, 7], [7.796296296296297, 3], [7.170212765957447, 4], [6.746478873239437, 22], [5.5777777777777775, 9]]



```python
print("Top 5 Hours for Ask Posts Comments")

for avg, hr in sorted_swap[:5]:
    print(
        "{}: {:.2f} average comments per post".format(
            dt.datetime.strptime(str(hr), "%H").strftime("%H:%M"),avg
        )
    )
```

    Top 5 Hours for Ask Posts Comments
    15:00: 38.59 average comments per post
    02:00: 23.81 average comments per post
    20:00: 21.52 average comments per post
    16:00: 16.80 average comments per post
    21:00: 16.01 average comments per post


## Conclusion

From the results above we see that ask HN posts created at 3PM ET/10:00 PM UTC+3 have a higher chance of receiving comments.

ipynb code license: [MPL 2.0](https://www.mozilla.org/en-US/MPL/2.0/)
