
Whether financial, political, or social -- data's true power lies in its ability to answer questions definitively. I've created a Python script to visualize the weather of 500+ cities across the world of varying distance from the equator. To accomplish this, I've used a [simple Python library](https://pypi.python.org/pypi/citipy) and the [OpenWeatherMap API](https://openweathermap.org/api).

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

