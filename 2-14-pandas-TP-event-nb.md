---
jupytext:
  custom_cell_magics: kql
  text_representation:
    extension: .md
    format_name: myst
    format_version: 0.13
    jupytext_version: 1.17.3
kernelspec:
  display_name: Python 3 (ipykernel)
  language: python
  name: python3
---

# grouping by period and category

+++

before starting this TP:
- put the `2-14-pandas-TP-event-nb.md` file in your github repository `p25-numerique-groupe8`  
- put the file `events.csv` and the file `country.csv` in its `data` folder 
- put the files `result-color-w.png`, `result-color-m.png`, `result-color-y.png`,  
   `result-bw-w.png`, `result-bw-m.png` and `result-bw-y.png` in its `media` folder

+++

in this TP we work on 

- data that represents *periods* and not just one timestamp
- checking for overlaps
- grouping by period (week, month, year..)
- then later on, grouping by period *and* category
- and some simple visualization tools

+++

here's an example of the outputs we will obtain

````{grid} 3 3 3 3
```{image} media/result-color-w.png
```
```{image} media/result-color-m.png
```
```{image} media/result-color-y.png
```
````

+++

## imports

```{code-cell} ipython3
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
```

## the data

we have a table of events, each with a begin (`beg`) and `end` time; in addition each is attached to a `country`  
(we do not yet know what these events are)

```{code-cell} ipython3
events = pd.read_csv("data/events.csv")
events.head(10)
```

### adapt the type of each columns

surely the columns dtypes need some care
hints:
1. use the the `datetime` formats described here https://docs.python.org/3/library/datetime.html#strftime-and-strptime-behavior
2. or find and use the international format described https://en.wikipedia.org/wiki/ISO_8601

```{code-cell} ipython3
events.dtypes
```

```{code-cell} ipython3
events['end'] = pd.to_datetime(events['end']).dt.strftime('%a %d %b %Y, %I:%M%p')
events['beg'] = pd.to_datetime(events['beg']).dt.strftime('%a %d %b %Y, %I:%M%p')
#events['end'] = pd.to_datetime(events['end'], format='%a %d %b %Y, %I:%M%p')
#events['beg'] = pd.to_datetime(events['beg'], format='%a %d %b %Y, %I:%M%p')
events
```

```{code-cell} ipython3

```

```{code-cell} ipython3
# check it

events.dtypes
```

### raincheck

check that the data is well-formed, i.e. **the `end`** timestamp **happens after `beg`**

```{code-cell} ipython3
#Pour les calculs, je suis obligé de faire avec des datetime
events['end'] = pd.to_datetime(events['end'], format = '%a %d %b %Y, %I:%M%p')
events['beg'] = pd.to_datetime(events['beg'], format = '%a %d %b %Y, %I:%M%p')
dureebeg = 0
for x in events['beg']:
    dureebeg += x.timestamp()
dureebeg

dureeend = 0
for x in events['end']:
    dureeend += x.timestamp()
dureeend

dureebeg < dureeend

#True
```

```{code-cell} ipython3

```

```{code-cell} ipython3
events.sort_values(by = 'beg', inplace = True)
events
```

sort the dataframe by the `'beg'` column

+++

### are there any overlapping events ?

+++

check if there are overlapping events  

hints:
1. you can use the `pandas.Series.shift` method  
  (i.e. the method `shift` applied to a `pandas` `Series`)
2. there is a `pandas.Timedelta` function

```{code-cell} ipython3
events['new_beg'] = events['beg'].shift(1)

events['overlap'] = events ['end'] < events['new_beg']
events['overlap'].unique()
#array([False])
```

### timespan

What is the timespan covered by the dataset (**earliest** and **latest** events, and **duration** in-between) ?

```{code-cell} ipython3
print(events['beg'].iloc[0])
print(events['end'].iloc[len(events['end'])-1])
longueur =events['end'].iloc[len(events['end'])-1]-events['beg'].iloc[0]
longueur 
```

### aggregated duration

so, given that there is no overlap, we can assume this corresponds to "reservations" attached to a unique resource  
write a code that computes the **overall reservation time**, as well as the **average usage ratio** over the overall timespan

keep a column with the duration of each event

```{code-cell} ipython3
events['timedelta'] = events['end']-events['beg']
overall_reservation_time = events['timedelta'].sum()
print (str(overall_reservation_time/longueur*100) + ' %')
```

## visualization - grouping by period

### usage by period

grouping by periods: by week, by month or by year, plot the `bar` of the **total duration in that period**

There are at least 2 options to do this grouping, based on `resample()` and `to_period()` : **write them both**

(you can access the `dt` (datetime) attibuts and methods of a column if needed)


`````{admonition} for now, **just get the grouping right (do not improve your plots yet)**
:class: dropdown

you should produce something like e.g.

````{grid} 3 3 3 3
```{image} media/result-bw-w.png
```
```{image} media/result-bw-m.png
```
```{image} media/result-bw-y.png
```
````
we'll make cosmetic improvements below, and [the final results look like this](#label-events-output), but let's not get ahead of ourselves
`````

```{code-cell} ipython3
events['Month'] = events['beg'].dt.to_period('M')
events['Week'] = events['beg'].dt.to_period('W')
events['Year'] = events['beg'].dt.to_period('Y')
ParM = events.groupby(by = 'Month')['timedelta'].sum()
ParW = events.groupby(by = 'Week')['timedelta'].sum()
ParY = events.groupby(by = 'Year')['timedelta'].sum()
ParY.plot.bar()
plt.show()
ParM.plot.bar()
plt.show()
ParW.plot.bar()
plt.show()
```

```{code-cell} ipython3
events['beg'] = pd.to_datetime(events['beg'])
events['end'] = pd.to_datetime(events['end'])
events.sort_values(by = 'beg', inplace = True)
month = events.resample('ME', on = 'beg')['timedelta'].sum()
week = events.resample('W', on = 'beg')['timedelta'].sum()
year = events.resample('YE', on = 'beg')['timedelta'].sum()
month.plot.bar()
plt.show()
week.plot.bar()
plt.show()
year.plot.bar()
plt.show()
```

### improve the title and bottom ticks

add a title to your visualisations

also, and particularly relevant in the case of the per-week visu, we don't get to read **the labels on the horizontal axis**, because there are **too many of them**  
to improve this, you can use matplotlib's `set_xticks()` function; you can either figure out by yourself, or read the few tips below

````{admonition} a few tips
:class: dropdown tip

- the object that receives the `set_xticks()` method is an instance of `Axes` (one X&Y axes system),  
  which is not the figure itself (a figure may contain several Axes)  
  ask google or chatgpt to find the way you can spot the `Axes` instance in your figure
- it is not that clear in the docs, but all you need to do is to pass `set_xticks` a list of *indices* (integers)  
  i.e. if you have, say, a hundred bars, you could pass `[0, 10, 20, ..., 100]` and you will end up with one tick every 10 bars.
- there are also means to use smaller fonts, which may help see more relevant info
````

```{code-cell} ipython3
# let's say as arule of thumb
LEGEND = {
    'W': "week",
    'M': "month",
    'Y': "year",
}

SPACES = {
    'W': 12,   # in the per-week visu, show one tick every 12 - so about one every 3 months
    'M': 3,    # one every 3 months
    'Y': 1,    # on all years
}
```

```{code-cell} ipython3
monthbar = month.plot.bar()
ticksmonth = monthbar.get_xticks()
monthbar.set_xticks(ticksmonth[::3])
plt.title('Par mois')
plt.show()
weekbar = week.plot.bar()
ticksweek = weekbar.get_xticks()
weekbar.set_xticks(ticksweek[::12])
plt.title('Par semaines')
plt.show()
yearbar = year.plot.bar()
ticksyear = yearbar.get_xticks()
yearbar.set_xticks(ticksyear[::1])
plt.title('Par années')
plt.show()
```

### a function to convert to hours

you are to write a function that converts a `pd.Timedelta` into a number of hours  
1. read and understand the test code for the details of what is expected
2. use it to test your own implementation

note that if an hour has started even by one second, **it is counted**

```{code-cell} ipython3
type(events['timedelta'].iloc[3] ) == pd._libs.tslibs.timedeltas.Timedelta
events['timedelta'].iloc[3].components
```

```{code-cell} ipython3
# the type of timedelta is pd.Timedelta
# the function returns an int
def convert_timedelta_to_hours(timedelta: pd.Timedelta) -> int:
    if type(timedelta) != pd._libs.tslibs.timedeltas.Timedelta:
        timedelta = pd.to_timedelta(timedelta)
    
    days = timedelta.components[0]
    hours = timedelta.components[1]
    minutes = timedelta.components[2]
    seconds = timedelta.components[3]
    if minutes != 0 or seconds != 0:
        tot = 24*days + hours + 1
    else:
        tot = 24*days + hours
    return tot 
        
```

```{code-cell} ipython3
# test it

# if an hour has started even by one second, it is counted
test_cases = ( 
    # input in seconds, expected result in hours
    (0, 0), 
    (1, 1),     (3599, 1),     (3600, 1), 
    (3601, 2),  (7199, 2),     (7200, 2), 
    # 2 hours + 1s -> 3 hours
    (7201, 3),  
    # 3 hours + 2 minutes -> 4 hours
    (pd.Timedelta(3, 'h') + pd.Timedelta(2, 'm'), 4),
    # 2 days -> 48 hours
    (pd.Timedelta(2, 'D'), 48),
)

def test_convert_timedelta_to_hours():
    for seconds, exp in test_cases:
        # convert into pd.Timedelta if not already one
        if not isinstance(seconds, pd.Timedelta):
            timedelta = pd.Timedelta(seconds=seconds)
        else:
            timedelta = seconds
        # compute and compare
        got = convert_timedelta_to_hours(timedelta)
        print(f"with {timedelta=} we get {got} and expected {exp} -> {got == exp}")

test_convert_timedelta_to_hours()
```

```{code-cell} ipython3
# for debugging; this should return 48

convert_timedelta_to_hours(pd.Timedelta(2, 'D'))
```

### use it to display totals in hours

keep the same visu, but display **the Y axis in hours**  
btw, what was the unit in the graphs above ?

hint:  
you can use `map` to apply a function (for example `convert_timedelta_to_hours`) to a `pandas.Series`

```{code-cell} ipython3
events
```

```{code-cell} ipython3
for i in range (len(events['timedelta'])):
   events['timedelta'].iloc[i] = convert_timedelta_to_hours(events['timedelta'].iloc[i])
events


#ou bien 

events['timedelta'] = events['timedelta'].map(convert_timedelta_to_hours)
events
```

```{code-cell} ipython3

```

```{code-cell} ipython3
new_month = events.resample('ME', on = 'beg')['timedelta'].sum()
week = events.resample('W', on = 'beg')['timedelta'].sum()
year = events.resample('YE', on = 'beg')['timedelta'].sum()
nmonthbar = new_month.plot.bar()
ticksmonth = monthbar.get_xticks()
monthbar.set_xticks(ticksmonth[::3])
plt.title('Par mois')
plt.show()
weekbar = week.plot.bar()
ticksweek = weekbar.get_xticks()
weekbar.set_xticks(ticksweek[::12])
plt.title('Par semaines')
plt.show()
yearbar = year.plot.bar()
ticksyear = yearbar.get_xticks()
yearbar.set_xticks(ticksyear[::1])
plt.title('Par années')
plt.show()
```

## grouping by period and region

the following table allows you to map each country into a region

```{code-cell} ipython3
# load it

countries = pd.read_csv("data/countries.csv")
countries.head(3)
```

### a glimpse on regions

what's the most effective way to see how many regions and how many countries per region we have ?

```{code-cell} ipython3
nb_region = len(countries['region'].unique())
nb_region
#5
```

```{code-cell} ipython3
Par_reg = countries.groupby(by = 'region')
Par_reg.size()

#Africa      10
#Americas     5
#Asia         9
#Europe       6
#Oceania      1
```

### attach a region to each event

your mission is to now show the same graphs, but we want to reflect the relative usage of each region, so we want to [split each bar into several colors, one per region see expected result below](#label-events-output)

+++

most likely your first move is to tag all events with a `region` column

remember that you can `pandas.merge` two data frames

```{code-cell} ipython3
new_table = pd.merge(left = events,left_on='country', right = countries, right_on='name' ).drop(columns = 'name')
new_table
```

### visu by period by region

you can now produce [the target figures, again they look like this](#label-events-output)

remember that missing values can be filled with the `fillna` method and that timedelta can be computed with `pandas.Timedelta`

```{code-cell} ipython3
def plot_by_period(period_col, title, tick_step):
    grouped = new_table.groupby([period_col, 'region'])['timedelta'].sum()
    #print(grouped), permet de voir à quoi ressemble le groupby créé
    pivot = grouped.unstack(fill_value=0)
    # bar chart empilé
    ax = pivot.plot(kind='bar',stacked=True,figsize=(12,5))
    xticks = ax.get_xticks()
    ax.set_xticks(xticks[::tick_step])
    plt.title(title)
    plt.ylabel('Total Timedelta')
    plt.tight_layout()
    plt.show()


plot_by_period('Week',  'Usage per week (by region)', 12)
plot_by_period('Month', 'Usage per month (by region)', 3)
plot_by_period('Year',  'Usage per year (by region)', 1)
```

***
