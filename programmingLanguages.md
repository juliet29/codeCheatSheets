
# Programming Languages

## TODO 
[] setting up a file that is automatically imported to everything 
[] conda environments 





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

# delete an item from a dictionary 
a.pop("Item to Delete")
```

importing modules from local files 
```python
import sys
sys.path.insert(0, "../scripts")
from helpers import *
```

Retrieving name of a variable 
```python 
import inspect

def retrieve_name(var):
    callers_local_vars = inspect.currentframe().f_back.f_locals.items()
    return [var_name for var_name, var_val in callers_local_vars if var_val is var]

```

Reading from text files 
```python 
with open("colors.txt", "r") as colors:
	lines = colors.readlines()	
theme_colors = [l.replace("\n", "") for l in lines]
```

Working with Enums 
```python 
from enum import Enum

class In(Enum):
    SINGAPORE = 0
    SAN_FRANCISCO = 1
    COSTS = 2
    DIST = 3

```




### Python packages 

#### Icecream for debugging 
```python
from icecream import ic 
ic.configureOutput(includeContext=True)

```

#### Pandas
Importing and Exporting Data
```python

# dataframe from dictionary 
df = pd.DataFrame(dict).T # ‼️what if want numerical index
df = pd.DataFrame(dict, index=[0]).T 
df = pd.DataFrame.from_dict(s_dict, orient="index")


# read in csv 
sing_df = pd.read_excel("sheets/land_use_constrained_data.xlsx", sheet_name=1, header=5, usecols="B:V", nrows=9)

# delete nans in a dataframe in one line 
df = df[np.isfinite(df.rgdppc_2000)]

# dropping columbs
df.drop(0, inplace=True)

# reset indices
df_new = df.reset_index(drop=True) # for some reason inplace erases everything 


# export to csv
df.to_csv("outputs/name.csv", index=False)

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

Bringing Data Together 
```python
# compare column values across dataframes using merge ‼️
pd.merge(df.iloc[cid["BP 1A Structural Concrete"]], df_mar_start, on=['task_code'], how='inner')


# TODO pd.concat()
```

Grouping Data
```python 
# aggregate data based on hour and room 
df_hour = df.groupby([times.dt.date, times.dt.hour, "Room"]).mean()
df_hour.head()d

# bin data based on a deired number of bins 
transport_metric = df.iloc[:, 8]
a = pd.cut(transport_metric, bins=4, retbins=False)
transport_metric = a.apply(lambda x: x.right)

```

Splitting Data 
```python 
# split a dataframe into two 
df1, df0 = [x.reset_index(drop=True) for _, x in df_b.groupby(df_b['Room'] < 1)]
```


Generating List of Dates with Pandas 
```python 
pd.date_range('2025-01-01','2025-12-31', 
              freq='MS').strftime("%Y-%b").tolist()

```

Functions on Data 
```python 
# using the "apply" function to modify a series 
df["duration"] = df["duration"].apply(lambda x: x -1 if x >= 5 else x)

# doing this and involving multiple columns of a dataframe 
df['c'] = df.apply(lambda row: row.a + row.b, axis=1)

```


Datetimes 
- [Aliases for datetimes](https://pandas.pydata.org/pandas-docs/stable/user_guide/timeseries.html#timeseries-offset-aliases)
```python 
# every 6 month frequency 
times = pd.date_range(start="2023-07-01",end="2045-07-01", freq="6MS")

# anchor at the beginning of a month 
times = pd.date_range(start="2023-07-01",end="2045-07-01", freq="AS-JUL")

```

Visualization 
```python
# show all the data from a series / dataframe 
with pd.option_context('display.max_rows', None, 'display.max_columns', None):  
 print(df_downsample.isnull().sum()/len(df_downsample))

# quick histogram 
df.float.hist(bins=30, figsize=(3,2))
```


#### Seaborn and Matplotlib 
* plotting distributions, see github: [ma2021/description.ipynb](https://github.com/psych251/ma2021/blob/main/code/notebooks/description.ipynb)
  * click "see Raw" to see cutoff parts of code if needed

```python
# importing and set themes 
import seaborn as sns
sns.set_theme(style="ticks")

# simple probability distributions 
sns.displot(deriv_df, x="deriv")

# visualize all the data correlations -> pairplots
sns.pairplot(df, height=1, aspect =2, hue="<df col>")
plt.show()

# parplot with regression 
g = sns.pairplot(an_df.iloc[:, 0:-1],  kind="reg")
```

Plotting with Matplotlib using subplots
```python 
import seaborn as sns
sns.set_theme(style="ticks")
import matplotlib.pyplot as plt


fig, (ax0, ax) = plt.subplots(nrows=1, ncols=2, figsize=[10,3])
ax0.bar(x=range(1,len(per_var)+1), height=per_var, tick_label=short_labels)
ax0.set_ylabel('Percentage of Explained Variance')
ax0.set_xlabel('Principal Component')
ax0.set_title('Scree Plot')

```


[Hide Legends](https://stackoverflow.com/questions/9834452/how-do-i-make-a-single-legend-for-many-subplots)

#### Plotly 

[Named CSS Colors for Plotly](https://stackoverflow.com/questions/72496150/user-friendly-names-for-plotly-css-colors)

‼️ is there a function that turns plotlyjs plots into regular plots that can be viewed in github? 


```python
import plotly.graph_objects as go
from plotly.subplots import make_subplots

# super quick scatter plot 
fig = go.Figure()
fig.add_trace(go.Scatter(
    x=a,
    y=b, 
    mode='markers',
))

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


# scatter plot with color scale
fig.add_trace(go.Scatter(
    x=sing_space["Longitude"],
    y=sing_space["Latitude"], 
    mode='markers',
    marker=dict(
        color=kmeans.labels_,
        showscale=True,
        size=16
    )
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

[Making and setting plotly themes](https://stackoverflow.com/questions/63011674/plotly-how-to-change-the-default-color-pallete-in-plotly)
* [Plotly reference on themes](https://plotly.com/python/templates/#saving-and-distributing-custom-themes)
```python 
import plotly.io as pio
import plotly.graph_objects as go
pio.templates["myname"] = go.layout.Template(
    layout=go.Layout(
        colorway=['#ff0000', '#00ff00', '#0000ff']
    )
# combine templates 
pio.templates.default = 'plotly_white+myname'

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

Editing epJSON files 
``` python 
import json
with open('1ZoneUncontrolled.epJSON') as f:
    input_data=json.load(f)
input_data['Building']['Simple One Zone (Wireframe DXF)']['north_axis']=90

with open('new_edit.epJSON','w') as f:
    json.dump(input_data,f, indent=4)
```


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


Using Custom LoadSQL class defined in _UILCode/BuildingEnergyModel/scripts/helpers.py 
```python
# load sql 
dir0 = "../examples/AFN_SS/eplusout.sql"
a = LoadSQL(dir0)

# check available outputs
a.ao

# check headers for interesting outputs 
a.enumerate_data_headers("Site Outdoor Air Drybulb Temperature")

# make a list of tuples with desired data 
desired_data = [(i, j, k) for i, j, k in zip(["Zone Mean Air Temperature"]*4 + ["Site Outdoor Air Drybulb Temperature"], [4,5,6,7,1], ["West", "South", "North", "East", "Sep Out"] )]

ad = a.get_valid_data(desired_data) 

a.plot_temps()
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

# convert from epjson and run 
energyplus -w weather/USA_CA_San.Francisco.Intl.AP.724940_TMY3.epw -d rosseRoomModel/230301_rr/test_01 -c rosseRoomModel/230301_rr/test_01.epJSON

# convert between epJSON and IDF and vice-versa 
energyplus --convert-only -d folder/output_dir name.idf

```
IDF Modifications 
- [View CAD/.dxf files online](https://sharecad.org/)
- [Updating IDF Versions](https://energyplushelp.attachments8.freshdesk.com/data/helpdesk/attachments/production/67022359683/original/multipleversionidfconversionsreadme.txt?response-content-type=text%2Fplain&Expires=1677705989&Signature=K5PInHdpqAsBNfKyPhioqfRyl3L-0M12lDmJjlO5UXTGQ6HqBfZ9qNxDlA~yex0KdjVV-NJSO5IowrnyWfrMTvt-ob0~-KLsQfFiuPlsOCzjSu~ThGXZ3jVmQm9oR0I70iUhk-82tKi~WZqh3Ip0bxOtyc88vM4lBZhdz00nVjQWsNhJ7HdHNtM9NSactde9MisjE-~EIq5qEt2FE2VlvrDLr~HqiDEDfdtsT8gzKPkglYHuywbK45pEPtQCCFBFhJ~WM~OjogM7z9K4IxjwDvdkEe7pRAn45kjQTiJRagKFqsJyp3qL5l2ssMuzX4Il2q7aJqX2BSPXolBK5hz-lQ__&Key-Pair-Id=APKAJ7JARUX3F6RQIXLA)
  - To launch the GUI, click on IDFVersionUpdater application bundle from /Applications/EnergyPlus-X-X-X/PreProcess/IDFVersionUpdater
  
```bash
! add SQL files 
    Output:SQLite,
        SimpleAndTabular;                       !- Option Type

! add output for drawings 
  Output:Surfaces:Drawing,dxf;
```

Check against the schema for a 22.2 version 
```bash
code $EPLUS_BEM/Energy+.schema.epJSON 

```

Energyplus function CLI usage

    ```bash 

    Usage: energyplus [options] [input-file]
    Options:
    -a, --annual                 Force annual simulation
    -c, --convert                Output IDF->epJSON or epJSON->IDF, dependent on
                                input file type
    -d, --output-directory ARG   Output directory path (default: current
                                directory)
    -D, --design-day             Force design-day-only simulation
    -h, --help                   Display help information
    -i, --idd ARG                Input data dictionary path (default: Energy+.idd
                                in executable directory)
    -j, --jobs ARG               Multi-thread with N threads; 1 thread with no
                                arg. (Currently only for G-Function generation)
    -m, --epmacro                Run EPMacro prior to simulation
    -p, --output-prefix ARG      Prefix for output file names (default: eplus)
    -r, --readvars               Run ReadVarsESO after simulation
    -s, --output-suffix ARG      Suffix style for output file names (default: L)
                                    L: Legacy (e.g., eplustbl.csv)
                                    C: Capital (e.g., eplusTable.csv)
                                    D: Dash (e.g., eplus-table.csv)
    -v, --version                Display version information
    -w, --weather ARG            Weather file path (default: in.epw in current
                                directory)
    -x, --expandobjects          Run ExpandObjects prior to simulation
    --convert-only                 Only convert IDF->epJSON or epJSON->IDF,
                                dependent on input file type. No simulation
    Example: energyplus -w weather.epw -r input.idf
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




