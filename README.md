# Big Data: Amazon Vine Analysis

## Overview

As a data analyst at BigMarket, a big data marketing company, we were asked by a new client ($ellBy) about product reviews on Amazon.  Specifically, they want to know about Amazon's Vine program: a service that allows companies to receive reviews about their products.  These companies provide Amazon with products (on top of paying a fee) which are given to people who are required to publish a review.  Before potentially enrolling in the program, $ellBy wants to know if these paid reviews come with a bias. 

In in attempt to answer that question, we looked at a humongous dataset featuring over ten million Amazon reviews of, in our case, books.  After first loading the dataset onto an Amazon Web Services S3 server and trasferring it to a pgAdmin database, we used PySpark via Google Colab notebooks to dig in and analyze the data.  After breaking down the dataset into Vine and non-Vine reviews, we attempted to see if there was a bias toward favorable reviews or not when the reviews were paid for.

![Vine DataFrame in pgAdmin](https://github.com/Jeffstr00/Amazon_Vine_Analysis/blob/main/Resources/vine_postgres.png)

## Results

After loading in the dataset from our S3 server, we created our Vine DataFrame using `vine_df = df.select(["review_id", "star_rating", "helpful_votes", "total_votes", "vine", "verified_purchase"])`.  However, before we actually began comparing Vine to non-Vine reviews, we had a bit of filtering to do in order to ensure that our results would be more reliable.  Filtering by `((helpful_votes/total_votes)>=.5)` ensured that we looked at products with reviews that were largely helpful, while `("total_votes>=20")` left us with items with at least 20 reviews so that they couldn't be easily be skewed by outlier reviews.  This is one advantage of working with such huge datasets: you still have plenty of useful information to work with after trimming away the fat.  Now that we had what should be a solid, reliable dataset to work with, we could get to actually answering the questions:

* **How many Vine reviews and non-Vine reviews were there?**

In order to see if Vine reviews were biased when compared to non-Vine reviews, we had to split the data between the two groups.  We created a DataFrame for paid Vine reviews using `paid_df = helpful_df.filter("vine == 'Y'")`.  Once we had that, we used the .count function to show us how many Vine reviews we had: `paid_total = paid_df.count()`.  We then did the same for unpaid non-Vine reviews (just switching 'Y' to 'N').  In the end, we had a total of 5,012 Vine reviews and 109,297 which were non-Vine.

* **How many Vine reviews were 5 stars?  How many non-Vine reviews were 5 stars?**

In order to see how may 5 star Vine reviews we had, we first filtered the data for star_rating=5 and then once again used the .count function: `paid_five = paid_df.filter("star_rating=5").count()`.  After doing the same for non-Vine reviews, we saw that there were 2,031 (out of 5,012) 5 star Vine reviews and 49,967 (out of 109,297) non-Vine.

* **What percentage of Vine reviews were 5 stars?  What percentage of non-Vine reviews were 5 stars?**

Once we had both our total and number of 5 star reviews for both Vine and non-Vine categories, we could find the percentage of 5 star reviews by using the simple equation: `paid_five_percent = (paid_five/paid_total)*100`.  The percentage of five star reviews was 40.52% for Vine and 45.72% for non-Vine.

After accumulating all of our information, we displayed our results using the following code:
```print("Paid (Vine) Review Summary")
print("======================================================")
print(f"Total number of paid (Vine) reviews: \033[1m{paid_total}\033[0m")
print(f"Number of paid 5-star reviews: \033[1m{paid_five}\033[0m")
print(f"Percentage of paid reviews with 5-star reviews: \033[1m{paid_five_percent:.2f}%\033[0m")
```

![Paid Summary](https://github.com/Jeffstr00/Amazon_Vine_Analysis/blob/main/Resources/paid_summary.png)

![Unpaid Summary](https://github.com/Jeffstr00/Amazon_Vine_Analysis/blob/main/Resources/unpaid_summary.png)

## Summary

At least when looking at five star reviews for books, there does **not** appear to be a positivity bias in the Vine program.  In fact, it is the opposite: unpaid reviews are 45.72% five star while paid reviews receive five star ratings only 40.52% of the time.  Given such a relatively large difference (45.72 vs 40.52%) over such a huge sample size, it is extraordinarily likely that the result is statistically significant, and this was confirmed using [InvestDigital's Significance Calculator](https://www.investisdigital.com/insights/resources-and-tools/statistical-significance-calculator) :
![Statistically Significant](https://github.com/Jeffstr00/Amazon_Vine_Analysis/blob/main/Resources/statistically_significant.png)

How could this be?  At first glance, these results seem counterintuitive.  After all, you might expect compensated reviewers to feel that they need to "pay it back" with a positive review, resulting in a positivity bias for five star reviews.  However, plausible explanations do exist which could explain why that might not be the case.  Paid reviewers might feel that, since they are being paid, they need to do a better job at fairly grading a product instead of following their heart, leading to lower (or at least more balanced) reviews.  Also, in theory, unpaid reviews would be done pretty much exclusively by people who were naturally interested in the product in the first place, while paid reviewers could be doing it just because it is part of their job.  Someone who chooses to pick up and read a Star Wars book on their own accord is probably a fan and thus more likely to reward it with a five star rating.  On the other hand, someone who doesn't care for science fiction but is required to read and review it because they are being paid is less likely to enjoy it, resulting in a lower rating.

However, that does not necessarily mean that there is an overall negative bias for paid Vine reviews.  It is possible that, instead of unpaid reviews always being higher (which was observed in this case with five star reviews), maybe they are more extreme, meaning that they higher five and one star reviews (with Vine reviews being more moderate).  In a way, that would make sense.  Vine reviewers will review the product no matter how they enjoy it: their score will reflect how much they like it, but their liklihood of leaving a review in the first place shouldn't.  However, unpaid reviews might be more likely to be extreme, in either direction.  After all, if I go to a restaurant, I may feel compelled to leave a review either if I have a wonderful time (to reward the company and encourage others to enjoy it as well) or if it's a disaster (to "punish" the business and help save other people from suffering the same fate).  On the other hand, I probably wouldn't feel the need to rush home and leave a review to let everyone know that the food was pretty good, the wait time was average, prices seemed reasonable, etc.

### Additional Study: Are unpaid non-Vine reviews always simply lower, or are they more extreme in either direction?

In order to see if this was the case, I decided to perform further analysis and look at the number of one star reviews left by Vine vs non-Vine reviewers.  This time, `paid_one = paid_df.filter("star_rating=1").count()` was used to find ratings with one star as opposed to five.  After changing some variables around, a similar analysis was run, providing us with the following results:

![Paid One Star Summary](https://github.com/Jeffstr00/Amazon_Vine_Analysis/blob/main/Resources/paid_onestar.png)

For paid Vine reviews, one star ratings were very low at only 4.33%.  Maybe this is a case of companies with poor products not signing up for the Vine program to solicit reviews, or maybe it's a case of reviewers feeling bad for leaving a terrible review when they are being paid and the company provided them with the product.

![Unpaid One Star Summary](https://github.com/Jeffstr00/Amazon_Vine_Analysis/blob/main/Resources/unpaid_onestar.png)

On the other side of the coin, one star reviews were (relatively) much, much higher at 26.39%.  This is likely a result of the fact that having a terrible experience with a product is a strong catalyst for causing someone to choose to go out and write a review.

Overall, it does appear that, instead of unpaid reviews always being higher, it is actually the case that **unpaid non-Vine reviews are more extreme in either direction, while paid Vine views are more evenly spread out and moderate**.  Unpaid reviews were 45.72% five star and 36.39% one star, while Vine reviews were 40.52% and 4.33% respectively.
