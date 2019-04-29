
Whether financial, political, or social -- data's true power lies in its ability to answer questions definitively. To that end, I've used a Python script to visualize the weather of 500+ cities across the world of varying distance from the equator in order to demonstrate the impact proximaty to the equator has on temperature. To accomplish this, I used [CitiPy](https://pypi.python.org/pypi/citipy) and the [OpenWeatherMap API](https://openweathermap.org/api).

```
# Dependencies and Setup
import matplotlib.pyplot as plt
import pandas as pd
import numpy as np
import requests
import time
from pprint import pprint


# Import API key
import api_keys
api_key = api_keys.api_key
#print(api_key)
# Incorporated citipy to determine city based on latitude and longitude
from citipy import citipy

# Output File (CSV)
output_data_file = "output_data/cities.csv"

# Range of latitudes and longitudes
lat_range = (-90, 90)
lng_range = (-180, 180)
```

```
# List for holding lat_lngs and cities
lat_lngs = []
cities = []

# Create a set of random lat and lng combinations
lats = np.random.uniform(low=-90.000, high=90.000, size=1500)
lngs = np.random.uniform(low=-180.000, high=180.000, size=1500)
lat_lngs = zip(lats, lngs)

# Identify nearest city for each lat, lng combination
for lat_lng in lat_lngs:
    city = citipy.nearest_city(lat_lng[0], lat_lng[1]).city_name
    
    # If the city is unique, then add it to a our cities list
    if city not in cities:
        cities.append(city)

# Print the city count to confirm sufficient count
len(cities)
```

```
url = "http://api.openweathermap.org/data/2.5/weather?"
units = "imperial"

city_temp = []
cnt = 0
city_name = []
city_lat = []
city_lng = []
city_max_temp = []
city_humidity = []
city_clouds = []
city_wind = []
city_country = []
city_date = []
set_cnt = 0
rec_cnt = 0
fails = 0


for city in cities:
    cnt = cnt + 1
    if (cnt % 50 == 0 and cnt >= 50):
        set_cnt += 1
        print("Set Count =" + str(set_cnt))
        print("Count =" + str(cnt))
        rec_cnt = 0
        time.sleep(5)
    try:
        query_url = url + units + "&APPID=" + api_key + "&q=" + city
        response = requests.get(query_url).json()
        print ("Processing reccord" + " " + str(cnt) +" " + city)
        city_temp.append(response['main']["temp"])
        city_name.append(response["name"])
        city_lat.append(response["coord"]["lat"])
        city_lng.append(response["coord"]["lon"])
        city_max_temp.append(response["main"]["temp_max"])
        city_humidity.append(response["main"]["humidity"])
        city_clouds.append(response["clouds"]["all"])
        city_wind.append(response["wind"]["speed"])
        city_country.append(response["sys"]["country"])
        city_date.append(response["dt"])
        rec_cnt += 1
        
    except:
        print("City not found. Skipping...")
        fails += 1
        print("Failed = " + str(fails))
```
  
```
print(len(city_name))
print(len(city_clouds))
print(len(city_max_temp))
print(len(city_humidity))
print(len(city_lat))
```

```
TempF = [(x - 273.15) * 9/5 + 32 for x in city_max_temp] 

weatherdict=dict()
weatherdict={"City":city_name,"Cloudiness":city_clouds,"Country_code":city_country,"Date":city_date,
             "Humidity":city_humidity,"Latitude":city_lat,"Longitude":city_lng,"Max_temp":TempF,
             "Wind_speed":city_wind}
#print(len(weatherdict))
weather_df = pd.DataFrame(weatherdict)
weather_df.tail(10)
```

```
weather_df.to_csv("weatherdata.csv",index=False,header=True)
weather_df.info()
```

```
plt.scatter(city_lat, TempF, s=40, c="skyblue",
           edgecolor="black", linewidths=1, marker="o",
           alpha=0.8)
plt.grid(True)
plt.title("City Latitude vs. Max Temperature (01/15/2019)")
plt.xlabel("Latitude")
plt.ylabel("Max Temperature (F)")
plt.savefig('Latitude_vs_Max_Temp')
```

![Latitude vs Max Temp](https://github.com/xjarodx/PythonandAPIs/blob/master/images/Latitude_vs_Max_Temp.png)

```
plt.scatter(city_lat, city_humidity, s=40, c="skyblue",
           edgecolor="black", linewidths=1, marker="o",
           alpha=0.8)
plt.grid(True)
plt.title("City Latitude vs. Humidity (01/15/2019)")
plt.xlabel("Latitude")
plt.ylabel("Humidity (%)")
plt.savefig('Latitude_vs_Humidity')
```

![Latitude vs Humidity](https://github.com/xjarodx/PythonandAPIs/blob/master/images/Latitude_vs_Humidity.png)

```
plt.scatter(city_lat, city_clouds, s=40, c="skyblue",
           edgecolor="black", linewidths=1, marker="o",
           alpha=0.8)
plt.grid(True)
plt.title("City Latitude vs. Cloudiness (01/15/2019)")
plt.xlabel("Latitude")
plt.ylabel("Cloudiness (%)")
plt.savefig('Latitude_vs_Cloudiness')
```

![Latitude vs Cloudiness](https://github.com/xjarodx/PythonandAPIs/blob/master/images/Latitude_vs_Cloudiness.png)

```
plt.scatter(city_lat, city_wind, s=40, c="skyblue",
           edgecolor="black", linewidths=1, marker="o",
           alpha=0.8)
plt.grid(True)
plt.title("City Latitude vs. Wind Speed (01/15/2019)")
plt.xlabel("Latitude")
plt.ylabel("Wind Speed (mph)")
plt.savefig('Latitude_vs_WindSpeed')
```

![Latitude vs Windspeed](https://github.com/xjarodx/PythonandAPIs/blob/master/images/Latitude_vs_WindSpeed.png)
