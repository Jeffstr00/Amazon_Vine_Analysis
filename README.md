# Big Data: Amazon Vine Analysis

## Overview

As a data market at BigMarket, a big data marketing company, we were asked by a new client ($ellBy) about product reviews on Amazon.  Specifically, they want to know about Amazon's Vine program: a service that allows companies to receive reviews about their products.  These companies provide Amazon with products (on top of paying a fee) which are given to people who are required to publish a review.  Before potentially enrolling in the program, $ellBy wants to know if these paid reviews come with a bias. 

In in attempt to answer that question, we looked at a humongous dataset featuring over ten million Amazon reviews of, in our case, books.  After first loading the dataset onto an Amazon Web Services S3 server and trasferring it to a pgAdmin database, we used PySpark via Google Colab notebooks to dig in and analyze the data.  After breaking down the dataset into Vine and non-Vine reviews, we attempted to see if there was a bias toward favorable reviews or not when the reviews were paid for.

![Vine DataFrame in pgAdmin](https://github.com/Jeffstr00/Amazon_Vine_Analysis/blob/main/Resources/vine_postgres.png)

## Results

## Summary
