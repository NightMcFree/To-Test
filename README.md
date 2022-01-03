import dash                     
from dash.dependencies import Input, Output, State
import dash_core_components as dcc
import dash_html_components as html
import plotly.express as px 
import pandas as pd     

df = pd.read_csv(r"C:\Users\Hoang Dao\Desktop\TEST.csv")
df["Time"] = pd.to_datetime(df["Time"])
df["Time"] = df["Time"].dt.hour

print(df.head())


external_stylesheets = ['https://codepen.io/chriddyp/pen/bWLwgP.css']
app = dash.Dash(__name__, external_stylesheets=external_stylesheets)

app.layout = html.Div([
    html.H1("Thống Kê Lấy Hàng", style={"textAlign":"center"}),
    html.Hr(),
    html.P("Choose Delivery Status:"),
    html.Div(html.Div([
      dcc.Dropdown(id='status-type', clearable=True,
                     options=[{'label': x, 'value': x} for x in
                     df["status_type"].unique()],multi = True ),
    ],className="two columns"),className="row"),

    html.Div(id="output-div", children=[]),
])

@app.callback(Output(component_id="output-div", component_property="children"),
              Input(component_id="status-type", component_property="value"),)
      
def make_graphs(status_chosen):
    # HISTOGRAM
    df_hist = df[df["status_type"]==status_chosen]
    fig_hist = str (px.histogram(df_hist, x="status_type"))
    fig_hist.update_xaxes(categoryorder="total descending")
    
    return [
        html.Div([
            html.Div([dcc.Graph(figure= str(fig_hist))], className="six columns")]
            ,className="row"
            )]

if __name__ == '__main__':
    app.run_server(debug=True, use_reloader=False)
