
# Heroes of Pymoli Data Analysis 
## Melvin Garcia


```python
# Import Dependencies

import pandas as pd
import os
```


```python
# load data with purchase data 1
file = os.path.join('..', 'HeroesofPymoli', 'purchase_data.json')

df = pd.read_json(file)
```


```python
# Peek at data

df.head()
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
      <th>Age</th>
      <th>Gender</th>
      <th>Item ID</th>
      <th>Item Name</th>
      <th>Price</th>
      <th>SN</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>38</td>
      <td>Male</td>
      <td>165</td>
      <td>Bone Crushing Silver Skewer</td>
      <td>3.37</td>
      <td>Aelalis34</td>
    </tr>
    <tr>
      <th>1</th>
      <td>21</td>
      <td>Male</td>
      <td>119</td>
      <td>Stormbringer, Dark Blade of Ending Misery</td>
      <td>2.32</td>
      <td>Eolo46</td>
    </tr>
    <tr>
      <th>2</th>
      <td>34</td>
      <td>Male</td>
      <td>174</td>
      <td>Primitive Blade</td>
      <td>2.46</td>
      <td>Assastnya25</td>
    </tr>
    <tr>
      <th>3</th>
      <td>21</td>
      <td>Male</td>
      <td>92</td>
      <td>Final Critic</td>
      <td>1.36</td>
      <td>Pheusrical25</td>
    </tr>
    <tr>
      <th>4</th>
      <td>23</td>
      <td>Male</td>
      <td>63</td>
      <td>Stormfury Mace</td>
      <td>1.27</td>
      <td>Aela59</td>
    </tr>
  </tbody>
</table>
</div>



## Player Count


```python
player_count = len(df['SN'].unique())
total_players= {'Total Players': [player_count]}
total_players_df = pd.DataFrame(total_players)
total_players_df
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
      <td>573</td>
    </tr>
  </tbody>
</table>
</div>



## Purchasing Analysis (Total)


```python
unique_item_num = len(df['Item Name'].unique())
avg_purchase_price = round(df['Price'].mean(), 2)
total_num_purchase = len(df['Price'])
total_revenue = round(df['Price'].sum(), 2)

purchasing_dict = {'Number of Unique Items': [unique_item_num],
                'Average Price': ['$'+str(avg_purchase_price)],
                'Number of Purchases': [total_num_purchase],
                'Total Revenue': ['$'+str(total_revenue)]}

purchasing_df = pd.DataFrame(purchasing_dict)

purchasing_df
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
      <th>Average Price</th>
      <th>Number of Purchases</th>
      <th>Number of Unique Items</th>
      <th>Total Revenue</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>$2.93</td>
      <td>780</td>
      <td>179</td>
      <td>$2286.33</td>
    </tr>
  </tbody>
</table>
</div>



## Gender Demographics


```python
df_del_dup = df.drop_duplicates(['SN'], keep='last')

male_count = len(df_del_dup.loc[df_del_dup['Gender'] == 'Male'])
female_count = len(df_del_dup.loc[df_del_dup['Gender'] == 'Female'])
other_count = len(df_del_dup.loc[df_del_dup['Gender'] == 'Other / Non-Disclosed'])

total = male_count + female_count + other_count

gender_count = {'Male': [round((male_count/total)*100, 2), male_count],
                  'Female': [round((female_count/total)*100, 2), female_count],
                  'Other / Non-Disclosed' : [round((other_count/total)*100, 2), other_count]}

cols = ['Percentage of Players', 'Total Count']

gender_count_df = pd.DataFrame.from_items(gender_count.items(), 
                                          orient='index',
                                         columns = cols)

gender_count_df
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
      <th>Percentage of Players</th>
      <th>Total Count</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Male</th>
      <td>81.15</td>
      <td>465</td>
    </tr>
    <tr>
      <th>Female</th>
      <td>17.45</td>
      <td>100</td>
    </tr>
    <tr>
      <th>Other / Non-Disclosed</th>
      <td>1.40</td>
      <td>8</td>
    </tr>
  </tbody>
</table>
</div>



## Purchasing Analysis (Gender)


```python
# Set values

# Set male and female purchase count
gen_purchase_count = df.groupby(['Gender']).count()['Price']

# Set Average Purchase Price

gen_avg_purchase = round(df.groupby(['Gender']).mean()['Price'], 2)

# Set Total Purchase Value

gen_total_purchase = df.groupby(['Gender']).sum()['Price']

# Set normalized totals

gen_normalized = round(gen_total_purchase / gender_count_df['Total Count'], 2)

# Create df

purchase_gen_analysis = {'Purchase Count' : gen_purchase_count,
                           'Average Purchase Count' : gen_avg_purchase,
                           'Total Purchase Value': gen_total_purchase,
                           'Normalized Totals': gen_normalized}

purchase_gen_analysis_df = pd.DataFrame(purchase_gen_analysis)


# Formatting to $

purchase_gen_analysis_df['Average Purchase Count'] = purchase_gen_analysis_df['Average Purchase Count'].map("${:.2f}".format)

purchase_gen_analysis_df['Total Purchase Value'] = purchase_gen_analysis_df['Total Purchase Value'].map("${:.2f}".format)

purchase_gen_analysis_df['Normalized Totals'] = purchase_gen_analysis_df['Normalized Totals'].map("${:.2f}".format)


# Display data

purchase_gen_analysis_df
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
      <th>Average Purchase Count</th>
      <th>Normalized Totals</th>
      <th>Purchase Count</th>
      <th>Total Purchase Value</th>
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
      <td>$2.82</td>
      <td>$3.83</td>
      <td>136</td>
      <td>$382.91</td>
    </tr>
    <tr>
      <th>Male</th>
      <td>$2.95</td>
      <td>$4.02</td>
      <td>633</td>
      <td>$1867.68</td>
    </tr>
    <tr>
      <th>Other / Non-Disclosed</th>
      <td>$3.25</td>
      <td>$4.47</td>
      <td>11</td>
      <td>$35.74</td>
    </tr>
  </tbody>
</table>
</div>



## Age Demographics


```python
# Create bins to organize data

bins = [0, 9.9, 14.9, 19.9, 24.9, 29.9, 34.9, 39.9, 1000000000]

age_labels = ['<10', '10-14', '15-19', '20-24', '25-29', '30-34', '35-39', '40+']

# Organize data according to set bins and create df

count_per_age = pd.cut(df_del_dup['Age'], bins, labels=age_labels).value_counts().rename('Total Count')
count_age_df = pd.DataFrame(count_per_age)


percent_by_age = round(count_per_age/ count_per_age.sum()*100, 2).rename('Percentage of Players')
percent_age_df = pd.DataFrame(percent_by_age)

# Create df

age_demo_df = pd.concat([percent_age_df, count_age_df], axis=1).reindex(age_labels)

age_demo_df
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
      <th>Percentage of Players</th>
      <th>Total Count</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>&lt;10</th>
      <td>3.32</td>
      <td>19</td>
    </tr>
    <tr>
      <th>10-14</th>
      <td>4.01</td>
      <td>23</td>
    </tr>
    <tr>
      <th>15-19</th>
      <td>17.45</td>
      <td>100</td>
    </tr>
    <tr>
      <th>20-24</th>
      <td>45.20</td>
      <td>259</td>
    </tr>
    <tr>
      <th>25-29</th>
      <td>15.18</td>
      <td>87</td>
    </tr>
    <tr>
      <th>30-34</th>
      <td>8.20</td>
      <td>47</td>
    </tr>
    <tr>
      <th>35-39</th>
      <td>4.71</td>
      <td>27</td>
    </tr>
    <tr>
      <th>40+</th>
      <td>1.92</td>
      <td>11</td>
    </tr>
  </tbody>
</table>
</div>



## Purchasing Analysis (Age)


```python
# Filter main df by age groups
df.loc[(df['Age'] < 10), 'Age_label'] = '<10'

df.loc[(df['Age'] >= 10) & (df['Age'] <= 14), 'Age_label'] = '10-14'

df.loc[(df['Age'] >= 15) & (df['Age'] <= 19), 'Age_label'] = '15-19'

df.loc[(df['Age'] >= 20) & (df['Age'] <= 24), 'Age_label'] = '20-24'

df.loc[(df['Age'] >= 25) & (df['Age'] <= 29), 'Age_label'] = '25-29'

df.loc[(df['Age'] >= 30) & (df['Age'] <= 34), 'Age_label'] = '30-34'

df.loc[(df['Age'] >= 35) & (df['Age'] <= 39), 'Age_label'] = '35-39'

df.loc[(df['Age'] >= 40), 'Age_label'] = '40+'


# Set count per age group
age_purchase_count = df.groupby(['Age_label']).count()['SN']

# Set avg purchase per age group
age_avg_purchase = df.groupby(['Age_label'])['Price'].mean()

# Set total purchase per age group
age_total_purchase = df.groupby(['Age_label'])['Price'].sum().reindex(age_labels)

# Set normalized total purchase per age group
age_normalized = age_total_purchase / age_demo_df['Total Count']

# # Create dict to create df

purchase_age_analysis = {'Purchase Count' : age_purchase_count,
                           'Average Purchase Count' : age_avg_purchase,
                           'Total Purchase Value': age_total_purchase,
                           'Normalized Totals': age_normalized}

purchase_age_analysis_df = pd.DataFrame(purchase_age_analysis).reindex(age_labels)

# Formatting to $ 

purchase_age_analysis_df['Average Purchase Count'] = purchase_age_analysis_df['Average Purchase Count'].map("${:.2f}".format)

purchase_age_analysis_df['Total Purchase Value'] = purchase_age_analysis_df['Total Purchase Value'].map("${:.2f}".format)

purchase_age_analysis_df['Normalized Totals'] = purchase_age_analysis_df['Normalized Totals'].map("${:.2f}".format)


# Display data

purchase_age_analysis_df
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
      <th>Average Purchase Count</th>
      <th>Normalized Totals</th>
      <th>Purchase Count</th>
      <th>Total Purchase Value</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>&lt;10</th>
      <td>$2.98</td>
      <td>$4.39</td>
      <td>28</td>
      <td>$83.46</td>
    </tr>
    <tr>
      <th>10-14</th>
      <td>$2.77</td>
      <td>$4.22</td>
      <td>35</td>
      <td>$96.95</td>
    </tr>
    <tr>
      <th>15-19</th>
      <td>$2.91</td>
      <td>$3.86</td>
      <td>133</td>
      <td>$386.42</td>
    </tr>
    <tr>
      <th>20-24</th>
      <td>$2.91</td>
      <td>$3.78</td>
      <td>336</td>
      <td>$978.77</td>
    </tr>
    <tr>
      <th>25-29</th>
      <td>$2.96</td>
      <td>$4.26</td>
      <td>125</td>
      <td>$370.33</td>
    </tr>
    <tr>
      <th>30-34</th>
      <td>$3.08</td>
      <td>$4.20</td>
      <td>64</td>
      <td>$197.25</td>
    </tr>
    <tr>
      <th>35-39</th>
      <td>$2.84</td>
      <td>$4.42</td>
      <td>42</td>
      <td>$119.40</td>
    </tr>
    <tr>
      <th>40+</th>
      <td>$3.16</td>
      <td>$4.89</td>
      <td>17</td>
      <td>$53.75</td>
    </tr>
  </tbody>
</table>
</div>



## Top Spenders


```python
# Identify top 5 spenders

top5_spenders = df.groupby(['SN'])['Price'].sum().sort_values(ascending=False).head().index

top5_SN_df = df.loc[df['SN'].isin(top5_spenders)]

# Organize data

top5_count = top5_SN_df.groupby(['SN']).count()['Price']

top5_avg = top5_SN_df.groupby(['SN'])['Price'].mean()

top5_total = top5_SN_df.groupby(['SN'])['Price'].sum()

# Create dict to create df

purchase_top5_analysis = {'Purchase Count' : top5_count,
                           'Average Purchase Count' : top5_avg,
                           'Total Purchase Value': top5_total}

purchase_top5_analysis_df = pd.DataFrame(purchase_top5_analysis).sort_values(['Purchase Count'], ascending=False)

# Formatting with $ 
purchase_top5_analysis_df['Average Purchase Count'] = purchase_top5_analysis_df['Average Purchase Count'].map("${:.2f}".format)

purchase_top5_analysis_df['Total Purchase Value'] = purchase_top5_analysis_df['Total Purchase Value'].map("${:.2f}".format)

purchase_top5_analysis_df
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
      <th>Average Purchase Count</th>
      <th>Purchase Count</th>
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
      <th>Undirrala66</th>
      <td>$3.41</td>
      <td>5</td>
      <td>$17.06</td>
    </tr>
    <tr>
      <th>Mindimnya67</th>
      <td>$3.18</td>
      <td>4</td>
      <td>$12.74</td>
    </tr>
    <tr>
      <th>Saedue76</th>
      <td>$3.39</td>
      <td>4</td>
      <td>$13.56</td>
    </tr>
    <tr>
      <th>Eoda93</th>
      <td>$3.86</td>
      <td>3</td>
      <td>$11.58</td>
    </tr>
    <tr>
      <th>Haellysu29</th>
      <td>$4.24</td>
      <td>3</td>
      <td>$12.73</td>
    </tr>
  </tbody>
</table>
</div>



## Most Popular Items


```python
# Organize data

top_items = df['Item ID'].value_counts().head().index

top_items_df = df.loc[df['Item ID'].isin(top_items)]

top_items_count = top_items_df.groupby(['Item ID', 'Item Name']).count()['Price']

top_items_price = top_items_df.groupby(['Item ID', 'Item Name']).Price.unique().astype(float)

top_items_total = top_items_df.groupby(['Item ID', 'Item Name'])['Price'].sum()

# Create dict to create df

top_items_analysis = {'Purchase Count' : top_items_count,
                           'Item Price' : top_items_price,
                           'Total Purchase Value': top_items_total}

top_items_analysis_df = pd.DataFrame(top_items_analysis).sort_values(['Purchase Count'], ascending=False)

# Formatting to $

top_items_analysis_df['Item Price'] = top_items_analysis_df['Item Price'].map("${:.2f}".format)
top_items_analysis_df['Total Purchase Value'] = top_items_analysis_df['Total Purchase Value'].map("${:.2f}".format)

# Display data

top_items_analysis_df
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
      <th>Item Price</th>
      <th>Purchase Count</th>
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
      <th>39</th>
      <th>Betrayal, Whisper of Grieving Widows</th>
      <td>$2.35</td>
      <td>11</td>
      <td>$25.85</td>
    </tr>
    <tr>
      <th>84</th>
      <th>Arcane Gem</th>
      <td>$2.23</td>
      <td>11</td>
      <td>$24.53</td>
    </tr>
    <tr>
      <th>31</th>
      <th>Trickster</th>
      <td>$2.07</td>
      <td>9</td>
      <td>$18.63</td>
    </tr>
    <tr>
      <th>34</th>
      <th>Retribution Axe</th>
      <td>$4.14</td>
      <td>9</td>
      <td>$37.26</td>
    </tr>
    <tr>
      <th>175</th>
      <th>Woeful Adamantite Claymore</th>
      <td>$1.24</td>
      <td>9</td>
      <td>$11.16</td>
    </tr>
  </tbody>
</table>
</div>



## Most Profitable Items


```python
# Organize data

top5_profit = pd.DataFrame(df.groupby(['Item ID'])['Price'].sum()).sort_values('Price', ascending=False)

top5_profit = top5_profit.head().index

top5_profit_df = df.loc[df['Item ID'].isin(top5_profit)]

top5_profit_count = top5_profit_df.groupby(['Item ID', 'Item Name']).count()['Price']

top5_profit_price = top5_profit_df.groupby(['Item ID', 'Item Name']).Price.unique().astype(float)

top5_profit_total = top5_profit_df.groupby(['Item ID', 'Item Name'])['Price'].sum()

# Create dict to create df

top5_profit_analysis = {'Purchase Count' : top5_profit_count,
                           'Item Price' : top5_profit_price,
                           'Total Purchase Value': top5_profit_total}

top5_profit_analysis_df = pd.DataFrame(top5_profit_analysis).sort_values(['Purchase Count'], ascending=False)

# Formatting to $

top5_profit_analysis_df['Item Price'] = top5_profit_analysis_df['Item Price'].map("${:.2f}".format)
top5_profit_analysis_df['Total Purchase Value'] = top5_profit_analysis_df['Total Purchase Value'].map("${:.2f}".format)

# Display data

top5_profit_analysis_df
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
      <th>Item Price</th>
      <th>Purchase Count</th>
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
      <th>34</th>
      <th>Retribution Axe</th>
      <td>$4.14</td>
      <td>9</td>
      <td>$37.26</td>
    </tr>
    <tr>
      <th>107</th>
      <th>Splitter, Foe Of Subtlety</th>
      <td>$3.61</td>
      <td>8</td>
      <td>$28.88</td>
    </tr>
    <tr>
      <th>115</th>
      <th>Spectral Diamond Doomblade</th>
      <td>$4.25</td>
      <td>7</td>
      <td>$29.75</td>
    </tr>
    <tr>
      <th>32</th>
      <th>Orenmir</th>
      <td>$4.95</td>
      <td>6</td>
      <td>$29.70</td>
    </tr>
    <tr>
      <th>103</th>
      <th>Singed Scalpel</th>
      <td>$4.87</td>
      <td>6</td>
      <td>$29.22</td>
    </tr>
  </tbody>
</table>
</div>




```python
df.loc[df['SN'] == 'Undirrala66']
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
      <th>Age</th>
      <th>Gender</th>
      <th>Item ID</th>
      <th>Item Name</th>
      <th>Price</th>
      <th>SN</th>
      <th>Age_label</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>79</th>
      <td>29</td>
      <td>Male</td>
      <td>144</td>
      <td>Blood Infused Guardian</td>
      <td>2.86</td>
      <td>Undirrala66</td>
      <td>25-29</td>
    </tr>
    <tr>
      <th>107</th>
      <td>29</td>
      <td>Male</td>
      <td>115</td>
      <td>Spectral Diamond Doomblade</td>
      <td>4.25</td>
      <td>Undirrala66</td>
      <td>25-29</td>
    </tr>
    <tr>
      <th>131</th>
      <td>29</td>
      <td>Male</td>
      <td>62</td>
      <td>Piece Maker</td>
      <td>4.36</td>
      <td>Undirrala66</td>
      <td>25-29</td>
    </tr>
    <tr>
      <th>537</th>
      <td>29</td>
      <td>Male</td>
      <td>18</td>
      <td>Torchlight, Bond of Storms</td>
      <td>1.77</td>
      <td>Undirrala66</td>
      <td>25-29</td>
    </tr>
    <tr>
      <th>596</th>
      <td>29</td>
      <td>Male</td>
      <td>133</td>
      <td>Faith's Scimitar</td>
      <td>3.82</td>
      <td>Undirrala66</td>
      <td>25-29</td>
    </tr>
  </tbody>
</table>
</div>




```python

```

## Observable Trends
### - The age group that has the most players, 20-24, spends the least amongst all other age groups.
### - No player has spent more than 20 dollars on items.
### - The top spender, Undirrala66,  has only purchased 1 item that appears in either most popular and most profitable items


```python

```
