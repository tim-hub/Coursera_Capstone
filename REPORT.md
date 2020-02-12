



# Exploratory Data Analysis for Neighbourhoods in Auckland

**A Capstone Project - The Battle of Neighbourhoods**

## 1 Introduction 

In this project, `K-Means` clustering methodology is used to cluster neighbourhoods in Auckland. `Foursqaure` api is used to get relevant venues in the neighbourhoods for better exploratory data analysis. 

### 1.1 Background
Auckland(Tāmaki Makaurau) is the biggest city in New Zealand, with the population growing, the crimes happening frequency is increasing too. 
This project is to help people who migrate to Auckland to know more about the neighbourhoods in Auckland.

### 1.2 Current Problems
In Auckland, neighbourhood and neighbourhood are very different, some neighbourhood is quiet, some are busy. 
With the population growing, the crime frequency and safety of neighbourhood are changing too. 
For example, Albany was the place for trash land filling, but now more and more houses are built, at the same time land price increased too.
New Zealand Police published the crime dataset on its website, but the problem there is no easy and direct way to know about the neighbourhoods' safety.
This project is to help to solve this problem to let the residents and new migrants in Auckland to know about their neighbourhoods.

## 2 Data and Dataset

New Zealand goverment provides crime data which is public, to build this project, there are 2 data sets required.
- `NZ Crimes Data`
- `neighbourhood Coordinate Data`

The crime dataset can be easily gotten from NZ Police Website, but they do not offer a neighbourhood and coordinate dataset.
To get the coordinate data, we can use `geopy`'s Nominatim function to call Open Street Map geocode api. 
There might be a problem to use this method that there is a limit for the api usage. In this project, we generated a JSON file to build a dictionary of neighbourhood and coordinates.

### 2.1 How to Solve the Problem

1. Clustering neighbourhoods in Auckland
2. Calling `foursquare` api to explore the neighbourhood

For example, the sample below is a venue information getting through `foursqaure explore` api.

```javascript
 {'reasons': {'count': 0,
   'items': [{'summary': 'This spot is popular',
     'type': 'general',
     'reasonName': 'globalInteractionReason'}]},
  'venue': {'id': '4cafb344562d224ba94b1088',
   'name': '茶顏觀色 Bubble Tea Cafe',
   'location': {'address': '198-200 SH 1',
    'crossStreet': 'Albany',
    'lat': -36.72617803610858,
    'lng': 174.6972947295745,
    'labeledLatLngs': [{'label': 'display',
      'lat': -36.72617803610858,
      'lng': 174.6972947295745}],
    'distance': 111,
    'cc': 'NZ',
    'city': 'North Shore',
    'state': 'Auckland',
    'country': 'New Zealand',
    'formattedAddress': ['198-200 SH 1 (Albany)',
     'North Shore',
     'New Zealand']},
   'categories': [{'id': '52e81612bcbc57f1066b7a0c',
     'name': 'Bubble Tea Shop',
     'pluralName': 'Bubble Tea Shops',
     'shortName': 'Bubble Tea',
     'icon': {'prefix': 'https://ss3.4sqi.net/img/categories_v2/food/bubble_',
      'suffix': '.png'},
     'primary': True}],
   'photos': {'count': 0, 'groups': []}},
  'referralId': 'e-0-4cafb344562d224ba94b1088-1'},
```
We can simply get category, address and location for the venue.

### 2.2 Neighbourhood Data Preparation

The first data can be easily got through `police.govt.nz`. There is more than 400 neighbourhood in Auckland. It is easy to get time out error when getting location through `geopy` geocoder. To fix this problem, the `locations_2020.sjon` file is used. This json file contains all location data required for this project.

To generate this file, each API calling of the geocoder is waited by 1 or 2 seconds, and every about 60 to 90 api calls are run separatelly and manuelly.



## 3 Methodology

### 3.1 Clustering

Before explore each neighbourhood in Auckland, cluster the neighbourhoods will help to get a basic understanding of Auckland. There are many methodologies in Sklearn, such as K-Means, Mean-shift and Rich, implementing all these methodologies on our neighbourhoods is not required here. In this project `K-Means` is used for the clustering.

#### 3.1.1 Data Preparation for Clustering

Pandas and Numpy are 2 popular data science libraries for Python users. To get the clean and correct data before clustering, in sum, these step below are implemented. 

##### Data Cleaning

1. Filter out all non-Auckland records. The dataset here for the New Zealand Crimes, it includes all crime records for each neighbourhoods in whole country in 2019, so or better performance of the implementation all non-Auckland records are dropped.
2. Drop unused and duplicated columns. Many columns are not used for the clustering, for example "Meshblock". The more detail about the dropped columns can be found in Jupiter Notebook attached.
3. Drop dirty rows in the data frame. some neighbourhood names is not correct, for example, one row in the data set, the neighbourhood name of it is `-29`, which does not exist as a neighbourhood or suburb name in Auckland.

![Screen Shot 2020-02-11 at 10.09.59 PM](/Users/tim/Desktop/Screenshots/Screen Shot 2020-02-11 at 10.09.59 PM.jpg)*Fig. 1: Data Sample After Cleaning*

There are 74156 rows in the dataset, after cleaning there are 69507 rows.

##### Data Preparation

As you can see in the Fig.1, Crime data set itself does not contain coordinate data for the neighbourhood. which is required to the further clustering. In this step, `geopy` is used to get the coordinate data for each neighbourhood.

![Screen Shot 2020-02-11 at 10.13.15 PM](/Users/tim/Desktop/Screenshots/Screen Shot 2020-02-11 at 10.13.15 PM.jpg)*Fig. 2: Data Sample after inserting Coordinate Data*

There is one tricky step here is that it is not allowed to batch to call the Open Street Map api to geocode the location in a short period. So the API is called manually and a cached coordinate JSON file is generated in this step.

#### 3.1.2 Clustering Implementation

K-Means methodology is used here to do the clustering. K's value is set to 10, basically it can help us to cluster Auckland to 10 categories.

##### Clustering

```python
from sklearn.cluster import KMeans
# set number of clusters
kclusters = 10

df_to_cluster = df_borough.drop(['Neighborhood','CrimeCount'], 1)

# run k-means clustering
kmeans = KMeans(n_clusters=kclusters, random_state=0).fit(df_to_cluster)
```

##### Group by "name" to Get the Crime Count

![image-20200211222844831](/Users/tim/Library/Application Support/typora-user-images/image-20200211222844831.png)                     *Fig. 3: Data Sample after Clustering*



## 4 Results and Data Visualisation

### 4.1 Results

After clustering, all data will be clustered to 10 categories.

![image-20200212205913099](/Users/tim/Library/Application Support/typora-user-images/image-20200212205913099.png)*Fig. 4: Neighbourhood Crime Markers on Map*

To visualise the clustering neighbourhoods more clearly, different colour are used for different category, and the markers' radius is set with normalised crime count.

![image-20200212210623636](/Users/tim/Library/Application Support/typora-user-images/image-20200212210623636.png)

The base radius is 4, the scaler is a number between 0 and 1. As you can see in the figure below, Crime Count in Albany is 829 and the scaler is 0.360784.

![image-20200212210905998](/Users/tim/Library/Application Support/typora-user-images/image-20200212210905998.png)*Fig. 5: Data Frame with Crime Count Scaler*

Other Clustering Results during Implementation

Before clustering by location to 10 categories, other implementation is tried too, for example, clustering by Crime Count in each neighbourhood.

![Screen Shot 2020-02-10 at 9.55.47 PM](/Users/tim/Desktop/Screenshots/Screen Shot 2020-02-10 at 9.55.47 PM.jpg)*Fig .6: Clustering Result by Crime Count*

The figure above is a basic result to cluster the neighbourhoods in Auckland by Crime Count. 



### 4.2 Exploratory Data Analysis

Foursqaure API is used here to get the related venues information for the neighbourhoods. 

![Screen Shot 2020-02-12 at 10.43.39 PM](/Users/tim/Desktop/Screenshots/Screen Shot 2020-02-12 at 10.43.39 PM.jpg)*Fig. 7: Top Venue Categories for Neighbourhoods*

![Screen Shot 2020-02-12 at 10.43.18 PM](/Users/tim/Desktop/Screenshots/Screen Shot 2020-02-12 at 10.43.18 PM.jpg)*Fig. 8: Join Clustering Data Fram and Venue Category Data Frame*

In this data frame, each neighbourhood can be easily explored, for example, to explore `Auckland Central West` .

![Screen Shot 2020-02-12 at 10.43.27 PM](/Users/tim/Desktop/Screenshots/Screen Shot 2020-02-12 at 10.43.27 PM.jpg)*Fig. 8: Top Venue Categories for Neighbourhood Auckland Central West*





## 5 Discussion & Conclusion

### 5.1 Discussion

As we can see in the Fig 4. `Auckland Central West` is the area which has the highest crime rate among all neighbourhoods in Auckland, and the most common Venue category is `Cafe`.

### 5.2 Conclusion

For the people who want to choose a place to live, if security is the most important concern, North West Auckland is a good option, South and Central Auckland are not good options.




## 6 References
- [Jupyter Notebook Repo](https://github.com/tim-hub/Coursera_Capstone/blob/master/week5-notebook.ipynb)
- [Police Stat for the crime records data](https://www.police.govt.nz/about-us/publications-statistics/data-and-statistics)
- [Foursquare API for the venues data](https://developer.foursquare.com/)
- Coursera [IBM Data Science Professional Certificate](https://www.coursera.org/specializations/ibm-data-science)

### 6.1 Machine Learning Data Science Python Packages

- [geopy for the corrdinate data](https://www.police.govt.nz/about-us/publications-statistics/data-and-statistics)
- [sklearn](https://scikit-learn.org/stable/)
- [numpy](https://numpy.org/)
- [pandas](https://pandas.pydata.org/docs/)



