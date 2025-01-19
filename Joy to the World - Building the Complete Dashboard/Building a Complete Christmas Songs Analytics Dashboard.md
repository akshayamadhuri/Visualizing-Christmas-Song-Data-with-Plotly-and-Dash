# Final Project: Building a Complete Christmas Songs Analytics Dashboard

## Data Preparation with Pandas
First, let's focus on preparing our data using pandas. We will be working with the Billboard Christmas Songs dataset, aiming to gather insights about the songs that have charted over the years. We'll start by loading the data and preparing it for visualization.

Firstly, let's load the dataframe, convert relevant columns to their necessary types, and derive a summary of the top songs:

```Python

import pandas as pd

# Load and prepare data
df = pd.read_csv('billboard_christmas.csv')
df['weekid'] = pd.to_datetime(df['weekid'])

# Prepare summary data
top_songs = df.groupby(['song', 'performer']).agg({
    'peak_position': 'min',
    'weeks_on_chart': 'max',
    'year': ['min', 'max']
}).reset_index()

top_songs.columns = ['Song', 'Performer', 'Peak Position', 'Weeks on Chart', 'First Year', 'Last Year']
top_songs.sort_values('Peak Position', inplace=True)
```
We're interested in each song's peak position, the number of weeks it stayed on the chart, and the range of years it appeared. With our top_songs dataframe, we've derived clean and meaningful summary data. This gives us a strong foundation upon which to build our visualizations.

## Building the Dashboard: Structure
Let's break down the four visualizations that will make up our dashboard. Each visualization provides a unique insight into the dataset:

`Timeline Chart`: This line chart will showcase the number of Christmas songs making it to the Billboard chart over the years. It gives us a historical perspective on seasonal song popularity.

`Top Songs Table`: A data table highlighting the top-performing Christmas songs based on their peak positions across several artists. We'll list them in ascending order based on their chart position.

Seasonal Performance Chart: This bar chart will focus on analyzing the average chart positions of songs on a monthly basis, revealing seasonal trends in song performance.

`Artist Comparison Chart`: This line chart allows users to select artists and compare their songs' performances over time, shedding light on which artists hold the most Christmas chart clout.

These graphs provide a comprehensive overview of the dataset and help reveal trends and patterns in the musical landscape.

## Building the Layout
To efficiently present our visual insights, we need a structured layout. We'll utilize both HTML and Dash components to design our dashboard for interactivity and user engagement.

First, we create our app layout using Dash, including headers and the arrangement of our visual components:

```Python

from dash import Dash, html, dcc, dash_table

# Initialize app
app = Dash(__name__)

# Layout
app.layout = html.Div([
    html.Div([
        html.H1('🎄 Christmas Billboard Hits Dashboard 🎅', 
                style={'color': COLORS['white'], 'textAlign': 'center'})
    ], style={
        'backgroundColor': COLORS['red'],
        'padding': '20px',
        'borderRadius': '10px',
        'margin': '10px'
    }),

    html.Div([
        html.Div([
            html.Div([
                html.H3('Christmas Songs Timeline 📊', style={'color': COLORS['green']}),
                dcc.Graph(id='timeline-chart')
            ], style={'backgroundColor': COLORS['white'], 'padding': '20px', 'borderRadius': '10px', 'marginBottom': '20px'}),

            html.Div([
                html.H3('Top Christmas Songs 🎵', style={'color': COLORS['green']}),
                dash_table.DataTable(
                    id='top-songs-table',
                    columns=[{"name": i, "id": i} for i in top_songs.columns],
                    data=top_songs.nsmallest(10, 'Peak Position').to_dict('records'),
                    style_header={'backgroundColor': COLORS['green'], 'color': 'white'},
                    style_cell={'textAlign': 'left'},
                    style_data_conditional=[{'if': {'row_index': 'odd'}, 'backgroundColor': COLORS['light_red']}],
                    export_format='csv'
                )
            ], style={'backgroundColor': COLORS['white'], 'padding': '20px', 'borderRadius': '10px'})
        ], style={'width': '60%', 'display': 'inline-block', 'verticalAlign': 'top'}),

        html.Div([
            html.Div([
                html.H3('Seasonal Performance 🌟', style={'color': COLORS['green']}),
                dcc.Graph(id='seasonal-chart')
            ], style={'backgroundColor': COLORS['white'], 'padding': '20px', 'borderRadius': '10px', 'marginBottom': '20px'}),

            html.Div([
                html.H3('Artist Comparison 🎤', style={'color': COLORS['green']}),
                dcc.Dropdown(
                    id='artist-dropdown',
                    options=[{'label': artist, 'value': artist} for artist in df['performer'].unique()],
                    value=[df['performer'].iloc[0]],
                    multi=True,
                    style={'marginBottom': '10px'}
                ),
                dcc.Graph(id='artist-chart')
            ], style={'backgroundColor': COLORS['white'], 'padding': '20px', 'borderRadius': '10px'})
        ], style={'width': '38%', 'display': 'inline-block', 'marginLeft': '2%'})
    ], style={'padding': '20px'})
])
```
Here, we defined containers for each visualization and interactive element, setting the stage for dynamic content updates.

Implementing and Explaining Callbacks
Dash uses callbacks to empower our dashboard with interactivity — updating charts with user inputs, providing an unparalleled exploration experience.

We'll employ a callback function to link the dropdown selection to the three graphs requiring updates:

```Python

from dash import callback, Input, Output

@callback(
    [Output('timeline-chart', 'figure'),
     Output('seasonal-chart', 'figure'),
     Output('artist-chart', 'figure')],
    [Input('artist-dropdown', 'value')]
)
def update_charts(selected_artists):
    # Timeline chart displaying number of songs per year
    yearly_data = df.groupby('year').agg({'song': 'nunique', 'peak_position': 'min'}).reset_index()
    timeline = px.line(yearly_data, x='year', y='song', title='Christmas Songs on Billboard Over Time')
    timeline.update_traces(line_color=COLORS['red'])

    # Seasonal average positioning by month
    monthly_data = df.groupby(df['weekid'].dt.month)['week_position'].mean().reset_index()
    seasonal = px.bar(monthly_data, x='weekid', y='week_position', title='Average Chart Position by Month')
    seasonal.update_traces(marker_color=COLORS['green'])
    seasonal.update_layout(yaxis_autorange='reversed')

    # Artist performance over time
    artist_data = df[df['performer'].isin(selected_artists)]
    artist_perf = px.line(artist_data, x='weekid', y='week_position', color='performer', title='Artist Performance Over Time')
    artist_perf.update_layout(yaxis_autorange='reversed')

    return timeline, seasonal, artist_perf
```
Can you guess the output? Take your time to understand how this code works!

## Dashboard: The Outcome

Let's look what we finally got at the end! While we've built many different features across the way and not all of them made it to the final preview (otherwise, the dashboard would be too big!), the version of the dashboard we have built in this lesson looks like this:

![image](https://github.com/user-attachments/assets/2360a4b7-d1e9-4f75-8540-397c0ddd7f4b)




With any changes to the artist-dropdown that we make, the dashboard updates the Timeline Chart with songs per year, the Seasonal Chart with monthly trends, and the Artist Chart with performance data based on the selected artists. It fully leverages the power of Dash to create a responsive and interactive dashboard.
