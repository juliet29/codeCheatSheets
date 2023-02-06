General Code Cheat Sheet 
#code-cheat-sheet
#current 

(Technologies, like Sherlock, openfoam, that involve combos of different coding languages  should get their own cheat sheets!) 

------------------------------ 
✨ Functional Languages 
------------------------------ 

Github  
Initialize a repo on the command line 
touch README.md
git init
git add README.md
git commit -m "first commit"

# need to make a repo online first - DO NOT INITIALIZE W ANYTHING
git remote add origin https://github.com/juliet29/bthermal_model
git push -u origin master

# if mess up 
git remote rm origin

undo git add
git reset . 

undo a git init
rm -rf .git

set a new origin 
git remote set-url origin git://new.url.here

------------------------------ 
General Bash Stuff
untarring files
tar -xvf archive.tar.gz
gunzip file.txt.gz

ls
# list all files and directories (including hidden)
ls -a 

# list directories and the things w/in them (think only goes one level down?)
ls * 

# details about files in a directory (like when they were last edited) 
ls -l


find and delete files 

# first find files (in current path, can replace . with real path)
find . -name '*py*'

# then delete as needed
find . -name '*py*' -delete

# all files with numbers
find . -name "[0-9]*"

find . -name '*([0-5][0-9]|6[0-7])*'

find <path> -type f | grep -E "/myfile_([0-9]|[0-5][0-9]|6[0-7])\.log$"

# files between 1 and 59? -> cant get delete to work, but this is recursive 
find . -type d | grep -E "([1-5]|[1-5])"
find .|  grep -E "([1-5]|[1-5])"




Iterm2
https://iterm2.com/documentation-shell-integration.html

# hidden files, ls command 


------------------------------ 
✨ Programming Languages 
------------------------------ 
Python 

Remove venv
rm -rf psych251env/

New python environment 
# in folder directory, vscode terminal 
virtualenv <env_name>

# activate
source <env_name>/bin/activate

popular packages 
pip install ipykernel pandas numpy scipy matplotlib pandas plotly 
# for plotly charts 
pip install --upgrade nbformat
# energy modeling 
pip install energyplus eppy ladybug-core

note requirements 
pip freeze > requirements.txt
pip install -r requirments.txt

getting help 
#first, activate venv, then:
python 
help()
## type name of function need help with 
pandas.read_csv

lists and dictionaries
# list comprehension dictionary 
myDict = { k:v for (k,v) in zip(keys, values)} 

# add item to a list 
a.append(7)

# add list to a list 
a.extend([2, 5, 7])


messed up venv -> distutils issue...
pip freeze > requirements.txt
deactivate
rm -rf cfdenv
virtualenv cfdenv
source cfdenv/bin/activate
pip install -r requirements.txt



Python packages 


↪ Pandas: (Cheat Sheet Pandas)

visualize the data 
# show all the data from a series / dataframe 
with pd.option_context('display.max_rows', None, 'display.max_columns', None):  
 print(df_downsample.isnull().sum()/len(df_downsample))

# quick histogram 
df.float.hist(bins=30, figsize=(3,2))

bringing in data, making new dataframes, exporting data 
# delete nans in a dataframe in one line 
df = df[np.isfinite(df.rgdppc_2000)]

# reset indices 
df_new = df.reset_index(drop=True) # for some reason inplace erases everything 

# dataframe from dictionary 
df = pd.DataFrame(dict).T ‼️what if want numerical index
df = pd.DataFrame(dict, index=[0]).T 

# export to csv
df.to_csv("outputs/name.csv", index=False)

using the "apply" function to modify a series 
df["duration"] = df["duration"].apply(lambda x: x -1 if x >= 5 else x)

# doing this and involving multiple columns of a dataframe 
df['c'] = df.apply(lambda row: row.a + row.b, axis=1)

comparisons
# compare column values across dataframes using merge ‼️
pd.merge(df.iloc[cid["BP 1A Structural Concrete"]], df_mar_start, on=['task_code'], how='inner')

selection
# select in between two dates => 
## without making time indices:
mask = (df['DateTime'] >= noon_start) & (df['DateTime'] <= noon_end)
df_b = df.loc[mask].reset_index(drop=True)
df_b 

# select rows in a dataframe where the dates match those in a list 
time_mask = df0_split["DateTime"].dt.time.isin(dfr_split.index.time)

splitting
df1, df0 = [x.reset_index(drop=True) for _, x in df_b.groupby(df_b['Room'] < 1)]



↪ Seaborn
visualize all the data correlations -> pairplots
sns.pairplot(df, height=1, aspect =2)
plt.show()

plotting distributions, see github: ma2021/description.ipynb
    -> click "see Raw" to see cutoff parts of code if needed

↪ Plotly 
quick line plot 
fig = go.Figure()
fig.add_trace(go.Scatter(
    x=df0["DateTime"],
    y=df0["Temp C"], 
    mode='lines+markers',
    marker_symbol='<"0" | "x"  | "square" >',
    marker_color='< >'
))

fig.update_layout(title='All Data',
                   xaxis_title='Dates',
                   yaxis_title='Temperature (ºC)')


Named css colors for plotly

subplots
fig = make_subplots(rows=5, cols=1, shared_xaxes=True, subplot_titles=("Plot 1", "Plot 2", "Plot 3", "Plot 4")
)

fig.add_trace(go.Scatter(
x=rmse_df.index.time,
y=rmse_df[0],
name="RMSE of Temperature in Rooms (15 min intervals)",
), row=ix+1, col=1)



↪ Jupyter notebooks 
Magic python functions enable interactive work ‼️
%matplotlib inline  

↪ Other packages 
Apache parquet - docs
Julia Plotly - docs
Icecream - article, GitHub 

My Virtual Envioronments
"/Users/julietnwagwuume-ezeoke/Documents/cee256_local/cee256venv"
# activate windows env from windows folder ‼️TODO move this to actual folder since its local now 
source ../../mylocalenvs/localwindowsenv/bin/activate.fish 




------------------------------ 
Julia 

# debugging 
https://opensourc.es/blog/basics-debugging/

# plotting multiple lines with plotly that have different appearances for differentiation 
traces = [PlotlyJS.scatter(x=time_hr[1:n]/24,  y=vol[1:n], mode="lines", name=label, line=attr(width=i, dash=dashtype)) for (vol,label, i, dashtype) in zip([V1, V2, V3, V1+V2+V3 ], ["V1", "V2", "V3", "V_total"], [9,7,5,3], ["dot", "dashdot", "dot", "none"])];
# => => actually with plotly, this isnt needed, can just click on the lines to remove traces if needed 

‼️ is there a function that turns plotlyjs plots into regular plots that can be viewed in github? 

------------------------------ 
R

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

# questions 
-> why the <- arrow sign vs a normal equal sign for assigninig values 



------------------------------ 
✨ Scripting/Templating Language
------------------------------ 
Markdown - cheat sheet 

# make a link 
[title](https://www.example.com)

# add a block quotes
> type type type 

------------------------------ 
Latex


