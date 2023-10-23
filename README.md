# Project_01_Melbourne_Real_Estate_Analysis
## Dependencies

- Python 3.x
- Pandas
- Matplotlib
- Plotly (for interactive graphs)
- numpy
- datatime
- pathlib
- seaborn
- hvplot.pandas
- os
- warnings
- datatime
  

## Describe the project's core message or hypothesis.
- Using python to clean and analysis data, generating geoview, correlation, bar chart, line chart and scatter to visual data in a better way 
- Understand what properties’ features impact the sales and price, understand sales pattern, customer preferences and evaluate property growth
- Studies price patterns and market trends using charts and other tools to forecast future price movements.


## Describe the questions the group found of interest, and why.
**Hasn't include Renee's part questions**

  - What properties’ features impact the sales and price?
  - How do house prices from the start of the observed period compare with those at the end? Is there a general increase, decrease, or stable trend
  - Based on this trend, which suburb might be a potential hotspot for real estate investment in the coming years?
  - How have the monthly sales volumes trended, indicating market activity and potential demand in Melbourne?
  - Over all the years, which suburbs have consistently seen the most sales activity, potentially indicating areas of sustained demand?
  - For the top-performing suburbs based on overall sales activity, how do the median property prices compare across the years, and what is the price distribution for each property type over all years?
    
 ## Summarize how, and where, the data was found to answer these questions.
 
- `Real_Estate_Project_Script.ipynb`: Main script to perform data analysis and generate visualizations.
- `Melbourne_housing_FULL.csv`: Sample property data in CSV format.
- The property data is sourced from https://www.kaggle.com/code/bichithkcherian/melbourne-housing-analysis/input
- The data source was originally extracted from www.domain.com.au
  
## Describe the data exploration and cleanup process (accompanied by Jupyter Notebook).

 - We found a set of data related to 2016-2018 Melbourne property raw data, which including 'Suburb', 'Address', 'Rooms', 'Type', 'Price', 'Method', 'SellerG','Date', 'Distance', 'Postcode', 'Bedroom2', 'Bathroom', 'Car','Landsize', 'BuildingArea', 'YearBuilt', 'CouncilArea', 'Lattitude','Longtitude', 'Regionname', 'Propertycount'
 - We used Percentage of NAs in the column with most NA by:

```python
(pty_df['BuildingArea'].isnull().sum()/pty_df['BuildingArea'].isnull().count())*100
```

Due to the building area and year built lack of information, we use drop function to tidy up:

```python
  {'pty_df.drop(columns=['BuildingArea', 'YearBuilt'],inplace=True)}
```

- after using the sort to understand the landsize, we found it was varied a lot, so we set up landsize range"

```python
{bins = [0, 300, 1000, 2000, pty_df['Landsize'].max()]
labels = ['<300', '300-1000', '1000-2000', '2000+']}```

# Use pd.cut to categorize the data
pty_df['Landsize_range'] = pd.cut(pty_df['Landsize'], bins=bins, labels=labels, include_lowest=True)
pty_df.sort_values("Price", ascending=False)
```

## Describe the analysis process (accompanied by Jupyter Notebook).
The analysis was split to three parts, geographic analysis, property analysis and investment analysis.
In geographic analysis:
We use region to breakdown the house(h), townhouse(t) and unit(u) to understand the property values.
```python
geo_pty_df=pd.DataFrame(pty_df.groupby(['Regionname','Type'])['Date'].count())
geo_pty_df.rename(columns={"Date": "Values"},inplace=True)
geo_pty_df
```

Furthermore, using the geoview to understand the hotzone:
```python
rg_pty_df=pty_df.groupby(['Regionname','Suburb']).agg({'Price':'mean', 'Date':'count','Landsize':'mean','Longtitude':'mean','Lattitude':'mean'}).rename(columns={'Date':'Count'})
rg_pty_df
```

For the property analysis, using below codes to pick the most popular choice:
```python
most_popular_room = pty_df['Rooms'].value_counts().idxmax()
print(f"The most popular room type is: {most_popular_room}")

most_popular_Landsize_range = pty_df['Landsize_range'].value_counts().idxmax()
print(f"The most popular landsize range is :{most_popular_Landsize_range}")

most_popular_suburb = pty_df['Suburb'].value_counts().idxmax()
print(f"The most popular suburb is :{most_popular_suburb}")
```
Additionally, using correlation to understanding how property features impact prices:
```python
correlation_matrix = pty_df[['Suburb', 'Distance', 'Rooms', 'Bathroom', 'Car', 'Landsize', 'Price']].corr()
plt.figure(figsize=(10, 8))
sns.heatmap(correlation_matrix, annot=True, cmap='coolwarm', fmt=".5f")
plt.title("Correlation Heatmap")
plt.show()
```
For the investment analysis, using below codes to pick the most frequent sales occured:
```python
# Identifying the top 5 suburbs with the most sales activity over all years
top_suburbs_all_years = top_suburbs.head(5).index.tolist()

# Extracting data for the top suburbs
data_top_suburbs_all_years = pty_df[pty_df['Suburb'].isin(top_suburbs_all_years)]
```

## Summarize the conclusions. 
  This should include a numerical summary (i.e., what data did the analysis produce), as well as visualizations of that summary (plots of the final analysis data).


## Discuss the implications of the findings (which shall take the form of an open-ended discussion about what the findings mean).
- Housing takes the majority of the properties sold (75%), followed by units (17%) and townhouses (7%).
- Metropolitan Areas have the largest trading volumes, started off with the Southern part, followed by the Northern part.
- Out of $19B transactions across 2016-2018, about 18k properties had been sold. 
- Southern Metropolitan takes up 42% of properties sold totaling about $8.1B.  
- Western Victoria’s suburbs have the highest concentration of property sold. First led by Melton South (62%), followed by Melton (30%).
- Majority of the regions seem to share the same correlation relationship.
  ↑Price ; ↑ # of Rooms  - Metropolitan Regions shares the highest correlation, followed by Victoria. Except Northern Metropolitan
  ↑Price; ↓ Distance from CBD - Close to -1.
  ↑Distance from ; ↑ Rooms ; ↑ Landsize. Except Norther Metropolitan.
- More rooms and bathroom means the price tends to be higher
- Distance closer to CBD, price to be more expensive
- a very weak positive correlation between price and landsize
- price pattern:
-   August 2016 to April 2017 - increase
    April 2017 - Oct 2017 - frustrated
    After Oct 2017 - relatively flat
- Unit - increased from Oct 17 while House and Townhouse decreased
- The most popular choice: 3 bedrooms
- The most popular land size: 300-1000 sqr meters
- The most popular suburb: Reservoir
- The housing market has seasonal trends with certain months seeing more activity.
- Starting from mid-2016 and extending to early 2017, there was a sharp increase in the number of sales, indicating heightened market activity. 
- After 2018, there appears to be a general decline in sales volume. 
- "Reservoir" stands out as having the highest sales activity across all years, indicating high property turnover and possibly high demand.
- "Bentleigh East" and "Preston" follow closely in terms of sales activity, whereas "Richmond" and "Brunswick" have slightly lower sales figures but still rank in the top 5.
- "Brighton" has the highest average purchase price among the top 5 suburbs
- "Richmond" and "Reservoir", despite their high sales activity, have relatively lower average prices
