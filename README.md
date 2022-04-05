# Big Data: Amazon Vine Analysis

## Overview

As a data market at BigMarket, a big data marketing company, we were asked by a new client ($ellBy) about product reviews on Amazon.  Specifically, they want to know about Amazon's Vine program: a service that allows companies to receive reviews about their products.  These companies provide Amazon with products (on top of paying a fee) which are given to people who are required to publish a review.  Before potentially enrolling in the program, $ellBy wants to know if these paid reviews come with a bias. 

In in attempt to answer that question, we looked at a humongous dataset featuring over ten million Amazon reviews of, in our case, books.  After first loading the dataset onto an Amazon Web Services S3 server and trasferring it to a pgAdmin database, we used PySpark via Google Colab notebooks to dig in and analyze the data.  After breaking down the dataset into Vine and non-Vine reviews, we attempted to see if there was a bias toward favorable reviews or not when the reviews were paid for.

![Vine DataFrame in pgAdmin](https://github.com/Jeffstr00/Amazon_Vine_Analysis/blob/main/Resources/vine_postgres.png)

## Results

After loading in the dataset from our S3 server, we created our Vine DataFrame using `vine_df = df.select(["review_id", "star_rating", "helpful_votes", "total_votes", "vine", "verified_purchase"])`.  However, before we actually began comparing Vine to non-Vine reviews, we had a bit of filtering to do in order to ensure that our results would be more reliable, and not skewed by items with either non-helpful `((helpful_votes/total_votes)>=.5)` or a small number `("total_votes>=20")` of reviews.  This is one advantage of working with such huge datasets: you still have plenty of useful information to work with after trimming away the fat.  

* How many Vine reviews and non-Vine reviews were there?

* How many Vine reviews were 5 stars?  How many non-Vine reviews were 5 stars?

* What percentage of Vine reviews were 5 stars?  What percentage of non-Vine reviews were 5 stars?

## Summary
