# Scraping CIA Data [Tutorial]
> "A quick tutorial on scraping and cleaing CIA factbook data"
- comments: true
- categories: [Tutorial, jupyter, Pandas, BeautifulSoup, spy]
- image: images/cia.jpg

We are interested in the CIA factbook data which is [located here](https://www.cia.gov/the-world-factbook/) (Please note this may have changed since 2020), I have downloaded the report from [here](https://www.cia.gov/library/publications/download/) and step by step we will read the data, clean it and presented in structured manner


```python
import pandas as pd
import numpy as np
import statsmodels.api as sm 
import matplotlib.pyplot as plt
from bs4 import BeautifulSoup
from IPython import display
import os
import re
import seaborn as sns; sns.set()
%matplotlib inline
```

# Scraping CIA Factbook


```python
cat = 'CIA data/docs/notesanddefs.html'
page = open(cat).read()
page = BeautifulSoup(page)
data_map = {}
cols = page.select("div.category")
for col in cols:
    links = col.select('a')
    if len(links) > 0:
        fpath = links[0]['href']
        field = col.text.strip()
        data_map[field] =  fpath
        print(field, fpath)
```

    Administrative divisions ../fields/302.html
    Age structure ../fields/341.html
    Agriculture - products ../fields/215.html
    Airports ../fields/379.html
    Airports - with paved runways ../fields/380.html
    Airports - with unpaved runways ../fields/381.html
    Area ../fields/279.html
    Area - comparative ../fields/280.html
    Background ../fields/325.html
    Birth rate ../fields/345.html
    Broadband - fixed subscriptions ../fields/206.html
    Broadcast media ../fields/199.html
    Budget ../fields/224.html
    Budget surplus (+) or deficit (-) ../fields/226.html
    Capital ../fields/301.html
    Carbon dioxide emissions from consumption of energy ../fields/274.html
    Central bank discount rate ../fields/230.html
    Children under the age of 5 years underweight ../fields/368.html
    Citizenship ../fields/310.html
    Civil aircraft registration country code prefix ../fields/378.html
    Climate ../fields/284.html
    Coastline ../fields/282.html
    Commercial bank prime lending rate ../fields/231.html
    Communications - note ../fields/205.html
    Constitution ../fields/307.html
    Contraceptive prevalence rate ../fields/357.html
    Country name ../fields/296.html
    Crude oil - exports ../fields/262.html
    Crude oil - imports ../fields/263.html
    Crude oil - production ../fields/261.html
    Crude oil - proved reserves ../fields/264.html
    Current account balance ../fields/238.html
    Death rate ../fields/346.html
    Debt - external ../fields/246.html
    Demographic profile ../fields/340.html
    Dependency ratios ../fields/342.html
    Dependency status ../fields/298.html
    Dependent areas ../fields/304.html
    Diplomatic representation from the US ../fields/319.html
    Diplomatic representation in the US ../fields/318.html
    Disputes - international ../fields/326.html
    Distribution of family income - Gini index ../fields/223.html
    Drinking water source ../fields/361.html
    Economy - overview ../fields/207.html
    Education expenditures ../fields/369.html
    Electricity - consumption ../fields/253.html
    Electricity - exports ../fields/254.html
    Electricity - from fossil fuels ../fields/257.html
    Electricity - from hydroelectric plants ../fields/259.html
    Electricity - from nuclear fuels ../fields/258.html
    Electricity - from other renewable sources ../fields/260.html
    Electricity - imports ../fields/255.html
    Electricity - installed generating capacity ../fields/256.html
    Electricity - production ../fields/252.html
    Electricity access ../fields/251.html
    Elevation ../fields/286.html
    Environment - current issues ../fields/293.html
    Environment - international agreements ../fields/294.html
    Ethnic groups ../fields/400.html
    Exchange rates ../fields/249.html
    Executive branch ../fields/312.html
    Exports ../fields/239.html
    Exports - commodities ../fields/240.html
    Exports - partners ../fields/241.html
    Fiscal year ../fields/228.html
    Flag description ../fields/320.html
    GDP (official exchange rate) ../fields/209.html
    GDP (purchasing power parity) ../fields/208.html
    GDP - composition, by end use ../fields/213.html
    GDP - composition, by sector of origin ../fields/214.html
    GDP - per capita (PPP) ../fields/211.html
    GDP - real growth rate ../fields/210.html
    Geographic coordinates ../fields/277.html
    Geography - note ../fields/295.html
    Government - note ../fields/323.html
    Government type ../fields/299.html
    Gross national saving ../fields/212.html
    Health expenditures ../fields/358.html
    Heliports ../fields/382.html
    HIV/AIDS - adult prevalence rate ../fields/363.html
    HIV/AIDS - deaths ../fields/365.html
    HIV/AIDS - people living with HIV/AIDS ../fields/364.html
    Hospital bed density ../fields/360.html
    Household income or consumption by percentage share ../fields/222.html
    Illicit drugs ../fields/329.html
    Imports ../fields/242.html
    Imports - commodities ../fields/243.html
    Imports - partners ../fields/403.html
    Independence ../fields/305.html
    Industrial production growth rate ../fields/217.html
    Industries ../fields/216.html
    Infant mortality rate ../fields/354.html
    Inflation rate (consumer prices) ../fields/229.html
    International law organization participation ../fields/309.html
    International organization participation ../fields/317.html
    Internet country code ../fields/202.html
    Internet users ../fields/204.html
    Irrigated land ../fields/289.html
    Judicial branch ../fields/314.html
    Labor force ../fields/218.html
    Labor force - by occupation ../fields/219.html
    Land boundaries ../fields/281.html
    Land use ../fields/288.html
    Languages ../fields/402.html
    Legal system ../fields/308.html
    Legislative branch ../fields/313.html
    Life expectancy at birth ../fields/355.html
    Literacy ../fields/370.html
    Location ../fields/276.html
    Major infectious diseases ../fields/366.html
    Major urban areas - population ../fields/350.html
    Map references ../fields/278.html
    Maritime claims ../fields/283.html
    Maritime threats ../fields/334.html
    Market value of publicly traded shares ../fields/237.html
    Maternal mortality rate ../fields/353.html
    Median age ../fields/343.html
    Merchant marine ../fields/387.html
    Military - note ../fields/332.html
    Military branches ../fields/331.html
    Military expenditures ../fields/330.html
    Military service age and obligation ../fields/333.html
    Mother's mean age at first birth ../fields/352.html
    National air transport system ../fields/377.html
    National anthem ../fields/322.html
    National holiday ../fields/306.html
    National symbol(s) ../fields/321.html
    Nationality ../fields/336.html
    Natural gas - consumption ../fields/270.html
    Natural gas - exports ../fields/271.html
    Natural gas - imports ../fields/272.html
    Natural gas - production ../fields/269.html
    Natural gas - proved reserves ../fields/273.html
    Natural hazards ../fields/292.html
    Natural resources ../fields/287.html
    Net migration rate ../fields/347.html
    Obesity - adult prevalence rate ../fields/367.html
    People - note ../fields/374.html
    Physicians density ../fields/359.html
    Pipelines ../fields/383.html
    Political parties and leaders ../fields/315.html
    Population ../fields/335.html
    Population below poverty line ../fields/221.html
    Population distribution ../fields/348.html
    Population growth rate ../fields/344.html
    Ports and terminals ../fields/388.html
    Public debt ../fields/227.html
    Railways ../fields/384.html
    Refined petroleum products - consumption ../fields/266.html
    Refined petroleum products - exports ../fields/267.html
    Refined petroleum products - imports ../fields/268.html
    Refined petroleum products - production ../fields/265.html
    Refugees and internally displaced persons ../fields/327.html
    Religions ../fields/401.html
    Reserves of foreign exchange and gold ../fields/245.html
    Roadways ../fields/385.html
    Sanitation facility access ../fields/398.html
    School life expectancy (primary to tertiary education) ../fields/371.html
    Sex ratio ../fields/351.html
    Stock of broad money ../fields/235.html
    Stock of direct foreign investment - abroad ../fields/248.html
    Stock of direct foreign investment - at home ../fields/247.html
    Stock of domestic credit ../fields/236.html
    Stock of narrow money ../fields/234.html
    Suffrage ../fields/311.html
    Taxes and other revenues ../fields/225.html
    Telephone system ../fields/198.html
    Telephones - fixed lines ../fields/196.html
    Telephones - mobile cellular ../fields/197.html
    Terrain ../fields/285.html
    Terrorist groups - foreign based ../fields/396.html
    Terrorist groups - home based ../fields/397.html
    Total fertility rate ../fields/356.html
    Trafficking in persons ../fields/328.html
    Transportation - note ../fields/389.html
    Unemployment rate ../fields/220.html
    Unemployment, youth ages 15-24 ../fields/373.html
    Urbanization ../fields/349.html
    Waterways ../fields/386.html
    

So these are all the fields in the report, we will try now to map each field to its data while also keeping track of the country 


```python
def map_data(data_map):
    data = {}
    for field in data_map:
        page = open('CIA data/fields/' + data_map[field].split('/')[-1]).read()
        page_field = BeautifulSoup(page)
        cols = page_field.select('td')
        for i in range(len(cols)):
            if i % 2 == 0:
                country = cols[i].select('a')[0].text
            else:
                value = cols[i].select('div.category_data')
                if field not in data:
                    data[field] = [(country, [x.text for x in value])]
                else:
                    data[field].append((country, [x.text for x in value]))
                    
    return data
data = map_data(data_map)       
```

# Demographics and Constructing a data Frame

We will start first with the demographics and answer general questions about each country, below are the columns of interest


```python
Columns = ['Age structure', 'Area','Budget', 'Birth rate', 'Death rate', 'Debt - external', 'GDP (official exchange rate)',
          'GDP - per capita (PPP)', 'Hospital bed density','Physicians density', 'Life expectancy at birth',
           'Household income or consumption by percentage share', 'Population', 'Population below poverty line',
           'Religions', 'Unemployment rate', 'Median age']
```


```python
from functools import reduce
dfList = []
for field in Columns:
    df = pd.DataFrame({'Country': [key[0] for key in data[field]], 
                       field: [ key[1] for key in data[field]]})
    dfList.append(df)
    

df = reduce(lambda x, y: pd.merge(x, y, how='outer', on = 'Country'), dfList)
```


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
      <th>Country</th>
      <th>Age structure</th>
      <th>Area</th>
      <th>Budget</th>
      <th>Birth rate</th>
      <th>Death rate</th>
      <th>Debt - external</th>
      <th>GDP (official exchange rate)</th>
      <th>GDP - per capita (PPP)</th>
      <th>Hospital bed density</th>
      <th>Physicians density</th>
      <th>Life expectancy at birth</th>
      <th>Household income or consumption by percentage share</th>
      <th>Population</th>
      <th>Population below poverty line</th>
      <th>Religions</th>
      <th>Unemployment rate</th>
      <th>Median age</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>Afghanistan</td>
      <td>[\n0-14 years:\n40.92%\n(male 7,263,716 /femal...</td>
      <td>[\ntotal:\n652,230 sq km\n\n, \nland:\n652,230...</td>
      <td>[\nrevenues:\n2.276 billion\n\n(2017 est.)\n, ...</td>
      <td>[\n37.5 births/1,000 population\n\n(2018 est.)\n]</td>
      <td>[\n13.2 deaths/1,000 population\n\n(2018 est.)\n]</td>
      <td>[\n$2.84 billion\n\n(FY/)\n]</td>
      <td>[\n$20.24 billion\n(2017 est.)\n(2017 est.)\n]</td>
      <td>[\n$2,000\n\n(2017 est.)\n, \n$2,000\n\n(2016 ...</td>
      <td>[\n0.5 beds/1,000 population\n\n(2014)\n]</td>
      <td>[\n0.3 physicians/1,000 population\n\n(2016)\n]</td>
      <td>[\ntotal population:\n52.1 years\n\n(2018 est....</td>
      <td>[\nlowest 10%:\n3.8%\n\n(2008)\n, \nhighest 10...</td>
      <td>[\n34,940,837\n\n(July 2018 est.)\n]</td>
      <td>[\n54.5%\n\n(2017 est.)\n]</td>
      <td>[\n          \n        Muslim 99.7% (Sunni 84....</td>
      <td>[\n23.9%\n\n(2017 est.)\n, \n22.6%\n\n(2016 es...</td>
      <td>[\ntotal:\n19 years\n\n, \nmale:\n19 years\n\n...</td>
    </tr>
    <tr>
      <td>1</td>
      <td>Albania</td>
      <td>[\n0-14 years:\n17.84%\n(male 287,750 /female ...</td>
      <td>[\ntotal:\n28,748 sq km\n\n, \nland:\n27,398 s...</td>
      <td>[\nrevenues:\n3.614 billion\n\n(2017 est.)\n, ...</td>
      <td>[\n13.2 births/1,000 population\n\n(2018 est.)\n]</td>
      <td>[\n6.9 deaths/1,000 population\n\n(2018 est.)\n]</td>
      <td>[\n$9.505 billion\n\n(31 December 2017 est.)\n...</td>
      <td>[\n$13.07 billion\n(2017 est.)\n(2017 est.)\n]</td>
      <td>[\n$12,500\n\n(2017 est.)\n, \n$12,100\n\n(201...</td>
      <td>[\n2.9 beds/1,000 population\n\n(2013)\n]</td>
      <td>[\n1.29 physicians/1,000 population\n\n(2013)\n]</td>
      <td>[\ntotal population:\n78.6 years\n\n(2018 est....</td>
      <td>[\nlowest 10%:\n19.6%\n\n(2015 est.)\n, \nhigh...</td>
      <td>[\n3,057,220\n\n(July 2018 est.)\n]</td>
      <td>[\n14.3%\n\n(2012 est.)\n]</td>
      <td>[\n          \n        Muslim 56.7%, Roman Cat...</td>
      <td>[\n13.8%\n\n(2017 est.)\n, \n15.2%\n\n(2016 es...</td>
      <td>[\ntotal:\n33.4 years\n\n, \nmale:\n32 years\n...</td>
    </tr>
    <tr>
      <td>2</td>
      <td>Algeria</td>
      <td>[\n0-14 years:\n29.49%\n(male 6,290,619 /femal...</td>
      <td>[\ntotal:\n2,381,740 sq km\n\n, \nland:\n2,381...</td>
      <td>[\nrevenues:\n54.15 billion\n\n(2017 est.)\n, ...</td>
      <td>[\n21.5 births/1,000 population\n\n(2018 est.)\n]</td>
      <td>[\n4.3 deaths/1,000 population\n\n(2018 est.)\n]</td>
      <td>[\n$6.26 billion\n\n(31 December 2017 est.)\n,...</td>
      <td>[\n$167.6 billion\n(2017 est.)\n(2017 est.)\n]</td>
      <td>[\n$15,200\n\n(2017 est.)\n, \n$15,200\n\n(201...</td>
      <td>[\n1.9 beds/1,000 population\n\n(2015)\n]</td>
      <td>NaN</td>
      <td>[\ntotal population:\n77.2 years\n\n(2018 est....</td>
      <td>[\nlowest 10%:\n26.8%\n\n(1995)\n, \nhighest 1...</td>
      <td>[\n41,657,488\n\n(July 2018 est.)\n]</td>
      <td>[\n23%\n\n(2006 est.)\n]</td>
      <td>[\n          \n        Muslim (official; predo...</td>
      <td>[\n11.7%\n\n(2017 est.)\n, \n10.5%\n\n(2016 es...</td>
      <td>[\ntotal:\n28.3 years\n\n, \nmale:\n28 years\n...</td>
    </tr>
    <tr>
      <td>3</td>
      <td>American Samoa</td>
      <td>[\n0-14 years:\n29.59%\n(male 7,732 /female 7,...</td>
      <td>[\ntotal:\n224 sq km\n\n, \nland:\n224 sq km\n...</td>
      <td>[\nrevenues:\n249 million\n\n(2016 est.)\n, \n...</td>
      <td>[\n19 births/1,000 population\n\n(2018 est.)\n]</td>
      <td>[\n5.9 deaths/1,000 population\n\n(2018 est.)\n]</td>
      <td>[\nNA\n]</td>
      <td>[\n$658 million\n(2016 est.)\n(2016 est.)\n]</td>
      <td>[\n$11,200\n\n(2016 est.)\n, \n$11,300\n\n(201...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>[\ntotal population:\n73.9 years\n\n(2018 est....</td>
      <td>[\nlowest 10%:\nNA\n, \nhighest 10%:\nNA\n]</td>
      <td>[\n50,826\n\n(July 2018 est.)\n]</td>
      <td>[\nNA\n]</td>
      <td>[\n          \n        Christian 98.3%, other ...</td>
      <td>[\n29.8%\n\n(2005)\n]</td>
      <td>[\ntotal:\n26.1 years\n\n, \nmale:\n25.6 years...</td>
    </tr>
    <tr>
      <td>4</td>
      <td>Andorra</td>
      <td>[\n0-14 years:\n14.06%\n(male 6,197 /female 5,...</td>
      <td>[\ntotal:\n468 sq km\n\n, \nland:\n468 sq km\n...</td>
      <td>[\nrevenues:\n1.872 billion\n\n(2016)\n, \nexp...</td>
      <td>[\n7.3 births/1,000 population\n\n(2018 est.)\n]</td>
      <td>[\n7.4 deaths/1,000 population\n\n(2018 est.)\n]</td>
      <td>[\n$0\n\n(2016)\n]</td>
      <td>[\n$2.712 billion\n(2016 est.)\n(2016 est.)\n]</td>
      <td>[\n$49,900\n\n(2015 est.)\n, \n$51,300\n\n(201...</td>
      <td>[\n2.5 beds/1,000 population\n\n(2009)\n]</td>
      <td>[\n3.69 physicians/1,000 population\n\n(2015)\n]</td>
      <td>[\ntotal population:\n82.9 years\n\n(2018 est....</td>
      <td>[\nlowest 10%:\nNA\n, \nhighest 10%:\nNA\n]</td>
      <td>[\n85,708\n\n(July 2018 est.)\n]</td>
      <td>NaN</td>
      <td>[\n          \n        Roman Catholic  (predom...</td>
      <td>[\n3.7%\n\n(2016 est.)\n, \n4.1%\n\n(2015 est....</td>
      <td>[\ntotal:\n44.9 years\n\n, \nmale:\n45.1 years...</td>
    </tr>
  </tbody>
</table>
</div>



## Age Strucuture

as we can see, we managed to place data in a dataframe, however, it is not quite readable and contains descriptions and a lot of redunduncy that we can remove. We are dividing our data into 4 main tables:
   - Age structure
   - Median age
   - Demographics
   - Economics
    
We will alo rename our columns and structure our data such that its easy and ready for analysis

The `apply` method applies a transformation along an axis specified, in my opinion it's one of the most important methods in the pandas library and as you will see, we wil be using it almost in every block of code in order to clean or transform our data.


```python
Age_groups = ['0-14', '15-24', '25-54', '55-64', '65+']
for i, age in enumerate(Age_groups):    
    df[age+'_male'] = df['Age structure'].apply(lambda l: re.search('male \d*,?\d*,?\d*', str(l[i])) 
                                                                    if isinstance(l, list) else float('Nan'))
    df[age+'_female'] = df['Age structure'].apply(lambda l: re.search('female \d*,?\d*,?\d*', str(l[i])) 
                                                                    if isinstance(l, list) else float('Nan'))
    
# cleaning up and turning into some readable numbers
def get_number(reg):
    if isinstance(reg, re.Match):
        return float(reg.string[reg.start(): reg.end()].split(' ')[-1].replace(',', ''))
    
    else:
        return float('nan')
```


```python
age_columns = []
for i, age in enumerate(Age_groups):  
    df[age+'_male'] = df[age+'_male'].apply(get_number)
    df[age+'_female'] = df[age+'_female'].apply(get_number)
    df[age] = df[age+'_male'] + df[age+'_female']
    age_columns += [age+'_male', age+'_female']
```


```python
Frame = pd.melt(df, id_vars=['Country'] , value_vars=age_columns,
        var_name='Category', value_name='Population')
```


```python
Frame.head()
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
      <th>Country</th>
      <th>Category</th>
      <th>Population</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>Afghanistan</td>
      <td>0-14_male</td>
      <td>7263716.0</td>
    </tr>
    <tr>
      <td>1</td>
      <td>Albania</td>
      <td>0-14_male</td>
      <td>287750.0</td>
    </tr>
    <tr>
      <td>2</td>
      <td>Algeria</td>
      <td>0-14_male</td>
      <td>6290619.0</td>
    </tr>
    <tr>
      <td>3</td>
      <td>American Samoa</td>
      <td>0-14_male</td>
      <td>7732.0</td>
    </tr>
    <tr>
      <td>4</td>
      <td>Andorra</td>
      <td>0-14_male</td>
      <td>6197.0</td>
    </tr>
  </tbody>
</table>
</div>



df


```python
Frame[['Category', 'Sex']] = Frame['Category'].str.split('_', expand=True)
Frame.head()
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
      <th>Country</th>
      <th>Category</th>
      <th>Population</th>
      <th>Sex</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>Afghanistan</td>
      <td>0-14</td>
      <td>7263716.0</td>
      <td>male</td>
    </tr>
    <tr>
      <td>1</td>
      <td>Albania</td>
      <td>0-14</td>
      <td>287750.0</td>
      <td>male</td>
    </tr>
    <tr>
      <td>2</td>
      <td>Algeria</td>
      <td>0-14</td>
      <td>6290619.0</td>
      <td>male</td>
    </tr>
    <tr>
      <td>3</td>
      <td>American Samoa</td>
      <td>0-14</td>
      <td>7732.0</td>
      <td>male</td>
    </tr>
    <tr>
      <td>4</td>
      <td>Andorra</td>
      <td>0-14</td>
      <td>6197.0</td>
      <td>male</td>
    </tr>
  </tbody>
</table>
</div>




```python
len(df) == len(Frame)/5/2 # 5 age cateories and 2 sex cat
```




    True




```python
## median age:
median_df = df.copy()[['Country', 'Median age']]
median_df.head()
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
      <th>Country</th>
      <th>Median age</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>Afghanistan</td>
      <td>[\ntotal:\n19 years\n\n, \nmale:\n19 years\n\n...</td>
    </tr>
    <tr>
      <td>1</td>
      <td>Albania</td>
      <td>[\ntotal:\n33.4 years\n\n, \nmale:\n32 years\n...</td>
    </tr>
    <tr>
      <td>2</td>
      <td>Algeria</td>
      <td>[\ntotal:\n28.3 years\n\n, \nmale:\n28 years\n...</td>
    </tr>
    <tr>
      <td>3</td>
      <td>American Samoa</td>
      <td>[\ntotal:\n26.1 years\n\n, \nmale:\n25.6 years...</td>
    </tr>
    <tr>
      <td>4</td>
      <td>Andorra</td>
      <td>[\ntotal:\n44.9 years\n\n, \nmale:\n45.1 years...</td>
    </tr>
  </tbody>
</table>
</div>




```python
median_df['median_age'] = median_df['Median age'].apply(lambda t: float(t[0].split('\n')[2].split()[0])
                                                    if isinstance(t, list) else float('nan'))

median_df['median_male'] = median_df['Median age'].apply(lambda t: float(t[1].split('\n')[2].split()[0])
                                                    if isinstance(t, list) else float('nan'))

median_df['median_female'] = median_df['Median age'].apply(lambda t: float(t[2].split('\n')[2].split()[0])
                                                    if isinstance(t, list) else float('nan'))
```


```python
median_df.drop('Median age', axis=1, inplace=True)
```


```python
median_df.head()
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
      <th>Country</th>
      <th>median</th>
      <th>median_male</th>
      <th>median_female</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>Afghanistan</td>
      <td>19.0</td>
      <td>19.0</td>
      <td>19.1</td>
    </tr>
    <tr>
      <td>1</td>
      <td>Albania</td>
      <td>33.4</td>
      <td>32.0</td>
      <td>34.7</td>
    </tr>
    <tr>
      <td>2</td>
      <td>Algeria</td>
      <td>28.3</td>
      <td>28.0</td>
      <td>28.7</td>
    </tr>
    <tr>
      <td>3</td>
      <td>American Samoa</td>
      <td>26.1</td>
      <td>25.6</td>
      <td>26.5</td>
    </tr>
    <tr>
      <td>4</td>
      <td>Andorra</td>
      <td>44.9</td>
      <td>45.1</td>
      <td>44.8</td>
    </tr>
  </tbody>
</table>
</div>



## More Demographics by Country


```python
demo = ['Area','Birth rate', 'Death rate', 'Hospital bed density','Physicians density', 
        'Life expectancy at birth','Population below poverty line','Unemployment rate']
```


```python
for c in demo:
    df[c] = df[c].apply(lambda l: l[0].replace('\n', '') if isinstance(l , list) else float('nan'))
```


```python
demo_df = df.copy()[demo]
demo_df.head()
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
      <th>Area</th>
      <th>Birth rate</th>
      <th>Death rate</th>
      <th>Hospital bed density</th>
      <th>Physicians density</th>
      <th>Life expectancy at birth</th>
      <th>Population below poverty line</th>
      <th>Unemployment rate</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>total:652,230 sq km</td>
      <td>37.5 births/1,000 population(2018 est.)</td>
      <td>13.2 deaths/1,000 population(2018 est.)</td>
      <td>0.5 beds/1,000 population(2014)</td>
      <td>0.3 physicians/1,000 population(2016)</td>
      <td>total population:52.1 years(2018 est.)</td>
      <td>54.5%(2017 est.)</td>
      <td>23.9%(2017 est.)</td>
    </tr>
    <tr>
      <td>1</td>
      <td>total:28,748 sq km</td>
      <td>13.2 births/1,000 population(2018 est.)</td>
      <td>6.9 deaths/1,000 population(2018 est.)</td>
      <td>2.9 beds/1,000 population(2013)</td>
      <td>1.29 physicians/1,000 population(2013)</td>
      <td>total population:78.6 years(2018 est.)</td>
      <td>14.3%(2012 est.)</td>
      <td>13.8%(2017 est.)</td>
    </tr>
    <tr>
      <td>2</td>
      <td>total:2,381,740 sq km</td>
      <td>21.5 births/1,000 population(2018 est.)</td>
      <td>4.3 deaths/1,000 population(2018 est.)</td>
      <td>1.9 beds/1,000 population(2015)</td>
      <td>NaN</td>
      <td>total population:77.2 years(2018 est.)</td>
      <td>23%(2006 est.)</td>
      <td>11.7%(2017 est.)</td>
    </tr>
    <tr>
      <td>3</td>
      <td>total:224 sq km</td>
      <td>19 births/1,000 population(2018 est.)</td>
      <td>5.9 deaths/1,000 population(2018 est.)</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>total population:73.9 years(2018 est.)</td>
      <td>NA</td>
      <td>29.8%(2005)</td>
    </tr>
    <tr>
      <td>4</td>
      <td>total:468 sq km</td>
      <td>7.3 births/1,000 population(2018 est.)</td>
      <td>7.4 deaths/1,000 population(2018 est.)</td>
      <td>2.5 beds/1,000 population(2009)</td>
      <td>3.69 physicians/1,000 population(2015)</td>
      <td>total population:82.9 years(2018 est.)</td>
      <td>NaN</td>
      <td>3.7%(2016 est.)</td>
    </tr>
  </tbody>
</table>
</div>



### Area (km^2) & Life expectancy at birth


```python
area = demo_df['Area'].apply(lambda x: (x.split(':')[1].split(' ')[0]).replace(',',''))
area.at[243] = 439781 #French Southern and Antarctic Lands'
demo_df['Area'] = area.astype(float)
```


```python
def get_exp(c):
    try:
        return float((c.split(':')[1].split(' ')[0]).replace(',',''))
    except:
        return float('nan')
        
life_exp = df['Life expectancy at birth'].apply(get_exp)
demo_df['Life expectancy at birth'] = life_exp.astype(float)
```


```python
demo_df
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
      <th>Area</th>
      <th>Birth rate</th>
      <th>Death rate</th>
      <th>Hospital bed density</th>
      <th>Physicians density</th>
      <th>Life expectancy at birth</th>
      <th>Population below poverty line</th>
      <th>Unemployment rate</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>652230.00</td>
      <td>37.5 births/1,000 population(2018 est.)</td>
      <td>13.2 deaths/1,000 population(2018 est.)</td>
      <td>0.5 beds/1,000 population(2014)</td>
      <td>0.3 physicians/1,000 population(2016)</td>
      <td>52.1</td>
      <td>54.5%(2017 est.)</td>
      <td>23.9%(2017 est.)</td>
    </tr>
    <tr>
      <td>1</td>
      <td>28748.00</td>
      <td>13.2 births/1,000 population(2018 est.)</td>
      <td>6.9 deaths/1,000 population(2018 est.)</td>
      <td>2.9 beds/1,000 population(2013)</td>
      <td>1.29 physicians/1,000 population(2013)</td>
      <td>78.6</td>
      <td>14.3%(2012 est.)</td>
      <td>13.8%(2017 est.)</td>
    </tr>
    <tr>
      <td>2</td>
      <td>2381740.00</td>
      <td>21.5 births/1,000 population(2018 est.)</td>
      <td>4.3 deaths/1,000 population(2018 est.)</td>
      <td>1.9 beds/1,000 population(2015)</td>
      <td>NaN</td>
      <td>77.2</td>
      <td>23%(2006 est.)</td>
      <td>11.7%(2017 est.)</td>
    </tr>
    <tr>
      <td>3</td>
      <td>224.00</td>
      <td>19 births/1,000 population(2018 est.)</td>
      <td>5.9 deaths/1,000 population(2018 est.)</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>73.9</td>
      <td>NA</td>
      <td>29.8%(2005)</td>
    </tr>
    <tr>
      <td>4</td>
      <td>468.00</td>
      <td>7.3 births/1,000 population(2018 est.)</td>
      <td>7.4 deaths/1,000 population(2018 est.)</td>
      <td>2.5 beds/1,000 population(2009)</td>
      <td>3.69 physicians/1,000 population(2015)</td>
      <td>82.9</td>
      <td>NaN</td>
      <td>3.7%(2016 est.)</td>
    </tr>
    <tr>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <td>262</td>
      <td>5.00</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <td>263</td>
      <td>62045.00</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <td>264</td>
      <td>12.00</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>2.72 physicians/1,000 population(2010)</td>
      <td>NaN</td>
      <td>NA</td>
      <td>NA</td>
    </tr>
    <tr>
      <td>265</td>
      <td>6959.41</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <td>266</td>
      <td>6.50</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
<p>267 rows × 8 columns</p>
</div>




```python

```

### Birth Rate, Death rate, Physicians density, Hospital bed density, Population below poverty line and Unemployment rate


```python
section1 = ['Birth rate', 'Death rate', 'Hospital bed density','Physicians density']
```


```python
for s in section1:
    demo_df[s] = demo_df[s].map(lambda x: float(x.split(' ')[0]) if isinstance(x, str) else float('nan'))
```


```python
section2 = ['Population below poverty line', 'Unemployment rate']
def clean_perc(c):
    try:
        return float(c.split('%')[0])
    except:
        return float('nan')
        
for s in section2:
    demo_df[s] = demo_df[s].apply(clean_perc)
```


```python
demo_df.index = df['Country']
```

## GDP and Budget by Country


```python
selection = ['Country', 'Budget', 'Debt - external', 'GDP (official exchange rate)', 'GDP - per capita (PPP)']
eco_df = df.copy()[selection]
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
      <th>Country</th>
      <th>Budget</th>
      <th>Debt - external</th>
      <th>GDP (official exchange rate)</th>
      <th>GDP - per capita (PPP)</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>Afghanistan</td>
      <td>[\nrevenues:\n2.276 billion\n\n(2017 est.)\n, ...</td>
      <td>[\n$2.84 billion\n\n(FY/)\n]</td>
      <td>[\n$20.24 billion\n(2017 est.)\n(2017 est.)\n]</td>
      <td>[\n$2,000\n\n(2017 est.)\n, \n$2,000\n\n(2016 ...</td>
    </tr>
    <tr>
      <td>1</td>
      <td>Albania</td>
      <td>[\nrevenues:\n3.614 billion\n\n(2017 est.)\n, ...</td>
      <td>[\n$9.505 billion\n\n(31 December 2017 est.)\n...</td>
      <td>[\n$13.07 billion\n(2017 est.)\n(2017 est.)\n]</td>
      <td>[\n$12,500\n\n(2017 est.)\n, \n$12,100\n\n(201...</td>
    </tr>
    <tr>
      <td>2</td>
      <td>Algeria</td>
      <td>[\nrevenues:\n54.15 billion\n\n(2017 est.)\n, ...</td>
      <td>[\n$6.26 billion\n\n(31 December 2017 est.)\n,...</td>
      <td>[\n$167.6 billion\n(2017 est.)\n(2017 est.)\n]</td>
      <td>[\n$15,200\n\n(2017 est.)\n, \n$15,200\n\n(201...</td>
    </tr>
    <tr>
      <td>3</td>
      <td>American Samoa</td>
      <td>[\nrevenues:\n249 million\n\n(2016 est.)\n, \n...</td>
      <td>[\nNA\n]</td>
      <td>[\n$658 million\n(2016 est.)\n(2016 est.)\n]</td>
      <td>[\n$11,200\n\n(2016 est.)\n, \n$11,300\n\n(201...</td>
    </tr>
    <tr>
      <td>4</td>
      <td>Andorra</td>
      <td>[\nrevenues:\n1.872 billion\n\n(2016)\n, \nexp...</td>
      <td>[\n$0\n\n(2016)\n]</td>
      <td>[\n$2.712 billion\n(2016 est.)\n(2016 est.)\n]</td>
      <td>[\n$49,900\n\n(2015 est.)\n, \n$51,300\n\n(201...</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Budget
def extract_budget(l, i):
    try:
        return l[i].split('\n')[2]
    except:
        return float('nan')
    
eco_df['Revenues($)'] = eco_df['Budget'].map(lambda t: extract_budget(t, 0))
eco_df['Expenditures($)'] = eco_df['Budget'].map(lambda t: extract_budget(t, 1))
```


```python
eco_df['Debt_ext($)'] = eco_df['Debt - external'].map(lambda t: t[0].split('\n')[1] 
                                                   if isinstance(t, list) else float('nan'))
eco_df['GDP($)'] = eco_df['GDP (official exchange rate)'].map(lambda t: t[0].split('\n')[1] 
                                                   if isinstance(t, list) else float('nan'))
eco_df['GDP_per_Capita($)'] = eco_df['GDP - per capita (PPP)'].map(lambda t: t[0].split('\n')[1] 
                                                   if isinstance(t, list) else float('nan'))
```


```python
eco_df.drop(['Budget', 'Debt - external', 'GDP (official exchange rate)', 'GDP - per capita (PPP)']
            , axis=1, inplace=True)
```


```python
eco_df.head()
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
      <th>Country</th>
      <th>Revenues($)</th>
      <th>Expenditures($)</th>
      <th>Debt_ext($)</th>
      <th>GDP($)</th>
      <th>GDP_per_Capita($)</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>Afghanistan</td>
      <td>2.276 billion</td>
      <td>5.328 billion</td>
      <td>$2.84 billion</td>
      <td>$20.24 billion</td>
      <td>$2,000</td>
    </tr>
    <tr>
      <td>1</td>
      <td>Albania</td>
      <td>3.614 billion</td>
      <td>3.874 billion</td>
      <td>$9.505 billion</td>
      <td>$13.07 billion</td>
      <td>$12,500</td>
    </tr>
    <tr>
      <td>2</td>
      <td>Algeria</td>
      <td>54.15 billion</td>
      <td>70.2 billion</td>
      <td>$6.26 billion</td>
      <td>$167.6 billion</td>
      <td>$15,200</td>
    </tr>
    <tr>
      <td>3</td>
      <td>American Samoa</td>
      <td>249 million</td>
      <td>262.5 million</td>
      <td>NA</td>
      <td>$658 million</td>
      <td>$11,200</td>
    </tr>
    <tr>
      <td>4</td>
      <td>Andorra</td>
      <td>1.872 billion</td>
      <td>2.06 billion</td>
      <td>$0</td>
      <td>$2.712 billion</td>
      <td>$49,900</td>
    </tr>
  </tbody>
</table>
</div>




```python
def clean_fig(e, mode=1):
    try:
        l = e.split(' ')
        if mode == 1:
            num = float(l[0].replace(',', ''))
        else:
            num = float(l[0][1:].replace(',', ''))
        if len(l) == 1:
            return num
        else:
            return num * 1e6 if l[1].strip() == 'million' else num * 1e9
    except:
        return float('nan')
```


```python
eco_df['Revenues($)'] = eco_df['Revenues($)'].apply(clean_fig)
eco_df['Expenditures($)'] = eco_df['Expenditures($)'].apply(clean_fig)
eco_df['Debt_ext($)'] = eco_df['Debt_ext($)'].apply(lambda t: clean_fig(t, 2))
eco_df['GDP($)'] = eco_df['GDP($)'].apply(lambda t: clean_fig(t, 2))
eco_df['GDP_per_Capita($)'] = eco_df['GDP_per_Capita($)'].apply(lambda t: clean_fig(t, 2))
```


```python
eco_df.head(5)
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
      <th>Country</th>
      <th>Revenues($)</th>
      <th>Expenditures($)</th>
      <th>Debt_ext($)</th>
      <th>GDP($)</th>
      <th>GDP_per_Capita($)</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>Afghanistan</td>
      <td>2.276000e+09</td>
      <td>5.328000e+09</td>
      <td>2.840000e+09</td>
      <td>2.024000e+10</td>
      <td>2000.0</td>
    </tr>
    <tr>
      <td>1</td>
      <td>Albania</td>
      <td>3.614000e+09</td>
      <td>3.874000e+09</td>
      <td>9.505000e+09</td>
      <td>1.307000e+10</td>
      <td>12500.0</td>
    </tr>
    <tr>
      <td>2</td>
      <td>Algeria</td>
      <td>5.415000e+10</td>
      <td>7.020000e+10</td>
      <td>6.260000e+09</td>
      <td>1.676000e+11</td>
      <td>15200.0</td>
    </tr>
    <tr>
      <td>3</td>
      <td>American Samoa</td>
      <td>2.490000e+08</td>
      <td>2.625000e+08</td>
      <td>NaN</td>
      <td>6.580000e+08</td>
      <td>11200.0</td>
    </tr>
    <tr>
      <td>4</td>
      <td>Andorra</td>
      <td>1.872000e+09</td>
      <td>2.060000e+09</td>
      <td>0.000000e+00</td>
      <td>2.712000e+09</td>
      <td>49900.0</td>
    </tr>
  </tbody>
</table>
</div>



## Overview so far


```python
Frame.sample(n=10)
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
      <th>Country</th>
      <th>Category</th>
      <th>Population</th>
      <th>Sex</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>955</td>
      <td>Norway</td>
      <td>15-24</td>
      <td>324088.0</td>
      <td>female</td>
    </tr>
    <tr>
      <td>1867</td>
      <td>United States Pacific Island Wildlife Refuges</td>
      <td>55-64</td>
      <td>NaN</td>
      <td>male</td>
    </tr>
    <tr>
      <td>945</td>
      <td>Namibia</td>
      <td>15-24</td>
      <td>257984.0</td>
      <td>female</td>
    </tr>
    <tr>
      <td>1028</td>
      <td>World</td>
      <td>15-24</td>
      <td>572229547.0</td>
      <td>female</td>
    </tr>
    <tr>
      <td>492</td>
      <td>West Bank</td>
      <td>0-14</td>
      <td>491676.0</td>
      <td>female</td>
    </tr>
    <tr>
      <td>577</td>
      <td>Christmas Island</td>
      <td>15-24</td>
      <td>202.0</td>
      <td>male</td>
    </tr>
    <tr>
      <td>2475</td>
      <td>France</td>
      <td>65+</td>
      <td>7569011.0</td>
      <td>female</td>
    </tr>
    <tr>
      <td>1373</td>
      <td>Cayman Islands</td>
      <td>25-54</td>
      <td>12855.0</td>
      <td>female</td>
    </tr>
    <tr>
      <td>421</td>
      <td>Norway</td>
      <td>0-14</td>
      <td>471014.0</td>
      <td>female</td>
    </tr>
    <tr>
      <td>2204</td>
      <td>European Union</td>
      <td>65+</td>
      <td>43673572.0</td>
      <td>male</td>
    </tr>
  </tbody>
</table>
</div>




```python
Frame.to_csv('clean_CIA_data/Age_structure.csv')
```


```python
median_df.sample(n=10)
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
      <th>Country</th>
      <th>median</th>
      <th>median_male</th>
      <th>median_female</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>201</td>
      <td>Tajikistan</td>
      <td>24.8</td>
      <td>24.2</td>
      <td>25.4</td>
    </tr>
    <tr>
      <td>23</td>
      <td>Bhutan</td>
      <td>28.1</td>
      <td>28.6</td>
      <td>27.6</td>
    </tr>
    <tr>
      <td>207</td>
      <td>Trinidad and Tobago</td>
      <td>36.6</td>
      <td>36.1</td>
      <td>37.1</td>
    </tr>
    <tr>
      <td>184</td>
      <td>Sierra Leone</td>
      <td>19.1</td>
      <td>18.4</td>
      <td>19.7</td>
    </tr>
    <tr>
      <td>213</td>
      <td>Uganda</td>
      <td>15.9</td>
      <td>15.8</td>
      <td>16.0</td>
    </tr>
    <tr>
      <td>70</td>
      <td>Fiji</td>
      <td>29.2</td>
      <td>29.0</td>
      <td>29.4</td>
    </tr>
    <tr>
      <td>101</td>
      <td>Israel</td>
      <td>30.1</td>
      <td>29.5</td>
      <td>30.7</td>
    </tr>
    <tr>
      <td>144</td>
      <td>Namibia</td>
      <td>21.4</td>
      <td>20.7</td>
      <td>22.2</td>
    </tr>
    <tr>
      <td>10</td>
      <td>Aruba</td>
      <td>39.5</td>
      <td>37.8</td>
      <td>41.2</td>
    </tr>
    <tr>
      <td>3</td>
      <td>American Samoa</td>
      <td>26.1</td>
      <td>25.6</td>
      <td>26.5</td>
    </tr>
  </tbody>
</table>
</div>




```python
median_df.to_csv('clean_CIA_data/median_age_country.csv')
```


```python
demo_df = demo_df.rename(columns={'Area': 'Area(km^2)',
                        'Birth rate': 'Birth_rate_per_1000',
                        'Death rate': 'Birth_rate_per_1000',
                        'Hospital bed density': 'Hospital_bed_density_per_1000',
                        'Life expectancy at birth': 'Life_expec_at_birth',
                        'Physicians density': 'Physicians_density_per_1000',
                        'Population below poverty line': 'Percentage_below_poverty_line',
                        'Unemployment rate': 'Unemployment_rate_%'})
```


```python
demo_df.sample(n=10)
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
      <th>Area(km^2)</th>
      <th>Birth_rate_per_1000</th>
      <th>Birth_rate_per_1000</th>
      <th>Hospital_bed_density_per_1000</th>
      <th>Physicians_density_per_1000</th>
      <th>Life_expec_at_birth</th>
      <th>Percentage_below_poverty_line</th>
      <th>Unemployment_rate_%</th>
    </tr>
    <tr>
      <th>Country</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>South Sudan</td>
      <td>644329.0</td>
      <td>36.9</td>
      <td>19.3</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>66.0</td>
      <td>NaN</td>
    </tr>
    <tr>
      <td>Western Sahara</td>
      <td>266000.0</td>
      <td>28.9</td>
      <td>7.9</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>63.8</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <td>Saint Vincent and the Grenadines</td>
      <td>389.0</td>
      <td>13.0</td>
      <td>7.4</td>
      <td>2.6</td>
      <td>NaN</td>
      <td>75.8</td>
      <td>NaN</td>
      <td>18.8</td>
    </tr>
    <tr>
      <td>Panama</td>
      <td>75420.0</td>
      <td>17.6</td>
      <td>5.0</td>
      <td>2.3</td>
      <td>1.59</td>
      <td>78.9</td>
      <td>23.0</td>
      <td>6.0</td>
    </tr>
    <tr>
      <td>Malta</td>
      <td>316.0</td>
      <td>10.0</td>
      <td>7.9</td>
      <td>4.7</td>
      <td>3.91</td>
      <td>82.7</td>
      <td>16.3</td>
      <td>4.6</td>
    </tr>
    <tr>
      <td>Wallis and Futuna</td>
      <td>142.0</td>
      <td>13.0</td>
      <td>5.5</td>
      <td>NaN</td>
      <td>1.10</td>
      <td>80.0</td>
      <td>NaN</td>
      <td>8.8</td>
    </tr>
    <tr>
      <td>Denmark</td>
      <td>43094.0</td>
      <td>10.9</td>
      <td>9.3</td>
      <td>2.5</td>
      <td>3.66</td>
      <td>81.0</td>
      <td>13.4</td>
      <td>5.7</td>
    </tr>
    <tr>
      <td>Kiribati</td>
      <td>811.0</td>
      <td>21.0</td>
      <td>7.0</td>
      <td>1.9</td>
      <td>0.20</td>
      <td>66.9</td>
      <td>NaN</td>
      <td>30.6</td>
    </tr>
    <tr>
      <td>Greece</td>
      <td>131957.0</td>
      <td>8.3</td>
      <td>11.4</td>
      <td>4.3</td>
      <td>6.26</td>
      <td>80.8</td>
      <td>36.0</td>
      <td>21.5</td>
    </tr>
    <tr>
      <td>Zambia</td>
      <td>752618.0</td>
      <td>41.1</td>
      <td>12.0</td>
      <td>2.0</td>
      <td>0.09</td>
      <td>53.0</td>
      <td>54.4</td>
      <td>15.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
demo_df.to_csv('clean_CIA_data/country_data.csv')
```


```python
eco_df.index = eco_df['Country']; eco_df.drop('Country', axis=1, inplace=True)
eco_df.sample(n=10)
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
      <th>Revenues($)</th>
      <th>Expenditures($)</th>
      <th>Debt_ext($)</th>
      <th>GDP($)</th>
      <th>GDP_per_Capita($)</th>
    </tr>
    <tr>
      <th>Country</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Uganda</td>
      <td>3.848000e+09</td>
      <td>4.928000e+09</td>
      <td>1.080000e+10</td>
      <td>2.662000e+10</td>
      <td>2400.0</td>
    </tr>
    <tr>
      <td>Jan Mayen</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <td>Belize</td>
      <td>5.535000e+08</td>
      <td>5.720000e+08</td>
      <td>1.315000e+09</td>
      <td>1.854000e+09</td>
      <td>8300.0</td>
    </tr>
    <tr>
      <td>Bahrain</td>
      <td>5.854000e+09</td>
      <td>9.407000e+09</td>
      <td>5.215000e+10</td>
      <td>3.533000e+10</td>
      <td>49000.0</td>
    </tr>
    <tr>
      <td>Honduras</td>
      <td>4.658000e+09</td>
      <td>5.283000e+09</td>
      <td>8.625000e+09</td>
      <td>2.298000e+10</td>
      <td>5600.0</td>
    </tr>
    <tr>
      <td>Kyrgyzstan</td>
      <td>2.169000e+09</td>
      <td>2.409000e+09</td>
      <td>8.164000e+09</td>
      <td>7.565000e+09</td>
      <td>3700.0</td>
    </tr>
    <tr>
      <td>Jordan</td>
      <td>9.462000e+09</td>
      <td>1.151000e+10</td>
      <td>2.934000e+10</td>
      <td>4.013000e+10</td>
      <td>9200.0</td>
    </tr>
    <tr>
      <td>Mongolia</td>
      <td>2.967000e+09</td>
      <td>3.681000e+09</td>
      <td>2.533000e+10</td>
      <td>1.114000e+10</td>
      <td>13000.0</td>
    </tr>
    <tr>
      <td>Sweden</td>
      <td>2.712000e+11</td>
      <td>2.644000e+11</td>
      <td>9.399000e+11</td>
      <td>5.356000e+11</td>
      <td>51200.0</td>
    </tr>
    <tr>
      <td>Solomon Islands</td>
      <td>5.325000e+08</td>
      <td>5.705000e+08</td>
      <td>7.570000e+08</td>
      <td>1.298000e+09</td>
      <td>2200.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
eco_df.to_csv('clean_CIA_data/eco_overview.csv')
```

# Conclusion
Now you are ready to analyze the world factbook data. We can have went over a bunch of techniques and libraries to help us scrape a web page:
- BeautifulSoup: parse HTML content and get fields we need
- pandas: structure the data and apply manipulation
- re: regular expression library to helps us clean data

in addition we can use `requests` to import the HTML content.

Hopefully this guided code provided you with an idea on how you can apply the same to your own.
