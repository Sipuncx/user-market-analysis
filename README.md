# user-market-analysis
import pandas as pd
import plotly.express as px
import plotly.io as pio
import plotly.graph_objects as go
pio.templates.default = "plotly_white"
df= pd.read_csv("C:\\Users\\HP\\Downloads\\bounce-rate.csv")
df.head()
df.isnull().sum()
df.info()
The Avg. Session Duration and Bounce Rate columns are not numerical. We need to convert them into appropriate data types for this task. Here’s how we can prepare our data
df['Avg. Session Duration'] = df['Avg. Session Duration'].str[1:]
df['Avg. Session Duration'] = pd.to_timedelta(df['Avg. Session Duration'])
df['Avg. Session Duration'] = df['Avg. Session Duration'] / pd.Timedelta(minutes=1)
df['Bounce Rate'] = df['Bounce Rate'].str.rstrip('%').astype('float') 
df.describe()
data_without_id = df.drop('Client ID', axis=1) # Exclude 'Client Id' column from the dataset
# Calculate the correlation matrix
correlation_m# Visualize the correlation matrix
correlation_fig = px.imshow(correlation_matrix, 
                            labels=dict(x='Features', 
                                        y='Features', 
                                        color='Correlation'))
correlation_fig.update_layout(title='Correlation Matrix')
correlation_fig.show()atrix = data_without_id.corr()
# Define the thresholds for high, medium, and low bounce rates
high_bounce_rate_threshold = 70
low_bounce_rate_threshold = 30
# Segment the clients based on bounce rates
df['Bounce Rate Segment'] = pd.cut(df['Bounce Rate'], 
                                     bins=[0, low_bounce_rate_threshold, 
                                           high_bounce_rate_threshold, 100],
                                   labels=['Low', 'Medium', 'High'], right=False)
# Count the number of clients in each segment
segment_counts = df['Bounce Rate Segment'].value_counts().sort_index()
 #Visualize the segments
segment_fig = px.bar(segment_counts, labels={'index': 'Bounce Rate Segment', 
                                             'value': 'Number of Clients'},
                     title='Segmentation of Clients based on Bounce Rates')
segment_fig.show()
# Calculate the average session duration for each segment
segment_avg_duration = df.groupby('Bounce Rate Segment')['Avg. Session Duration'].mean()
# Create a bar chart to compare user engagement
engagement_fig = go.Figure(data=go.Bar(
    x=segment_avg_duration.index,
    y=segment_avg_duration,
    text=segment_avg_duration.round(2),
    textposition='auto',
    marker=dict(color=['#2ECC40', '#FFDC00', '#FF4136'])
))
engagement_fig.update_layout(
    title='Comparison of User Engagement by Bounce Rate Segment',
    xaxis=dict(title='Bounce Rate Segment'),
    yaxis=dict(title='Average Session Duration (minutes)'),
)
# Calculate the total session duration for each client
df['Total Session Duration'] = df['Sessions'] * df['Avg. Session Duration']

# Sort the DataFrame by the total session duration in descending order
df_sorted = df.sort_values('Total Session Duration', ascending=False)

# the top 10 most loyal users
df_sorted.head(10)
# Create a scatter plot to analyze the relationship between bounce rate and avg session duration
scatter_fig = px.scatter(df, x='Bounce Rate', y='Avg. Session Duration',
                         title='Relationship between Bounce Rate and Avg. Session Duration', trendline='ols')

scatter_fig.update_layout(
    xaxis=dict(title='Bounce Rate'),
    yaxis=dict(title='Avg. Session Duration')
)

scatter_fig.show()
# Define the retention segments based on number of sessions
def get_retention_segment(row):
    if row['Sessions'] >= 32: # 32 is mean of sessions
        return 'Frequent Users'
    else:
        return 'Occasional Users'

# Create a new column for retention segments
df['Retention Segment'] = df.apply(get_retention_segment, axis=1)

# Print the updated DataFrame
print(df)
# Calculate the average bounce rate for each retention segment
segment_bounce_rates = df.groupby('Retention Segment')['Bounce Rate'].mean().reset_index()

# Create a bar chart to visualize the average bounce rates by retention segment
bar_fig = px.bar(segment_bounce_rates, x='Retention Segment', y='Bounce Rate',
                 title='Average Bounce Rate by Retention Segment',
                 labels={'Retention Segment': 'Retention Segment', 'Bounce Rate': 'Average Bounce Rate'})

bar_fig.show()
# Count the number of users in each retention segment
segment_counts = df['Retention Segment'].value_counts()

# Define the pastel colors
colors = ['#FFB6C1', '#87CEFA']

# Create a pie chart using Plotly
fig = px.pie(segment_counts, 
             values=segment_counts.values, 
             names=segment_counts.index, 
             color=segment_counts.index, 
             color_discrete_sequence=colors,
             title='User Retention Rate')

# Update layout and show the chart
fig.update_traces(textposition='inside', textinfo='percent+label')
fig.update_layout(showlegend=False)
fig.show()

