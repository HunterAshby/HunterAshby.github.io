---
layout: post
title: Covid-19 Visulizations
subtitle: Made using Google Colab and Plotly
gh-repo: HunterAshby/DS19-Unit-1-Build
tags: [Covid-19, Datascience]
---

This is my results from my first build at Lambda School, I chose to do my build on a Covid-19 Dataset, with my code a can generate many diffrent line and bar graphs of my choosing, first I looked at Confirmed cases as a percent of population, so this bar graphs represents that, looking at it you can see that the U.S has have 1.2% of their total population has had covid, that is around 4 million people.

![Bar Graph](https://raw.githubusercontent.com/HunterAshby/DS19-Unit-1-Build/master/Graphs/Bar%20Graph.png)

This line graph is used to show the curve of the epidemic, by showing how many active cases are there currently as you can see Italy has gotten over thier curve, while others have flattend out, and the U.S is climbing rapidly
![Line Graph](https://raw.githubusercontent.com/HunterAshby/DS19-Unit-1-Build/master/Graphs/Line%20Graph.png)

The data really isn't that suprising, if you follow some sort of news you would know that the US is last place in public health in a lot of areas regarding Covid-19.
However the build was pretty difficult for my first one, the hardest challenge I had was how to make it so the charts could be comparable to eachother instead of just a total 
number of cases.
Here is the code I used to generate the graphs


## Bar Graph Code


~~~
def compare_countries_totals(countries, on='Confirmed', how='%'):
  #Will not plot 1 country, or World values
  #Setting up necessary values
  pd.set_option('mode.chained_assignment', None)
  plt_title = ''
  dfi = pd.DataFrame()
  world_pop = df['Population'].sum()

  #Iterates through list of given countries to grab relevent data for graphing
  for i in countries:
    plt_title = plt_title + (i + ' VS ')
    dfi1 = df[df['Country/Region'] == i]
    dfi1['% of Pop'] = dfi1[on]/dfi1['Population']
    dfi = dfi.append(dfi1)

  #Runs if user wants the data to be a percentage of the population 
  #total for comparability, by defult this is true
  if how == '%':
    fig = px.bar(dfi, x='Country/Region', y=dfi['% of Pop']*100, color='Country/Region', 
                 title=plt_title.rstrip(' VS ')  + ' Covid Cases', 
                 labels={'Country/Region': '', 'y' : on +' % of Pop'}, 
                 hover_data=[on, 'Population'])
                 
    fig.update_layout(showlegend=False)

  #Runs if user just wants the total numbers to compare
  else:
    fig = px.bar(dfi, x='Country/Region', y=on, color='Country/Region',
                 title=plt_title.rstrip(' VS ')  + ' Covid Cases', 
                 labels={'Country/Region': '', 'y' : on}, 
                 hover_data=[on, 'Population'])
    
    fig.update_layout(showlegend=False)

  fig.show()
  pd.set_option('mode.chained_assignment', 'warn')
~~~


## Line Graph Code


~~~
#Plotly graph maker
def compare_countries(countries, on='New cases', how='%'):
  #Setting up needed values
  pd.set_option('mode.chained_assignment', None)
  plt_title = ''
  dfi = pd.DataFrame()
  world_pop = df['Population'].sum()
  
  #If the person only passes in one values
  if type(countries) == str:
    #If the person wants to see the value of the worldwide crisis
    if countries == 'World':
      #Runs if user wants the data to be a percentage of the population 
      #total for comparability, by defult this is true
      if how == '%':
        fig = px.line(df_worldwide, x='Date', y=(df_worldwide[on]/world_pop)*100,
                      title='Worldwide Covid Cases', labels={'y': on + ' % of Pop'}, 
                      hover_data=[on])
      else:
        #Else the person will see total values
        fig = px.line(df_worldwide, x='Date', y=df_worldwide[on],
                      title='Worldwide Covid Cases', labels={'y': on}, 
                      hover_data=[on])
    else:
      #Grabs population total for given country
      country_pop = df[df['Country/Region'] == countries]['Population']
      dfi = df_day[df_day['Country/Region'] == countries]
      #Percentage of Population Numbers
      if how == '%':
        fig = px.line(dfi, x='Date', y=(dfi[on]/country_pop.iloc[0])*100,
                      title=countries + ' Covid Cases', labels={'y': on + ' % of Pop'}, 
                      hover_data=[on])
      else:
        #Total numbers
        fig = px.line(dfi, x='Date', y=dfi[on],
                      title=countries + ' Covid Cases', labels={'y': on}, 
                      hover_data=[on])
  else:
    #Builds a new DF called dfi that holds all the info need to graph the specified countries
    for i in countries:
      #Builds the Title of the graph
      plt_title = plt_title + (i + ' VS ') 
      if i == 'World':
        #Worldwide Values
        dfiw = df_worldwide
        dfiw['% of Pop'] = dfiw[on]/world_pop
        dfi = dfi.append(dfiw)
        dfi.sort_values('Date', axis=0, ascending=False, inplace=True)

      else:
        #Building DF with Specified Countries
        dfi1 = df_day[df_day['Country/Region'] == i]
        country_pop = df[df['Country/Region'] == i]['Population']
        dfi1['% of Pop'] = (dfi1[on]/country_pop.iloc[0])
        dfi = dfi.append(dfi1)
        dfi.sort_values('Date', axis=0, ascending=False, inplace=True)

    #Will only display title and legend if the total countries number 
    #less than or equal to 10
    if len(countries) <= 10:
      #Percentage of Population Numbers
      if how == '%':
        fig = px.line(dfi, x='Date', y=dfi['% of Pop']*100, color='Country/Region', 
                      title=plt_title.rstrip(' VS ')  + ' Covid Cases', 
                      labels={'Country/Region': '', 'y' : on +' % of Pop'}, 
                      hover_data=[on])
      #Total Numbers
      else:
        fig = px.line(dfi, x='Date', y=dfi[on], color='Country/Region', 
                      title=plt_title.rstrip(' VS ')  + ' Covid Cases', 
                      labels={'Country/Region': '', 'y' : on}, 
                      hover_data=[on])
    #Builds Graph without legend and Title
    else:
      #Percentage of Population Numbers
      if how == '%':
        fig = px.line(dfi, x='Date', y=dfi['% of Pop']*100, 
                      color='Country/Region', labels={'y': on + ' % of Pop'}, 
                      hover_data=[on])
      #Total Numbers
      else:
        fig = px.line(dfi, x='Date', y=dfi[on], 
                      color='Country/Region', labels={'y': on}, 
                      hover_data=[on])
      fig.update_layout(showlegend=False)

  fig.show()
  pd.set_option('mode.chained_assignment', 'warn')
~~~


Here is where I got the datasets to make these graphs
[kaggle.com](https://www.kaggle.com/imdevskp/corona-virus-report)<br/>
Here is the Repo where the notebook and datasets can be found
[Repo](https://github.com/HunterAshby/DS19-Unit-1-Build)
