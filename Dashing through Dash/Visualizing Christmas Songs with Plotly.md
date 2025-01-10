# Hands-on Project: Visualizing Christmas Songs with Plotly and Dash

Dataset Preparation
Welcome to this hands-on lesson where we'll dive into visualizing Christmas song data using Plotly and Dash. We'll start by preparing the dataset, which forms the backbone for any data visualization. Here, we will use the Billboard Christmas Songs dataset.

First, let's load the dataset and ensure the date columns are in the correct format for a time-series analysis. This involves using pandas, a powerful data manipulation library in Python.

Python
Copy to clipboard
import pandas as pd

# Load the dataset
df = pd.read_csv('billboard_christmas.csv')

# Convert the 'weekid' column to datetime
df['weekid'] = pd.to_datetime(df['weekid'])
This code snippet reads the CSV file containing our dataset and converts the date column weekid to a datetime object. Converting to datetime is crucial for proper time-series operations, such as plotting trends over time. Now, let's move on to aggregating our data.

Next, we'll aggregate our data to find out the number of unique Christmas songs per year. We use groupby() to group data by year and agg() to count unique songs and calculate the average week position.

Python
Copy to clipboard
# Aggregate data: count unique songs per year
yearly_songs = df.groupby('year').agg({
    'song': 'nunique',
    'week_position': 'mean'
}).reset_index()

print(yearly_songs.head())
With this aggregation, we have a dataframe yearly_songs that tells us how many unique Christmas songs were charted each year along with their average positions. This sets the stage for visualizing how Christmas music trends have changed over the years.

Plain text
Copy to clipboard
   year  song  week_position
0  1958     4      64.125000
1  1959     6      67.000000
2  1960    12      60.636364
3  1961     7      53.935484
4  1962    10      58.384615
Creating a Line Chart with Plotly
Now that our data is prepared, let's visualize it using Plotly. We'll create a line chart, which is perfect for showing trends over time.

First, ensure to import Plotly Express, and Dash, as it simplifies many high-level charting tasks.

Python
Copy to clipboard
import plotly.express as px
from dash import Dash, html, dcc

# Create the main line chart
fig = px.line(
    yearly_songs,
    x='year',
    y='song',
    title='Christmas Songs on the Billboard Hot 100 (1958-2024)'
)
# Initialize the Dash app
app = Dash(__name__)

app.layout = html.Div([
    dcc.Graph(figure=fig)
])

# Run the app
if __name__ == '__main__':
    app.run_server(debug=True, host='0.0.0.0', port=3000)
In this initial chart, the x-axis represents years and the y-axis represents the unique number of songs. Plotly's px.line function does a lot with minimal code, automatically interpreting the data we pass in to produce a clear, concise graph. Using dcc.Graph allows us to host the figure right inside the layout.

This simple HTML output allows us to view the chart in a web browser, making it an interactive and sharable visualization. Time to enhance our chart using hover information for more in-depth insights.

Enhancing Chart with Hover Information
A significant advantage of Plotly is its interactivity, especially the capability to provide detailed hover information. Let's add more information that appears when we hover over each data point.

Python
Copy to clipboard
# Adding hover info
fig.update_traces(
    line_color=COLORS['red'],
    line_width=3,
    hovertemplate='<b>Year: %{x}</b><br>Unique Songs: %{y}<br>Avg Position: %{customdata:.1f}<br>',
    customdata=yearly_songs['week_position']
)
With customdata, we pass additional data, which in this case is the average position of songs during the year, to be displayed in the hover. This enhancement provides dynamic insights, encouraging users to explore trends more interactively.

Applying Festive Styling
Our chart should not only be informative but also visually appealing. Let’s apply some festive styling using a predefined palette to represent the Christmas theme.

Python
Copy to clipboard
# Define festive styling
fig.update_layout(
    plot_bgcolor=COLORS['white'],
    paper_bgcolor=COLORS['white'],
    title_font_color=COLORS['green'],
    xaxis=dict(
        showgrid=True,
        gridcolor='lightgray',
        tickmode='linear',
        dtick=5,
        title='Year',
        title_font_color=COLORS['green']
    ),
    yaxis=dict(
        showgrid=True,
        gridcolor='lightgray',
        zeroline=True,
        zerolinecolor='lightgray',
        title='Number of Different Christmas Songs',
        title_font_color=COLORS['green']
    )
)
These changes include setting background colors and axis titles to reflect a festive theme. Christmas colors enhance the storytelling element, making the visualization more engaging and thematic. Save the updated chart as HTML again.

This gives the viewer a visual experience that's both festive and informative.

Building the Dash Application
To make our visualization widely accessible and interactive, can use Dash to build components with our Plotly figure embedded.embedded.

Python
Copy to clipboard
# Define the layout
app.layout = html.Div([
    html.H1(
        '🎄 Christmas Songs Through the Years 🎅', 
        style={
            'textAlign': 'center',
            'color': COLORS['white'],
            'padding': '20px',
            'backgroundColor': COLORS['red'],
            'borderRadius': '10px',
            'margin': '20px'
        }
    ),
    html.Div(
        dcc.Graph(figure=fig),
        style={
            'padding': '20px',
            'backgroundColor': COLORS['white'],
            'borderRadius': '10px',
            'border': f'2px solid {COLORS["green"]}',
            'margin': '20px'
        }
    )
], style={
    'backgroundColor': COLORS['holly'],
    'padding': '20px',
    'fontFamily': 'Arial, sans-serif',
    'minHeight': '100vh'
})
This code sets up a basic Dash application with a header and graph display. The HTML Div components are styled to match the Christmas theme, enhancing the visual appeal.

Output


