# Schema & API feedback

## Questions
1. What is the search table for?


## API Considerations
Consider that the common factor between all the latest calls is the geospatial search. This means ultimately you should mainly be trying to get to latitude/longitude on each user to get a complete picture of data. You can use [HTML5 geolocation](https://www.w3schools.com/html/html5_geolocation.asp) or traverse back via zipcode in the pollen API call when the user enrolls/updates location in their profile.


## Users/Locations
If the goal is to use SQL to cache the data, I would mainly cache the location data on the user directly and assume that a limitation will be that a user will only be tracking their home location.

You can effectively simplify much of your schema in this way, because now your tables will simply be users and point-in-time data for pollen, AQ, and weather.

## Point in time data

You can keep them separate like you have them, or you can combine them into one. If you have it in your mind that you will be making all three API calls together every time, it will be simpler to keep them together in one table.
Either way, you should use a datetime field to reflect the API query date / as of date. This way, your application will know whether it needs to make a query to retrieve today's data before it pulls it back from the database.

### AQI
Just because the API has a schema with a nested object, doesn't mean you need to include it that way. I'm referring to the aqiInfo object -  it doesn't appear to require its own table separate from Air Quality. Separate tables are implied from collections (arrays) in the API response.


### Pollen
I would strongly consider including both the numerical counts as well as the risk rating. The reason for this is that in the long term, you can more easily graph pollen counts for users over time from your cached data.


### Weather

Temperature should be a decimal. Most non-integer numeric types are better as decimals than floats because floating point math is very inaccurate.


# Summary
User
UserLocation
UserLocationData

When a user is viewing their page
=> Check if data is in database within a certain timeframe (i.e. run quick db query)
=> if not, then make API call given a user's location (already stored)
=> if so, or after API call is made, then retrieve data from database and show on screen