# CapstoneProject1 - Powerlifting

This project explores data from the OpenPowerlifting project, https://www.openpowerlifting.org. You may download a copy of the data at https://gitlab.com/openpowerlifting/opl-data.


# Problem:
Through the last few years, there has been a large shift in people focusing on a solely strength based exercise regimen, with more competitions available for those athletes to compete. 
As the sport grows, how is the demographic changing? 
Is overall strength increasing? 
How much of an impact does sex have on powerlifting? 

These outlined questions above need to be explored to allow new powerlifters see how they fit in, as well as allow current powerlifters identify where they can improve to stand out.

# The Data:
The powerlifting database contains two tables. 

The first table is filled with the competition meet data. This includes the MeetID, the date, location, and name.  A preview of this is displayed below. 


The other database includes each of the competitors information for each meet. It includes information like meetID, name, age, weight, meet division, best lift amounts for bench, squat, and deadlift, as well as the competitors place, and Wilks score. The meetID in each table allows for combining these tables for more information about when the lifts took place. 

A preview of this dataFrame prior to any adjustments is displayed below; because there are so many columns it is split on 2 lines. 


The first table was pretty clean and didn’t need much manipulation. The date column was converted to date type object to allow for the creation of year and month columns. Pandas to_datetime function allowed for this conversion, after using it, lambda functions were used to create the two new columns.  

The second table needed more work. The Squat4Kg, Bench4Kg, and Deadlift4Kg columns had very few values, so they were removed. After exploring that, the method describe was used to explore the maximum and minimum values to see if anything seemed off. It turned out a small portion of the table had negative values for the squat, deadlift, or bench values. This was due to an unsuccessful attempt at the weight. Since there was only a small percentage of records that contained negative values (<1%) the records were removed from the table. 

The Equipment column originally had seven columns that had mislabeled “wraps” as “straps”; these columns were corrected to having the right label. This column, as well as the Sex column, was changed to a category.

A large range of ages participate in these competitions (from 5-95), and since we couldn’t easily reclassify the weight classes, and the “division” column has too many options, an ageGroup column was created. The USAPL age classifications were used. There were some rows that were missing an age; these were assigned the “Open” ageGroup. The first few rows of the updated dataFrame are shown below.

 


# Exploratory Data Analysis:
Initial findings from exploratory analysis
Summary of findings
Visuals and statistics to support findings
To get an idea of how demographics were changing, a bar graph was created to show the number of male vs female competitors there have been over the years since the sport began being recorded. The figure below shows that it is gaining a lot of popularity in the more recent years (the most recent date in the dataset is January 2018), though males are still competing far more than females.


The Wilks score is a number used to assign a strength value for each individual. It is computed with a formula to normalize lifts across weight classes. The constant values on the bottom differ between Males and Females, to help bridge a gap between them. 

While exploring the changes in the WIlks scores for males and females, it was interesting to see that the max Wilks scores for both males and females were so close; the max for females is 776.17, while the max for males is 779.38. Both males and females are getting stronger each year, with females increasing at a faster rate; this could be because women got into lifting later than males, and there are less competitors so stronger individuals stand out more. 

I would like to note that the current two highest Wilks scores noted above were both obtained with the use of wraps, and the current top 5 highest Raw wilks scores are held by females. (This information is available on the open powerlifting website).

Because of its normalization, the wilks score makes for an interesting data field to apply some analytics on. Originally when looking at this data, and creating the bar graph below, I noticed that there were two peaks in the chart for the scores. 

This was due to individuals who didn’t complete all three lifts. A new subset of the dataset was obtained that contained only individuals that had values for bench, squat, and deadlift. The new bar graph of the data resembled more of a normal distribution.

That graph appears to show that there is a different mean Wilks Score for male and female competitors, even though it’s normalized. This data was then put into the ECDF function to help determine whether it was a normal distribution. Both male and females tend to closely follow a theoretical curve for the ECDF.

The means displayed above do look different, but still fairly close. The null hypothesis to be tested was that the average wilks score for males was equal to the average wilks score for females.
H0: mean(females.Wilks) = mean(males.Wilks)
H1: mean(females.Wilks) ≠ mean(males.Wilks)
To test this, bootstrapping was first used. I adjusted the females wilks scores so that the mean matched that of the males, and drew 10,000 replicates to see how many times we could obtain the number of times our bootstrap sample had a mean less than or equal to our females average wilks score. Out of the 10,000 samples, not a single one came back with a mean as extreme as our data; a P-value of 0.
After this test, a t-test for two datasets, with an assumed identical mean, was performed. This test (in scipy.stats) resulted in a P-value of 0, and a T-statistic value of 94.2. 
From this, we can conclude that we should reject the null hypothesis, and offer up our H1 hypothesis that there is a difference in Wilks scores for males and females, even with altered constants used in computing them. Were males and females ever to compute against each other, this Wilks score formula would need to be adjusted to account for the difference in male and female strength. In fact, some powerlifting competitions have started switching away from using the wilks score, and are normalizing lifts differently. 
There is still a large gap in the number of male and female lifters, and in the future, this gap in the wilks score could close on its own, as more females begin competing, and more research is done of female strength, but for now it is safe to say male competitors are stronger overall. 

# Machine Learning

After doing some exploring on the data, I decided to start looking into predicting Wilks scores based off of the information given at the start of a meet.

We achieved our best R-squared score from the KNeighbors model, and it was paired with the lowest mean squared error. From every model, we saw that the biggest factor in predicting the Wilks score was the "Multiplie" column that indicated whether a person participated in 2 or more lifts.

While it may have improved the model to further separate it out to competing in the 3 combinations of lift pairs, and competing in all three, this wouldn't make sense with the data. In powerlifting competitions, you can select to do a single lift, or all three; the only way to do two lifts is to completely miss one of your lifts for all three attempts.

In all but the random forests, knowing the number of meets someone had competed in was the third best indicator in how they would do, just after knowing what lifts they were competing in. While we saw through our EDA that males and females do have different averages, knowing someone's sex didn't help much in predicting how they would do.

Every year new records are being broken for every lift, total, and even the wilks score. But all of those are the outliers. The majority of lifters compete similarly, which is why just another 5 pounds on a lift, or 2 pounds less on a scale can tip a competition in someone's favor. These machine learning algorithms have shown that people do compete similarly, and knowing just the info someone gives going in to a meet (as well as knowing how many meets they've previously done), the model can predict how they will perform with almost 70% accuracy.

Due to the fact that the formula for the wilks score was created over 20 years ago, and there has been strong evidence that men and women are competing more and more closely. While the males scores have been trending downward, the females are trending upwards. It seems like a new method of quatifying a person's strength is needed to once again attempt to normalize performance.
