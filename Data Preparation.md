# <span style="color:#7F9DC0">**DATA PREPARATION**</span>

## <span style="color:#AF91C9">*Source files*</span>
We downloaded the Yelp Open Dataset which consisted of the following files. For each record in the yelp_academic_dataset_photos.json file, there were corresponding image files that were also downloaded.
```
      Records Filename
      160,585 yelp_academic_dataset_business.json
      138,876 yelp_academic_dataset_checkin.json
      200,000 yelp_academic_dataset_photos.json
    8,635,403 yelp_academic_dataset_review.json
    1,162,119 yelp_academic_dataset_tip.json
    2,189,457 yelp_academic_dataset_user.json
```
For our project, we used only the following files along with the image files.
```
yelp_academic_dataset_business.json - information about a business
yelp_academic_dataset_photos.json - name of the image file and the business it is associated with
yelp_academic_dataset_review.json - a review of a business
yelp_academic_dataset_user.json - information about the yelp user that wrote a review
```

## <span style="color:#AF91C9">*Finding a useful subset of data*</span>
To understand the data and find a subset that we could work with, the "keys" (and "foreign keys") were 'cut' out of each file and loaded into a database where some basic agregation queries could be run to see what the data really looked like. Here are the top 15 cities from the yelp_academic_dataset_business.json file with the number of businesses in each city.

```
city       state  business_count
---------- -----  --------------
Austin     TX             22,426
Portland   OR             18,212
Atlanta    GA             12,623
Orlando    FL             10,667
Vancouver  BC             10,302
Boston     MA              8,268
Columbus   OH              6,640
Vancouver  WA              3,028
Boulder    CO              2,543
Cambridge  MA              2,434
Beaverton  OR              2,252
Richmond   BC              1,792
Burnaby    BC              1,728
Kissimmee  FL              1,723
Decatur    GA              1,412
```
Additional queries were run to join the businesses to the both the photos and reviews files to get counts of how many photos and reviews were associated with each of the businesses in each city, as well as a query that joined the businesses to the reviews to the users to see how many users wrote reviews in each city.

Based on the meta data about the data files we decided to go with Boulder, CO

## <span style="color:#AF91C9">*Filtering out the desired data*</span>
Once we identified the city we wanted to use, the following process was followed to get the data related to that city.
1. "Boulder, CO" was 'grep'ed out of yelp_academic_dataset_business.json and the results were put into business_boulder.json
2. For each record in business_boulder.json, the business_id was 'grep'ed out of yelp_academic_dataset_photos.json and the results were put into photos_boulder.json
3. For each record in business_boulder.json, the business_id was 'grep'ed out of yelp_academic_dataset_review.json and the results were put into review_boulder.json
4. The user_id field was 'cut' out of review_boulder.json and a list of unique user_ids was created which was looped through. Each of these user_ids was 'grep'ed out of yelp_academic_dataset_user.json and the results were put into user_boulder.json

The data to be processed then looked like this.
```
   Records Filename
     2,542 business_boulder.json
     1,788 photos_boulder.json
   117,585 review_boulder.json
    17,039 user_boulder.json
```

## <span style="color:#AF91C9">*Processing the files*</span>
Of the four types of json files, only one could be be read and used as is--the photos. Two of them were generally easily read, but required some data cleaning and processing of some columns to produce other columns that were more useful--reviews and users. One of the files provided required a fair amount of processing to deal with unclean data as well as flatten nested information--businesses.
### Sample business_boulder.json record ###
```json
{
    "business_id": "6iYb2HFDywm3zjuRg0shjw",
    "name": "Oskar Blues Taproom",
    "address": "921 Pearl St",
    "city": "Boulder",
    "state": "CO",
    "postal_code": "80302",
    "latitude": 40.0175444,
    "longitude": -105.2833481,
    "stars": 4.0,
    "review_count": 86,
    "is_open": 1,
    "attributes": {
        "RestaurantsTableService": "True",
        "WiFi": "u'free'",
        "BikeParking": "True",
        "BusinessParking": "{'garage': False, 'street': True, 'validated': False, 'lot': False, 'valet': False}",
        "BusinessAcceptsCreditCards": "True",
        "RestaurantsReservations": "False",
        "WheelchairAccessible": "True",
        "Caters": "True",
        "OutdoorSeating": "True",
        "RestaurantsGoodForGroups": "True",
        "HappyHour": "True",
        "BusinessAcceptsBitcoin": "False",
        "RestaurantsPriceRange2": "2",
        "Ambience": "{'touristy': False, 'hipster': False, 'romantic': False, 'divey': False, 'intimate': False, 'trendy': False, 'upscale': False, 'classy': False, 'casual': True}",
        "HasTV": "True",
        "Alcohol": "'beer_and_wine'",
        "GoodForMeal": "{'dessert': False, 'latenight': False, 'lunch': False, 'dinner': False, 'brunch': False, 'breakfast': False}",
        "DogsAllowed": "False",
        "RestaurantsTakeOut": "True",
        "NoiseLevel": "u'average'",
        "RestaurantsAttire": "'casual'",
        "RestaurantsDelivery": "None"
    },
    "categories": "Gastropubs, Food, Beer Gardens, Restaurants, Bars, American (Traditional), Beer Bar, Nightlife, Breweries",
    "hours": {
        "Monday": "11:0-23:0",
        "Tuesday": "11:0-23:0",
        "Wednesday": "11:0-23:0",
        "Thursday": "11:0-23:0",
        "Friday": "11:0-23:0",
        "Saturday": "11:0-23:0",
        "Sunday": "11:0-23:0"
    }
}
```
### Sample photos_boulder.json record ###
```json
{
    "photo_id": "ESP_DCIW2eWyO77fSzY6yQ",
    "business_id": "8zehGz9jnxPqXtOc7KaJxA",
    "caption": "",
    "label": "drink"
}
```
### Sample review_boulder.json record ###
```json
{
    "review_id": "sjm_uUcQVxab_EeLCqsYLg",
    "user_id": "0kA0PAJ8QFMeveQWHFqz2A",
    "business_id": "8zehGz9jnxPqXtOc7KaJxA",
    "stars": 4.0,
    "useful": 0,
    "funny": 0,
    "cool": 0,
    "text": "The food is always great here. The service from both the manager as well as the staff is super. Only draw back of this restaurant is it's super loud. If you can, snag a patio table!",
    "date": "2011-07-28 18:05:01"
}
```
### Sample user_boulder.json record ###
```json
{
    "user_id": "-7skc_K0qZhSKuoE4_54Kg",
    "name": "Dale",
    "review_count": 6,
    "yelping_since": "2009-10-29 21:18:54",
    "useful": 8,
    "funny": 2,
    "cool": 3,
    "elite": "",
    "friends": "Ho8blR0oO0Lni3WDmG92hA",
    "fans": 0,
    "average_stars": 4.71,
    "compliment_hot": 0,
    "compliment_more": 0,
    "compliment_profile": 0,
    "compliment_cute": 0,
    "compliment_list": 0,
    "compliment_note": 0,
    "compliment_plain": 0,
    "compliment_cool": 0,
    "compliment_funny": 0,
    "compliment_writer": 0,
    "compliment_photos": 0
}
```

## <span style="color:#AF91C9">*Final Result*</span>
After all the processing was done, four new clean and flattened CSV files were produced to be easily read in for further analysis.