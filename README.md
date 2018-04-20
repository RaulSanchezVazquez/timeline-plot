# Timeline in pure Matplotlib

```python
import matplotlib.patches as mpatches
import matplotlib.pyplot as plt
import random

import pandas as pd
import random

import matplotlib.pyplot as plt

def gen_rand_date():
    year = random.randint(2010, 2018)
    month = random.randint(1, 12)
    day = random.randint(1, 28)
    
    return pd.to_datetime('%s-%s-%s' % (year, month, day))

random.seed(4)
n_events = random.randint(20, 50)

data = pd.DataFrame({
    "feature": [random.randint(1, 10) for x in range(n_events)],
    "flag_special_event": [random.randint(1, 10) == 4 for x in range(n_events)],
    "event_type": [random.randint(0, 312) % 4 for x in range(n_events)],
    "date": [gen_rand_date() for _ in range(n_events)]
})


event_colors = {
    0: '#ffc50f',
    1: '#f3452c',
    2: '#80cacc',
    3: '#8c9f0f',
    4: '#8060cb'}

data['event_color'] = data['event_type'].apply(
    lambda x: event_colors[x]).tolist()

data = data[['date', 'event_type', 'event_color', 'feature', 'flag_special_event']]
data.head(10)
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
      <th>date</th>
      <th>event_type</th>
      <th>event_color</th>
      <th>feature</th>
      <th>flag_special_event</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2015-02-27</td>
      <td>2</td>
      <td>#80cacc</td>
      <td>5</td>
      <td>False</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2010-12-08</td>
      <td>3</td>
      <td>#8c9f0f</td>
      <td>2</td>
      <td>False</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2014-10-20</td>
      <td>0</td>
      <td>#ffc50f</td>
      <td>7</td>
      <td>False</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2013-02-11</td>
      <td>0</td>
      <td>#ffc50f</td>
      <td>8</td>
      <td>False</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2012-05-15</td>
      <td>2</td>
      <td>#80cacc</td>
      <td>3</td>
      <td>False</td>
    </tr>
    <tr>
      <th>5</th>
      <td>2010-01-12</td>
      <td>1</td>
      <td>#f3452c</td>
      <td>2</td>
      <td>False</td>
    </tr>
    <tr>
      <th>6</th>
      <td>2011-05-24</td>
      <td>3</td>
      <td>#8c9f0f</td>
      <td>2</td>
      <td>False</td>
    </tr>
    <tr>
      <th>7</th>
      <td>2015-01-11</td>
      <td>0</td>
      <td>#ffc50f</td>
      <td>1</td>
      <td>True</td>
    </tr>
    <tr>
      <th>8</th>
      <td>2014-06-05</td>
      <td>3</td>
      <td>#8c9f0f</td>
      <td>7</td>
      <td>False</td>
    </tr>
    <tr>
      <th>9</th>
      <td>2016-10-22</td>
      <td>1</td>
      <td>#f3452c</td>
      <td>9</td>
      <td>True</td>
    </tr>
  </tbody>
</table>
</div>



# Create the legend, plot time/feature line, style x/y axis


```python
fig, ax = plt.subplots(1, 1)

handles = []
for label, color in event_colors.items():
    handle = mpatches.Patch(
        color=color, 
        label=str(label))

    handles.append(handle)
    
ax.legend(
    handles=handles, 
    ncol=len(handles))

#Stile ticks
fig.autofmt_xdate()
fig.set_size_inches(18, 5)

#Hide y axis 
ax.yaxis.set_visible(False)
ax.get_yaxis().set_ticklabels([])

#Hide spines
ax.spines['right'].set_visible(False)
ax.spines['left'].set_visible(False)
ax.spines['top'].set_visible(False)
#
#ax.xaxis.set_ticks_position('bottom')

#Leave left/right marging on x-axis
min_date = data['date'].min()
max_date = data['date'].max()
month_offset = pd.DateOffset(months=12)
ax.set_xlim(min_date - month_offset, max_date + month_offset)

#Limit y-range
ax.set_ylim(.9, 1.4);

#Time line
timeline_y = 1
ax.axhline(
    timeline_y,
    linewidth=1,
    color='#CCCCCC')
ax.annotate(
    'Timeline', 
    (min_date - month_offset, timeline_y),
    fontsize=15)
fig

#Feature line
feature_line_y = 1.2
ax.axhline(
    feature_line_y, 
    linewidth=1,
    c='#CCCCCC')

ax.annotate(
    'Feature', 
    (min_date - month_offset, feature_line_y),
    fontsize=15)

fig.set_size_inches(18, 4)
```


![png](output_2_0.png)


# Plot events
- Event type is denoted by a color
- Special event is denoted by the size of the disk
- X-axis are dates
- Y-axis is dummy (set 1 to all items)


```python
dot_size = 500
dot_special_scale = 2

s = (
    data['flag_special_event'].astype(int) + 1
) ** dot_special_scale * dot_size

ax.scatter(
    data['date'].tolist(),
    [1] * data.shape[0],
    c=data['event_color'].tolist(),
    s=s,
    marker='o',
    linewidth=1,
    alpha=.5)

for idx, row_data in data.iterrows():
    if row_data['flag_special_event'] == True:
        ax.annotate(
            'Spe. Ev.', 
            (row_data['date'], 1.12),
            rotation=60)
#fig.set_size_inches(18, 4)
fig
```




![png](output_4_0.png)



## Plot feature


```python
ax.scatter(
    data['date'].values,
    [feature_line_y] * data.shape[0],
    c='b',
    alpha=.2,
    s=data['feature'].values ** 4)

ax.scatter(
    data['date'].values,
    [feature_line_y] * data.shape[0],
    c='b',
    alpha=.8,
    s=50)


for idx, row_data in data.iterrows():
    ax.axvline(
        row_data['date'], 
        linewidth=1,
        c='b',
        linestyle='-.')
    
#fig.set_size_inches(18, 4)
fig.savefig('timeline.png', format='png')
fig
```




![png](output_6_0.png)


