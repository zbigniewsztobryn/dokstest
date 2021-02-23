---
title: "Basic movie analysis using R"
description: ""
lead: "This is how I took dataframe containing movies. It's about cleaning missing data and get relevant insights. I this task I woud use <b>R</b> with few packages like <b>Stringr</b>, <b>Tibble</b> and <b>Dplyer</b>"
content: ""
date: 2020-10-16T09:19:42+01:00
lastmod: 2020-10-16T09:19:42+01:00
draft: false
weight: 50
images: ["say-hello-to-doks.png"]
contributors: ["R"]
---

<p align="center">
  <img src="https://i.stack.imgur.com/sABdO.png" width="100%" title="hover text">
</p>
I would like to make basic analytics on movies dataset. It includes ranking, preferences by gender, and average production year in particular group age. Its all done in R and is introduction to my work with this language. Commentaries could be well to detailed for some advanced users.

In order to get whole chunk of code without explanation <b><a href="https://github.com/zbigniewsztobryn/worksR/blob/main/Basic%20movie%20analytics%20using%20R.R" target="_blank"> visit this repo on Github→</a></b>




### Let's get started
&emsp;Set working directories in running folder

{{< btn-copy text="setwd(dirname(rstudioapi::getActiveDocumentContext()$path))" >}}
```bash
setwd(dirname(rstudioapi::getActiveDocumentContext()$path))
```

&emsp;Import necessary dataframes.
<b><a href="https://github.com/zbigniewsztobryn/worksR/tree/main/data" target="_blank"> Get datasets→</a></b>
{{< btn-copy text="readRDS('./data/movies.rds') -> movies &#013;readRDS('./data/ratings.rds') -> ratings &#013;readRDS('./data/users.rds') -> users" >}}
```bash
readRDS('./data/movies.rds') -> movies
readRDS('./data/ratings.rds') -> ratings
readRDS('./data/users.rds') -> users
```

&emsp;Adding new column 'year' for later modification

{{< btn-copy text="year <- movies$title &#013;movies&#91;&#34;year&#34;&#93; <- year" >}}

```bash
year <- movies$title
movies["year"] <- year
```

&emsp;Importing packages
{{< btn-copy text="library(stringr) &#013;library(dplyr) &#013;library(tibble)" >}}
```bash
library(stringr)
library(dplyr)
library(tibble)
```

{{< btn-copy text="movies$title <- str_sub(movies$title,1, -8)" >}}

```bash
movies$title <- str_sub(movies$title,1, -8)
```

&emsp;Used <b>complete.cases()</b> in order to get rid of missing values
{{< btn-copy text="nona_movies <- movies&#91;complete.cases(movies$year,movies$movieID),&#93;" >}}
```bash
nona_movies <- movies[complete.cases(movies$year,movies$movieID),]
```

&emsp; Operations on columns
{{< btn-copy text="nona_movies$year <- as.integer(nona_movies$year) &#013;nona_movies <- nona_movies&#91;complete.cases(nona_movies$year),&#93;" >}}
```bash
nona_movies$year <- as.integer(nona_movies$year)
nona_movies <- nona_movies[complete.cases(nona_movies$year),]
```

&emsp; Checking number of elements in dataframe
{{< btn-copy text="str(nona_movies)" >}}
```bash
str(nona_movies)
```
&emsp; >result:
```bash
'data.frame':	3882 obs. of  4 variables:
 $ movieID : int  1 2 3 4 5 6 7 8 9 10 ...
 $ title   : chr  "Toy Story" "Jumanji" "Grumpier Old Men" "Waiting to Exhale" ...
 $ category: chr  "Animation|Children's|Comedy" "Adventure|Children's|Fantasy" "Comedy|Romance" "Comedy|Drama" ...
 $ year    : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
```

&emsp; Counting films in specific years
{{< btn-copy text="nona_movies %>% count(year) -> movies_by_year" >}}
```bash
nona_movies %>% count(year) -> movies_by_year
```
#### Sort top 5 results
&emsp; Sorting and get 5 top values using <b>head()</b> function
{{< btn-copy text="names(movies_by_year)&#91;names(movies_by_year) == 'n'&#93;<- 'total_movies' &#013;movies_by_year&#91;with(movies_by_year, order(total_movies, year,decreasing = TRUE)), &#93; -> sorted_by_year &#013;head(sorted_by_year,5)" >}}
```bash
names(movies_by_year)[names(movies_by_year) == "n"] <- "total_movies"
movies_by_year[with(movies_by_year, order(total_movies, year,decreasing = TRUE)), ] -> sorted_by_year
head(sorted_by_year,5)
```


&emsp; Count sum of users, users by gender, and percentage by gender
{{< btn-copy text="users  %>% count(Gender) -> gender_quant &#013;sum(gender_quant[2]) &#013;gender_quant$n[1] &#013;gender_quant$n[2] &#013;male_percentage <- gender_quant$n[2]*100/sum(gender_quant[2]) &#013;male_percentage &#013;female_percentage <- gender_quant$n[1]*100/sum(gender_quant[2]) &#013;female_percentage &#013;" >}}

```bash
users  %>% count(Gender) -> gender_quant
sum(gender_quant[2])
gender_quant$n[1]
gender_quant$n[2]

male_percentage <- gender_quant$n[2]*100/sum(gender_quant[2])
male_percentage

female_percentage <- gender_quant$n[1]*100/sum(gender_quant[2])
female_percentage
```
&emsp; Connecting two dataframes and grouping them by rates
{{< btn-copy text="movies_and_ratings <- ratings %>% inner_join(nona_movies,by='movieID') &#013;aggregate( &#013;movies_and_ratings$Rating, list( &#013;movies_and_ratings$title &#013;), mean) -> ratings_calculated &#013;names(ratings_calculated)[names(ratings_calculated) == &#34;Group.1&#34;] <- &#34;title&#34;" >}}

```bash
movies_and_ratings <- ratings %>% inner_join(nona_movies,by="movieID")

aggregate(
  movies_and_ratings$Rating, list(
    movies_and_ratings$title
    ), mean) -> ratings_calculated

names(ratings_calculated)[names(ratings_calculated) == 'Group.1'] <- "title"
```

&emsp;It seems like we get some of the movies which have 5.0 rate, but not enough rate number to consider them in general ranking. Now I would do some operations like connectig columns and cleaning names. Finally I am going to use <b>filter()</b> in order to get get rid of movies with less than 100 rates. Now <b>head()</b> will show top 1 movie and top 100 movies without <b>outliers</b>.

{{< btn-copy text="movies_and_ratings %>% count(title) -> rating_quant &#013;ranking <- ratings_calculated %>% inner_join(rating_quant,by='title') &#013;names(ranking)[names(ranking) == 'x'] <- 'rank_mean' &#013;names(ranking)[names(ranking) == 'n'] <- 'rank_quant' &#013;ranking <- filter(ranking, rank_quant > 100) &#013;ranking[with(ranking, order(rank_mean,decreasing = TRUE)), ] -> ranking &#013;ranking_1 <- head(ranking,1) &#013;select (ranking_1,c(title)) &#013;ranking_100 <- head(ranking,100)" >}}
```bash
movies_and_ratings %>% count(title) -> rating_quant

ranking<-ratings_calculated %>% inner_join(rating_quant,by="title")

names(ranking)[names(ranking) == "x"]<-"rank_mean"
names(ranking)[names(ranking) == "n"]<-"rank_quant"

ranking <- filter(ranking,rank_quant > 100)

ranking[with(ranking,order(rank_mean,decreasing=TRUE)),]->ranking

ranking_1 <- head(ranking,1)
select (ranking_1,c(title))
ranking_100 <- head(ranking,100)
```

&emsp; Drop rank_quant column, join dataframes and create two dataframe instances for each gender
{{< btn-copy text="select(ranking_100,-c(rank_quant)) &#013;full_df <- movies_and_ratings %>% inner_join(users,by='userID') &#013;male_rates <- filter(full_df, Gender == 'M') &#013;female_rates <- filter(full_df, Gender == 'F')" >}}

```bash
select(ranking_100,-c(rank_quant))
full_df <- movies_and_ratings %>% inner_join(users,by="userID")

male_rates <- filter(full_df, Gender == 'M')
female_rates <- filter(full_df, Gender == 'F')
```

&emsp; Function for getting top 10 movies by gender. I need to pick up right column, than make some operations like <b>mean</b>. Finally I need to sort results and get top 10.
{{< btn-copy text="" >}}

```bash
rating_by_gender <- function(gender_rate){

  gender_rate <- select(gender_rate, c(Rating, title))

  rates_count <- gender_rate %>% count(title)

  rates_mean <-aggregate(
    gender_rate$Rating, list(
      gender_rate$title
    ), mean)

  names(rates_count)[names(rates_count) == "n"] <- "votes_quant"
  names(rates_mean)[names(rates_mean) == "x"] <- "rank"
  names(rates_mean)[names(rates_mean) == "Group.1"] <- "title"

  gender_ranking <- rates_mean %>% inner_join(rates_count,by="title")

  gender_ranking <- filter(gender_ranking, votes_quant > 100)

  gender_ranking <- gender_ranking[with(gender_ranking, order(rank,decreasing = TRUE)), ]

  result <- head(gender_ranking, 10)
  result <- select(result, -c(votes_quant))

  return(result)
}
```
&emsp; Calling functions and get top 10 by gender
{{< btn-copy text="male_top_10   <- rating_by_gender(male_rates) &#013;female_top_10 <-rating_by_gender(female_rates) &#013;male_top_10 &#013;female_top_10" >}}
```bash
male_top_10   <- rating_by_gender(male_rates)
female_top_10 <-rating_by_gender(female_rates)
male_top_10
female_top_10
```

&emsp; >result

```bash
                                                            title     rank
646                                                Godfather, The 4.583333
1372 Seven Samurai (The Magnificent Seven) (Shichinin no samurai) 4.576628
1389                                    Shawshank Redemption, The 4.560625
1253                                      Raiders of the Lost Ark 4.520597
1642                                          Usual Suspects, The 4.518248
1484                           Star Wars: Episode IV - A New Hope 4.495307
1355                                             Schindler's List 4.491415
1162                                               Paths of Glory 4.485149
1720                                          Wrong Trousers, The 4.478261
339                                                Close Shave, A 4.473795
```
```bash
163                                  Close Shave, A 4.644444
800                             Wrong Trousers, The 4.588235
712          Sunset Blvd. (a.k.a. Sunset Boulevard) 4.572650
769 Wallace & Gromit: The Best of Aardman Animation 4.563107
624                                Schindler's List 4.562602
644                       Shawshank Redemption, The 4.539075
323                                Grand Day Out, A 4.537879
740                           To Kill a Mockingbird 4.536667
760                             Usual Suspects, The 4.513317
385                           It Happened One Night 4.500000
```


&emsp; Now, I would like to get the mean production year of the movie. For specific age-groups. In order to do that I neet to run some quick test and see what I am going to find about this dataframe.

{{< btn-copy text="clean_df <- select(full_df, c(title, year, Age,)) &#013;clean_df %>% count(Age)" >}}

```bash
clean_df <- select(full_df, c(title, year, Age,))
clean_df %>% count(Age)
```


|         | Age          | n  |
| ------------- |:-------------:| -----:|
| 1 | 1 | 27210 |
| 2 | 18      |   183534 |
| 3 | 25      |    395555 |
| 4 | 35     |    199001 |
| 5 | 45      |    83631 |
| 6 | 50      |    72484 |
| 7 | 56      |    38766 |

<br></br>

&emsp; I turned out that there is many entries i age columns which are '1'. It probably mean that people who either were to lazy to enter their age or didn't wat to share it publicly. I am going to clean this entries.
{{< btn-copy text="clean_df <- filter(clean_df, Age != 1) &#013;age_groups <- clean_df %>% count(Age) &#013;age_groups$Age" >}}
```bash
clean_df <- filter(clean_df, Age != 1)
age_groups <- clean_df %>% count(Age)
age_groups$Age
```
&emsp; Function which filters data, gets mean and round age
```bash
movie_by_year <- function(var_age){
  age_str <- sprintf('Dla grupy wiekowej %s. usredniony rok producji filmu wynosi:',var_age)
  result <- paste(c(age_str,round(
                                  mean(
                                    filter(clean_df, Age == var_age)$year),
                                      digits=0)))
  print(result)
}
```
&emsp; And there is a loop which iterates through age-groups giving the results
{{< btn-copy text="" >}}

```bash
for (i in age_groups$Age){ &#013;
  movie_by_year(i)
}
```

##### Result
```bash
[1] "Dla grupy wiekowej 18. usredniony rok producji filmu wynosi:"
[2] "1990"
[1] "Dla grupy wiekowej 25. usredniony rok producji filmu wynosi:"
[2] "1988"
[1] "Dla grupy wiekowej 35. usredniony rok producji filmu wynosi:"
[2] "1985"
[1] "Dla grupy wiekowej 45. usredniony rok producji filmu wynosi:"
[2] "1984"
[1] "Dla grupy wiekowej 50. usredniony rok producji filmu wynosi:"
[2] "1982"
[1] "Dla grupy wiekowej 56. usredniony rok producji filmu wynosi:"
[2] "1982"
```

#### Top 3 by gender

&emsp; Now I am gonna pick 3 favorite movies for each gender. Firs I will select correct columns and than I would make a function to filter, sort and return results.

```bash
movies_and_gender <- select(full_df, c(title,Gender))

popular_gender_3 <- function(gender,gender_str){
    movies_and_gender <- filter(movies_and_gender,Gender == gender)
    count_by_gender <- movies_and_gender %>% count(title)
    sorted <- count_by_gender[with(count_by_gender, order(n, decreasing = TRUE)),]
    print(sprintf('Trzy najpopularniejsze filmy wsrod %s to:',gender_str))
    print(head(sorted,3)$title)

}
```
&emsp; Finally, I call this functions and return results.
{{< btn-copy text="popular_gender_3('M','mezczyzn') &#013;popular_gender_3('F','kobiet')" >}}

```bash
popular_gender_3('M','mezczyzn')
popular_gender_3('F','kobiet')
```

##### result
```bash
[1] "Trzy najpopularniejsze filmy wsrod mezczyzn to:"
[1] "American Beauty"
[2] "Star Wars: Episode IV - A New Hope"
[3] "Star Wars: Episode V - The Empire Strikes Back"
> popular_gender_3('F','kobiet')
[1] "Trzy najpopularniejsze filmy wsrod kobiet to:"
[1] "American Beauty"           "Shakespeare in Love"       "Silence of the Lambs, The"
>
```


&emsp; There it is!

