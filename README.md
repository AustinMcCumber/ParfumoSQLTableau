# Parfumo Database Analysis

#### Background

This project is an exploratory analysis of data scraped from the website Parfumo.net. Parfumo is one of the largest review sites and databases for fragrances, and a variety of descriptive statistics will be calculated via PostgreSQL and visualized in Tableau.

For the purposes of this project, I used the app webscraper.io and regex matching to collect data from the top 100 male, female, and unisex fragrance lists on Parfumo. The data was output to an excel spreadsheet with columns that included the fragrance name, brand, perfumer, rating, gender, and the two listed accords for each fragrance. In perfumery, accords are essentially a summary of the scent; in the 300 fragrances I sampled there were 20 listed and contained descriptors such as woody, spicy, sweet, or fruity. Each fragrance was also given a unique ID to serve as the primary key for future analysis. After this, I used sqlizer to quickly input the dataset into PostgreSQL/pgAdmin 4 for analysis. 

Three tables were created: acccords (A list of all accords in the set), perfumers (Primary Key and perfumers) and parfumodb (full table with many columns). Note that the accords table is for calculations only, and the perfumer table has only been created for the purposes of demonstrating a simple inner join. A snippet of the code code to generate and fill these tables follows:

```
CREATE TABLE IF NOT EXISTS perfumers (
    "id" INT PRIMARY KEY,
    "perfumer" TEXT
);
INSERT INTO perfumers VALUES (1,'Aaron Terence Hughes'),
	(2,'Aaron Terence Hughes'),
	(3,'Other'),
	(4,'Other'),
  .....etc;
```


#### Analysis

Now that the tables have been created, we can begin analysis. First, we will use the accords table to find the total number of accords in the database as well as the most common accords. 

```
SELECT COUNT(DISTINCT(accord1))
FROM parfumoaccords;

SELECT accord1, COUNT(accord1)
FROM parfumoaccords
GROUP BY accord1
ORDER BY COUNT(accord1) DESC;

```
<p float = "left">
  <img src="https://user-images.githubusercontent.com/98286027/213352782-106d1218-e22f-4a75-aa39-82f392163b58.png" width=500px height=500px>
  <img src="https://user-images.githubusercontent.com/98286027/213351941-9421910c-9640-43db-afb7-b60e9a47dc29.png" width=500px height=500px>
</p>

We can see that there are 20 unique accords in the database, and the most frequently appearing accords are Spicy(120), Sweet(108), Floral(67), Woody(64), and Oriental(55). Next, we will determine which brands appear on the list the most. 

```
SELECT fraghouse AS brand, COUNT(fraghouse)
FROM parfumosql 
GROUP BY fraghouse
ORDER BY COUNT(fraghouse) DESC
LIMIT 10;

```


<img src="https://user-images.githubusercontent.com/98286027/213354048-c6e3beb9-0639-4858-84cc-99caaf412a08.png" width=500px height=500px>


Guerlain dominates the list with nearly double the amount of entries as Chanel in second place. This is also interesting because in the top 10, we see an even split between niche and designer brands; niche perfumes are known for being generally higher quality, but perhaps the elevated prices lead to less reviews for them. To analyze this further, we will look at the average rating for brands that have at least 3 separate entries in the database. 

```
SELECT fraghouse AS brand, ROUND(AVG(rating), 2)
FROM parfumosql 
GROUP BY fraghouse
HAVING COUNT(fraghouse) >= 3
ORDER BY AVG(rating) DESC;

```
<img src="https://user-images.githubusercontent.com/98286027/213354349-e6b12619-383c-4777-b93f-6f59c40e4623.png" width=500px height=500px>


We can see here that designer niche fragrances here seem to lag slightly. It should also be noted that because this data comes from a top 100 list all fragrances will have relatively high ratings!
Next we will take a look at the most popular individual perfumers. In this case, we will exclude "Other" as a result to filter out unnamed perfumers. 

```
SELECT perfumer, COUNT(perfumer)
FROM perfumers
WHERE perfumer != 'Other'
GROUP BY perfumer
ORDER BY COUNT(perfumer) DESC
LIMIT 5;
```

<img src="https://user-images.githubusercontent.com/98286027/213354629-fe42b72e-cf58-4447-aa2f-d0bf04460b93.png" width=500px height=500px>


The results here make sense when taken into context with the previous findings. Because Guerlain and Roja had so many highly rated perfumes, their eponymous perfumers (Jacques Guerlain, JP Guerlain, Roja Dove) will top the lists. Jacques Polger was an unfamiliar name to me, but it appears that he is responsible for many of the popular Chanel and Dior fragrances which is also in line with the previous analysis. 

Finally, we will use the perfumers table to create an inner join to provide a view that now includes fragrance, brand, perfumer, and rating. Note: this is not for analysis but a simple exercise to demonstrate an inner join. 

```
SELECT fragrance, fraghouse AS brand, perfumers.perfumer, rating
FROM parfumosql
INNER JOIN perfumers
ON parfumosql."ID" = perfumers."id";
```

This achieves the following result:

<img src="https://user-images.githubusercontent.com/98286027/213355110-a6ad2c63-9b83-49b7-bc35-fd751a0988f1.png" width=600px height=250px>

#### Conclusion

In this project we scraped data from a fragrance website, performed exploratory data analysis, and created Tableau visualizations to support our findings. Thank you for reading. 

--Austin McCumber
