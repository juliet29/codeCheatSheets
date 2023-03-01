
# Programming Languages

-----------
## Python 

### Python Environment 
```bash
# Remove venv
rm -rf psych251env/

# in folder directory, vscode terminal 
virtualenv <env_name>

# activate
source <env_name>/bin/activate

---
# popular packages 
pip install ipykernel pandas numpy scipy matplotlib pandas plotly 
# for plotly charts 
pip install --upgrade nbformat

# note requirements 
pip freeze > requirements.txt
pip install -r requirments.txt

---
# messed up venv -> distutils issue...
pip freeze > requirements.txt
deactivate
rm -rf cfdenv
virtualenv cfdenv
source cfdenv/bin/activate
pip install -r requirements.txt
```
### General Python
[Code imporvement article -> see Enums](https://towardsdatascience.com/5-python-tricks-that-distinguish-senior-developers-from-juniors-826d57ab3940)

Getting help 
```bash
#first, activate venv, then:
python 
help()
## type name of function need help with 
pandas.read_csv
```

lists and dictionaries
``` python
# list comprehension dictionary 
myDict = { k:v for (k,v) in zip(keys, values)} 

# add item to a list 
a.append(7)

# add list to a list 
a.extend([2, 5, 7])
```

importing modules from local files 
```python
import sys
sys.path.insert(0, "../scripts")
from helpers import *


```




### Python packages 


#### Pandas

Visualization 
```python
# show all the data from a series / dataframe 
with pd.option_context('display.max_rows', None, 'display.max_columns', None):  
 print(df_downsample.isnull().sum()/len(df_downsample))

# quick histogram 
df.float.hist(bins=30, figsize=(3,2))
```

Bringing in data, making new dataframes, exporting data 
```python

# delete nans in a dataframe in one line 
df = df[np.isfinite(df.rgdppc_2000)]

# reset indices
df_new = df.reset_index(drop=True) # for some reason inplace erases everything 

# dataframe from dictionary 
df = pd.DataFrame(dict).T ‼️what if want numerical index
df = pd.DataFrame(dict, index=[0]).T 

# export to csv
df.to_csv("outputs/name.csv", index=False)

# using the "apply" function to modify a series 
df["duration"] = df["duration"].apply(lambda x: x -1 if x >= 5 else x)
# doing this and involving multiple columns of a dataframe 
df['c'] = df.apply(lambda row: row.a + row.b, axis=1)

```

Comparisons
```python
# compare column values across dataframes using merge ‼️
pd.merge(df.iloc[cid["BP 1A Structural Concrete"]], df_mar_start, on=['task_code'], how='inner')
```

Selection 
```python
# select in between two dates => 
## without making time indices:
noon_start = str2dt('2022, 07, 20, 12, 00') # strdt in in _UILCode/windows/analysis/scripts
mask = (df['DateTime'] >= noon_start) & (df['DateTime'] <= noon_end)
df_b = df.loc[mask].reset_index(drop=True)

# select rows in a dataframe where the dates match those in a list 
time_mask = df0_split["DateTime"].dt.time.isin(dfr_split.index.time)
```

Splitting Data 
```python 
# split a dataframe into two 
df1, df0 = [x.reset_index(drop=True) for _, x in df_b.groupby(df_b['Room'] < 1)]
```

Grouping Data
```python 
# aggregate data based on hour and room 
df_hour = df.groupby([times.dt.date, times.dt.hour, "Room"]).mean()
df_hour.head()d

```

Generating List of Dates with Pandas 
```python 
pd.date_range('2025-01-01','2025-12-31', 
              freq='MS').strftime("%Y-%b").tolist()

```


#### Seaborn
```python
# visualize all the data correlations -> pairplots
sns.pairplot(df, height=1, aspect =2)
plt.show()

# plotting distributions, see github: ma2021/description.ipynb
    -> click "see Raw" to see cutoff parts of code if needed
```

#### Plotly 

[Named CSS Colors for Plotly](https://stackoverflow.com/questions/72496150/user-friendly-names-for-plotly-css-colors)

‼️ is there a function that turns plotlyjs plots into regular plots that can be viewed in github? 


```python
import plotly.graph_objects as go
from plotly.subplots import make_subplots

# quick line plot 
fig = go.Figure()
fig.add_trace(go.Scatter(
    x=df0["DateTime"],
    y=df0["Temp C"], 
    name="Data name",
    mode='lines+markers',
    marker_symbol='<"0" | "x"  | "square" >',
    marker_color='< >',
    showlegend=False,
    text=df0["Other Data"] # hover text label 
))

fig.update_layout(xaxis_title='Dates',
                  yaxis_title='Temperature (ºC)',
                  title='All Data')
                  


# subplots
fig = make_subplots(rows=5, cols=1, shared_xaxes=True, subplot_titles=("Plot 1", "Plot 2", "Plot 3", "Plot 4")

for ix, room in enumerate(rooms.values()):
  # only show legend the first time, and make similar items have the same color 
    showlegendbool = True if ix == 0 else False
    fig.add_trace(go.Scatter(
        x=room["air"][0].datetimes,
        y=room["air"][0].values, 
        mode='lines+markers',
        name="Zone Air Temperature",
        showlegend=showlegendbool,
        marker_color="blue"
    ), row=ix+1, col=1)
    fig.add_trace(go.Scatter(
        x=room["amb"][0].datetimes,
        y=room["amb"][0].values, 
        mode='lines+markers',
        name="Site Outdoor Air Temperature",
        showlegend=showlegendbool,
        marker_color="blue"
    ), row=ix+1, col=1)

# plot multidimensional subplots
fig = make_subplots(rows=3, cols=2, shared_xaxes=False, subplot_titles=list(exp_dct.keys()))

for ix, df in enumerate(exp_dct.values()):
    # only show legend the first time
    showlegendbool = True if ix == 0 else False
    # keep row numbers in check 
    row_num = int(ix/2) + 1

    if ix%2 == 0:
        fig.add_trace(go.Scatter(
            x=df["DateTime"],
            y=df["Temp C"],
            mode='lines+markers',
            name="Room Air Temperature",
            showlegend=showlegendbool,
            marker_color="blue"
        ), row=row_num, col=1)
    else:
        fig.add_trace(go.Scatter(
            x=df["DateTime"],
            y=df["Temp C"],
            mode='lines+markers',
            name="Site Outdoor Air Temperature",
            showlegend=showlegendbool,
            marker_color="blue"
        ), row=row_num, col=2) 

fig.show()

```

#### Jupyter notebooks 
Magic python functions enable interactive work ‼️
%matplotlib inline  

#### Other packages 
Apache parquet - docs
Julia Plotly - docs
Icecream - article, GitHub 



#### Energy Modeling in Python
[Github example for adjusting IDFs](https://github.com/juliet29/cee256_final_2022/blob/main/calibration/adjustModels.ipynb)

[Github example for generating multiple IDFs,and extracting data from SQL files](https://github.com/juliet29/cee256_final_2022/blob/main/optimization/opt_fun.py)

Installation of E+ on Mac is in Applications/EnergyPlus-9-4-0

[Ladybug SQL Module](https://www.ladybug.tools/ladybug/docs/ladybug.sql.html)

[Ladybug Data Collections Module](https://www.ladybug.tools/ladybug/docs/ladybug.datacollection.html)

Installing the requisite Python modules 
```bash
# energy modeling pip installs
pip install energyplus eppy ladybug-core
```

SketchUp -> IDF process 
* Need to make sure that a zone is established for computation to happen 


Post-processing from SQL files with Ladybug SQL 
```python 
# imports 
from ladybug.sql import SQLiteResult
from ladybug.analysisperiod import AnalysisPeriod as ap

# See which surface data is coming from for multi-surface data 
sqld.data_collections_by_output_name("Surface Outside Face Temperature")[0].header 
# --> @1 seems to mean every one hour...
# can change this to be a dictionary, so can extract the data
<...>.header.to_dict()
# to get the specific surface
out_surf[1].header.to_dict()["metadata"]["Surface"]

# See time intervals of the data 
sqld.data_collections_by_output_name("Surface Outside Face Temperature")[0].datetime_strings

# Creating an Analysis Period, and the filtering based on it  
exp01_ap = ap(st_month=7, st_day=20, end_month=7, end_day=23,  is_leap_year=True)
# ---> for some reasone, including st_hour, and end_hour makes it take one value per day.. ‼️
zone_air.filter_by_analysis_period(exp01_ap)


# Plotting
x=zone_air_exp01.datetimes,
y=zone_air_exp01.values, 
```

#### Energy Modeling, Generally 
[EnergyPlus Essentials](https://energyplus.net/assets/nrel_custom/pdfs/pdfs_v22.2.0/EnergyPlusEssentials.pdf)

Bash Shortcuts for E+
```bash
# in BEM folder, can get to eplus as...
ls $EPLUS_BEM #=> ../../../../Applications/EnergyPlus-22-2-0/

# EPlus 22.2 idd file 
$EPLUS_BEM_IDD
```

Running E+ on the CLI
```bash
# run from command line and direct oupput to a different directtory 
energyplus -i $EPLUS_BEM_IDD -w weather/USA_IL_Chicago-OHare.Intl.AP.725300_TMY3.epw -d examples/AFNWindowOp examples/AFNWindowOp.idf

# only need to spec idd if want to use a different one than where the executable is 
energyplus -w weather/USA_IL_Chicago-OHare.Intl.AP.725300_TMY3.epw -d examples/AFNWindowOp examples/AFNWindowOp.idf
```
Modifying IDFs
- [View CAD/.dxf files online](https://sharecad.org/)
```IDF
! add SQL files 
    Output:SQLite,
        SimpleAndTabular;                       !- Option Type

! add output for drawings 
  Output:Surfaces:Drawing,dxf;


```




-----------------
## Julia 
[Debugging](https://opensourc.es/blog/basics-debugging)

```Julia
# plotting multiple lines with plotly that have different appearances for differentiation 
traces = [PlotlyJS.scatter(x=time_hr[1:n]/24,  y=vol[1:n], mode="lines", name=label, line=attr(width=i, dash=dashtype)) for (vol,label, i, dashtype) in zip([V1, V2, V3, V1+V2+V3 ], ["V1", "V2", "V3", "V_total"], [9,7,5,3], ["dot", "dashdot", "dot", "none"])];
# => => actually with plotly, this isnt needed, can just click on the lines to remove traces if needed 
```



---------
## R
Question: -> why the <- arrow sign vs a normal equal sign for assigninig values 
```R
# typical imports 
library(tidyr)
library(dplyr)
library(stringr) # useful for some string manipulation
library(ggplot2)
library(readr) # read csv 
library(tidyverse) # how does this differ from tidyr?‼️

# grouping data 
by_subid <- fvs %>% group_by(subid)

# making a scatter plot with ggplot 
ggplot(by_subid, aes(x=age, y=hand.look, color=condition)) +
  geom_point(alpha=0.5) +
  geom_smooth(method="lm", formula = "y~x") + 
  labs(y="Hand Looking", x="Age") 

```

------------
## Markdown 

```markdown
# make a link 
[title](https://www.example.com)

# add a block quotes
> type type type 
```




