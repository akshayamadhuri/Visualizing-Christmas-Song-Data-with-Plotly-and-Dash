# Using the Plotly Graph Objects API

we're transitioning from the Plotly Express API to the more feature-rich (but also more complex) Plotly Graph Objects API. Understanding the difference between these two will enhance your data visualization capabilities:

`Plotly Express`: This high-level interface allows users to create visualizations quickly and easily, auto-managing many details for standard plots with minimal code.

`Plotly Graph Objects`: This lower-level interface offers advanced customization and flexibility. Here, each component of the visualization is an object, enabling detailed control over styling, interactions, and functionality.

## Why use Plotly Graph Objects?

`Advanced Customization`: For visualizations requiring complex styling, enhanced interactivity, or combining multiple chart types, Graph Objects provide necessary control.

`Fine-tuning`: Allows for detailed layout configurations like axis properties, hover labels, and background styles that go beyond default settings in Plotly Express.

`Integration Features`: Facilitates easier integration of interactive elements when developing dashboards or handling complex data structures.

`Comprehensive Feature Set`: Unlocks the full capabilities of Plotly for professional-grade visualizations where precision and detail are essential.

By the end of this lesson, you'll know how to craft a refined scatter plot using the Billboard Christmas Songs dataset, leveraging interactivity and customization to gain deeper insights. Let's dive into the robust feature set of Plotly Graph Objects to tell compelling data stories effectively.

## Introduction to Plotly Graph Objects

Let's start with understanding the transition to Plotly Graph Objects. Unlike Plotly Express, which simplifies the creation of plots, Graph Objects allow for deeper customization and flexibility. Each graph component is an object, offering more control over styling and functionality.

You'll primarily interact with two components:

`go.Figure()`: This creates a new figure for plotting, serving as the canvas.
`go.Scatter()`: This represents the scatter plot trace, where you define data points and various aesthetics.
Let's begin by building our visualization structure with these components:

```Python

import plotly.graph_objects as go
import pandas as pd

# Load cleaned data
df = pd.read_csv('billboard_christmas.csv')
df['weekid'] = pd.to_datetime(df['weekid'])

# Create a new figure
fig = go.Figure()
```
The output of the above code will be a new, empty Plotly graph object called fig, ready to be customized. This part sets the foundation for creating interactive charts. Here, the necessary libraries are imported and a new figure object is initialized without yet adding any data or visual representations.

## Building Customized Visualizations

The true power of Plotly Graph Objects comes when adding traces and customizing them. Traces are graphical representations of data points.

In our example, we'll add a `go.Scatter()` trace to represent songs' chart positions over time. Here's how you can achieve this:

```Python

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
            colorbar=dict(title='Peak Position')
        ),
        text=[f"Song: {song}<br>Performer: {performer}"
              for song, performer in zip(df['song'], df['performer'])],
        hovertemplate="%{text}<br>"
                      "Date: %{x}<br>" +
                      "Position: %{y}<br>" +
                      "Weeks on Chart: %{marker.size}<br>" +
                      "<extra></extra>"
    )
)
```
The output will be a Plotly scatter plot visualizing songs' chart positions with markers sized by weeks on the chart and colored by peak position. This setup effectively showcases the visualization capabilities of Plotly Graph Objects, including detailed customization of plot elements.

Enhancing Interactivity with Detailed Hover Information
Interactivity is a key strength of Plotly Graph Objects. You can enrich user experience by customizing the hover information. Here’s how you do it:

```Python

fig.add_trace(
    go.Scatter(
        # Rest of the code...
        
        # Customizing Hover Information
        hovertemplate="%{text}<br>"
                      "Date: %{x}<br>" +
                      "Position: %{y}<br>" +
                      "Weeks on Chart: %{marker.size}<br>" +
                      "<extra></extra>"
    )
)
```
With updating the hover information, the plot now provides users with detailed insights on hovering over the markers, including the song title, performer, chart position, week, and weeks on the chart, giving them rich contextual data about each point without cluttering the visual field. This feature exemplifies how Plotly can make data sets more interactive and engaging.

## Layout Customization and Aesthetic Improvements

Once you've enhanced trace interactivity, the next step is to refine the layout for visual clarity and aesthetic appeal. Adjusting layout properties like titles, labels, and grid colors can greatly impact readability.

Here's how to finalize your figure's layout:

```Python

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
    ),
    
    # Static Height and Width will not resize in browser
    width=600,
    height=480
)
```
After updating the layout, the visualization now has a clearly defined title, axis labels, and customized hover labels, which enhance user interaction by making the visual data more digestible and structured. The reversal of the y-axis visually aligns with the common understanding that a lower chart position number represents a higher ranking.

## Saving and Sharing Your Interactive Visualization

Similarly to Plotly Express, Plotly Graph Objects allows you to save and share your interactive visualizations. The charts remain interactive when saved as HTML files, preserving features like hover interactions.

```Python

# Save to HTML
fig.write_html('templates/chart.html')
```
In this snippet, an HTML file named 'chart.html' is created in the 'templates' directory. This file houses the fully interactive Plotly chart, which maintains all interactive elements such as detailed tooltips when you hover over data points. It can be seamlessly integrated into web pages or shared directly, similar to how you'd work with Plotly Express.

Output
![image](https://github.com/user-attachments/assets/bc417c98-7495-41f4-855e-07a29f4a75af)



