# Amazon_Vine_Analysis
An analysis of Amazon Vine data following an ETL procedure using PySpark, AWS, SQL and Pandas. 

## Overview
To assist our client, SellBy, with an analysis of Amazon product reviews, we retrieved data from the Amazon Vine program, a service that allows manufacturers and publishers to receive paid member reviews for their products.

For the purposes of our analysis we limited our data-pull to reviews for musical instruments, using the following source: https://s3.amazonaws.com/amazon-reviews-pds/tsv/index.txt. We then used PySpark to perform an extract-transform-load (ETL) process to extract the dataset, transform the data, connect to an AWS RDS instance, and load the transformed data into PgAdmin where we could easily query it using SQL and then export it as a csv file.

To see whether having a paid Vine membership makes a difference in the percentage of 5-star reviews for musical instruments (and so reveals a bias), we performed an additional analysis using Pandas. The results of this analysis are below.

## Results
To examine whether reviews with a Vine subscription revealed a bias as to the number of five-star review ratings, raw data pertaining to musical instruments was extracted from its online source (https://s3.amazonaws.com/amazon-reviews-pds/tsv/amazon_reviews_us_Musical_Instruments_v1_00.tsv.gz) and transformed into a readable dataframe within a Google Colaboratory notebook:

![](images/vine_table_df.png)

This dataframe was then loaded into PgAdmin via a connection with our Amazon AWS database.

![](images/vine_table_SQL.png)

We then exported the resulting vine_table that was uploaded to PgAdmin in part one, as a .csv file, which we read into a Jupyter notebook. From there, the data was further subdivided into four dataframes:

### Dataframe 1: filtered_df

![](images/df1_filtered_df.png)

The dataframe shown above was created to retrieve all the rows where the total_votes count is equal to or greater than 20, in order to select reviews that are more helpful and to avoid having division by zero errors later on.

### Dataframe 2: positive_df

![](images/df2_positive_df.png)

The dataframe shown above was created by further subdividing the first, to retrieve all the rows in the filtered_df dataset where the number of helpful_votes divided by total_votes is equal to or greater than 50%. This yields us reviews that had some audience engagement, and so provides a stronger data pool.

### Dataframe 3: vine_df

![](images/df3_vine_df.png)

This dataframe was created using our positive_df to retrieve all the rows where a review was written as part of the Vine program (paid), vine == 'Y'. The image shown above only shows the first five rows.

### Dataframe 4: no_vine_df

![](images/df4_no_vine_df.png)

This dataframe was created using our positive_df to retrieve all the rows where a review was written from someone with no connection to the Vine program (unpaid), vine == 'N'. The image only shows the first five rows. This gives us a complete dataset that could be used to analyze and compare reviews from individuals with and without a Vine subscription.

Following the creation of the dataframes above, an analysis was performed to retrieve the total number of reviews, the total number of reviews with a five-star rating, and the percentage of five-star reviews for both datasets (i.e., those with a Vine subscription and those without a Vine subscription). The results of this analysis were as follows:

![](images/Summary.png)

As the above summary indicates:
- The total number of reviews in our Vine subscribers dataset was 60.
- The total number of five-star reviews in our Vine subscribers dataset was 34.
- The percentage of five-star reviews in our Vine subscriber dataset it 56.67%.

- The total number of reviews in our non-Vine subscribers dataset waws 14,477.
- The total number of five-star reviews in our non-Vine subscriber dataset was 8,212.
- The percentage of five-star reviews in our non-Vine subscriber dataset was 56.72%.

## Summary
Our analysis shows there is no positivity bias for reviews in the Vine program. While the sizes of the two datasets used to determine the percentage vary widely, because 56.67% of reviews with a Vine subscription were five-star reviews and 56.72% of reviews without a Vine subscription were also five-star reviews, the proximity in these measurements indicates that **having a Vine subscription does not necessarily impact a customer's inclination to make a five-star review.**

To see whether the absence of a postivity bias holds true for other star-ratings, we can also use our dataframes to compare the datasets for single-star reviews, to see whether a Vine subscription impacts a customer's inclination to leave a negative review. 

Running this analysis shows that 0% of the reviews from the Vine subscribers dataset were single-star and only 10.58% of reviews from the non-subscribers dataset gave single-star reviews. This shows a significant positivity bias __against__ issuing a single-star review for Vine subscribers. 

Using the .describe() function from the pandas library, we can also compare other stats from the two dataframes:

![](images/no_vine_df_describe.png)

![](images/vine_df_describe.png)

The above images reveal additional quantifiable data about the relationship between those with a Vine subscription and those without, namely:
- The mean star-rating issued for those with a Vine subscription is 4.38 while the mean star-rating issued for those without a Vine subscription is 4.05.
- The lowest star rating for those without a Vine subscription is 1 while the lowest star rating given by those with a Vine subscription is 2.
- There are also strong correlations between the two datasets for the 25%, 50% and 75% quartiles.