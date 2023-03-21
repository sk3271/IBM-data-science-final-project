# Import required libraries
import pandas as pd
import dash
#import dash_html_components as html
#import dash_core_components as dcc
from dash import dcc
from dash import html
from dash.dependencies import Input, Output
import plotly.express as px
import math

# Read the airline data into pandas dataframe
spacex_df = pd.read_csv("spacex_launch_dash.csv")
max_payload = spacex_df['Payload Mass (kg)'].max()
min_payload = spacex_df['Payload Mass (kg)'].min()
max_ = math.ceil(max_payload*1.1/500)*500
# Create a dash application
app = dash.Dash(__name__)
launch_site = ["ALL"]
launch_site.extend(spacex_df['Launch Site'].unique())

#for i in range(b.shape[0]):
#    print(b.iloc[i, 'Launch Site'])
#print(launch_site)
# Create an app layout
app.layout = html.Div(children=[html.H1('SpaceX Launch Records Dashboard',
                                        style={'textAlign': 'center', 'color': '#503D36',
                                               'font-size': 40}),
                                # TASK 1: Add a dropdown list to enable Launch Site selection
                                # The default select value is for ALL sites
                                # dcc.Dropdown(id='site-dropdown',...)
                                html.P("Launch Site:", style={'font-size': 20}),
                                dcc.Dropdown(id = 'site-dropdown', options = [{'label': i, 'value': i} for i in launch_site], 
                                value = 'ALL', placeholder = "Select a launch site here", searchable = True, style={'font-size': 20}),
                                html.Br(),
                                # TASK 2: Add a pie chart to show the total successful launches count for all sites
                                # If a specific launch site was selected, show the Success vs. Failed counts for the site
                                html.Div(dcc.Graph(id='success-pie-chart')),
                                html.Br(),

                                html.P("Payload range (Kg):", style={'font-size': 20}),
                                # TASK 3: Add a slider to select payload range
                                dcc.RangeSlider(min=0, max=max_, step = 1000,
                                value = [min_payload, max_payload], id='payload-slider'),

                                # TASK 4: Add a scatter chart to show the correlation between payload and launch success
                                html.Div(dcc.Graph(id='success-payload-scatter-chart')),
                                ])

# TASK 2:
# Add a callback function for `site-dropdown` as input, `success-pie-chart` as output
@app.callback(Output(component_id = 'success-pie-chart', component_property='figure'),
               Input(component_id = 'site-dropdown', component_property='value'))
def get_pie_chart(site):
    if site == 'ALL':
        new_df3 = spacex_df.groupby('Launch Site', as_index = False)['class'].sum()
        fig = px.pie(new_df3, values = 'class', names = 'Launch Site', title = 'Success Pie Chart')
        fig.update_layout(legend_title="Launch Site", font=dict(
        size=18))
        return fig
    else:
        new_df4 = spacex_df[spacex_df['Launch Site'] == site]
        success = new_df4.loc[:, 'class'].sum()
        fail = new_df4.shape[0] - success
        new_df5 = pd.DataFrame(index = [0, 1], columns = ['count', 'class'], data = [[success, 1], [fail, 0]])
        fig = px.pie(new_df5, values = 'count', names = 'class', title = 'Success Pie Chart')
        fig.update_layout(legend_title="Class", font=dict(
        size=18))
        return fig

# TASK 4:
# Add a callback function for `site-dropdown` and `payload-slider` as inputs, `success-payload-scatter-chart` as output
@app.callback(
    Output(component_id = 'success-payload-scatter-chart', component_property = 'figure'),
    [Input(component_id = 'site-dropdown', component_property = 'value'),
    Input(component_id = 'payload-slider', component_property = 'value')])
def get_plot(site, payload):
    if site == 'ALL':
        mask = (spacex_df['Payload Mass (kg)'] < payload[1]) & (spacex_df['Payload Mass (kg)'] > payload[0])
        new_df = spacex_df.loc[mask]
        scatter = px.scatter(new_df, x = 'Payload Mass (kg)', y = 'class', color = "Booster Version Category", title = 'Success vs Payload')
        scatter.update_traces(marker_size=10)
        scatter.update_layout(yaxis_title = 'Class', legend_title="Booster Version Category", font=dict(
        size=18))
        return scatter
    else:
        mask = (spacex_df['Payload Mass (kg)'] < payload[1]) & (spacex_df['Payload Mass (kg)'] > payload[0])
        new_df = spacex_df.loc[mask]
        new_df2 =new_df[new_df['Launch Site'] == site]
        scatter = px.scatter(new_df2, x = 'Payload Mass (kg)', y =  'class', color = 'Booster Version Category', title = 'Success vs Payload')
        scatter.update_traces(marker_size=10)
        scatter.update_layout(yaxis_title = 'Class', legend_title="Booster Version Category", font=dict(
        size=18))
        return scatter

# Run the app
if __name__ == '__main__':
    app.run_server()
