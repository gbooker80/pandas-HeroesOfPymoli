
# Heroes Of Pymoli Data Analysis
* The 16-25 age demographic accounts for nearly two-thirds (65.33%) of all players.
* Although accounting for a small subset of total number of players, those who decline to state their gender outspend both the male and female groups.
* Our oldest demographic (36+) makes the largest purchases on average.  


```python
import pandas as pd
import numpy as np
import json
```


```python
purchase_data_1 = "purchase_data.json"
purchase_data_2 = "purchase_data2.json"

purchase_data_1_df = pd.read_json(purchase_data_1)
purchase_data_2_df = pd.read_json(purchase_data_2)
```


```python
combined_purchases = pd.concat([purchase_data_1_df, purchase_data_2_df])
```

## Player Count


```python
total_players = combined_purchases.loc[:, ["Gender", "SN", "Age"]]
total_players = total_players.drop_duplicates()
count = total_players.count()[0]
```


```python
pd.DataFrame({"Total Players": [count]})
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Total Players</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>646</td>
    </tr>
  </tbody>
</table>
</div>




```python
average_spent = combined_purchases["Price"].mean()
total_revenue = combined_purchases["Price"].sum()
total_purchases = combined_purchases["Price"].count()
item_count = len(combined_purchases["Item ID"].unique())

summary_table = pd.DataFrame({"Unique Items": item_count,
                              "Total Revenue": [total_revenue],
                              "Total Purchases": [total_purchases],
                              "Average Spent": [average_price]})

summary_table ["Average Price"] = summary_table["Average Spent"].map("${:,.2f}".format)
summary_table ["Total Revenue"] = summary_table["Total Revenue"].map("${:,.2f}".format)
summary_table = summary_table.loc[:,["Unique Items", "Average Spent", "Total Purchases", "Total Revenue"]]

summary_table
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Unique Items</th>
      <th>Average Spent</th>
      <th>Total Purchases</th>
      <th>Total Revenue</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>184</td>
      <td>2.930571</td>
      <td>858</td>
      <td>$2,514.43</td>
    </tr>
  </tbody>
</table>
</div>



## Gender Demographics


```python
gender_totals = player_demographics["Gender"].value_counts()
gender_percentage = (gender_totals / count * 100).round(2)
gender_totals = pd.DataFrame({"Total Count": gender_totals, "Percentage of Total": gender_percentage})

gender_totals
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Percentage of Total</th>
      <th>Total Count</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Male</th>
      <td>81.11</td>
      <td>524</td>
    </tr>
    <tr>
      <th>Female</th>
      <td>17.49</td>
      <td>113</td>
    </tr>
    <tr>
      <th>Other / Non-Disclosed</th>
      <td>1.39</td>
      <td>9</td>
    </tr>
  </tbody>
</table>
</div>



## Purchases by Gender


```python
gender_purchase_total = combined_purchases.groupby(["Gender"]).sum()["Price"].rename("Total Spent").map("${:,.2f}".format)
gender_average = combined_purchases.groupby(["Gender"]).mean()["Price"].rename("Average Purchase").map("${:,.2f}".format)
gender_counts = combined_purchases.groupby(["Gender"]).count()["Price"].rename("Total Purchases")
gender_data = pd.DataFrame({"Total Purchases": gender_counts, "Average Purchase": gender_average, "Total Spent": gender_purchase_total})

gender_data
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Average Purchase</th>
      <th>Total Purchases</th>
      <th>Total Spent</th>
    </tr>
    <tr>
      <th>Gender</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Female</th>
      <td>$2.85</td>
      <td>149</td>
      <td>$424.29</td>
    </tr>
    <tr>
      <th>Male</th>
      <td>$2.94</td>
      <td>697</td>
      <td>$2,052.28</td>
    </tr>
    <tr>
      <th>Other / Non-Disclosed</th>
      <td>$3.15</td>
      <td>12</td>
      <td>$37.86</td>
    </tr>
  </tbody>
</table>
</div>



## Age Demographics


```python
age_bins = [0, 9.90, 15.90, 25.90, 35.90, 999]
age_groups = ["<10", "10-15", "16-25", "26-35", "36+"]

player_demographics["Age Ranges"] = pd.cut(player_demographics["Age"], age_bins, labels=group_names)

age_demographics_totals = player_demographics["Age Ranges"].value_counts()
age_demographics_percents = (age_demographics_totals / count * 100).round(2)
age_demographics = pd.DataFrame({"Percentage of Total": age_demographics_percents, "Group Total": age_demographics_totals})

age_demographics.sort_index()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Group Total</th>
      <th>Percentage of Total</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>&lt;10</th>
      <td>24</td>
      <td>3.72</td>
    </tr>
    <tr>
      <th>10-15</th>
      <td>61</td>
      <td>9.44</td>
    </tr>
    <tr>
      <th>16-25</th>
      <td>422</td>
      <td>65.33</td>
    </tr>
    <tr>
      <th>26-35</th>
      <td>106</td>
      <td>16.41</td>
    </tr>
    <tr>
      <th>36+</th>
      <td>33</td>
      <td>5.11</td>
    </tr>
  </tbody>
</table>
</div>



## Purchases by Age


```python
combined_purchases["Age Ranges"] = pd.cut(combined_purchases["Age"], age_bins, labels=group_names)

age_purchase_total = combined_purchases.groupby(["Age Ranges"]).sum()["Price"].rename("Total Spent").map("${:,.2f}".format)
age_average = combined_purchases.groupby(["Age Ranges"]).mean()["Price"].rename("Average Spent").map("${:,.2f}".format)
age_counts = combined_purchases.groupby(["Age Ranges"]).count()["Price"].rename("Total Purchases")
age_data = pd.DataFrame({"Total Purchases": age_counts, "Average Spent": age_average, "Total Spent": age_purchase_total})
age_data = age_data.loc[:, ["Total Purchases", "Average Spent", "Total Spent"]]

age_data
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Total Purchases</th>
      <th>Average Spent</th>
      <th>Total Spent</th>
    </tr>
    <tr>
      <th>Age Ranges</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>&lt;10</th>
      <td>33</td>
      <td>$2.95</td>
      <td>$97.28</td>
    </tr>
    <tr>
      <th>10-15</th>
      <td>86</td>
      <td>$2.90</td>
      <td>$249.52</td>
    </tr>
    <tr>
      <th>16-25</th>
      <td>542</td>
      <td>$2.93</td>
      <td>$1,586.46</td>
    </tr>
    <tr>
      <th>26-35</th>
      <td>145</td>
      <td>$2.93</td>
      <td>$425.32</td>
    </tr>
    <tr>
      <th>36+</th>
      <td>52</td>
      <td>$3.00</td>
      <td>$155.85</td>
    </tr>
  </tbody>
</table>
</div>



## Biggest Spenders


```python
user_spend = combined_purchases.groupby(["SN"]).sum()["Price"].rename("Total Spent").map("${:,.2f}".format)
user_average = combined_purchases.groupby(["SN"]).mean()["Price"].rename("Average Spent").map("${:,.2f}".format)
user_purchases = combined_purchases.groupby(["SN"]).count()["Price"].rename("Total Purchases")

user_data = pd.DataFrame({"Total Spent": user_total, "Average Spent": user_average, "Total Purchases": user_count})
user_data = user_data.loc[:,["Total Purchases", "Average Spent", "Total Spent"]]

user_data.sort_values("Total Spent", ascending=False).head(5)
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Total Purchases</th>
      <th>Average Spent</th>
      <th>Total Spent</th>
    </tr>
    <tr>
      <th>SN</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Qarwen67</th>
      <td>4</td>
      <td>$2.49</td>
      <td>$9.97</td>
    </tr>
    <tr>
      <th>Ila44</th>
      <td>4</td>
      <td>$2.42</td>
      <td>$9.68</td>
    </tr>
    <tr>
      <th>Airi27</th>
      <td>3</td>
      <td>$3.16</td>
      <td>$9.48</td>
    </tr>
    <tr>
      <th>Lisistaya47</th>
      <td>3</td>
      <td>$3.06</td>
      <td>$9.19</td>
    </tr>
    <tr>
      <th>Tillyrin30</th>
      <td>3</td>
      <td>$3.06</td>
      <td>$9.19</td>
    </tr>
  </tbody>
</table>
</div>



## Most Popular Items


```python
item_popular = combined_purchases.loc[:,["Item ID", "Item Name", "Price"]]

item_revenue = item_popular.groupby(["Item ID", "Item Name"]).sum()["Price"].rename("Total Spent")
item_average = item_popular.groupby(["Item ID", "Item Name"]).mean()["Price"]
item_total = item_popular.groupby(["Item ID", "Item Name"]).count()["Price"].rename("Number of Purchases")
item_popular_pd = item_popular_pd.loc[:,["Number of Purchases", "Item Price", "Total Spent"]]

item_popular_pd.sort_values("Number of Purchases", ascending=False).head(5)
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th></th>
      <th>Number of Purchases</th>
      <th>Item Price</th>
      <th>Total Spent</th>
    </tr>
    <tr>
      <th>Item ID</th>
      <th>Item Name</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>107</th>
      <th>Splitter, Foe Of Subtlety</th>
      <td>9</td>
      <td>$3.67</td>
      <td>$33.03</td>
    </tr>
    <tr>
      <th>108</th>
      <th>Extraction, Quickblade Of Trembling Hands</th>
      <td>9</td>
      <td>$3.14</td>
      <td>$28.25</td>
    </tr>
    <tr>
      <th>175</th>
      <th>Woeful Adamantite Claymore</th>
      <td>9</td>
      <td>$1.24</td>
      <td>$11.16</td>
    </tr>
    <tr>
      <th>44</th>
      <th>Bonecarvin Battle Axe</th>
      <td>9</td>
      <td>$2.67</td>
      <td>$24.04</td>
    </tr>
    <tr>
      <th>34</th>
      <th>Retribution Axe</th>
      <td>9</td>
      <td>$4.14</td>
      <td>$37.26</td>
    </tr>
  </tbody>
</table>
</div>



## Most Profitable Items


```python
item_popular_pd.sort_values("Total Spent", ascending=False).head(5)
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th></th>
      <th>Number of Purchases</th>
      <th>Item Price</th>
      <th>Total Spent</th>
    </tr>
    <tr>
      <th>Item ID</th>
      <th>Item Name</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>21</th>
      <th>Souleater</th>
      <td>3</td>
      <td>$3.27</td>
      <td>$9.81</td>
    </tr>
    <tr>
      <th>37</th>
      <th>Shadow Strike, Glory of Ending Hope</th>
      <td>5</td>
      <td>$1.93</td>
      <td>$9.65</td>
    </tr>
    <tr>
      <th>120</th>
      <th>Agatha</th>
      <td>5</td>
      <td>$1.91</td>
      <td>$9.55</td>
    </tr>
    <tr>
      <th>96</th>
      <th>Blood-Forged Skeletal Spine</th>
      <td>2</td>
      <td>$4.77</td>
      <td>$9.54</td>
    </tr>
    <tr>
      <th>47</th>
      <th>Alpha, Reach of Ending Hope</th>
      <td>6</td>
      <td>$1.55</td>
      <td>$9.30</td>
    </tr>
  </tbody>
</table>
</div>


