# Analista-de-dados-Rocket-Houses.py
Projeto analista de dados primeiros passos Rocket Houses
#Bibliotecas
import pandas as pd
import matplotlib.pyplot as plt

#Extração
data = pd.read_csv('projeto/kc_house_data.csv')

#Transformacao
data = data.drop(columns=['sqft_living15', 'sqft_lot15'])
data['house_age']='null'
data['condition_type']='null'
data.loc[data['condition']<=2, 'condition_type']='bad'
data.loc[(data['condition'] == 3) | (data['condition'] == 4),'condition_type']= 'regular'
data.loc[data['condition']==5, 'condition_type']='good'
data.loc[data['date']>"2014-01-01", 'house_age']='new_house'
data.loc[data['date']<'2014-01-01','house_age']='old_house'
data.loc[data['bedrooms']==1, 'dormitory_type']='studio'
data.loc[data['bedrooms']==2, 'dormitory_type']='apartament'
data.loc[data['bedrooms']>2, 'dormitory_type']='house'
from matplotlib import gridspec

fig=plt.figure(figsize=(20,20))
specs = gridspec.GridSpec( ncols=2, nrows=2, figure=fig)
ax1=fig.add_subplot (specs[0,:])
ax2=fig.add_subplot (specs[1,0])
ax3=fig.add_subplot(specs[1,1])
#Grafico 1
data ['year'] =pd.to_datetime(data['date']).dt.year
by_year= data[['price', 'year']].groupby('year').sum().reset_index()
ax1.bar(by_year['year'],by_year['price'])
#grafico 2
data ['day'] =pd.to_datetime(data['date'])
by_day= data[['price', 'day']].groupby('day').mean().reset_index()
ax2.plot(by_day['day'],by_day['price'])
#Grafico 3
data ['year_week'] =pd.to_datetime(data['date']).dt.strftime('%Y-%U')
by_year_week= data[['price', 'year_week']].groupby('year_week').mean().reset_index()
ax3.plot(by_year_week['year_week'],by_year_week['price'])
plt.show()
import plotly.express as px
houses =data[['id','lat','long','price']]

fig= px.scatter_mapbox(houses,
                   lat = 'lat' ,
                   lon = 'long' ,
                   size = 'price' ,
                   color_continuous_scale=px.colors.cyclical.IceFire,
                       size_max=15,
                      zoom=10)

fig.update_layout(mapbox_style='open-street-map')
fig.update_layout(height = 600, margin={'r':0, 't':0, 'b':0})
fig.show()
