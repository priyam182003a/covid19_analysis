# covid19_analysis
"D:\codes\covid19_analysis.ipynb"
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
import plotly.express as px
from plotly.subplots import make_subplots
from datetime import datetime
covid_df=pd.read_csv("/content/covid_19_india (1).csv")
covid_df.head(10)
covid_df.drop(["Sno","Time","ConfirmedIndianNational","ConfirmedForeignNational"], inplace=True, axis=1)
covid_df.head()
covid_df['Date']=pd.to_datetime(covid_df['Date'], format='%Y-%m-%d')
covid_df.head()
#Active Cases

covid_df['Active_Cases']=covid_df['Confirmed']- (covid_df['Cured']+covid_df['Deaths'])
covid_df.tail()
statewise=pd.pivot_table(covid_df,values=["Confirmed","Cured","Deaths"],index= "State/UnionTerritory", aggfunc=max)
statewise["Recovery Rate"]=statewise["Cured"]*100/statewise["Confirmed"]
statewise["Mortality Rate"]=statewise["Deaths"]*100/statewise["Confirmed"]
statewise=statewise.sort_values(by="Confirmed",ascending=False)
statewise.style.background_gradient(cmap="gist_earth")

top_10_active_cases=covid_df.groupby(by="State/UnionTerritory").max()[["Active_Cases","Date"]].sort_values(by="Active_Cases",ascending=False).reset_index()
fig=plt.figure(figsize=(16,9))
plt.title("Top 10 States with most active cases in India", size=25)
ax=sns.barplot(data=top_10_active_cases.iloc[:10],y="Active_Cases",x="State/UnionTerritory",linewidth=2,edgecolor='orange')
plt.xlabel("States")
plt.ylabel("Total Active Cases")
plt.show()

#top 10 active cases with high deaths

top_10_deaths=covid_df.groupby(by="State/UnionTerritory").max()[["Deaths","Date"]].sort_values(by="Deaths",ascending=False).reset_index()
fig=plt.figure(figsize=(18,9))
plt.title("Top 10 States with most deaths in India", size=25)
ax=sns.barplot(data=top_10_deaths.iloc[:10],y="Deaths",x="State/UnionTerritory",linewidth=2,edgecolor='black')
plt.xlabel("States")
plt.ylabel("Total Deaths Cases")
plt.show()

 Growth Trend
fig=plt.figure(figsize=(12,6))
ax=sns.lineplot(data = covid_df[covid_df['State/UnionTerritory'].isin(['Maharastra','Karnataka','TamilNadu','Kerala','Uttar Pradesh'])])
ax.set_title("Top 10 Affected States in India",size=18)

vaccine_df=pd.read_csv("/content/covid_vaccine_statewise.csv.zip")
vaccine_df.head()
vaccine_df.rename(columns={'Updated On' : 'Vaccine_Date'},inplace=True)
vaccine_df.head(10)
vaccine_df.isnull().sum()
vaccination=vaccine_df.drop(columns=['Sputnik V (Doses Administered)', 'AEFI', '18-44 Years (Doses Administered)', '45-60 Years (Doses Administered)', '60+ Years (Doses Administered)'], axis=1)
vaccination.head()
#male and female vaccination

male=vaccination["Male(Individuals Vaccinated)"].sum()
female=vaccination["Female(Individuals Vaccinated)"].sum()
px.pie (names=["Male","Female"], values=[male,female], title = " Male and Female Classification")

# remove the vaccine from state-India

vaccine=vaccine_df[vaccine_df.State!='India']
vaccine

vaccine_df.rename(columns={"Total Individuals Vaccinated":"Total"},inplace=True)
vaccine_df.head()

 #most vaccinated states

max_vac=vaccine_df.groupby('State')['Total'].sum().to_frame('Total')
max_vac=max_vac.sort_values('Total',ascending=False)[:5]
max_vac

fig=plt.figure(figsize=(10,5))
plt.title("Top 5 Vaccinated States in India", size=25)
x=sns.barplot(data=max_vac.iloc[:10],y=max_vac.Total,x=max_vac.index,linewidth=2,edgecolor='black')
plt.xlabel("States")
plt.ylabel("Vaccinaion")
plt.show()
