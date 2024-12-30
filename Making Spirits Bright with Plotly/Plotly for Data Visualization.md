# Introduction to Plotly for Data Visualization

## Understanding Plotly Express and its Benefits

Plotly Express is a concise, high-level API for creating interactive plots in Python. It simplifies data visualization by reducing the amount of code needed. Unlike lower-level Plotly functions, Plotly Express is designed for quick prototyping and data exploration.

The main benefits of Plotly Express include:

`Ease of Use`: With minimal code, you can generate complex plots.
`Interactivity`: Plots are not just static images; they are interactive and can be easily exported as HTML files.
`Data Exploration`: Helps in rapidly gaining insights into datasets by visualizing trends and distributions.
Plotly Express is particularly useful in situations where quick insights are needed without much overhead. For example, when initially exploring a new dataset, such as the Billboard Christmas Songs dataset we're working with today.

## Loading and Preparing Data with Pandas

Before diving into visualization, it's essential to load and prepare your data. We'll use the Billboard Christmas Songs dataset. This dataset includes information about songs that appeared on the Billboard Hot 100 chart.

Let's load the dataset and ensure our date field (weekid) is in the correct format using Pandas:

```Python

import pandas as pd

# Load the Billboard Christmas Songs dataset
df = pd.read_csv('billboard_christmas.csv')

# Convert 'weekid' column to datetime format
df['weekid'] = pd.to_datetime(df['weekid'])

# Display the first few rows of the dataframe
print(df.head())
```
The output will be:

```Plain text

      weekid             song      performer  peak_position  year
0 2023-10-01     Jingle Bells  Michael Bublé              1  2023
1 2023-10-08  White Christmas    Bing Crosby              2  2023
2 2023-10-15   Last Christmas          Wham!              3  2023
3 2023-10-22        Mistletoe  Justin Bieber              4  2023
4 2023-10-29    Santa Tell Me  Ariana Grande              5  2023
```
This output is a simplified display of the dataset's structure, showcasing its columns and a few rows. It ensures our weekid column is properly formatted as datetime, essential for accurate time-based visualizations.

Converting weekid to datetime is crucial for accurate time-based plotting, allowing us to examine trends over the years.

## Creating a Line Chart with Plotly Express

Now that our data is ready, we can create visualizations that reveal trends within the dataset.

Our first visualization is a line chart that displays the number of unique Christmas songs per year on the Billboard Hot 100. This chart helps us understand trends over time.

```Python

import plotly.express as px

# Aggregate data to get yearly counts of unique songs
yearly_songs = df.groupby('year')['song'].nunique().reset_index()

# Create a line chart
fig = px.line(yearly_songs, 
              x='year', 
              y='song',
              title='Christmas Songs on Billboard Hot 100 by Year')
```

Output:

![image](https://github.com/user-attachments/assets/16ac4d0d-87ea-4588-894c-a833591205c4)


## Creating a Scatter Plot with Plotly Express
Next, we have a scatter plot illustrating the peak positions of songs over time, offering insights into song performance throughout the years.

```Python

# Generate a scatter plot of all the peak positions over time
fig = px.scatter(df,
                 x='weekid',
                 y='peak_position',
                 color='song',
                 title='Peak Positions Over Time')


# Hide the legend
fig.update_layout(
    yaxis=dict(autorange="reversed"),
    showlegend=False)
```
Notes:

Reversing the Y axis because a lower number is better so we want it near the top
Hiding the legend because it is noisy. You can hover over the plot to get details instead

Output:
![image](https://github.com/user-attachments/assets/79096319-7035-46cd-93d0-d407d397b3bd)


## Creating a Bar Chart with Plotly Express

Lastly, a bar chart ranks performers by the number of unique songs that charted, highlighting the most successful artists. Each of these visualizations serves a distinct purpose, and together they provide a comprehensive view of the dataset.

```Python

# Find top performers
top_performers = df.groupby('performer')['song'].nunique().sort_values(ascending=False).head(10)

# Create a bar chart
fig = px.bar(x=top_performers.index,
             y=top_performers.values,
             title='Top 10 Christmas Song Performers',
             color_discrete_sequence=['darkred'])

# Set y-axis ticks to integers
fig.update_layout(yaxis=dict(dtick=1))
```
Each of these visualizations serves a distinct purpose and together they provide a comprehensive view of the dataset.

Note: We set the Y axis to have integer ticks to avoid partial values.

Output:
![image](https://github.com/user-attachments/assets/e464c910-e473-4a8a-ac80-7f7f3e4dac30)


