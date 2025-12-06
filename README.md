# Spotify Advanced SQL Project 
Project Category: Advanced
[Click Here to get Dataset](https://www.kaggle.com/datasets/sanjanchaudhari/spotify-dataset)

![Spotify Logo](https://github.com/ranajmi10/Spotify_SQL_project/blob/main/Spotify-Logo.wine.webp)

## Overview
This project involves analyzing a Spotify dataset with various attributes about tracks, albums, and artists using **SQL**. It covers an end-to-end process of normalizing a denormalized dataset, performing SQL queries of varying complexity (easy, medium, and advanced), and optimizing query performance. The primary goals of the project are to practice advanced SQL skills and generate valuable insights from the dataset.

## Schema
```sql
DROP TABLE IF EXISTS spotify;
CREATE TABLE spotify (
    artist VARCHAR(255),
    track VARCHAR(255),
    album VARCHAR(255),
    album_type VARCHAR(50),
    danceability FLOAT,
    energy FLOAT,
    loudness FLOAT,
    speechiness FLOAT,
    acousticness FLOAT,
    instrumentalness FLOAT,
    liveness FLOAT,
    valence FLOAT,
    tempo FLOAT,
    duration_min FLOAT,
    title VARCHAR(255),
    channel VARCHAR(255),
    views FLOAT,
    likes BIGINT,
    comments BIGINT,
    licensed BOOLEAN,
    official_video BOOLEAN,
    stream BIGINT,
    energy_liveness FLOAT,
    most_played_on VARCHAR(50)
);
```

## Business Problems and Solutions

### 1.Retrieve the names of all tracks that have more than 1 billion streams.

```sql
SELECT
       *
FROM spotify 
WHERE stream > 1000000000;
```
**Objective:** Retrieve the names of all tracks that have more than 1 billion streams

### 2. List all albums along with their respective artists.

```sql
SELECT 
      DISTINCT album , artist
FROM spotify 
ORDER BY  1;
```

**Objective:**  Extract a unique mapping between every album title and its artist from the dataset.


### 3. Get the total number of comments for tracks where `licensed = TRUE`.
```sql
 SELECT
        SUM(comments) AS total_comments
FROM spotify
WHERE licensed ='true';
```

**Objective:** Calculate a conditional aggregate metric by focusing only on a specific subset of the data.


### 4. Find all tracks that belong to the album type `single`.
```sql
SELECT 
       * 
FROM spotify 
WHERE album_type='single';
```

**Objective:** Isolate and retrieve a specific category of data within the dataset based on a text-based characteristic.

### 5. Count the total number of tracks by each artist.
```sql
SELECT 
      artist ,
	  COUNT(*) AS total_songs
FROM spotify
GROUP BY 1;
```

**Objective:** Perform a grouped aggregation to determine the contribution of every unique artist in the dataset.

### 6. Calculate the average danceability of tracks in each album.
```sql
SELECT 
      album,
	  AVG(danceability) AS average_danceability
FROM spotify
GROUP BY 1 
ORDER BY 2 DESC ;
```

**Objective:** Perform a segmented statistical calculation to assess the general characteristic of music within specific groupings.

### 7. Find the top 5 tracks with the highest energy values.
```sql
SELECT 
      track,
	  MAX(energy) 
FROM spotify
GROUP BY 1
ORDER BY 2 DESC
LIMIT 5;
```

**Objective:** Rank the data and retrieve the records with the extreme values based on a single numerical metric.

### 8. List all tracks along with their views and likes where `official_video = TRUE`.
```sql
SELECT 
      track ,
	  SUM(views) AS total_views,
	  SUM(likes) AS total_likes	
FROM spotify 
WHERE official_video ='true'
GROUP BY 1
ORDER BY 2 DESC ;
```

**Objective:** Filter the data based on a specific content attribute and then extract key engagement metrics for that subset.

### 9. For each album, calculate the total views of all associated tracks.
```sql
SELECT 
      album,
	  track,
	  SUM(views) AS total_views
FROM spotify 
GROUP BY 1,2
ORDER BY 3 DESC ;
```

**Objective:** Aggregate performance metrics across a category to determine the overall popularity or reach of a grouping entity.

### 10. Retrieve the track names that have been streamed on Spotify more than YouTube.
```sql
SELECT * FROM
(SELECT
       track,
	   COALESCE(SUM(CASE WHEN most_played_on ='Youtube' THEN stream END),0) AS streamed_on_youtube,
	   COALESCE(SUM(CASE WHEN most_played_on ='Spotify' THEN stream END),0) AS streamed_on_spotify
FROM spotify
GROUP BY 1
ORDER BY 2
) AS t1
WHERE 
     streamed_on_spotify > streamed_on_youtube
AND 
    streamed_on_youtube <> 0 ;
```

**Objective:** Perform a comparative, row-level analysis to identify records where a specific condition between two distinct performance metrics is met


### 11. Find the top 3 most-viewed tracks for each artist using window functions.
```sql
WITH ranking_artist
AS 
(SELECT 
       artist,
	   track,
	   SUM(views) AS total_views,
	   DENSE_RANK() OVER(PARTITION BY artist ORDER BY SUM(views) DESC ) AS rank
FROM spotify 
GROUP BY 1,2
ORDER BY 1,3 DESC 
)

SELECT 
      * 
FROM ranking_artist
WHERE rank <= 3 ;
```

**Objective:** Perform sophisticated, partitioned ranking and filtering within a dataset.

### 12. Write a query to find tracks where the liveness score is above the average.
```sql
SELECT
      track,
	  album,
	  liveness
FROM spotify
WHERE liveness > (SELECT AVG(liveness) FROM spotify);
```

**Objective:** Perform a two-step comparative analysis to identify records that perform better than the overall population mean

### 13. Use a `WITH` clause to calculate the difference between the highest and lowest energy values for tracks in each album.
```sql
WITH cte
AS
(SELECT 
	   album,
	   MAX(energy) as highest_energy,
	   MIN(energy) as lowest_energery
FROM spotify
GROUP BY 1
)
SELECT 
	  album,
	  highest_energy - lowest_energery as energy_diff
FROM cte
ORDER BY 2 DESC
```

**Objective:** Perform an advanced, multi-step analysis involving both grouping and calculating a range metric, demonstrating the proper use of Common Table Expressions (CTEs).

## Findings and Conclusion


▪  **Top Performance Identification:**  The analysis identified the specific set of tracks with over 1 billion streams and the Top 5 tracks based on highest energy values, demonstrating the ability to pinpoint extreme high-performing data points.

▪  **Top Performance Identification:**  The analysis identified the specific set of tracks with over 1 billion streams and the Top 5 tracks based on highest energy values, demonstrating the ability to pinpoint extreme high-performing data points.


▪  **Artist Contribution:** The project provided a clear summary of track count per artist, and critically, the Top 3 most-viewed tracks for every single artist using partitioned ranking.  


▪  **Album Consistency vs. Variety:** By calculating the range (difference between max and min energy) for each album, the analysis generated a metric to assess the internal consistency or variation in musical style (energy) within an album.


▪  **Platform Dominance:** The project identified tracks that show a clear preference for streaming on one platform over another, specifically listing tracks streamed more on Spotify than YouTube, which is a crucial cross-platform performance metric.


▪  **Official Content Engagement:** By isolating tracks with official_video = TRUE, the project retrieved the cumulative views and likes for all officially supported content, providing a baseline metric for its audience reach.



