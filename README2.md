

```python
import pandas as pd
```


```python
filename = 'Resources/purchase_data2.json'
purchase_df = pd.read_json(filename)
```


```python
players_df = purchase_df.groupby(["SN"])
total_players = len(players_df)
player_count_summary = pd.DataFrame({"Total Players":total_players},index=[0])
print("Player Count")
player_count_summary
```

    Player Count
    




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
      <td>74</td>
    </tr>
  </tbody>
</table>
</div>




```python
items_df = purchase_df.groupby(["Item ID","Item Name"])
unique_items = len(items_df)
average_price = purchase_df["Price"].mean()
total_purchases = len(purchase_df)
total_revenue = purchase_df["Price"].sum()
pd.options.display.float_format = '${:,.2f}'.format
purchasing_analysis_summary = pd.DataFrame({"Number of Unique Items":unique_items,
                                    "Average Price":average_price,
                                    "Number of Purchases":total_purchases,
                                   "Total Revenue":total_revenue},index=[0])
purchasing_analysis_summary = purchasing_analysis_summary[["Number of Unique Items","Average Price","Number of Purchases","Total Revenue"]]
print("Purchasing Analysis (Total)")
purchasing_analysis_summary
```

    Purchasing Analysis (Total)
    




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
      <th>Number of Unique Items</th>
      <th>Average Price</th>
      <th>Number of Purchases</th>
      <th>Total Revenue</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>64</td>
      <td>$2.92</td>
      <td>78</td>
      <td>$228.10</td>
    </tr>
  </tbody>
</table>
</div>




```python
unique_players_df = purchase_df.drop_duplicates(subset="SN", keep='first')
gender_counts = unique_players_df["Gender"].value_counts()
player_percentage = (gender_counts / total_players)*100
gender_counts_df = pd.DataFrame(gender_counts)
gender_counts_df = gender_counts_df.rename(columns={"Gender": "Total Count"})
gender_counts_df["Percentage of Players"] = player_percentage.values
gender_counts_df = gender_counts_df[["Percentage of Players", "Total Count"]]
pd.options.display.float_format = '{:,.2f}'.format
print("Gender Demographics")
gender_counts_df
```

    Gender Demographics
    




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
      <th>Percentage of Players</th>
      <th>Total Count</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Male</th>
      <td>81.08</td>
      <td>60</td>
    </tr>
    <tr>
      <th>Female</th>
      <td>17.57</td>
      <td>13</td>
    </tr>
    <tr>
      <th>Other / Non-Disclosed</th>
      <td>1.35</td>
      <td>1</td>
    </tr>
  </tbody>
</table>
</div>




```python
gender_counts = purchase_df["Gender"].value_counts()
gender_counts_df = pd.DataFrame(gender_counts)
gender_counts_df = gender_counts_df.rename(columns={"Gender": "Purchase Count"})
gender_average_price = purchase_df.groupby("Gender").mean()
gender_average_price = gender_average_price[["Price"]]
gender_average_price = gender_average_price.rename(columns={"Price": "Average Purchase Price"})
merge1 = pd.concat([gender_average_price, gender_counts_df], axis=1)
gender_total_purchase = purchase_df.groupby("Gender").sum()
gender_total_purchase = gender_total_purchase[["Price"]]
gender_total_purchase = gender_total_purchase.rename(columns={"Price": "Total Purchase Value"})
merge2 = pd.concat([merge1, gender_total_purchase], axis=1)
merge2["Normalized Totals"] = merge2["Total Purchase Value"] / merge2["Purchase Count"]
gender_purchases = merge2[["Purchase Count", "Average Purchase Price", "Total Purchase Value", "Normalized Totals"]]
pd.options.display.float_format = '${:,.2f}'.format
print("Purchasing Analysis (Gender)")
gender_purchases
```

    Purchasing Analysis (Gender)
    




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
      <th>Purchase Count</th>
      <th>Average Purchase Price</th>
      <th>Total Purchase Value</th>
      <th>Normalized Totals</th>
    </tr>
    <tr>
      <th>Gender</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Female</th>
      <td>13</td>
      <td>$3.18</td>
      <td>$41.38</td>
      <td>$3.18</td>
    </tr>
    <tr>
      <th>Male</th>
      <td>64</td>
      <td>$2.88</td>
      <td>$184.60</td>
      <td>$2.88</td>
    </tr>
    <tr>
      <th>Other / Non-Disclosed</th>
      <td>1</td>
      <td>$2.12</td>
      <td>$2.12</td>
      <td>$2.12</td>
    </tr>
  </tbody>
</table>
</div>




```python
bins = [0, 9, 14, 19, 24, 29, 34, 39, 99]
group_names = ["<10", "10-14", "15-19", "20-24", "25-29", "30-34", "35-39", "40+"]
unique_players_df["Age Group"] = pd.cut(unique_players_df["Age"], bins, labels=group_names)
age_group = unique_players_df.groupby("Age Group").count()
age_group = age_group[["Age"]]
age_group = age_group.rename(columns={"Age": "Total Count"})
age_group["Percentage of Players"] = age_group["Total Count"] / total_players * 100
age_group = age_group[["Percentage of Players", "Total Count"]]
pd.options.display.float_format = '{:,.2f}'.format
print("Age Demographics")
age_group
```

    Age Demographics
    

    C:\Users\Victor\AppData\Local\conda\conda\envs\PythonData\lib\site-packages\ipykernel_launcher.py:3: SettingWithCopyWarning: 
    A value is trying to be set on a copy of a slice from a DataFrame.
    Try using .loc[row_indexer,col_indexer] = value instead
    
    See the caveats in the documentation: http://pandas.pydata.org/pandas-docs/stable/indexing.html#indexing-view-versus-copy
      This is separate from the ipykernel package so we can avoid doing imports until
    




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
      <th>Percentage of Players</th>
      <th>Total Count</th>
    </tr>
    <tr>
      <th>Age Group</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>&lt;10</th>
      <td>6.76</td>
      <td>5</td>
    </tr>
    <tr>
      <th>10-14</th>
      <td>4.05</td>
      <td>3</td>
    </tr>
    <tr>
      <th>15-19</th>
      <td>14.86</td>
      <td>11</td>
    </tr>
    <tr>
      <th>20-24</th>
      <td>45.95</td>
      <td>34</td>
    </tr>
    <tr>
      <th>25-29</th>
      <td>10.81</td>
      <td>8</td>
    </tr>
    <tr>
      <th>30-34</th>
      <td>8.11</td>
      <td>6</td>
    </tr>
    <tr>
      <th>35-39</th>
      <td>8.11</td>
      <td>6</td>
    </tr>
    <tr>
      <th>40+</th>
      <td>1.35</td>
      <td>1</td>
    </tr>
  </tbody>
</table>
</div>




```python
purchase_df["Age Group"] = pd.cut(purchase_df["Age"], bins, labels=group_names)
age_counts_df = purchase_df.groupby("Age Group").count()
age_counts_df = age_counts_df[["Age"]]
age_counts_df = age_counts_df.rename(columns={"Age": "Purchase Count"})
age_average_price = purchase_df.groupby("Age Group").mean()
age_average_price = age_average_price[["Price"]]
age_average_price = age_average_price.rename(columns={"Price": "Average Purchase Price"})
merge1 = pd.concat([age_average_price, age_counts_df], axis=1)
age_total_purchase = purchase_df.groupby("Age Group").sum()
age_total_purchase = age_total_purchase[["Price"]]
age_total_purchase = age_total_purchase.rename(columns={"Price": "Total Purchase Value"})
merge2 = pd.concat([merge1, age_total_purchase], axis=1)
merge2["Normalized Totals"] = merge2["Total Purchase Value"] / merge2["Purchase Count"]
age_purchases = merge2[["Purchase Count", "Average Purchase Price", "Total Purchase Value", "Normalized Totals"]]
pd.options.display.float_format = '${:,.2f}'.format
print("Purchasing Analysis (Age)")
age_purchases
```

    Purchasing Analysis (Age)
    




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
      <th>Purchase Count</th>
      <th>Average Purchase Price</th>
      <th>Total Purchase Value</th>
      <th>Normalized Totals</th>
    </tr>
    <tr>
      <th>Age Group</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>&lt;10</th>
      <td>5</td>
      <td>$2.76</td>
      <td>$13.82</td>
      <td>$2.76</td>
    </tr>
    <tr>
      <th>10-14</th>
      <td>3</td>
      <td>$2.99</td>
      <td>$8.96</td>
      <td>$2.99</td>
    </tr>
    <tr>
      <th>15-19</th>
      <td>11</td>
      <td>$2.76</td>
      <td>$30.41</td>
      <td>$2.76</td>
    </tr>
    <tr>
      <th>20-24</th>
      <td>36</td>
      <td>$3.02</td>
      <td>$108.89</td>
      <td>$3.02</td>
    </tr>
    <tr>
      <th>25-29</th>
      <td>9</td>
      <td>$2.90</td>
      <td>$26.11</td>
      <td>$2.90</td>
    </tr>
    <tr>
      <th>30-34</th>
      <td>7</td>
      <td>$1.98</td>
      <td>$13.89</td>
      <td>$1.98</td>
    </tr>
    <tr>
      <th>35-39</th>
      <td>6</td>
      <td>$3.56</td>
      <td>$21.37</td>
      <td>$3.56</td>
    </tr>
    <tr>
      <th>40+</th>
      <td>1</td>
      <td>$4.65</td>
      <td>$4.65</td>
      <td>$4.65</td>
    </tr>
  </tbody>
</table>
</div>




```python
top_five_spenders = players_df[["Price"]].sum().nlargest(5,"Price")
top_five_spenders = top_five_spenders.rename(columns={"Price": "Total Purchase Value"})
player_purchases = players_df[["Price"]].count()
player_purchases = player_purchases.rename(columns={"Price": "Purchase Count"})
merge1 = top_five_spenders.join(player_purchases)
average_player_purchase = players_df[["Price"]].mean()
average_player_purchase = average_player_purchase.rename(columns={"Price": "Average Purchase Price"})
merge2 = merge1.join(average_player_purchase)
top_five_spenders = merge2[["Purchase Count", "Average Purchase Price", "Total Purchase Value"]]
print("Top Spenders")
top_five_spenders
```

    Top Spenders
    




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
      <th>Purchase Count</th>
      <th>Average Purchase Price</th>
      <th>Total Purchase Value</th>
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
      <th>Sundaky74</th>
      <td>2</td>
      <td>$3.71</td>
      <td>$7.41</td>
    </tr>
    <tr>
      <th>Aidaira26</th>
      <td>2</td>
      <td>$2.56</td>
      <td>$5.13</td>
    </tr>
    <tr>
      <th>Eusty71</th>
      <td>1</td>
      <td>$4.81</td>
      <td>$4.81</td>
    </tr>
    <tr>
      <th>Chanirra64</th>
      <td>1</td>
      <td>$4.78</td>
      <td>$4.78</td>
    </tr>
    <tr>
      <th>Alarap40</th>
      <td>1</td>
      <td>$4.71</td>
      <td>$4.71</td>
    </tr>
  </tbody>
</table>
</div>




```python
top_five_items = items_df[["Price"]].count().nlargest(5,"Price")
top_five_items = top_five_items.rename(columns={"Price": "Purchase Count"})
item_prices = items_df[["Price"]].mean()
item_prices = item_prices.rename(columns={"Price": "Item Price"})
merge1 = top_five_items.join(item_prices)
item_purchase_total = items_df[["Price"]].sum()
item_purchase_total = item_purchase_total.rename(columns={"Price": "Total Purchase Value"})
top_five_items = merge1.join(item_purchase_total)
print("Most Popular Items")
top_five_items
```

    Most Popular Items
    




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
      <th>Purchase Count</th>
      <th>Item Price</th>
      <th>Total Purchase Value</th>
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
      <th>94</th>
      <th>Mourning Blade</th>
      <td>3</td>
      <td>$3.64</td>
      <td>$10.92</td>
    </tr>
    <tr>
      <th>60</th>
      <th>Wolf</th>
      <td>2</td>
      <td>$2.70</td>
      <td>$5.40</td>
    </tr>
    <tr>
      <th>64</th>
      <th>Fusion Pummel</th>
      <td>2</td>
      <td>$2.42</td>
      <td>$4.84</td>
    </tr>
    <tr>
      <th>90</th>
      <th>Betrayer</th>
      <td>2</td>
      <td>$4.12</td>
      <td>$8.24</td>
    </tr>
    <tr>
      <th>93</th>
      <th>Apocalyptic Battlescythe</th>
      <td>2</td>
      <td>$4.49</td>
      <td>$8.98</td>
    </tr>
  </tbody>
</table>
</div>




```python
top_five_item_sales = items_df[["Price"]].sum().nlargest(5,"Price")
top_five_item_sales = top_five_item_sales.rename(columns={"Price": "Total Purchase Value"})
top_five_item_sales
item_purchases = items_df[["Price"]].count()
item_purchases = item_purchases.rename(columns={"Price": "Purchase Count"})
merge1 = top_five_item_sales.join(item_purchases)
item_prices = items_df[["Price"]].mean()
item_prices = item_prices.rename(columns={"Price": "Item Price"})
merge2 = merge1.join(item_prices)
top_five_item_sales = merge2[["Purchase Count", "Item Price", "Total Purchase Value"]]
print("Most Profitable Items")
top_five_item_sales
```

    Most Profitable Items
    




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
      <th>Purchase Count</th>
      <th>Item Price</th>
      <th>Total Purchase Value</th>
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
      <th>94</th>
      <th>Mourning Blade</th>
      <td>3</td>
      <td>$3.64</td>
      <td>$10.92</td>
    </tr>
    <tr>
      <th>117</th>
      <th>Heartstriker, Legacy of the Light</th>
      <td>2</td>
      <td>$4.71</td>
      <td>$9.42</td>
    </tr>
    <tr>
      <th>93</th>
      <th>Apocalyptic Battlescythe</th>
      <td>2</td>
      <td>$4.49</td>
      <td>$8.98</td>
    </tr>
    <tr>
      <th>90</th>
      <th>Betrayer</th>
      <td>2</td>
      <td>$4.12</td>
      <td>$8.24</td>
    </tr>
    <tr>
      <th>154</th>
      <th>Feral Katana</th>
      <td>2</td>
      <td>$4.11</td>
      <td>$8.22</td>
    </tr>
  </tbody>
</table>
</div>


