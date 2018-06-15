
# Pyber Ride Sharing

## Analysis 

#### 1- Urban cities have a relative lower fare avg than the rural cities, this can be related to the distance of each ride. It is more likely to take longer rides on the rural area since the desire locations are farther from the initial point of departure. 
#### 2-Rural cities have the lowest number of drivers, which can be strongly related to the numer of habitants on the rural cities, on the other hand, urban cities have a singnifcanlty higher number of drivers. 
#### 3-There seems to be a negative relationship between the number of rides and the average fare, however the suburban cities have some intances where this asumption fails. 


```python
# Import Dependencies
import matplotlib.pyplot as plt
import pandas as pd
import numpy as np
import seaborn as sns
```


```python
#Importing the Data as CSV file 

#city Data
city_data = 'raw_data/city_data.csv'
city_df= pd.read_csv(city_data)

#Ride Data
ride_data = 'raw_data/ride_data.csv'
ride_df= pd.read_csv(ride_data)
```


```python
#mergin the two data sources 
merged_df = city_df.merge( ride_df , on= 'city', how = 'outer')
merged_df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>city</th>
      <th>driver_count</th>
      <th>type</th>
      <th>date</th>
      <th>fare</th>
      <th>ride_id</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Richardfort</td>
      <td>38</td>
      <td>Urban</td>
      <td>2018-02-24 08:40:38</td>
      <td>13.93</td>
      <td>5628545007794</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Richardfort</td>
      <td>38</td>
      <td>Urban</td>
      <td>2018-02-13 12:46:07</td>
      <td>14.00</td>
      <td>910050116494</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Richardfort</td>
      <td>38</td>
      <td>Urban</td>
      <td>2018-02-16 13:52:19</td>
      <td>17.92</td>
      <td>820639054416</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Richardfort</td>
      <td>38</td>
      <td>Urban</td>
      <td>2018-02-01 20:18:28</td>
      <td>10.26</td>
      <td>9554935945413</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Richardfort</td>
      <td>38</td>
      <td>Urban</td>
      <td>2018-04-17 02:26:37</td>
      <td>23.00</td>
      <td>720020655850</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Average Fare ($) Per City

fare_avg= merged_df.groupby("city")["fare"].mean()
fare_avg.head()
```




    city
    Amandaburgh      24.641667
    Barajasview      25.332273
    Barronchester    36.422500
    Bethanyland      32.956111
    Bradshawfurt     40.064000
    Name: fare, dtype: float64




```python
# Total Number of Rides per City 
total_rides =merged_df.groupby('type')['ride_id'].count()
total_rides.head()
```




    type
    Rural        125
    Suburban     625
    Urban       1625
    Name: ride_id, dtype: int64




```python
#Total Number of Drivers Per City
total_drivers = merged_df.groupby('city')["driver_count"].count()
total_drivers.head()
```




    city
    Amandaburgh      18
    Barajasview      22
    Barronchester    16
    Bethanyland      18
    Bradshawfurt     10
    Name: driver_count, dtype: int64




```python
# Create var to hold city type values 

rural = merged_df.loc[merged_df['type'] == 'Rural']
suburban= merged_df.loc[merged_df['type'] == 'Suburban']
urban = merged_df.loc[merged_df['type'] == 'Urban']
```


```python
#  Average Fare, Total Rides and Total Drivers for City Types 

#Rural 
rural_fare= rural.groupby('city')["fare"].mean()  
rural_rides = rural.groupby('city')['ride_id'].count()
rural_drivers= rural.groupby('city')["driver_count"].value_counts()

#Suburban 
suburban_fare= suburban.groupby('city')["fare"].mean()  
suburban_rides = suburban.groupby('city')['ride_id'].count()
suburban_drivers= suburban.groupby('city')["driver_count"].value_counts()


# Urban 

urban_fare= urban.groupby('city')["fare"].mean()  
urban_rides = urban.groupby('city')['ride_id'].count()
urban_drivers= urban.groupby('city')["driver_count"].value_counts()



```


```python
#plots for city type information 


plt.scatter(rural_rides, rural_fare,s= rural_drivers*10,
            marker ='o', facecolors ="lightskyblue", edgecolors='black',alpha = 0.5, label="Rural", linewidth = 1.5)

plt.scatter(suburban_rides, suburban_fare,s= suburban_drivers*10,
            marker ='o', facecolors ="lightcoral", edgecolors='black',alpha = 0.5, label="Suburban", linewidth = 1.5)

plt.scatter(urban_rides, urban_fare,s= urban_drivers*10,
            marker ='o', facecolors ="gold", edgecolors='black',alpha = 0.5, label="Urban", linewidth = 1.5)

#plot chart format and style 

# Chart title

plt.title('Note : Size of Bubble Corresponds to Number of Drivers per City')
plt.suptitle(" Ride Sharing Market Analysis (2018)")
# x label
plt.xlabel("Total Numbers of Rides (Per City))")
# y label
plt.ylabel("Average Fare cost ($)")
# legend 
plt.legend(loc='upper right')

#Save an image of the chart and print to screen
plt.savefig("Pyber Ride Sharing.png")

# plt.show()

```


![png](output_10_0.png)



```python
# Fare Percentage per City Type 

total_fare = merged_df['fare'].sum() 

fare_per = merged_df.groupby('type')["fare"].sum()


percentage_per_fare = fare_per /total_fare


# Labels for the sections of our pie chart
labels = ["Rural","Suburban","Urban" ]

# The colors of each section of the pie chart
colors = ["gold","lightskyblue","lightcoral"]
explode = (0, 0, 0.1)
plt.title("% of Total Fares By City Types")
plt.pie(percentage_per_fare, explode=explode, labels=labels, colors=colors, autopct="%1.1f%%",shadow=True, startangle=160)
plt.axis("equal")

#Save an image of the chart and print to screen
plt.savefig("% of Total Fares By City Types.png")

plt.show()

```


![png](output_11_0.png)



```python
# # % of Total Rides by City Type

rides_per_total = merged_df['ride_id'].count()

rides_per = merged_df.groupby('type')['ride_id'].count()
rides_per 

percentage_per_ride= rides_per/rides_per_total
percentage_per_ride

# Labels for the sections of our pie chart
labels = ["Rural","Suburban","Urban" ]

# The colors of each section of the pie chart
colors = ["gold","lightskyblue","lightcoral"]
explode = (0, 0, 0.1)
plt.title("% of Total Rides By City Types")
plt.pie(percentage_per_ride, explode=explode, labels=labels, colors=colors,
        autopct="%1.1f%%",shadow=True, startangle=160)
plt.axis("equal")


#Save an image of the chart and print to screen
plt.savefig("% of Total Rides By City Types.png")

```


![png](output_12_0.png)



```python
# % of Total Drivers by City Type

total_drivers_sum = merged_df["driver_count"].sum()

driver_per = merged_df.groupby('type')['driver_count'].sum()

percentage_for_driver = driver_per/total_drivers_sum
percentage_for_driver 


# Labels for the sections of our pie chart
labels = ["Rural","Suburban","Urban" ]

# The colors of each section of the pie chart
colors = ["gold","lightskyblue","lightcoral"]
explode = (0, 0, 0.1)
plt.title("% of Total Drivers By City Types")

plt.pie(percentage_for_driver, explode=explode, labels=labels, colors=colors,
        autopct="%1.1f%%",shadow=True, startangle=160)
plt.axis("equal")
#Save an image of the chart and print to screen
plt.savefig("% of Total Drivers By City Types.png")

```


![png](output_13_0.png)

