# Wrapping and Gifting Your Plot


## Building the Visualization: Scatter Plot with Custom Markers

Now, let's remind ourselves what the scatter plot looks like.

```Python

import plotly.graph_objects as go

# Create figure
fig = go.Figure()

# Add scatter trace
fig.add_trace(
    go.Scatter(
        x=df['weekid'],
        y=df['week_position'],
        mode='markers',
        marker=dict(
            size=df['weeks_on_chart'],
            sizemode='area',
            sizeref=2 * max(df['weeks_on_chart']) / (40 ** 2),
            color=df['peak_position'],
            colorscale='RdYlGn_r',
            colorbar=dict(title='Peak Position')),
        text=[f"Song: {song}<br>Performer: {performer}"
              for song, performer in zip(df['song'], df['performer'])],
        hovertemplate="%{text}<br>"
                      "Date: %{x}<br>" +
                      "Position: %{y}<br>" +
                      "Weeks on Chart: %{marker.size}<br>" +
                      "<extra></extra>"
    )
)

# Update layout
fig.update_layout(
    title='Song Performance Matrix',
    xaxis_title='Date',
    yaxis_title='Chart Position',
    yaxis=dict(
        autorange="reversed",
        gridcolor='lightgray',
    ),
    xaxis=dict(gridcolor='lightgray'),
    plot_bgcolor='white',
    hoverlabel=dict(
        bgcolor="white",
        font_size=12,
    )
)
```
In this plot, we use various marker properties to convey additional information, such as size to indicate weeks_on_chart and color to represent peak_position. These customizations enrich the plot by visualizing complex data points in a straightforward manner.

Customizing layout properties like axis titles, direction, and background colors can significantly impact readability and visual balance, ensuring your audience focuses on the insights.

## Exporting the Masterpiece: Saving and Sharing image files

Now, let's save our work in different formats to suit various purposes. The write_image method from Plotly allows for exporting your visualization into multiple image formats.

```Python

# Save as different Image formats
fig.write_image("static_chart.svg")  # SVG format
fig.write_image("static_chart.pdf")  # PDF format
fig.write_image(
    "custom_size_chart.jpg",  # JPG format
    width=1200,
    height=800,
    scale=2
)
```
Here's how the parameters work:

`"static_chart.svg"`: Specifies the file name and format for saving. Here, it's saved as a Scalable Vector Graphics (SVG) file, which is ideal for high-quality scalable images.
`"static_chart.pdf"`: By changing the file extension, you can export your plot as a PDF document.
`"custom_size_chart.jpg"`: Exports the plot as a JPG image. Alongside, there's customization by adding width=1200 and height=800, defining the image dimensions for higher resolution.
`scale=2`: This parameter enhances the resolution of the JPG image by scaling its dimensions by the provided factor, ensuring clearer images, especially useful for printing purposes.

## Exporting your data using JSON
The JSON format is a versatile option for saving your visualizations, which enables easy sharing and reusability of the plot's data and layout. Here's a detailed look at the code:

```Python

# Save as JSON data
fig_json = fig.to_json()
with open('chart_data.json', 'w') as f:
    f.write(fig_json)
    
# Load from JSON data
with open('chart_data.json', 'r') as f:
    fig_json = f.read()

# Convert JSON to Figure object
fig = go.Figure(json.loads(fig_json))
```

`fig.to_json()`: This method converts the current Figure object into a JSON string, effectively capturing all the data and layout configurations of the plot.
`fig = go.Figure(json.loads(fig_json))`: Here, the JSON string is parsed back into a dictionary using json.loads(fig_json), and then it's passed to go.Figure to recreate the original plot. This step effectively regenerates the plot from the saved JSON data, preserving its structure and properties.

This JSON-based approach is particularly useful when you need to save the visualization for future modifications, share it with collaborators, or export it to another environment for further processing.

## Save to HTML
The HTML format is a powerful option for exporting interactive plots, allowing you to easily share or embed them into web pages. Here's an explanation of the code:

```Python

fig.write_html(
    'templates/chart.html',
    include_plotlyjs="cdn",
    full_html=True
)
```

`fig.write_html('templates/chart.html')`: This method saves the figure as an HTML file in the specified path, in this case, templates/chart.html. The resulting file can be viewed in any web browser, offering an interactive experience of the plot.

`include_plotlyjs="cdn"`: By setting this parameter, the Plotly JavaScript library is linked via a Content Delivery Network (CDN) rather than embedding it directly in the HTML file. This approach significantly reduces the file size and ensures the latest version of Plotly.js is used when the plot is displayed.

`full_html=True`: This parameter specifies that the HTML file should include a complete HTML document structure (DOCTYPE, HTML, HEAD, and BODY tags). It makes the file standalone, ready for direct upload or sharing without requiring any additional HTML context. Setting this to False is useful when you want a small snippet of HTML to embed in another document.

This method is an excellent choice for sharing interactive plots, as they maintain the hover, zoom, and pan capabilities of Plotly visualizations, making your data presentations more dynamic and engaging.
