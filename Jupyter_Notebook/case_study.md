# Process phase

I prepared the following to-do list for my cleaning process:
- Verify bike_type unique values
- Verify membership_type unique values
- Manage the timestamps
    - Create the columns start_day, start_time, end_day and end_time
    - Add the day of the week
    - Add the month
- Check duplicates
    - Check if there are duplicated rows across all columns
    - Check if there are duplicated rows across specific columns
- Calculate the ride duration
- Check if any row has a ride_id that doesn't have the right length
- Check if there are null values

## Verify that I can access one of the downloaded files, create a dataframe and show the first rows


```python
import pandas as pd

df = pd.read_csv("202410-divvy-tripdata.csv")
df.head()
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
      <th>ride_id</th>
      <th>rideable_type</th>
      <th>started_at</th>
      <th>ended_at</th>
      <th>start_station_name</th>
      <th>start_station_id</th>
      <th>end_station_name</th>
      <th>end_station_id</th>
      <th>start_lat</th>
      <th>start_lng</th>
      <th>end_lat</th>
      <th>end_lng</th>
      <th>member_casual</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>4422E707103AA4FF</td>
      <td>electric_bike</td>
      <td>2024-10-14 03:26:04.083</td>
      <td>2024-10-14 03:32:56.535</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>41.96</td>
      <td>-87.65</td>
      <td>41.98</td>
      <td>-87.67</td>
      <td>member</td>
    </tr>
    <tr>
      <th>1</th>
      <td>19DB722B44CBE82F</td>
      <td>electric_bike</td>
      <td>2024-10-13 19:33:38.926</td>
      <td>2024-10-13 19:39:04.490</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>41.98</td>
      <td>-87.67</td>
      <td>41.97</td>
      <td>-87.66</td>
      <td>member</td>
    </tr>
    <tr>
      <th>2</th>
      <td>20AE2509FD68C939</td>
      <td>electric_bike</td>
      <td>2024-10-13 23:40:48.522</td>
      <td>2024-10-13 23:48:02.339</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>41.97</td>
      <td>-87.66</td>
      <td>41.95</td>
      <td>-87.65</td>
      <td>member</td>
    </tr>
    <tr>
      <th>3</th>
      <td>D0F17580AB9515A9</td>
      <td>electric_bike</td>
      <td>2024-10-14 02:13:41.602</td>
      <td>2024-10-14 02:25:40.057</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>41.95</td>
      <td>-87.65</td>
      <td>41.96</td>
      <td>-87.65</td>
      <td>member</td>
    </tr>
    <tr>
      <th>4</th>
      <td>A114A483941288D1</td>
      <td>electric_bike</td>
      <td>2024-10-13 19:26:41.383</td>
      <td>2024-10-13 19:28:18.560</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>41.98</td>
      <td>-87.67</td>
      <td>41.98</td>
      <td>-87.67</td>
      <td>member</td>
    </tr>
  </tbody>
</table>
</div>



##  Look up last year data


```python
import pathlib

RAW_DATA = pathlib.Path("raw_data")
raw_csv202311 = RAW_DATA / "202311-divvy-tripdata" / "202311-divvy-tripdata.csv"
raw_csv202312 = RAW_DATA / "202312-divvy-tripdata" / "202312-divvy-tripdata.csv"
raw_csvs2024 = list(RAW_DATA.glob("**/2024*.csv"))


for raw_csv in raw_csvs2024:
    if "__MACOSX" in str(raw_csv.absolute()):
        raw_csv.unlink()
```

## Remove __MACOSX files


```python
for raw_csv in raw_csvs2024:
    if "__MACOSX" in str(raw_csv.absolute()):
        raw_csv.unlink()
```

## Load into dataframes one year worth of data


```python
dfs = []

for raw_csv in raw_csvs2024 + [raw_csv202311, raw_csv202312]:
    dfs.append(pd.read_csv(raw_csv))
```

## Verify that each dataframe has the same number of columns, column names and types


```python
first, *remain = dfs

for df in remain:
    if not first.dtypes.equals(df.dtypes):
        print("Not equal")
```

## Concatenate the loaded dataframes into a single dataframe


```python
df = pd.concat(dfs)
df.describe()
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
      <th>start_lat</th>
      <th>start_lng</th>
      <th>end_lat</th>
      <th>end_lng</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>5.933712e+06</td>
      <td>5.933712e+06</td>
      <td>5.926295e+06</td>
      <td>5.926295e+06</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>4.190224e+01</td>
      <td>-8.764630e+01</td>
      <td>4.190264e+01</td>
      <td>-8.764652e+01</td>
    </tr>
    <tr>
      <th>std</th>
      <td>4.473732e-02</td>
      <td>2.743938e-02</td>
      <td>5.585027e-02</td>
      <td>1.130093e-01</td>
    </tr>
    <tr>
      <th>min</th>
      <td>4.164000e+01</td>
      <td>-8.794000e+01</td>
      <td>1.606000e+01</td>
      <td>-1.440500e+02</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>4.188096e+01</td>
      <td>-8.766000e+01</td>
      <td>4.188096e+01</td>
      <td>-8.766000e+01</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>4.189745e+01</td>
      <td>-8.764275e+01</td>
      <td>4.189776e+01</td>
      <td>-8.764288e+01</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>4.193000e+01</td>
      <td>-8.762952e+01</td>
      <td>4.193000e+01</td>
      <td>-8.762954e+01</td>
    </tr>
    <tr>
      <th>max</th>
      <td>4.207000e+01</td>
      <td>-8.752000e+01</td>
      <td>8.796000e+01</td>
      <td>1.525300e+02</td>
    </tr>
  </tbody>
</table>
</div>




```python
df.head()
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
      <th>ride_id</th>
      <th>rideable_type</th>
      <th>started_at</th>
      <th>ended_at</th>
      <th>start_station_name</th>
      <th>start_station_id</th>
      <th>end_station_name</th>
      <th>end_station_id</th>
      <th>start_lat</th>
      <th>start_lng</th>
      <th>end_lat</th>
      <th>end_lng</th>
      <th>member_casual</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>C1D650626C8C899A</td>
      <td>electric_bike</td>
      <td>2024-01-12 15:30:27</td>
      <td>2024-01-12 15:37:59</td>
      <td>Wells St &amp; Elm St</td>
      <td>KA1504000135</td>
      <td>Kingsbury St &amp; Kinzie St</td>
      <td>KA1503000043</td>
      <td>41.903267</td>
      <td>-87.634737</td>
      <td>41.889177</td>
      <td>-87.638506</td>
      <td>member</td>
    </tr>
    <tr>
      <th>1</th>
      <td>EECD38BDB25BFCB0</td>
      <td>electric_bike</td>
      <td>2024-01-08 15:45:46</td>
      <td>2024-01-08 15:52:59</td>
      <td>Wells St &amp; Elm St</td>
      <td>KA1504000135</td>
      <td>Kingsbury St &amp; Kinzie St</td>
      <td>KA1503000043</td>
      <td>41.902937</td>
      <td>-87.634440</td>
      <td>41.889177</td>
      <td>-87.638506</td>
      <td>member</td>
    </tr>
    <tr>
      <th>2</th>
      <td>F4A9CE78061F17F7</td>
      <td>electric_bike</td>
      <td>2024-01-27 12:27:19</td>
      <td>2024-01-27 12:35:19</td>
      <td>Wells St &amp; Elm St</td>
      <td>KA1504000135</td>
      <td>Kingsbury St &amp; Kinzie St</td>
      <td>KA1503000043</td>
      <td>41.902951</td>
      <td>-87.634470</td>
      <td>41.889177</td>
      <td>-87.638506</td>
      <td>member</td>
    </tr>
    <tr>
      <th>3</th>
      <td>0A0D9E15EE50B171</td>
      <td>classic_bike</td>
      <td>2024-01-29 16:26:17</td>
      <td>2024-01-29 16:56:06</td>
      <td>Wells St &amp; Randolph St</td>
      <td>TA1305000030</td>
      <td>Larrabee St &amp; Webster Ave</td>
      <td>13193</td>
      <td>41.884295</td>
      <td>-87.633963</td>
      <td>41.921822</td>
      <td>-87.644140</td>
      <td>member</td>
    </tr>
    <tr>
      <th>4</th>
      <td>33FFC9805E3EFF9A</td>
      <td>classic_bike</td>
      <td>2024-01-31 05:43:23</td>
      <td>2024-01-31 06:09:35</td>
      <td>Lincoln Ave &amp; Waveland Ave</td>
      <td>13253</td>
      <td>Kingsbury St &amp; Kinzie St</td>
      <td>KA1503000043</td>
      <td>41.948797</td>
      <td>-87.675278</td>
      <td>41.889177</td>
      <td>-87.638506</td>
      <td>member</td>
    </tr>
  </tbody>
</table>
</div>



## Drop columns I don't plan on using


```python
to_drop = ['start_lat',
           'start_lng',
            'end_lat',
            'end_lng']

df.drop(to_drop, inplace=True, axis=1)
df.head()

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
      <th>ride_id</th>
      <th>rideable_type</th>
      <th>started_at</th>
      <th>ended_at</th>
      <th>start_station_name</th>
      <th>start_station_id</th>
      <th>end_station_name</th>
      <th>end_station_id</th>
      <th>member_casual</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>C1D650626C8C899A</td>
      <td>electric_bike</td>
      <td>2024-01-12 15:30:27</td>
      <td>2024-01-12 15:37:59</td>
      <td>Wells St &amp; Elm St</td>
      <td>KA1504000135</td>
      <td>Kingsbury St &amp; Kinzie St</td>
      <td>KA1503000043</td>
      <td>member</td>
    </tr>
    <tr>
      <th>1</th>
      <td>EECD38BDB25BFCB0</td>
      <td>electric_bike</td>
      <td>2024-01-08 15:45:46</td>
      <td>2024-01-08 15:52:59</td>
      <td>Wells St &amp; Elm St</td>
      <td>KA1504000135</td>
      <td>Kingsbury St &amp; Kinzie St</td>
      <td>KA1503000043</td>
      <td>member</td>
    </tr>
    <tr>
      <th>2</th>
      <td>F4A9CE78061F17F7</td>
      <td>electric_bike</td>
      <td>2024-01-27 12:27:19</td>
      <td>2024-01-27 12:35:19</td>
      <td>Wells St &amp; Elm St</td>
      <td>KA1504000135</td>
      <td>Kingsbury St &amp; Kinzie St</td>
      <td>KA1503000043</td>
      <td>member</td>
    </tr>
    <tr>
      <th>3</th>
      <td>0A0D9E15EE50B171</td>
      <td>classic_bike</td>
      <td>2024-01-29 16:26:17</td>
      <td>2024-01-29 16:56:06</td>
      <td>Wells St &amp; Randolph St</td>
      <td>TA1305000030</td>
      <td>Larrabee St &amp; Webster Ave</td>
      <td>13193</td>
      <td>member</td>
    </tr>
    <tr>
      <th>4</th>
      <td>33FFC9805E3EFF9A</td>
      <td>classic_bike</td>
      <td>2024-01-31 05:43:23</td>
      <td>2024-01-31 06:09:35</td>
      <td>Lincoln Ave &amp; Waveland Ave</td>
      <td>13253</td>
      <td>Kingsbury St &amp; Kinzie St</td>
      <td>KA1503000043</td>
      <td>member</td>
    </tr>
  </tbody>
</table>
</div>



## Check the bike and membership unique values


```python
rideable_type = df['rideable_type'].unique()
member_casual = df['member_casual'].unique()

display(rideable_type)
display(member_casual)
```


    array(['electric_bike', 'classic_bike', 'electric_scooter'], dtype=object)



    array(['member', 'casual'], dtype=object)


## Rename columns


```python
df.rename(columns={'rideable_type':'ride_type'}, inplace=True)
df.rename(columns={'member_casual':'membership_type'}, inplace=True)

df.head()
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
      <th>ride_id</th>
      <th>ride_type</th>
      <th>started_at</th>
      <th>ended_at</th>
      <th>start_station_name</th>
      <th>start_station_id</th>
      <th>end_station_name</th>
      <th>end_station_id</th>
      <th>membership_type</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>C1D650626C8C899A</td>
      <td>electric_bike</td>
      <td>2024-01-12 15:30:27</td>
      <td>2024-01-12 15:37:59</td>
      <td>Wells St &amp; Elm St</td>
      <td>KA1504000135</td>
      <td>Kingsbury St &amp; Kinzie St</td>
      <td>KA1503000043</td>
      <td>member</td>
    </tr>
    <tr>
      <th>1</th>
      <td>EECD38BDB25BFCB0</td>
      <td>electric_bike</td>
      <td>2024-01-08 15:45:46</td>
      <td>2024-01-08 15:52:59</td>
      <td>Wells St &amp; Elm St</td>
      <td>KA1504000135</td>
      <td>Kingsbury St &amp; Kinzie St</td>
      <td>KA1503000043</td>
      <td>member</td>
    </tr>
    <tr>
      <th>2</th>
      <td>F4A9CE78061F17F7</td>
      <td>electric_bike</td>
      <td>2024-01-27 12:27:19</td>
      <td>2024-01-27 12:35:19</td>
      <td>Wells St &amp; Elm St</td>
      <td>KA1504000135</td>
      <td>Kingsbury St &amp; Kinzie St</td>
      <td>KA1503000043</td>
      <td>member</td>
    </tr>
    <tr>
      <th>3</th>
      <td>0A0D9E15EE50B171</td>
      <td>classic_bike</td>
      <td>2024-01-29 16:26:17</td>
      <td>2024-01-29 16:56:06</td>
      <td>Wells St &amp; Randolph St</td>
      <td>TA1305000030</td>
      <td>Larrabee St &amp; Webster Ave</td>
      <td>13193</td>
      <td>member</td>
    </tr>
    <tr>
      <th>4</th>
      <td>33FFC9805E3EFF9A</td>
      <td>classic_bike</td>
      <td>2024-01-31 05:43:23</td>
      <td>2024-01-31 06:09:35</td>
      <td>Lincoln Ave &amp; Waveland Ave</td>
      <td>13253</td>
      <td>Kingsbury St &amp; Kinzie St</td>
      <td>KA1503000043</td>
      <td>member</td>
    </tr>
  </tbody>
</table>
</div>



## Manage the timestamps

### Create start day, start time, end day and end time


```python
datetime_started_at =  pd.to_datetime(df['started_at'], format="ISO8601")
df['start_day'] = datetime_started_at.dt.date
df["start_time"] = datetime_started_at.dt.time

datetime_ended_at = pd.to_datetime(df['ended_at'], format="ISO8601")
df['end_day'] = datetime_ended_at.dt.date
df["end_time"] = datetime_ended_at.dt.time

df.head()
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
      <th>ride_id</th>
      <th>ride_type</th>
      <th>started_at</th>
      <th>ended_at</th>
      <th>start_station_name</th>
      <th>start_station_id</th>
      <th>end_station_name</th>
      <th>end_station_id</th>
      <th>membership_type</th>
      <th>start_day</th>
      <th>start_time</th>
      <th>end_day</th>
      <th>end_time</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>C1D650626C8C899A</td>
      <td>electric_bike</td>
      <td>2024-01-12 15:30:27</td>
      <td>2024-01-12 15:37:59</td>
      <td>Wells St &amp; Elm St</td>
      <td>KA1504000135</td>
      <td>Kingsbury St &amp; Kinzie St</td>
      <td>KA1503000043</td>
      <td>member</td>
      <td>2024-01-12</td>
      <td>15:30:27</td>
      <td>2024-01-12</td>
      <td>15:37:59</td>
    </tr>
    <tr>
      <th>1</th>
      <td>EECD38BDB25BFCB0</td>
      <td>electric_bike</td>
      <td>2024-01-08 15:45:46</td>
      <td>2024-01-08 15:52:59</td>
      <td>Wells St &amp; Elm St</td>
      <td>KA1504000135</td>
      <td>Kingsbury St &amp; Kinzie St</td>
      <td>KA1503000043</td>
      <td>member</td>
      <td>2024-01-08</td>
      <td>15:45:46</td>
      <td>2024-01-08</td>
      <td>15:52:59</td>
    </tr>
    <tr>
      <th>2</th>
      <td>F4A9CE78061F17F7</td>
      <td>electric_bike</td>
      <td>2024-01-27 12:27:19</td>
      <td>2024-01-27 12:35:19</td>
      <td>Wells St &amp; Elm St</td>
      <td>KA1504000135</td>
      <td>Kingsbury St &amp; Kinzie St</td>
      <td>KA1503000043</td>
      <td>member</td>
      <td>2024-01-27</td>
      <td>12:27:19</td>
      <td>2024-01-27</td>
      <td>12:35:19</td>
    </tr>
    <tr>
      <th>3</th>
      <td>0A0D9E15EE50B171</td>
      <td>classic_bike</td>
      <td>2024-01-29 16:26:17</td>
      <td>2024-01-29 16:56:06</td>
      <td>Wells St &amp; Randolph St</td>
      <td>TA1305000030</td>
      <td>Larrabee St &amp; Webster Ave</td>
      <td>13193</td>
      <td>member</td>
      <td>2024-01-29</td>
      <td>16:26:17</td>
      <td>2024-01-29</td>
      <td>16:56:06</td>
    </tr>
    <tr>
      <th>4</th>
      <td>33FFC9805E3EFF9A</td>
      <td>classic_bike</td>
      <td>2024-01-31 05:43:23</td>
      <td>2024-01-31 06:09:35</td>
      <td>Lincoln Ave &amp; Waveland Ave</td>
      <td>13253</td>
      <td>Kingsbury St &amp; Kinzie St</td>
      <td>KA1503000043</td>
      <td>member</td>
      <td>2024-01-31</td>
      <td>05:43:23</td>
      <td>2024-01-31</td>
      <td>06:09:35</td>
    </tr>
  </tbody>
</table>
</div>



### Add the day of the week


```python
import datetime

weekdays = {
    0: "Monday",
    1: "Tuesday",
    2: "Wednesday",
    3: "Thursday",
    4: "Friday",
    5: "Saturday",
    6: "Sunday"
}

def to_day(dt: datetime.datetime):
    return weekdays[dt.weekday()]

df['day_of_week'] = df['start_day'].map(to_day)

df.head()
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
      <th>ride_id</th>
      <th>ride_type</th>
      <th>started_at</th>
      <th>ended_at</th>
      <th>start_station_name</th>
      <th>start_station_id</th>
      <th>end_station_name</th>
      <th>end_station_id</th>
      <th>membership_type</th>
      <th>start_day</th>
      <th>start_time</th>
      <th>end_day</th>
      <th>end_time</th>
      <th>day_of_week</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>C1D650626C8C899A</td>
      <td>electric_bike</td>
      <td>2024-01-12 15:30:27</td>
      <td>2024-01-12 15:37:59</td>
      <td>Wells St &amp; Elm St</td>
      <td>KA1504000135</td>
      <td>Kingsbury St &amp; Kinzie St</td>
      <td>KA1503000043</td>
      <td>member</td>
      <td>2024-01-12</td>
      <td>15:30:27</td>
      <td>2024-01-12</td>
      <td>15:37:59</td>
      <td>Friday</td>
    </tr>
    <tr>
      <th>1</th>
      <td>EECD38BDB25BFCB0</td>
      <td>electric_bike</td>
      <td>2024-01-08 15:45:46</td>
      <td>2024-01-08 15:52:59</td>
      <td>Wells St &amp; Elm St</td>
      <td>KA1504000135</td>
      <td>Kingsbury St &amp; Kinzie St</td>
      <td>KA1503000043</td>
      <td>member</td>
      <td>2024-01-08</td>
      <td>15:45:46</td>
      <td>2024-01-08</td>
      <td>15:52:59</td>
      <td>Monday</td>
    </tr>
    <tr>
      <th>2</th>
      <td>F4A9CE78061F17F7</td>
      <td>electric_bike</td>
      <td>2024-01-27 12:27:19</td>
      <td>2024-01-27 12:35:19</td>
      <td>Wells St &amp; Elm St</td>
      <td>KA1504000135</td>
      <td>Kingsbury St &amp; Kinzie St</td>
      <td>KA1503000043</td>
      <td>member</td>
      <td>2024-01-27</td>
      <td>12:27:19</td>
      <td>2024-01-27</td>
      <td>12:35:19</td>
      <td>Saturday</td>
    </tr>
    <tr>
      <th>3</th>
      <td>0A0D9E15EE50B171</td>
      <td>classic_bike</td>
      <td>2024-01-29 16:26:17</td>
      <td>2024-01-29 16:56:06</td>
      <td>Wells St &amp; Randolph St</td>
      <td>TA1305000030</td>
      <td>Larrabee St &amp; Webster Ave</td>
      <td>13193</td>
      <td>member</td>
      <td>2024-01-29</td>
      <td>16:26:17</td>
      <td>2024-01-29</td>
      <td>16:56:06</td>
      <td>Monday</td>
    </tr>
    <tr>
      <th>4</th>
      <td>33FFC9805E3EFF9A</td>
      <td>classic_bike</td>
      <td>2024-01-31 05:43:23</td>
      <td>2024-01-31 06:09:35</td>
      <td>Lincoln Ave &amp; Waveland Ave</td>
      <td>13253</td>
      <td>Kingsbury St &amp; Kinzie St</td>
      <td>KA1503000043</td>
      <td>member</td>
      <td>2024-01-31</td>
      <td>05:43:23</td>
      <td>2024-01-31</td>
      <td>06:09:35</td>
      <td>Wednesday</td>
    </tr>
  </tbody>
</table>
</div>



### Add the month


```python
months = {
    1: "January",
    2: "February",
    3: "March",
    4: "April",
    5: "May",
    6: "June",
    7: "July",
    8: "August",
    9: "September",
    10: "October",
    11: "November",
    12: "December"
}

def to_month(dt: datetime.datetime):
    return months[dt.month]


df['month'] = df['start_day'].map(to_month)

df.head()

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
      <th>ride_id</th>
      <th>ride_type</th>
      <th>started_at</th>
      <th>ended_at</th>
      <th>start_station_name</th>
      <th>start_station_id</th>
      <th>end_station_name</th>
      <th>end_station_id</th>
      <th>membership_type</th>
      <th>start_day</th>
      <th>start_time</th>
      <th>end_day</th>
      <th>end_time</th>
      <th>day_of_week</th>
      <th>month</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>C1D650626C8C899A</td>
      <td>electric_bike</td>
      <td>2024-01-12 15:30:27</td>
      <td>2024-01-12 15:37:59</td>
      <td>Wells St &amp; Elm St</td>
      <td>KA1504000135</td>
      <td>Kingsbury St &amp; Kinzie St</td>
      <td>KA1503000043</td>
      <td>member</td>
      <td>2024-01-12</td>
      <td>15:30:27</td>
      <td>2024-01-12</td>
      <td>15:37:59</td>
      <td>Friday</td>
      <td>January</td>
    </tr>
    <tr>
      <th>1</th>
      <td>EECD38BDB25BFCB0</td>
      <td>electric_bike</td>
      <td>2024-01-08 15:45:46</td>
      <td>2024-01-08 15:52:59</td>
      <td>Wells St &amp; Elm St</td>
      <td>KA1504000135</td>
      <td>Kingsbury St &amp; Kinzie St</td>
      <td>KA1503000043</td>
      <td>member</td>
      <td>2024-01-08</td>
      <td>15:45:46</td>
      <td>2024-01-08</td>
      <td>15:52:59</td>
      <td>Monday</td>
      <td>January</td>
    </tr>
    <tr>
      <th>2</th>
      <td>F4A9CE78061F17F7</td>
      <td>electric_bike</td>
      <td>2024-01-27 12:27:19</td>
      <td>2024-01-27 12:35:19</td>
      <td>Wells St &amp; Elm St</td>
      <td>KA1504000135</td>
      <td>Kingsbury St &amp; Kinzie St</td>
      <td>KA1503000043</td>
      <td>member</td>
      <td>2024-01-27</td>
      <td>12:27:19</td>
      <td>2024-01-27</td>
      <td>12:35:19</td>
      <td>Saturday</td>
      <td>January</td>
    </tr>
    <tr>
      <th>3</th>
      <td>0A0D9E15EE50B171</td>
      <td>classic_bike</td>
      <td>2024-01-29 16:26:17</td>
      <td>2024-01-29 16:56:06</td>
      <td>Wells St &amp; Randolph St</td>
      <td>TA1305000030</td>
      <td>Larrabee St &amp; Webster Ave</td>
      <td>13193</td>
      <td>member</td>
      <td>2024-01-29</td>
      <td>16:26:17</td>
      <td>2024-01-29</td>
      <td>16:56:06</td>
      <td>Monday</td>
      <td>January</td>
    </tr>
    <tr>
      <th>4</th>
      <td>33FFC9805E3EFF9A</td>
      <td>classic_bike</td>
      <td>2024-01-31 05:43:23</td>
      <td>2024-01-31 06:09:35</td>
      <td>Lincoln Ave &amp; Waveland Ave</td>
      <td>13253</td>
      <td>Kingsbury St &amp; Kinzie St</td>
      <td>KA1503000043</td>
      <td>member</td>
      <td>2024-01-31</td>
      <td>05:43:23</td>
      <td>2024-01-31</td>
      <td>06:09:35</td>
      <td>Wednesday</td>
      <td>January</td>
    </tr>
  </tbody>
</table>
</div>



## Check if there are duplicate rows across all columns


```python
duplicateRows = df[df.duplicated()]

duplicateRows.head()

# No duplicate found
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
      <th>ride_id</th>
      <th>ride_type</th>
      <th>started_at</th>
      <th>ended_at</th>
      <th>start_station_name</th>
      <th>start_station_id</th>
      <th>end_station_name</th>
      <th>end_station_id</th>
      <th>membership_type</th>
      <th>start_day</th>
      <th>start_time</th>
      <th>end_day</th>
      <th>end_time</th>
      <th>day_of_week</th>
      <th>month</th>
    </tr>
  </thead>
  <tbody>
  </tbody>
</table>
</div>



## Check if there are duplicate rows with the columns ride_id and started_at


```python
# Finding duplicate rows across specific columns
duplicateRows2 = df[df.duplicated(['ride_id', 'started_at'])]

duplicateRows2.head()

# No duplicate found
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
      <th>ride_id</th>
      <th>ride_type</th>
      <th>started_at</th>
      <th>ended_at</th>
      <th>start_station_name</th>
      <th>start_station_id</th>
      <th>end_station_name</th>
      <th>end_station_id</th>
      <th>membership_type</th>
      <th>start_day</th>
      <th>start_time</th>
      <th>end_day</th>
      <th>end_time</th>
      <th>day_of_week</th>
      <th>month</th>
    </tr>
  </thead>
  <tbody>
  </tbody>
</table>
</div>



## Check if there are duplicate rows with the column ride_id

The column ride_id should have unique values.


```python
duplicateRows3 = df[df.duplicated(['ride_id'])]

duplicateRows3.head()

# 211 duplicates found (the table contains only the duplicated rows)
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
      <th>ride_id</th>
      <th>ride_type</th>
      <th>started_at</th>
      <th>ended_at</th>
      <th>start_station_name</th>
      <th>start_station_id</th>
      <th>end_station_name</th>
      <th>end_station_id</th>
      <th>membership_type</th>
      <th>start_day</th>
      <th>start_time</th>
      <th>end_day</th>
      <th>end_time</th>
      <th>day_of_week</th>
      <th>month</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>3368</th>
      <td>3B5CE4D8B3EE6ED8</td>
      <td>electric_bike</td>
      <td>2024-05-31 23:50:04.153</td>
      <td>2024-06-01 00:06:08.273</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>casual</td>
      <td>2024-05-31</td>
      <td>23:50:04.153000</td>
      <td>2024-06-01</td>
      <td>00:06:08.273000</td>
      <td>Friday</td>
      <td>May</td>
    </tr>
    <tr>
      <th>3392</th>
      <td>60B4DDFF369931B2</td>
      <td>electric_bike</td>
      <td>2024-05-31 23:51:07.398</td>
      <td>2024-06-01 00:25:37.453</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>casual</td>
      <td>2024-05-31</td>
      <td>23:51:07.398000</td>
      <td>2024-06-01</td>
      <td>00:25:37.453000</td>
      <td>Friday</td>
      <td>May</td>
    </tr>
    <tr>
      <th>3417</th>
      <td>1D8856396862BE62</td>
      <td>electric_bike</td>
      <td>2024-05-31 23:56:12.241</td>
      <td>2024-06-01 00:10:02.667</td>
      <td>California Ave &amp; Milwaukee Ave</td>
      <td>13084</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>casual</td>
      <td>2024-05-31</td>
      <td>23:56:12.241000</td>
      <td>2024-06-01</td>
      <td>00:10:02.667000</td>
      <td>Friday</td>
      <td>May</td>
    </tr>
    <tr>
      <th>4676</th>
      <td>B1B9E66D2E7C383D</td>
      <td>classic_bike</td>
      <td>2024-05-31 11:50:26.806</td>
      <td>2024-06-01 09:52:57.046</td>
      <td>Austin Blvd &amp; Madison St</td>
      <td>16918</td>
      <td>Austin Blvd &amp; Madison St</td>
      <td>16918</td>
      <td>casual</td>
      <td>2024-05-31</td>
      <td>11:50:26.806000</td>
      <td>2024-06-01</td>
      <td>09:52:57.046000</td>
      <td>Friday</td>
      <td>May</td>
    </tr>
    <tr>
      <th>8532</th>
      <td>9FF6FFB668739B47</td>
      <td>electric_bike</td>
      <td>2024-05-31 23:53:26.419</td>
      <td>2024-06-01 00:04:05.504</td>
      <td>Halsted St &amp; Wrightwood Ave</td>
      <td>TA1309000061</td>
      <td>Orleans St &amp; Chestnut St (NEXT Apts)</td>
      <td>620</td>
      <td>member</td>
      <td>2024-05-31</td>
      <td>23:53:26.419000</td>
      <td>2024-06-01</td>
      <td>00:04:05.504000</td>
      <td>Friday</td>
      <td>May</td>
    </tr>
  </tbody>
</table>
</div>



## Investigate the duplicated rows


```python
display(df.loc[df['ride_id'] == '3B5CE4D8B3EE6ED8'])
display(df.loc[df['ride_id'] == '60B4DDFF369931B2'])
display(df.loc[df['ride_id'] == '1D8856396862BE62'])
display(df.loc[df['ride_id'] == 'B1B9E66D2E7C383D'])

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
      <th>ride_id</th>
      <th>ride_type</th>
      <th>started_at</th>
      <th>ended_at</th>
      <th>start_station_name</th>
      <th>start_station_id</th>
      <th>end_station_name</th>
      <th>end_station_id</th>
      <th>membership_type</th>
      <th>start_day</th>
      <th>start_time</th>
      <th>end_day</th>
      <th>end_time</th>
      <th>day_of_week</th>
      <th>month</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>252296</th>
      <td>3B5CE4D8B3EE6ED8</td>
      <td>electric_bike</td>
      <td>2024-05-31 23:50:04</td>
      <td>2024-06-01 00:06:08</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>casual</td>
      <td>2024-05-31</td>
      <td>23:50:04</td>
      <td>2024-06-01</td>
      <td>00:06:08</td>
      <td>Friday</td>
      <td>May</td>
    </tr>
    <tr>
      <th>3368</th>
      <td>3B5CE4D8B3EE6ED8</td>
      <td>electric_bike</td>
      <td>2024-05-31 23:50:04.153</td>
      <td>2024-06-01 00:06:08.273</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>casual</td>
      <td>2024-05-31</td>
      <td>23:50:04.153000</td>
      <td>2024-06-01</td>
      <td>00:06:08.273000</td>
      <td>Friday</td>
      <td>May</td>
    </tr>
  </tbody>
</table>
</div>



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
      <th>ride_id</th>
      <th>ride_type</th>
      <th>started_at</th>
      <th>ended_at</th>
      <th>start_station_name</th>
      <th>start_station_id</th>
      <th>end_station_name</th>
      <th>end_station_id</th>
      <th>membership_type</th>
      <th>start_day</th>
      <th>start_time</th>
      <th>end_day</th>
      <th>end_time</th>
      <th>day_of_week</th>
      <th>month</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>252366</th>
      <td>60B4DDFF369931B2</td>
      <td>electric_bike</td>
      <td>2024-05-31 23:51:07</td>
      <td>2024-06-01 00:25:37</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>casual</td>
      <td>2024-05-31</td>
      <td>23:51:07</td>
      <td>2024-06-01</td>
      <td>00:25:37</td>
      <td>Friday</td>
      <td>May</td>
    </tr>
    <tr>
      <th>3392</th>
      <td>60B4DDFF369931B2</td>
      <td>electric_bike</td>
      <td>2024-05-31 23:51:07.398</td>
      <td>2024-06-01 00:25:37.453</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>casual</td>
      <td>2024-05-31</td>
      <td>23:51:07.398000</td>
      <td>2024-06-01</td>
      <td>00:25:37.453000</td>
      <td>Friday</td>
      <td>May</td>
    </tr>
  </tbody>
</table>
</div>



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
      <th>ride_id</th>
      <th>ride_type</th>
      <th>started_at</th>
      <th>ended_at</th>
      <th>start_station_name</th>
      <th>start_station_id</th>
      <th>end_station_name</th>
      <th>end_station_id</th>
      <th>membership_type</th>
      <th>start_day</th>
      <th>start_time</th>
      <th>end_day</th>
      <th>end_time</th>
      <th>day_of_week</th>
      <th>month</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>259866</th>
      <td>1D8856396862BE62</td>
      <td>electric_bike</td>
      <td>2024-05-31 23:56:12</td>
      <td>2024-06-01 00:10:02</td>
      <td>California Ave &amp; Milwaukee Ave</td>
      <td>13084</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>casual</td>
      <td>2024-05-31</td>
      <td>23:56:12</td>
      <td>2024-06-01</td>
      <td>00:10:02</td>
      <td>Friday</td>
      <td>May</td>
    </tr>
    <tr>
      <th>3417</th>
      <td>1D8856396862BE62</td>
      <td>electric_bike</td>
      <td>2024-05-31 23:56:12.241</td>
      <td>2024-06-01 00:10:02.667</td>
      <td>California Ave &amp; Milwaukee Ave</td>
      <td>13084</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>casual</td>
      <td>2024-05-31</td>
      <td>23:56:12.241000</td>
      <td>2024-06-01</td>
      <td>00:10:02.667000</td>
      <td>Friday</td>
      <td>May</td>
    </tr>
  </tbody>
</table>
</div>



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
      <th>ride_id</th>
      <th>ride_type</th>
      <th>started_at</th>
      <th>ended_at</th>
      <th>start_station_name</th>
      <th>start_station_id</th>
      <th>end_station_name</th>
      <th>end_station_id</th>
      <th>membership_type</th>
      <th>start_day</th>
      <th>start_time</th>
      <th>end_day</th>
      <th>end_time</th>
      <th>day_of_week</th>
      <th>month</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>137598</th>
      <td>B1B9E66D2E7C383D</td>
      <td>classic_bike</td>
      <td>2024-05-31 11:50:26</td>
      <td>2024-06-01 09:52:57</td>
      <td>Austin Blvd &amp; Madison St</td>
      <td>16918</td>
      <td>Austin Blvd &amp; Madison St</td>
      <td>16918</td>
      <td>casual</td>
      <td>2024-05-31</td>
      <td>11:50:26</td>
      <td>2024-06-01</td>
      <td>09:52:57</td>
      <td>Friday</td>
      <td>May</td>
    </tr>
    <tr>
      <th>4676</th>
      <td>B1B9E66D2E7C383D</td>
      <td>classic_bike</td>
      <td>2024-05-31 11:50:26.806</td>
      <td>2024-06-01 09:52:57.046</td>
      <td>Austin Blvd &amp; Madison St</td>
      <td>16918</td>
      <td>Austin Blvd &amp; Madison St</td>
      <td>16918</td>
      <td>casual</td>
      <td>2024-05-31</td>
      <td>11:50:26.806000</td>
      <td>2024-06-01</td>
      <td>09:52:57.046000</td>
      <td>Friday</td>
      <td>May</td>
    </tr>
  </tbody>
</table>
</div>


The duplicated rows are due to having one row with the milliseconds and the other without.
I want to remove the entries that have the milliseconds as I don't find them relevant for my future analysis

Before I choose to remove these rows, I can see that all the rows have different start_day and end_day.
Can the users keep their bikes through the night? 
It is a question that I would have asked to my team or the stakeholders. 
Because I don't have any stakeholder to ask that question, I choose that the bikes cannot be kept through the night.

# Check the rows where start_day and end_day are different


```python
df_different_days = df[(df['start_day'] != df['end_day'])]
df_different_days.head()

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
      <th>ride_id</th>
      <th>ride_type</th>
      <th>started_at</th>
      <th>ended_at</th>
      <th>start_station_name</th>
      <th>start_station_id</th>
      <th>end_station_name</th>
      <th>end_station_id</th>
      <th>membership_type</th>
      <th>start_day</th>
      <th>start_time</th>
      <th>end_day</th>
      <th>end_time</th>
      <th>day_of_week</th>
      <th>month</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>79</th>
      <td>C24AD33E4203FE44</td>
      <td>classic_bike</td>
      <td>2024-01-21 09:01:01</td>
      <td>2024-01-22 09:33:24</td>
      <td>Lincoln Ave &amp; Waveland Ave</td>
      <td>13253</td>
      <td>Lincoln Ave &amp; Roscoe St*</td>
      <td>chargingstx5</td>
      <td>member</td>
      <td>2024-01-21</td>
      <td>09:01:01</td>
      <td>2024-01-22</td>
      <td>09:33:24</td>
      <td>Sunday</td>
      <td>January</td>
    </tr>
    <tr>
      <th>97</th>
      <td>89ED82AC80B2C6EF</td>
      <td>classic_bike</td>
      <td>2024-01-17 15:10:04</td>
      <td>2024-01-18 09:24:40</td>
      <td>Adler Planetarium</td>
      <td>13431</td>
      <td>Franklin St &amp; Jackson Blvd</td>
      <td>TA1305000025</td>
      <td>member</td>
      <td>2024-01-17</td>
      <td>15:10:04</td>
      <td>2024-01-18</td>
      <td>09:24:40</td>
      <td>Wednesday</td>
      <td>January</td>
    </tr>
    <tr>
      <th>155</th>
      <td>96D5648124CE835E</td>
      <td>classic_bike</td>
      <td>2024-01-23 15:21:01</td>
      <td>2024-01-24 09:43:59</td>
      <td>Clark St &amp; Ida B Wells Dr</td>
      <td>TA1305000009</td>
      <td>Michigan Ave &amp; 8th St</td>
      <td>623</td>
      <td>member</td>
      <td>2024-01-23</td>
      <td>15:21:01</td>
      <td>2024-01-24</td>
      <td>09:43:59</td>
      <td>Tuesday</td>
      <td>January</td>
    </tr>
    <tr>
      <th>304</th>
      <td>189A29FA7326928F</td>
      <td>electric_bike</td>
      <td>2024-01-30 23:52:27</td>
      <td>2024-01-31 00:07:30</td>
      <td>Milwaukee Ave &amp; Fullerton Ave</td>
      <td>428</td>
      <td>Kedzie Ave &amp; Leland Ave</td>
      <td>KA1504000126</td>
      <td>member</td>
      <td>2024-01-30</td>
      <td>23:52:27</td>
      <td>2024-01-31</td>
      <td>00:07:30</td>
      <td>Tuesday</td>
      <td>January</td>
    </tr>
    <tr>
      <th>944</th>
      <td>A66231C35452D41C</td>
      <td>classic_bike</td>
      <td>2024-01-23 12:11:20</td>
      <td>2024-01-24 09:43:10</td>
      <td>Wells St &amp; 19th St</td>
      <td>SL-006</td>
      <td>Michigan Ave &amp; 8th St</td>
      <td>623</td>
      <td>member</td>
      <td>2024-01-23</td>
      <td>12:11:20</td>
      <td>2024-01-24</td>
      <td>09:43:10</td>
      <td>Tuesday</td>
      <td>January</td>
    </tr>
  </tbody>
</table>
</div>



## Remove the rows where the start day and end day are different


```python
index_names_different_days = df[(df['start_day'] != df['end_day'])].index
df.drop(index_names_different_days, inplace = True)
df.head()
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
      <th>ride_id</th>
      <th>ride_type</th>
      <th>started_at</th>
      <th>ended_at</th>
      <th>start_station_name</th>
      <th>start_station_id</th>
      <th>end_station_name</th>
      <th>end_station_id</th>
      <th>membership_type</th>
      <th>start_day</th>
      <th>start_time</th>
      <th>end_day</th>
      <th>end_time</th>
      <th>day_of_week</th>
      <th>month</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>C1D650626C8C899A</td>
      <td>electric_bike</td>
      <td>2024-01-12 15:30:27</td>
      <td>2024-01-12 15:37:59</td>
      <td>Wells St &amp; Elm St</td>
      <td>KA1504000135</td>
      <td>Kingsbury St &amp; Kinzie St</td>
      <td>KA1503000043</td>
      <td>member</td>
      <td>2024-01-12</td>
      <td>15:30:27</td>
      <td>2024-01-12</td>
      <td>15:37:59</td>
      <td>Friday</td>
      <td>January</td>
    </tr>
    <tr>
      <th>1</th>
      <td>EECD38BDB25BFCB0</td>
      <td>electric_bike</td>
      <td>2024-01-08 15:45:46</td>
      <td>2024-01-08 15:52:59</td>
      <td>Wells St &amp; Elm St</td>
      <td>KA1504000135</td>
      <td>Kingsbury St &amp; Kinzie St</td>
      <td>KA1503000043</td>
      <td>member</td>
      <td>2024-01-08</td>
      <td>15:45:46</td>
      <td>2024-01-08</td>
      <td>15:52:59</td>
      <td>Monday</td>
      <td>January</td>
    </tr>
    <tr>
      <th>2</th>
      <td>F4A9CE78061F17F7</td>
      <td>electric_bike</td>
      <td>2024-01-27 12:27:19</td>
      <td>2024-01-27 12:35:19</td>
      <td>Wells St &amp; Elm St</td>
      <td>KA1504000135</td>
      <td>Kingsbury St &amp; Kinzie St</td>
      <td>KA1503000043</td>
      <td>member</td>
      <td>2024-01-27</td>
      <td>12:27:19</td>
      <td>2024-01-27</td>
      <td>12:35:19</td>
      <td>Saturday</td>
      <td>January</td>
    </tr>
    <tr>
      <th>3</th>
      <td>0A0D9E15EE50B171</td>
      <td>classic_bike</td>
      <td>2024-01-29 16:26:17</td>
      <td>2024-01-29 16:56:06</td>
      <td>Wells St &amp; Randolph St</td>
      <td>TA1305000030</td>
      <td>Larrabee St &amp; Webster Ave</td>
      <td>13193</td>
      <td>member</td>
      <td>2024-01-29</td>
      <td>16:26:17</td>
      <td>2024-01-29</td>
      <td>16:56:06</td>
      <td>Monday</td>
      <td>January</td>
    </tr>
    <tr>
      <th>4</th>
      <td>33FFC9805E3EFF9A</td>
      <td>classic_bike</td>
      <td>2024-01-31 05:43:23</td>
      <td>2024-01-31 06:09:35</td>
      <td>Lincoln Ave &amp; Waveland Ave</td>
      <td>13253</td>
      <td>Kingsbury St &amp; Kinzie St</td>
      <td>KA1503000043</td>
      <td>member</td>
      <td>2024-01-31</td>
      <td>05:43:23</td>
      <td>2024-01-31</td>
      <td>06:09:35</td>
      <td>Wednesday</td>
      <td>January</td>
    </tr>
  </tbody>
</table>
</div>



## Verify if there are still duplicates


```python
duplicateRows3 = df[df.duplicated(['ride_id'])]

duplicateRows3.head()

# No duplicate found

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
      <th>ride_id</th>
      <th>ride_type</th>
      <th>started_at</th>
      <th>ended_at</th>
      <th>start_station_name</th>
      <th>start_station_id</th>
      <th>end_station_name</th>
      <th>end_station_id</th>
      <th>membership_type</th>
      <th>start_day</th>
      <th>start_time</th>
      <th>end_day</th>
      <th>end_time</th>
      <th>day_of_week</th>
      <th>month</th>
    </tr>
  </thead>
  <tbody>
  </tbody>
</table>
</div>



## Calculate the ride duration


```python
datetime_started_at =  pd.to_datetime(df['started_at'], format="ISO8601")
datetime_ended_at = pd.to_datetime(df['ended_at'], format="ISO8601")


df['ride_duration'] = (datetime_ended_at-datetime_started_at)

df.head()
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
      <th>ride_id</th>
      <th>ride_type</th>
      <th>started_at</th>
      <th>ended_at</th>
      <th>start_station_name</th>
      <th>start_station_id</th>
      <th>end_station_name</th>
      <th>end_station_id</th>
      <th>membership_type</th>
      <th>start_day</th>
      <th>start_time</th>
      <th>end_day</th>
      <th>end_time</th>
      <th>day_of_week</th>
      <th>month</th>
      <th>ride_duration</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>C1D650626C8C899A</td>
      <td>electric_bike</td>
      <td>2024-01-12 15:30:27</td>
      <td>2024-01-12 15:37:59</td>
      <td>Wells St &amp; Elm St</td>
      <td>KA1504000135</td>
      <td>Kingsbury St &amp; Kinzie St</td>
      <td>KA1503000043</td>
      <td>member</td>
      <td>2024-01-12</td>
      <td>15:30:27</td>
      <td>2024-01-12</td>
      <td>15:37:59</td>
      <td>Friday</td>
      <td>January</td>
      <td>0 days 00:07:32</td>
    </tr>
    <tr>
      <th>1</th>
      <td>EECD38BDB25BFCB0</td>
      <td>electric_bike</td>
      <td>2024-01-08 15:45:46</td>
      <td>2024-01-08 15:52:59</td>
      <td>Wells St &amp; Elm St</td>
      <td>KA1504000135</td>
      <td>Kingsbury St &amp; Kinzie St</td>
      <td>KA1503000043</td>
      <td>member</td>
      <td>2024-01-08</td>
      <td>15:45:46</td>
      <td>2024-01-08</td>
      <td>15:52:59</td>
      <td>Monday</td>
      <td>January</td>
      <td>0 days 00:07:13</td>
    </tr>
    <tr>
      <th>2</th>
      <td>F4A9CE78061F17F7</td>
      <td>electric_bike</td>
      <td>2024-01-27 12:27:19</td>
      <td>2024-01-27 12:35:19</td>
      <td>Wells St &amp; Elm St</td>
      <td>KA1504000135</td>
      <td>Kingsbury St &amp; Kinzie St</td>
      <td>KA1503000043</td>
      <td>member</td>
      <td>2024-01-27</td>
      <td>12:27:19</td>
      <td>2024-01-27</td>
      <td>12:35:19</td>
      <td>Saturday</td>
      <td>January</td>
      <td>0 days 00:08:00</td>
    </tr>
    <tr>
      <th>3</th>
      <td>0A0D9E15EE50B171</td>
      <td>classic_bike</td>
      <td>2024-01-29 16:26:17</td>
      <td>2024-01-29 16:56:06</td>
      <td>Wells St &amp; Randolph St</td>
      <td>TA1305000030</td>
      <td>Larrabee St &amp; Webster Ave</td>
      <td>13193</td>
      <td>member</td>
      <td>2024-01-29</td>
      <td>16:26:17</td>
      <td>2024-01-29</td>
      <td>16:56:06</td>
      <td>Monday</td>
      <td>January</td>
      <td>0 days 00:29:49</td>
    </tr>
    <tr>
      <th>4</th>
      <td>33FFC9805E3EFF9A</td>
      <td>classic_bike</td>
      <td>2024-01-31 05:43:23</td>
      <td>2024-01-31 06:09:35</td>
      <td>Lincoln Ave &amp; Waveland Ave</td>
      <td>13253</td>
      <td>Kingsbury St &amp; Kinzie St</td>
      <td>KA1503000043</td>
      <td>member</td>
      <td>2024-01-31</td>
      <td>05:43:23</td>
      <td>2024-01-31</td>
      <td>06:09:35</td>
      <td>Wednesday</td>
      <td>January</td>
      <td>0 days 00:26:12</td>
    </tr>
  </tbody>
</table>
</div>



## Calculate the ride duration in minutes for easier data visualiations


```python
df['ride_duration_minutes'] = pd.to_timedelta(df['ride_duration']).dt.total_seconds()/60
df['ride_duration_minutes']= round(df['ride_duration_minutes'],1)
df.head()
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
      <th>ride_id</th>
      <th>ride_type</th>
      <th>started_at</th>
      <th>ended_at</th>
      <th>start_station_name</th>
      <th>start_station_id</th>
      <th>end_station_name</th>
      <th>end_station_id</th>
      <th>membership_type</th>
      <th>start_day</th>
      <th>start_time</th>
      <th>end_day</th>
      <th>end_time</th>
      <th>day_of_week</th>
      <th>month</th>
      <th>ride_duration</th>
      <th>ride_duration_minutes</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>C1D650626C8C899A</td>
      <td>electric_bike</td>
      <td>2024-01-12 15:30:27</td>
      <td>2024-01-12 15:37:59</td>
      <td>Wells St &amp; Elm St</td>
      <td>KA1504000135</td>
      <td>Kingsbury St &amp; Kinzie St</td>
      <td>KA1503000043</td>
      <td>member</td>
      <td>2024-01-12</td>
      <td>15:30:27</td>
      <td>2024-01-12</td>
      <td>15:37:59</td>
      <td>Friday</td>
      <td>January</td>
      <td>0 days 00:07:32</td>
      <td>7.5</td>
    </tr>
    <tr>
      <th>1</th>
      <td>EECD38BDB25BFCB0</td>
      <td>electric_bike</td>
      <td>2024-01-08 15:45:46</td>
      <td>2024-01-08 15:52:59</td>
      <td>Wells St &amp; Elm St</td>
      <td>KA1504000135</td>
      <td>Kingsbury St &amp; Kinzie St</td>
      <td>KA1503000043</td>
      <td>member</td>
      <td>2024-01-08</td>
      <td>15:45:46</td>
      <td>2024-01-08</td>
      <td>15:52:59</td>
      <td>Monday</td>
      <td>January</td>
      <td>0 days 00:07:13</td>
      <td>7.2</td>
    </tr>
    <tr>
      <th>2</th>
      <td>F4A9CE78061F17F7</td>
      <td>electric_bike</td>
      <td>2024-01-27 12:27:19</td>
      <td>2024-01-27 12:35:19</td>
      <td>Wells St &amp; Elm St</td>
      <td>KA1504000135</td>
      <td>Kingsbury St &amp; Kinzie St</td>
      <td>KA1503000043</td>
      <td>member</td>
      <td>2024-01-27</td>
      <td>12:27:19</td>
      <td>2024-01-27</td>
      <td>12:35:19</td>
      <td>Saturday</td>
      <td>January</td>
      <td>0 days 00:08:00</td>
      <td>8.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>0A0D9E15EE50B171</td>
      <td>classic_bike</td>
      <td>2024-01-29 16:26:17</td>
      <td>2024-01-29 16:56:06</td>
      <td>Wells St &amp; Randolph St</td>
      <td>TA1305000030</td>
      <td>Larrabee St &amp; Webster Ave</td>
      <td>13193</td>
      <td>member</td>
      <td>2024-01-29</td>
      <td>16:26:17</td>
      <td>2024-01-29</td>
      <td>16:56:06</td>
      <td>Monday</td>
      <td>January</td>
      <td>0 days 00:29:49</td>
      <td>29.8</td>
    </tr>
    <tr>
      <th>4</th>
      <td>33FFC9805E3EFF9A</td>
      <td>classic_bike</td>
      <td>2024-01-31 05:43:23</td>
      <td>2024-01-31 06:09:35</td>
      <td>Lincoln Ave &amp; Waveland Ave</td>
      <td>13253</td>
      <td>Kingsbury St &amp; Kinzie St</td>
      <td>KA1503000043</td>
      <td>member</td>
      <td>2024-01-31</td>
      <td>05:43:23</td>
      <td>2024-01-31</td>
      <td>06:09:35</td>
      <td>Wednesday</td>
      <td>January</td>
      <td>0 days 00:26:12</td>
      <td>26.2</td>
    </tr>
  </tbody>
</table>
</div>



## Verify the number of rows


```python
len(df.index)
```




    5679014



## Find rows where start_time and end_time are the same


```python
df_same_time = df[(df['start_time'] == df['end_time'])]
df_same_time.head()

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
      <th>ride_id</th>
      <th>ride_type</th>
      <th>started_at</th>
      <th>ended_at</th>
      <th>start_station_name</th>
      <th>start_station_id</th>
      <th>end_station_name</th>
      <th>end_station_id</th>
      <th>membership_type</th>
      <th>start_day</th>
      <th>start_time</th>
      <th>end_day</th>
      <th>end_time</th>
      <th>day_of_week</th>
      <th>month</th>
      <th>ride_duration</th>
      <th>ride_duration_minutes</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>6951</th>
      <td>D20C5C3F081852A9</td>
      <td>electric_bike</td>
      <td>2024-01-10 17:34:38</td>
      <td>2024-01-10 17:34:38</td>
      <td>Sheridan Rd &amp; Irving Park Rd</td>
      <td>13063</td>
      <td>Sheridan Rd &amp; Irving Park Rd</td>
      <td>13063</td>
      <td>member</td>
      <td>2024-01-10</td>
      <td>17:34:38</td>
      <td>2024-01-10</td>
      <td>17:34:38</td>
      <td>Wednesday</td>
      <td>January</td>
      <td>0 days</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>9983</th>
      <td>2EC792ACA079CEAE</td>
      <td>electric_bike</td>
      <td>2024-01-26 11:56:58</td>
      <td>2024-01-26 11:56:58</td>
      <td>Kildare Ave &amp; 26th St</td>
      <td>365.0</td>
      <td>Kildare Ave &amp; 26th St</td>
      <td>365.0</td>
      <td>casual</td>
      <td>2024-01-26</td>
      <td>11:56:58</td>
      <td>2024-01-26</td>
      <td>11:56:58</td>
      <td>Friday</td>
      <td>January</td>
      <td>0 days</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>13425</th>
      <td>F55B9A59BA0D16D4</td>
      <td>classic_bike</td>
      <td>2024-01-18 07:39:00</td>
      <td>2024-01-18 07:39:00</td>
      <td>Burling St &amp; Diversey Pkwy</td>
      <td>TA1309000036</td>
      <td>Burling St &amp; Diversey Pkwy</td>
      <td>TA1309000036</td>
      <td>member</td>
      <td>2024-01-18</td>
      <td>07:39:00</td>
      <td>2024-01-18</td>
      <td>07:39:00</td>
      <td>Thursday</td>
      <td>January</td>
      <td>0 days</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>15370</th>
      <td>D0AD175B04D31071</td>
      <td>classic_bike</td>
      <td>2024-01-03 21:29:47</td>
      <td>2024-01-03 21:29:47</td>
      <td>Ashland Ave &amp; Wrightwood Ave</td>
      <td>13296</td>
      <td>Ashland Ave &amp; Wrightwood Ave</td>
      <td>13296</td>
      <td>member</td>
      <td>2024-01-03</td>
      <td>21:29:47</td>
      <td>2024-01-03</td>
      <td>21:29:47</td>
      <td>Wednesday</td>
      <td>January</td>
      <td>0 days</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>35908</th>
      <td>A25DDEEC43678E83</td>
      <td>classic_bike</td>
      <td>2024-01-31 05:16:21</td>
      <td>2024-01-31 05:16:21</td>
      <td>Clarendon Ave &amp; Junior Ter</td>
      <td>13389</td>
      <td>Clarendon Ave &amp; Junior Ter</td>
      <td>13389</td>
      <td>member</td>
      <td>2024-01-31</td>
      <td>05:16:21</td>
      <td>2024-01-31</td>
      <td>05:16:21</td>
      <td>Wednesday</td>
      <td>January</td>
      <td>0 days</td>
      <td>0.0</td>
    </tr>
  </tbody>
</table>
</div>



## Remove rows where start_time and end_time are the same


```python
index_names_same_time = df[(df['start_time'] == df['end_time'])].index
df.drop(index_names_same_time, inplace = True)
df.head()
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
      <th>ride_id</th>
      <th>ride_type</th>
      <th>started_at</th>
      <th>ended_at</th>
      <th>start_station_name</th>
      <th>start_station_id</th>
      <th>end_station_name</th>
      <th>end_station_id</th>
      <th>membership_type</th>
      <th>start_day</th>
      <th>start_time</th>
      <th>end_day</th>
      <th>end_time</th>
      <th>day_of_week</th>
      <th>month</th>
      <th>ride_duration</th>
      <th>ride_duration_minutes</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>C1D650626C8C899A</td>
      <td>electric_bike</td>
      <td>2024-01-12 15:30:27</td>
      <td>2024-01-12 15:37:59</td>
      <td>Wells St &amp; Elm St</td>
      <td>KA1504000135</td>
      <td>Kingsbury St &amp; Kinzie St</td>
      <td>KA1503000043</td>
      <td>member</td>
      <td>2024-01-12</td>
      <td>15:30:27</td>
      <td>2024-01-12</td>
      <td>15:37:59</td>
      <td>Friday</td>
      <td>January</td>
      <td>0 days 00:07:32</td>
      <td>7.5</td>
    </tr>
    <tr>
      <th>1</th>
      <td>EECD38BDB25BFCB0</td>
      <td>electric_bike</td>
      <td>2024-01-08 15:45:46</td>
      <td>2024-01-08 15:52:59</td>
      <td>Wells St &amp; Elm St</td>
      <td>KA1504000135</td>
      <td>Kingsbury St &amp; Kinzie St</td>
      <td>KA1503000043</td>
      <td>member</td>
      <td>2024-01-08</td>
      <td>15:45:46</td>
      <td>2024-01-08</td>
      <td>15:52:59</td>
      <td>Monday</td>
      <td>January</td>
      <td>0 days 00:07:13</td>
      <td>7.2</td>
    </tr>
    <tr>
      <th>2</th>
      <td>F4A9CE78061F17F7</td>
      <td>electric_bike</td>
      <td>2024-01-27 12:27:19</td>
      <td>2024-01-27 12:35:19</td>
      <td>Wells St &amp; Elm St</td>
      <td>KA1504000135</td>
      <td>Kingsbury St &amp; Kinzie St</td>
      <td>KA1503000043</td>
      <td>member</td>
      <td>2024-01-27</td>
      <td>12:27:19</td>
      <td>2024-01-27</td>
      <td>12:35:19</td>
      <td>Saturday</td>
      <td>January</td>
      <td>0 days 00:08:00</td>
      <td>8.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>0A0D9E15EE50B171</td>
      <td>classic_bike</td>
      <td>2024-01-29 16:26:17</td>
      <td>2024-01-29 16:56:06</td>
      <td>Wells St &amp; Randolph St</td>
      <td>TA1305000030</td>
      <td>Larrabee St &amp; Webster Ave</td>
      <td>13193</td>
      <td>member</td>
      <td>2024-01-29</td>
      <td>16:26:17</td>
      <td>2024-01-29</td>
      <td>16:56:06</td>
      <td>Monday</td>
      <td>January</td>
      <td>0 days 00:29:49</td>
      <td>29.8</td>
    </tr>
    <tr>
      <th>4</th>
      <td>33FFC9805E3EFF9A</td>
      <td>classic_bike</td>
      <td>2024-01-31 05:43:23</td>
      <td>2024-01-31 06:09:35</td>
      <td>Lincoln Ave &amp; Waveland Ave</td>
      <td>13253</td>
      <td>Kingsbury St &amp; Kinzie St</td>
      <td>KA1503000043</td>
      <td>member</td>
      <td>2024-01-31</td>
      <td>05:43:23</td>
      <td>2024-01-31</td>
      <td>06:09:35</td>
      <td>Wednesday</td>
      <td>January</td>
      <td>0 days 00:26:12</td>
      <td>26.2</td>
    </tr>
  </tbody>
</table>
</div>




```python
len(df.index)
```




    5673138



##  Check if any row has a ride_id that doesn't have the right length


```python
ride_id_lengths = df["ride_id"].str.len().unique()

print(ride_id_lengths)

```

    [16]
    

I have confirmed that all the ride_ids are 16 characters long.

## Check the numbers of null values per columns


```python
df.isnull().sum(axis = 0)

```




    ride_id                        0
    ride_type                      0
    started_at                     0
    ended_at                       0
    start_station_name       1039616
    start_station_id         1039616
    end_station_name         1063830
    end_station_id           1063830
    membership_type                0
    start_day                      0
    start_time                     0
    end_day                        0
    end_time                       0
    day_of_week                    0
    month                          0
    ride_duration                  0
    ride_duration_minutes          0
    dtype: int64



There is about 1/5 of the dataframe that don't have a start_station_id, start_station name, end_station_name and end_station_id.

Because I already have a lot of data to analyse, I will be creating a specific dataframe for Tableau without those rows.

# Remove all the rows that have a null value


```python
df_for_tableau = df.copy()
df_for_tableau = df_for_tableau.dropna(how='any',axis=0) 

len(df_for_tableau.index)
```




    4084229



## Remove additional columns I will not be using


```python
to_drop = ['started_at',
           'ended_at',
           'ride_duration']

df_for_tableau.drop(to_drop, inplace=True, axis=1)

df_for_tableau.head()
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
      <th>ride_id</th>
      <th>ride_type</th>
      <th>start_station_name</th>
      <th>start_station_id</th>
      <th>end_station_name</th>
      <th>end_station_id</th>
      <th>membership_type</th>
      <th>start_day</th>
      <th>start_time</th>
      <th>end_day</th>
      <th>end_time</th>
      <th>day_of_week</th>
      <th>month</th>
      <th>ride_duration_minutes</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>C1D650626C8C899A</td>
      <td>electric_bike</td>
      <td>Wells St &amp; Elm St</td>
      <td>KA1504000135</td>
      <td>Kingsbury St &amp; Kinzie St</td>
      <td>KA1503000043</td>
      <td>member</td>
      <td>2024-01-12</td>
      <td>15:30:27</td>
      <td>2024-01-12</td>
      <td>15:37:59</td>
      <td>Friday</td>
      <td>January</td>
      <td>7.5</td>
    </tr>
    <tr>
      <th>1</th>
      <td>EECD38BDB25BFCB0</td>
      <td>electric_bike</td>
      <td>Wells St &amp; Elm St</td>
      <td>KA1504000135</td>
      <td>Kingsbury St &amp; Kinzie St</td>
      <td>KA1503000043</td>
      <td>member</td>
      <td>2024-01-08</td>
      <td>15:45:46</td>
      <td>2024-01-08</td>
      <td>15:52:59</td>
      <td>Monday</td>
      <td>January</td>
      <td>7.2</td>
    </tr>
    <tr>
      <th>2</th>
      <td>F4A9CE78061F17F7</td>
      <td>electric_bike</td>
      <td>Wells St &amp; Elm St</td>
      <td>KA1504000135</td>
      <td>Kingsbury St &amp; Kinzie St</td>
      <td>KA1503000043</td>
      <td>member</td>
      <td>2024-01-27</td>
      <td>12:27:19</td>
      <td>2024-01-27</td>
      <td>12:35:19</td>
      <td>Saturday</td>
      <td>January</td>
      <td>8.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>0A0D9E15EE50B171</td>
      <td>classic_bike</td>
      <td>Wells St &amp; Randolph St</td>
      <td>TA1305000030</td>
      <td>Larrabee St &amp; Webster Ave</td>
      <td>13193</td>
      <td>member</td>
      <td>2024-01-29</td>
      <td>16:26:17</td>
      <td>2024-01-29</td>
      <td>16:56:06</td>
      <td>Monday</td>
      <td>January</td>
      <td>29.8</td>
    </tr>
    <tr>
      <th>4</th>
      <td>33FFC9805E3EFF9A</td>
      <td>classic_bike</td>
      <td>Lincoln Ave &amp; Waveland Ave</td>
      <td>13253</td>
      <td>Kingsbury St &amp; Kinzie St</td>
      <td>KA1503000043</td>
      <td>member</td>
      <td>2024-01-31</td>
      <td>05:43:23</td>
      <td>2024-01-31</td>
      <td>06:09:35</td>
      <td>Wednesday</td>
      <td>January</td>
      <td>26.2</td>
    </tr>
  </tbody>
</table>
</div>



## Create a csv for my analysis in Tableau


```python
df_for_tableau.to_csv("C:\\Users\\33626\\Documents\\case_study_1\\df_for_tableau.csv")

```
