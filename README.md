# Big Data: Amazon Vine Analysis

## Overview

As a data market at BigMarket, a big data marketing company, we were asked by a new client ($ellBy) about product reviews on Amazon.  Specifically, they want to know about Amazon's Vine program: a service that allows companies to receive reviews about their products.  These companies provide Amazon with products (on top of paying a fee) which are given to people who are required to publish a review.  Before potentially enrolling in the program, $ellBy wants to know if these paid reviews come with a bias. 

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
print(f"Percentage of paid reviews with 5-star reviews: \033[1m{paid_five_percent:.2f}%\033[0m") ```

![Paid Summary](https://github.com/Jeffstr00/Amazon_Vine_Analysis/blob/main/Resources/paid_summary.png)

![Unpaid Summary](https://github.com/Jeffstr00/Amazon_Vine_Analysis/blob/main/Resources/unpaid_summary.png)

## Summary
