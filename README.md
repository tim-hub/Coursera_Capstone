# Exploratory data analysis for Neighborhoods in Auckland
**A Capstone Project - The Battle of Neighborhoods**

Based on the Neighborhood and Crime Location data in Auckland, this project is 

## Introduction 

### Background
Auckland(Tāmaki Makaurau) is the biggest city in New Zealand, with the population growing, the crimes happening frequency is increasing too. 
This project is to help people who migrate to Auckland to know more about the neighborhoods in Auckland.

### Current Problems
In Auckland, neighborhood and neighborhood are very different, some neighborhood is quite, some are busy. 
With the population growing, the crime frequency and safety of neighborhood are changing too. 
For example, Albany was the place for trash land filling, but now more and more houses are built, at the same time land price increased too.
New Zealand Police published the crime dataset on its website, but the problem there is no easy and direct way to know about the neighborhoods' safety.
This project is to help to solve this problem to let the residents and new migrants in Auckland to know about their neighborhoods.

### Data and Dataset

New Zealand goverment provides crime data which is public, to build this project, there are 2 data sets required.
- `NZ Crimes Data`
- `Neighborhood Coordinate Data`

The crime dataset can be easily gotten from NZ Police Website, but they do not offer a neighborhood and coordinate dataset.
To get the coordinate data, we can use `geopy`'s Nominatim function to call Open Street Map geocode api. 
There might be a problem to use this method that there is a limit for the api usage. In this project, we generated a JSON file to build a dictionary of neighborhood and coordinates.

### How to Solve the Problem
1. Clustering neighborhoods in Auckland
2. Calling `foursquare` api to explore the neighborhood

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


#### Neighborhood Data Preparation
The first data can be easily got through `police.govt.nz`. There is more than 400 neighborhood in Auckland. It is easy to get time out error when getting location through `geopy` geocoder. To fix this problem, the `locations_2020.sjon` file is used. This json file contains all location data required for this project.

To generate this file, each API calling of the geocoder is waited by 1 or 2 seconds, and every about 60 to 90 api calls are run separatelly and manuelly.


## References
- [Police Stat](https://www.police.govt.nz/about-us/publications-statistics/data-and-statistics)
- [Foursquare API](https://developer.foursquare.com/)
- [geopy](https://www.police.govt.nz/about-us/publications-statistics/data-and-statistics)