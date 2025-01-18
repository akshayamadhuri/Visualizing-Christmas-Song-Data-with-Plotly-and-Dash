# Advanced Features in Dashboards

## Creating an Interactive Data Table
First, let's create a summary DataFrame tailored for tabular display. To make this table insightful, we'll aggregate key metrics, such as the minimum peak position and maximum weeks on the chart for each song. We'll also record the first and last years each song appeared.

Here's how you can achieve this:

```Python

import pandas as pd

# Load data
df = pd.read_csv('billboard_christmas.csv')

# Create summary dataframe for table
table_df = df.groupby(['song', 'performer']).agg({
    'peak_position': 'min',
    'weeks_on_chart': 'max',
    'year': ['min', 'max']
}).reset_index()
```

# Rename columns for clarity
table_df.columns = ['Song', 'Performer', 'Peak Position', 'Weeks on Chart', 'First Year', 'Last Year']
Next, let's integrate this data into your dashboard with dash_table.DataTable. This will allow users to interact with data in a table format:

```Python

from dash import dash_table, dcc, html, Dash

# Initialize app
app = Dash(__name__)

# Data table setup
app.layout = html.Div([
    dash_table.DataTable(
        id='song-table',
        columns=[{"name": i, "id": i} for i in table_df.columns],
        data=table_df.to_dict('records'),
        style_table={'overflowX': 'auto'},
        style_header={
            'backgroundColor': '#165B33',
            'color': 'white',
            'fontWeight': 'bold'
        },
        style_cell={
            'textAlign': 'left',
            'padding': '10px'
        },
        page_size=10,
        sort_action='native',
        filter_action='native',
        export_format='csv'
    )
])
```
In this layout, we specify column headers and transform the summarized DataFrame to a dictionary format. The table style adheres to our Christmas theme using the defined color scheme.

Here is what we get so far:

![image](https://github.com/user-attachments/assets/7697620e-8ff0-42e1-a992-886d27be5e61)




## Implementing Search Functionality: Search Input Field

Having a search feature dramatically improves user data exploration, making it a vital element in our dashboard. We'll add a search bar using dcc.Input to filter the displayed data:

```Python

# Add Search Box to the Layout
app.layout = html.Div([
    dcc.Input(
        id='search-box',
        type='text',
        placeholder='Search songs or artists...',
        style={
            'width': '100%',
            'padding': '10px',
            'marginBottom': '10px',
            'borderRadius': '5px',
            'border': f'2px solid #165B33'
        }
    ),
    dash_table.DataTable(
        id='song-table',
        columns=[{"name": i, "id": i} for i in table_df.columns],
        data=table_df.to_dict('records'),
        style_table={'overflowX': 'auto'},
        ...
    )
])
```
Our intermediate dashboard looks like this (as you can see, we just added the search field, it doesn't work yet):

![image](https://github.com/user-attachments/assets/95422fe2-9327-4007-8ad8-a62a9cbdebde)



## Implementing Search Functionality: Making it Functional

Now, let's implement a callback function that updates the table based on the search term. This responsiveness is driven by @callback, which listens for changes.

```Python

from dash import Input, Output

# Callback for search functionality
@app.callback(
    Output('song-table', 'data'),
    Input('search-box', 'value')
)
def update_table(search_term):
    if not search_term:
        return table_df.to_dict('records')  # Return all if search is empty

    # Filter dataset based on search term
    filtered_df = table_df[
        table_df['Song'].str.contains(search_term, case=False, na=False) |
        table_df['Performer'].str.contains(search_term, case=False, na=False)
    ]

    return filtered_df.to_dict('records')
```
This callback updates the table as the user types the search term, providing a seamless exploration experience.

As you can see, the search is functional now!
![image](https://github.com/user-attachments/assets/90b74bf9-a5d7-4910-b816-3858f396b487)




Enhancing User Experience with Data Conditionally
In the final touch, let's enhance the table's readability by using conditional styling. This feature highlights alternating rows using the Christmas color scheme to make data easy to read:

Python
Copy to clipboard
# Add this into your DataTable component
dash_table.DataTable(
    ...
    style_data_conditional=[{
        'if': {'row_index': 'odd'},
        'backgroundColor': '#F6E7E7'  # Light red background for odd rows
    }]
)
This not only visually appeals to your users but also aids in distinguishing rows, making data consumption more straightforward. Here we go, our final result looks amazing!



