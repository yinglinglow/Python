#connecting to sqlite (sqlalchemy)

from sqlalchemy import create_engine 
engine = create_engine('sqlite:///census.sqlite') # Create an engine that connects to the census.sqlite file on sqlite
print(engine.table_names())

---

from sqlalchemy import Table
from sqlalchemy import select
census = Table('census', metadata, autoload=True, autoload_with=engine) # Reflect the census table from the engine: census
stmt = select([census])
print(stmt)
results = connection.execute(stmt).fetchall()
first_row = results[0]

---

#connecting to postgreSQL
from sqlalchemy import create_engine
engine = create_engine('postgresql+psycopg2://student:datacamp@postgresql.csrrinzqubik.us-east-1.rds.amazonaws.com:5432/census')
print(engine.table_names())

"""
There are three components to the connection string in this exercise: the dialect and driver ('postgresql+psycopg2://'), followed by the username and password ('student:datacamp'), followed by the host and port ('@postgresql.csrrinzqubik.us-east-1.rds.amazonaws.com:5432/'), and finally, the database name ('census'). You will have to pass this string as an argument to create_engine() in order to connect to the database.
"""

---

stmt = select([census]).where(census.columns.state == 'New York')
results = connection.execute(stmt).fetchall()

for result in results:
    print(result.age, result.sex, result.pop2008)

---

stmt = stmt.where(census.columns.state.in_(states)) #can use in_

from sqlalchemy import and_  #can use and_ also, or_, not_()
stmt = select([census]).where(
    # The state of California with a non-male sex
    and_(census.columns.state == 'California',
         census.columns.sex != 'M'
         )
)


#importing data

##read csv
pd.read_csv(_, na_values='_', parse_dates=['nameofcol'])

##read excel
pd.read_excel(_, na_values='_', sheetname='_')

pd.ExcelFile('')
_.sheet_names #attribute of the excel that gives its sheetnames

##load a JSON file
with open("_.json") as json_file:
    json_data = json.load(json_file)

for k in json_data.keys():
    print(k + ': ', json_data[k]) # Print each key-value pair in json_data

##from web using DataReader
from pandas_datareader.data import DataReader #https://pandas-datareader.readthedocs.io/en/latest/
from datetime import date 
start = date(YYYY, MM, DD) #default is 2010 #if jan, just input 1, not 01
end = date(YYYY, MM, DD) #default is today
ticker = '_' #can also be series, etc (for multiple, insert as list)
data_source = 'google' #or fred, etc
stock_prices = DataReader(ticker, data_source, start, end)

if multiple tickers, store as panel (3d array). to convert back to dataframe:
panel.to_frame(). it gives multi-index.

unstacked = df['col'].unstack() #to unstack. can do it without a particular column as well with df.unstack()

df = DataReader(["C", "GS", "JPM", "MS", "WFC"], 'google', start, end)

df2 = df.to_frame().unstack()
df2.columns.names = ['Stock Info', 'Bank Ticker']
df3 = df2.stack().stack() #stack all tgt
df4 = df3.unstack(1) #unstack in a different order
bank_stocks = df4.unstack()

##from web HTTP requests
import requests
url='http://'
resp = requests.get(url)
html_doc = resp.text #or resp.json()
print(html_doc)

from bs4 import BeautifulSoup
soup = BeautifulSoup(html_doc)
pretty_soup = soup.prettify() #makes html readable
print(pretty_soup)

title = soup.title #just gets the title
a_tags = soup.find_all('a') #finds all the links
for link in a_tags:
    print(link.get('href'))

others: #scrapy is a crawler
"You give Scrapy a root URL to start crawling, then you can specify constraints on how many (number of) URLs you want to crawl and fetch,etc"

##from API
import requests
url='http://'
resp = requests.get(url)
html_doc = resp.text #or resp.json()
print(html_doc) #same as from web but replace the url with the api url.

eg the url shld be
'http://www.omdbapi.com' for the data corresponding to the movie The Social Network. The query string should have two arguments: apikey=ff21610b and t=social+network. You can combine them as follows: apikey=ff21610b&t=social+network

url='http://www.omdbapi.com/?apikey=ff21610b&t=social+network'

if json:
json_data = r.json()
for k in json_data.keys():
    print(k + ': ', json_data[k])

to extract (read from keys!)
eg:
pizza_extract = json_data['query']['pages']['24768']['extract']

##API with authentication (twitter)
import tweepy

access_token = "_"
access_token_secret = "_"
consumer_key = "_"
consumer_secret = "_" 

auth = tweepy.OAuthHandler(consumer_key, consumer_secret)
auth.set_access_token(access_token,access_token_secret)

l = MyStreamListener()
stream = tweepy.Stream(auth, l) #a class that defined tweet streem listener as MyStreamListener. have to create own?

stream.filter(track=['clinton','trump','sanders','cruz'])# Filter Twitter Streams to capture data by the keywords

###reading the data
import json
tweets_data_path='tweets.txt'

tweets_data = []

tweets_file = open(tweets_data_path, "r") # Open connection to file

for line in tweets_file:
    tweet=json.loads(line)
    tweets_data.append(tweet)

tweets_file.close() # Close connection to file

print(tweets_data[0].keys()) # Print the keys of the first tweet dict

###store data in df

df = pd.DataFrame(tweets_data, columns=['text','lang']) #see from keys previously that there were text n lang.


df_copy = df.copy()

#exploring data
_.shape
_.info
_.head()
_.tail()

##for numeric data

_.describe() #includes mean, median and std
_.mode() 

_.quantile([0.25,0.75])

quantiles = np.arange(0.1,1.0,0.1)
deciles = _.quantile(quantiles)
deciles.plot(kind='bar', title='_')
plt.tight_layout()

top_5 = list['col'].nlargest(n=5) #return top 5

##for categorical data

df['zip'].value_counts().head() this returns the top 5 with number of counts

df.col.nunique() #to get the number of unique values
df.apply(lambda column: column.nunique()) #repeats this across cols
_.value_counts() #counts true values/ how many times each unique value occurs
_.value_counts(normalize=True) #returns percentage of true values
.dropna().astype(int).value_counts() #removes na and change type from float to int


returns.loc['2015-01-01':'2015-12-31'].std() #search by index, get standard deviation

df.idxmin() #returns the index of the min value, for each col. idxmax does same for max
df.std() #gives std of all cols

#manipulating data

income['Income per Capita (,000)'] = income['Income per Capita'] // 1000 #use floor division operator // to add a new column for floats


returns = pd.DataFrame() #create empty dataframe
for tick in tickers:
    returns[tick+' Return'] = bank_stocks[tick]['Close'].pct_change() #creates new column, and adds data to it

df.xs('baz','three') # gets cross axis in multilevel
bank_stocks.xs(level='Stock Info', key='Close', axis=1)


 float( )

 pd.to_numeric()

error_bad_lines=False
header=None
sep='delimiter'
skiprows=2

print('MAE: {0}, MSE: {1}, RMSE: {2}'.format(MAE,MSE,RMSE))



sns.heatmap(df.isnull(), ytickslabels=False, cbar=False, cmap='viridis')


sns.set_palette("GnBu_d")
sns.set_style('whitegrid')


print('Coefficients: \n', lm.coef_)




coef = pd.DataFrame(lm.coef_,x.columns)
coef.columns = ['Coef']

import pandas as pd
import numpy as np
import seaborn as sns
import matplotlib as plt
%matplotlib inline




type(df['timeStamp'].iloc[0]) #to find type of object in the column

df['Hour'] = df['timeStamp'].apply(lambda time: time.hour)
df['Month'] = df['timeStamp'].apply(lambda time: time.month)
df['Day of Week'] = df['timeStamp'].apply(lambda time: time.dayofweek)
df['Date'] = df['timeStamp'].apply(lambda time: time.date())


dmap = {0:'Mon',1:'Tue',2:'Wed',3:'Thu',4:'Fri',5:'Sat',6:'Sun'}
df['Day of Week'] = df['Day of Week'].map(dmap) # map using dictionary

df['col2'].unique() #to get unique values. if just want number, nunique.
df['col2'].value_counts() #values and counts for each value.

df[(df['col']>2) & (df['col']<10)] #selects values and returns tt fulfill conditions

df['col'].apply(function) # applies def function across all values or apply(lambda x: x*2)


pd.concat #merge on rows. if want col, axis=1
bank_stocks = pd.concat((C, GS, JPM, MS, WFC),axis=1,keys=tickers) #insert keys to have labels

pd.merge(left,right,on=['key1','key2], how='inner') #sql

left.join(right) #sql


df.drop('col', axis=1, inplace=True)

df.columns #col info
df.index #index info

df.sort_values('col2') #sort by col 2

df.isnull() #find null val

df.pivot_table(values='D', index['A','B'], columns=['C'])


carparks['Coord'] = list(zip(df.Latitude, df.Longtitude)) #zips and combines two columns into one column


ecom[(ecom['CC Provider'] == 'American Express') & (ecom['Purchase Price'] > 95)].count() #two restrictions at one go, use & and (). 


advanced usage of apply:
sum(ecom['CC Exp Date'].apply(lambda x: x[3:]) == '25')
ecom['Email'].apply(lambda x: x.split('@')[1]).value_counts().head(5)


print("Total score for {} is {}".format(name, score))

df.values #makes it into a numpy array

pd.concat() #adds vertically. can just put in a list.

_.columns = ['newtitle'] #rename cols

df.rename(columns={'lat': 'Latitude', 'lng': 'Longitude'}, inplace=True) #rename

col.sort_values('colname', ascending=False) #sort by that column, descending




#visualising data

plt.tight_layout() #resizes to remove overlap

fig = plt.figure(figsize=(8,2))
ax = fig.add_axes([0.1,0.1,0.8,0.8])
ax.plot(x, y, label = 'line one', color='#000000', lw=2, aplha=0.5, linestyle='- .', marker='o', markersize=2) #rgb hexcode, line width is lw which is *2 default, alpha is transparency, linestyle dash or : or different styles, markers * or +, etc. also markerfacecolor, markeredgewidth,markeredgecolor
ax.plot(x, y2, label = 'line two')
ax.legend(loc=0) #or location code



df3.plot.scatter(x='a',y='b', color='red', edgecolor='black', lw=1, s=50, figsize=(12,3))
df3['a'].plot.hist()


plt.style.use('ggplot')
df3['a'].plot.hist(alpha=0.5, bins=25)


df3[['a','b']].plot.box()
df3['d'].plot.density(ls='--', lw=5) #kde
df3.iloc[:30].plot.area(alpha=0.5).legend(bbox_to_anchor=(1, 0.5)) #move legend out

fig.savefig('my_pic.png, dpi=200) #saves to a png

fig = plt.figure()
ax = fig.add_axes([0,0,1,1])
ax.plot(x,y)
ax.set_xlabel('x')
ax.set_ylabel('y')
ax.set_title('title')


sns.heatmap(df.corr(), square=True, cmap='RdYlGn') #but the dataframe has to be a mtrix. eg df.corr() OR df.pivot_table(index='month', columns='year', values='passengers'). can make linewidths=1. can make annot=True for labels




plt.figure(figsize=(12,6))
sns.clustermap(heatmap, cmap='viridis')
plt.show()

plt.figure() #makes it into a new figure

import matplotlib.pyplot as plt



_.plot(title='_') #if multiple plots,  include subplots=True; if two plots together, can include secondary_y='' to make nicer

plt.show()

import seaborn as sns

titanic = sns.load_dataset('titanic')


sns.distplot('_', bins=50, kde=False, rug=True) #default is histogram, kde=True where the histogram is smoothed, rug=False which does not add markers at bottom of chart to indicate density

for column in list.columns:
    sns.distplot(list[column], hist=False, label=column) #iterate over each col and plots it



ax.axvline(_.mean(), color='b')
ax.axvline(_.median(), color='g') #assign plot to ax, then use these to highlight the mean and median as lines

import seaborn first



.plot(kind='bar') #for horizontal barplot, use barh



plt.xticks(rotation=45) #rotates ticks to show nicely. if plt.xticks([0,1], ['No','Yes']), it arranges the ticks n renames it too OR .plot.hist()




df.plot.line(x=df.index, y='')
df.plot.scatter(x='col_one', y='col_two',c='col_three', cmap='coolwarm') #vary color by col three. if vary by size, use s instead of c.
df.plot.hexbin(x='', y='', gridsize=2)

plt.xlabel()

sns.countplot(x='_', hue='_', data=df) #x is the values for x axis, hue is the  additional split for each bar. data is the dataframe. count plot gives count for categorical data. palette='RdBu' gives a red blue color.



sns.barplot(x='_', y='_', data=df, estimator=np.median) #estimator shows the chosen method in the plot. if unspecified it uses mean. 

sns.pointplot(x='_', y='_', hue='_', data=df) #plot against multiple metrics. x is cat, y is numerical.

sns.boxplot(x='_', y='_', data=df)
sns.swarmplot(x='_', y='_', data=df)


plt.figure(figsize=(12,3)
sns.set_context('poster')
sns.set_style('white') #or ticks, etc, 'whitegrid'
sns.despine

palette #matplotlib colormap


sns.jointplot(x='',y='',data='') #plots x against y, scatter plot with freqeuncy hist on the side
can change type = 'hex' or others, color='red'

sns.set_style('darkgrid')


sns.kdeplot(data=setosa['sepal_width'], 
            data2=setosa['sepal_length'], 
            cmap='plasma', shade=True, shade_lowest=False)

sns.pairplot(iris, hue='species', palette='Dark2')

sns.pairplot(df) #plots all pairs. automate to scatter. if the first column(index) is useless (like, date), use df[1:]
g = sns.PairGrid(df) # gives the grid
g.map_upper(plt.scatter)
g.map_lower(sns.kdeplot) OR sns.displot


sns.set_style('darkgrid')
g = sns.FacetGrid(data=df, hue='Private', size=6, aspect=2, palette='coolwarm')
g.map(plt.hist,'Outstate', alpha=0.5, bins=20)

g = sns.FacetGrid(data=, col='', row='') #if put col=, separates into two plots by the col 
g.map(sns.displot)
OR
g = sns.FacetGrid(data=titanic,col='sex')
g.map(plt.hist,'age') #or plt.scatter



sns.lmplot(x='', y='', data=) #regression plot
if col='sex', row='time' #splits into many plots based on the category


quantiles = np.arange(0.1,1.0,0.1)
deciles = _.quantile(quantiles)
deciles.plot(kind='bar', title='_')
plt.tight_layout()


#STOPPED HERE
#
#

#replace
df_nonull['Time'].replace('Morning', '9:00',inplace=True)


#convert Date to datetime
test['Date'] = pd.to_datetime(test['Date'])

#convert Time to datetime
test['Time'] = test['Time'].apply(lambda timing: datetime.datetime.strptime(timing, '%H:%M').time())

type(test['Time'][0])


###plotly and cufflinks
import cufflinks as cf

from plotly.offline import download_plotlyjs,init_notebook_mode,plot,iplot
init_notebook_mode(connected=True)
cf.go_offline

df.plot()
df.iplot() #interactive plot

df.iplot(kind='scatter', x='A', y='B', mode='markers')
df.iplot(kind='bar', x='A', y='B')
df.iplot(kind='box')
df.iplot(kind='surface', colorscale='rdylbu')
df['A'].iplot(kid='hist',bins=50)

df[['a','b']].iplot(kind='spread') #line plot with spread underneath

df.iplot(kind='bubble', x='a', y='b', size='c')
df.scatter_matrix() #similar to pair plot in seaborn


df.count.iplot(kind='bar') #or replace count with sum()

##checking data and cleaning it


for country, data in df:
    data.plot(title=country)
    plt.show() # Iterate over df and plot per country




def check_null_or_valid(row_data):
    """Function that takes a row of data,
    drops all missing values,
    and checks if all remaining values are greater than or equal to 0
    """
    no_na = row_data.dropna()[1:-1]
    numeric = pd.to_numeric(no_na)
    ge0 = numeric >= 0
    return ge0



# Check whether the first column is 'Life expectancy'
assert df.columns[0] == 'Life expectancy'

# Check whether the values in the row are valid
assert df.iloc[:, 1:].apply(check_null_or_valid, axis=1).all().all()

# Check that there is only one instance of each country
assert df['Life expectancy'].value_counts()[0] == 1

---

# Melt gapminder: gapminder_melt
gapminder_melt = pd.melt(gapminder,id_vars='Life expectancy') #this melts data down into one column

# Convert the year column to numeric
gapminder.year = pd.to_numeric(gapminder['year'])

# Test if country is of type object
assert gapminder.country.dtypes == np.object

# Test if year is of type int64
assert gapminder.year.dtypes == np.int64

# Test if life_expectancy is of type float64
assert gapminder.life_expectancy.dtypes == np.float64

---

# Create the series of countries: countries
countries = gapminder['country']

# Drop all the duplicates from countries
countries = countries.drop_duplicates()

# Write the regular expression: pattern
pattern = '^[A-Za-z\.\s]*$'

# Create the Boolean vector: mask
mask = countries.str.contains(pattern)

# Invert the mask: mask_inverse
mask_inverse = ~mask

# Subset countries using mask_inverse: invalid_countries
invalid_countries = countries.loc[mask_inverse]

---

# Assert that country does not contain any missing values
assert pd.notnull(gapminder.country).all()

# Assert that year does not contain any missing values
assert pd.notnull(gapminder.year).all().all()

# Drop the missing values
gapminder = gapminder.dropna()

# Print the shape of gapminder
print(gapminder.shape)



-------


#links for API
https://developers.google.com/places/web-service/details
http://docs.aws.amazon.com/AWSECommerceService/latest/DG/EX_RetrievingPriceInformation.html
https://www.reddit.com/r/webdev/comments/3wrswc/what_are_some_fun_apis_to_play_with/
https://github.com/toddmotto/public-apis


---

#intro to decision trees

import numpy as np
from sklearn import tree

train["Age"] = train["Age"].fillna(train["Age"].median()) #substitute missing values with median values, or most common category.

ALTERNATIVELY:
check boxplot if age is related to pclass
sns.boxplot(x='pclass', y='age', data=train)

def impute_age(cols):
    age = cols[0]
    pclass = cols[1]

    if pd.isnull(age):
        if pclass == 1:
            return 37
        elif pclass == 2:
            return 29
        else:
            return 24
    else:
        return age

train['age'] = train[['age','pclass']].apply(impute_age, axis=1)

train["Embarked"][train["Embarked"] == "S"] = 0 #convert all categorical to numerical values


can use get dummies instead to convert to 0,1:
sex = pd.get_dummies(train['sex'],drop_first=True)
embark = pd.get_dummies(train['embarked'],drop_first=True)


train = pd.concat([train,sex,embark], axis=1)

--

target = train['Survived'].values
features_one = train[["Pclass", "Sex", "Age", "Fare"]].values

# Fit your first decision tree: my_tree_one
my_tree_one = tree.DecisionTreeClassifier()
my_tree_one = my_tree_one.fit(features_one, target)

# Look at the importance and score of the included features
print(my_tree_one.feature_importances_)
print(my_tree_one.score(features_one, target))

# Impute the missing value with the median
test.Fare[152] = test.Fare.median()

# Extract the features from the test set: Pclass, Sex, Age, and Fare.
test_features = test[['Pclass', 'Sex', 'Age', 'Fare']].values

# Make your prediction using the test set
my_prediction = my_tree_one.predict(test_features)

# Create a data frame with two columns: PassengerId & Survived. Survived contains your predictions
PassengerId =np.array(test["PassengerId"]).astype(int)
my_solution = pd.DataFrame(data=my_prediction, index=PassengerId, columns = ["Survived"])
print(my_solution)

# Check that your data frame has 418 entries
print(my_solution.shape)

# Write your solution to a csv file with the name my_solution.csv
my_solution.to_csv("my_solution_one.csv", index_label = ["PassengerId"])
df = pd.read_csv('my_solution_one.csv')
print(df)

---

from sklearn.ensemble import RandomForestClassifier
rfc = RandomForestClassifier(n_estimators=200)

rfc.fit(X_train, y_train)
rfc_pred = rfc.predict(X_test)

print(confusion_matrix(y_test,rfc_pred))
print('\n')
print(classification_report(y_test,rfc_pred))

-----
#KNN - for binaries/categorical data

from sklearn.neighbors import KNeighborsClassifier
from sklearn.model_selection import train_test_split

# Create feature and target arrays
X = digits.data
y = digits.target

# Split into training and test set
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 0.2, random_state=42, stratify=y)

# Create a k-NN classifier with 7 neighbors: knn
knn = KNeighborsClassifier(n_neighbors=7)

# Fit the classifier to the training data
knn.fit(X_train,y_train)

# Print the accuracy
print(knn.score(X_test, y_test))

---

for knn, important to ensure similar scales

from sklearn.preprocessing import StandardScaler
scaler = StandardScaler()
scaler.fit(df.drop('TARGET CLASS', axis=1))
scaled_features = scaler.transform(df.drop('TARGET CLASS', axis=1))
df_feat = pd.DataFrame(scaled_features, columns=df.columns[:-1]) #gives the scaled data in a df

---

error_rate = []

for i in range(1,40):
    knn = KNeighboursClassifier(n_neighbours=i)
    knn.fit(X_train,y_train)
    pred_i = knn.predict(X_test)
    error_rate.append(np.mean(pred_i != y_test))

plt.figure(figsize=10,6))
plt.plot(range(1,40),error_rate, color='blue', linestyle='dashed', marker='o',markerfacecolor='red',markersize=10)
plt.title('Error Rate vs K value')
plt.xlabel('K')
plt.ylabel('Error Rate')

---

# Predict and print the label for the new data point X_new
new_prediction = knn.predict(X_new)
print("Prediction: {}".format(new_prediction))

---

# Setup arrays to store train and test accuracies
neighbors = np.arange(1, 9)
train_accuracy = np.empty(len(neighbors))
test_accuracy = np.empty(len(neighbors))

# Loop over different values of k
for i, k in enumerate(neighbors):
    # Setup a k-NN Classifier with k neighbors: knn
    knn = KNeighborsClassifier(n_neighbors=k)

    # Fit the classifier to the training data
    knn.fit(X_train, y_train)
    
    #Compute accuracy on the training set
    train_accuracy[i] = knn.score(X_train, y_train)

    #Compute accuracy on the testing set
    test_accuracy[i] = knn.score(X_test, y_test)

# Generate plot
plt.title('k-NN: Varying Number of Neighbors')
plt.plot(neighbors, test_accuracy, label = 'Testing Accuracy')
plt.plot(neighbors, train_accuracy, label = 'Training Accuracy')
plt.legend()
plt.xlabel('Number of Neighbors')
plt.ylabel('Accuracy')
plt.show()

---

#reshaping
print("Dimensions of y before reshaping: {}".format(y.shape))
print("Dimensions of X before reshaping: {}".format(X.shape))

y = y.reshape(-1,1)
X = X.reshape(-1,1)

print("Dimensions of y after reshaping: {}".format(y.shape))
print("Dimensions of X after reshaping: {}".format(X.shape))


---

#linear regression (for continuous variables/prediction)

from sklearn.linear_model import LinearRegression
reg = LinearRegression()
prediction_space = np.linspace(min(X_fertility), max(X_fertility)).reshape(-1,1)

reg.fit(X_fertility, y)

y_pred = reg.predict(prediction_space)

print(reg.score(X_fertility, y))

plt.plot(prediction_space, y_pred, color='black', linewidth=3)
plt.show()

---

from sklearn.linear_model import LinearRegression
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split

# Create training and test sets
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 0.3, random_state=42)

# Create the regressor: reg_all
reg_all = LinearRegression()

# Fit the regressor to the training data
reg_all.fit(X_train, y_train)
print(reg_all.intercept_) #can do this if want to check the intercept
reg_all.coef_ #gives an array of all the coefs for each variable
coef_df = pd.DataFrame(reg_all.coef_, X_train.columns, columns=['Coeff']) #creates a dataframe with coeff as the column, the column names as the row titles.

# Predict on the test data: y_pred
y_pred = reg_all.predict(X_test)

# Compute and print R^2 and RMSE
print("R^2: {}".format(reg_all.score(X_test, y_test)))
rmse = np.sqrt(mean_squared_error(y_test, y_pred))
print("Root Mean Squared Error: {}".format(rmse))


#check for accuracy
can do a quick scatterplot to see if they are one straight line.
plt.scatter(y_test, predictions)

if distribution plot of residuals is normal, means the model used is correct
sns.distplot((y_test-predictions))

---

#cross validation

from sklearn.linear_model import LinearRegression
from sklearn.model_selection import cross_val_score

reg = LinearRegression()

# Compute 5-fold cross-validation scores: cv_scores
cv_scores = cross_val_score(reg, X, y, cv=5)

print(cv_scores)
print("Average 5-Fold CV Score: {}".format(np.mean(cv_scores)))

---

#regularization (lasso) #for feature selection. adds to loss function the absolute coef * alpha (L1 norm)

from sklearn.linear_model import Lasso
lasso = Lasso(alpha=0.4, normalize=True)

lasso.fit(X,y)
lasso_coef = lasso.coef_
print(lasso_coef)

plt.plot(range(len(df_columns)), lasso_coef)
plt.xticks(range(len(df_columns)), df_columns.values, rotation=60)
plt.margins(0.02)
plt.show()

#regularization (ridge) #L2 norm

---
#confusion matrix & classification report (for assessment of model instead of using accuracy)

from sklearn.metrics import classification_report
from sklearn.metrics import confusion_matrix

X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.4, random_state=42)

knn = KNeighborsClassifier(n_neighbors=6)

knn.fit(X_train, y_train)

y_pred = knn.predict(X_test)

print(confusion_matrix(y_test, y_pred)) #Generate the confusion matrix and classification report
print(classification_report(y_test, y_pred))

[[TN,FP]
[FN,TP]]


---

#logistic regression (for categorical!)

*usually good to do a countplot of the target variable first for a sense of the ratio
sns.countplot(x='survived', hue='sex', data=train)

import cufflinks as cf
cf.go_offline
df.iplot(kind='hist', bins=50)

from sklearn.linear_model import LogisticRegression
from sklearn.metrics import confusion_matrix, classification_report

X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 0.4, random_state=42)

logreg = LogisticRegression()

logreg.fit(X_train, y_train)

y_pred = logreg.predict(X_test)

print(confusion_matrix(y_test, y_pred))
print(classification_report(y_test, y_pred))

---

#roc curve - to assess classifier's perf

from sklearn.metrics import roc_curve

# Compute predicted probabilities: y_pred_prob
y_pred_prob = logreg.predict_proba(X_test)[:,1]

# Generate ROC curve values: fpr, tpr, thresholds
fpr, tpr, thresholds = roc_curve(y_test, y_pred_prob)

# Plot ROC curve
plt.plot([0, 1], [0, 1], 'k--')
plt.plot(fpr, tpr)
plt.xlabel('False Positive Rate')
plt.ylabel('True Positive Rate')
plt.title('ROC Curve')
plt.show()

---

#area under ROC curve to assess perf

from sklearn.metrics import roc_auc_score
from sklearn.model_selection import cross_val_score

# Compute predicted probabilities: y_pred_prob
y_pred_prob = logreg.predict_proba(X_test)[:,1]

# Compute and print AUC score
print("AUC: {}".format(roc_auc_score(y_test, y_pred_prob)))

# Compute cross-validated AUC scores: cv_auc
cv_auc = cross_val_score(logreg, X, y, scoring='roc_auc', cv=5)

# Print list of AUC scores
print("AUC scores computed using 5-fold cross-validation: {}".format(cv_auc))

---
##model tuning - gridsearch
# Import necessary modules
from sklearn.linear_model import LogisticRegression
from sklearn.model_selection import GridSearchCV

# Setup the hyperparameter grid
c_space = np.logspace(-5, 8, 15)
param_grid = {'C': c_space}

# Instantiate a logistic regression classifier: logreg
logreg = LogisticRegression()

# Instantiate the GridSearchCV object: logreg_cv
logreg_cv = GridSearchCV(logreg, param_grid, cv=5)

# Fit it to the data
logreg_cv.fit(X,y)

# Print the tuned parameters and score
print("Tuned Logistic Regression Parameters: {}".format(logreg_cv.best_params_)) 
print("Best score is {}".format(logreg_cv.best_score_))


---

# support vector machine

from sklearn.svm import SVC
model = SVC()
model.fit(X_train,y_train)
predictions = model.predict(X_test)


from sklearn.model_selection import GridSearchCV

param_grid = {'C':[0.1,1,10,100,1000], 'gamma':[1,0.1,0.01,0.001,0.0001]}

grid = GridSearchCV(SVC(),param_grid, verbose=3)

grid.fit(X_train, y_train)

grid.best_params_

grid.best_estimator_

grid_predictions = grid.predict(X_test)

print(confusion_matrix(y_test,grid_predictions))
-----

# k means clustering (unsupervised)
find patterns

from sklearn.cluster import KMeans

kmeans = KMeans(n_clusters=4)
kmeans.fit(data[0])
kmeans.cluster_centers_ #gives the centers
kmeans.labels_ 


ax1.scatter(data[0],[:,0], data[0][:,1],c=kmeans.labels_, cmap='rainbow')


---

#random

1) check info
2) check describe
3) can get column names (df.columns)
4) sns.pairplot(df) - if not too many
5) distplot(df['price']) - usually check ur target variable
6) sns.heatmap(df.corr(), annot=True)

shift tab for dropdown explanation

%timeit #insert this before any function and it will execute the function plus time it

np.zeros(10) #creating an array of 10 zeros
np.zeros((5,5)) # creating a 5x5 matrix of zeros

np.eye(4) #creates an identity matrix

np.linspace(0,10,3) #returns 3 evenly spaced numbers over interval0 to 10 (inclusive) ([0,5,10])

np.array(list) #converts a list to np array. list of lists gives matrix

np.arange(0,10) #range from 0 to 9.
arr.reshape(3,3) #reshape it to 3x3

OR np.arange(0.01,1.01,0.01).reshape(10,10)

np.random.rand() #random number from uniform dist
np.random.randn() #random number from normal dist

arr3 = np.arange(12,26)
index = np.argwhere(arr3==16)
arr4 = np.delete(arr3, index)

arr4.sum() #sum of all the values in the array
arr4.sum(axis=0) # sum of all the columns

datestring = datetime.strftime(datetime.now(), '%Y%m%d')



df.loc['Cazenovia College']['Grad.Rate'] #to get the value

df['Grad.Rate']['Cazenovia College'] = 100 #to set it as a new value



_.set_index('col', inplace=True) #sets the column as the index. instead of assigning result to a new variable, you can also put inplace=True.

df.reset_index(drop=True) #replaces the index
df.index = range(1,100) #sets index for range of numbers

list.loc[(list.col=='_') & (list['col']==''), 'maxcol'].idxmax #multiple conditions,take the max of maxcol and returns the index

list = _.index.tolist() #converts index to list. useful when u find the top 5 and want to convert it to list

.dropna()
.fillna()
.astype(int)
.div(1e6) #divides by 1million
df.drop('colname', axis=1) #drops a column
df.groupby('colname') #list it to see it
df.groupby('colname').'col'.mean() #groups by each category in the column, aggregates by mean on 'col'

df_grouped = df.groupby('zip').e.sum().reset_index()
df_sorted = df_grouped.sort_values('e', ascending=False)
df_sorted.head(5)

df.groupby(['',''])
df.agg({'Average': 'mean', 'Median': 'median', 'Standard Deviation': 'std'}) #using the values, and putting the key as header

df['newcol'] = float('NaN') #create new col
df['newcol'][df['col'] < 18] = 1 #assign value to newcol 

#exclude outliers
df[df.col < df.col.quantile(.95)] #remove those above .95 percentile


fig, axes = plt.subplots(nrows=3, ncols=3) #3 by 3 subplots
axes[0].plot(x,y, lw=3, color='blue', ls='--')
axes[1].plot(x,z, lw=3, color='red')


#opening files type: sklearn.datasets.base.Bunch (like dictionary)
cancer = load_breast_cancer()
cancer.keys()
print(cancer['DESCR'])
df = pd.DataFrame(cancer['data'], columns=cancer['feature_names'])

from sklearn.preprocessing import StandardScaler
scaler = StandardScaler()
scaler.fit(df)

scaled_data = scaler.transform(df)

#PCA
from sklearn.decomposition import PCA
pca = PCA(n_components=2)

pca.fit(scaled_data)
x_pca = pca.transform(scaled_data)

scaled_data.shape
x_pca.shape #shows cut down to 2 instead of 30 original

plt.figure(figsize=(8,6))
plt.scatter(x_pca[:,0], x_pca[:,1], c=cancer['target'], cmap='plasma')
plt.xlabel('First Principle Component')
plt.ylabel('Second Principle Component')

pca.components_

df_comp = pd.DataFrame(pca.components_, columns=cancer['feature_names'])

plt.figure(figsize=(12,6))
sns.heatmap(df_comp, cmap='plasma')


---

df.groupby(by=('Day of Week','Hour')).count()['lat'].unstack() #manipulate to make into heatmap. pick on column with the data (apart from the row n col axis, then groupby 'row' and 'col'. unstack - automatically the last level in this case 'col' unstacks first)

---

messages = [line.rstrip() for line in open('smsspamcollection/SMSSpamCollection')]
print(len(messages)) #get a sense of how many messages

for message_no, message in enumerate(messages[:10]):
    print(message_no, message)
    print('\n') #get a sense of the messages and spacing to see that this is tab separated (tsv)

so
messages = pd.read_csv('smsspamcollection/SMSSpamCollection', sep='\t',
                           names=["label", "message"])
messages.head()


---

import string
mess = 'Sameple message!'
nopunc = [c for c in mess if c not in string.punctuation]

from nltk.corpus import stopwords
stopwords.words('english')

nopunc = ''.join(nopunc)
nopunc.split()

clean_mess = [word for word in nopunc.split() if word.lower not in stopwards.words('english')]

def text_process(mess):
    """
    1. remove punctuation
    2. remove stop words
    3. return list of clean text words
    """

    nopunc = [char for char in mess if char not in string.punctuation]
    nopunc = ''.join(nopunc)
    return [word for word in nopunc.split() if word.lower() not in stopwords.words('english')]

messages['message'].head(5).apply(text_process)


from sklearn.feature_extraction.text import CountVectorizer
bow_transformer = CountVectorizer(analyzer=text_process).fit(messages['message']) #creates a big matrxix for every word in every message

print(len(bow_transformer.vocabulary_))

mess4 = messages['message'][3]
bow4 = bow_transformer.transform([mess4])
print(bow4)
bow_transformer.get_feature_names()[4068]

messages_bow = bow_transformer.transform(messages['message'])
print('Shape of Sparse Matrix: ', messages_bow.shape)

messages_bow.nnz #non zero occurences

sparsity = (100 * messages_bow.nnz / (messages_bow.shape[0] * messages_bow.shape[1]))

print('sparsity: {}'.format(sparsity))



from sklearn.feature_Extraction.text import TfidfTransformer
tfidf_transformer = TfidfTransformer().fit(messages_bow)

tfidf4 = tfidf_transformer.transform(bow4)
print(tfidf4) #numerical vectors

tfidf_transformer.idf_[bow_transformer.vocabulary_['university]]

messages_tfidf = tfidf_transformer.transform(messages_bow)


from sklearn.naive_bayes import MultinomialNB
spam_detect_model = MultinomialNB().fit(messages_tfidf,messages['label'])

spam_detect_model.predict(tfidf4)[0]

all_pred = spam_detect_model.predict(messages_tfidf)



from sklearn.pipeline import Pipeline
pipeline = Pipeline([
    ('bow', CountVectorizer()), # strings to token integer counts
    ('tfidf', TfidfTransformer()), #integer counts to weighted TD_IDF scores
    ('classifier', MultinomialNB()) #train on TF-IDF vectors w/ Naive Bayes classifier
])


pipeline.fit(msg_train,label_train)

predictions = pipeline.predict(msg_test)

from sklearn.metrics import classification_report

print(classification_report(label_test,predictions))


---


df.groupby('col').mean()
df.groupby('col').mean().corr() #returns correlation


---

# Tensorflow

```python
import tensor flow as tf
hello = tf.constant('Hello World')

sess = tf.Session()
sess.run(hello)

```

```python
x = tf.constant(2)
y = tf.constant(3)

with tf.Session() as sess:
    print('Operations with Constants')
    print('Addition: ', sess.run(x+y))

```

```python
x = tf.placeholder(tf.int32)
y = tf.placeholder(tf.int32)
add = tf.add(x,y)
mul = tf.mul(x,y) #builtin multiplication

d = {x:20, y:30}

with tf.Session() as sess:
    print('Operations with Constants')
    print('Addition: ', sess.run(add, feed_dict=d))

a = np.array([[5.0,5.0]])
b = np.array([[2.0],[2.0]])

mat1 = tf.constant(a)
mat2 = tf.constant(b)

matrix_multi = tf.matmul(mat1,mat2)

with tf.Session() as sess:
    result = sess.run(matrix_multi)
    print(result)
```

---

```python
import tensor flow as tf
from tensorflow.examples.tutorials.mnist import input_data

mnist = input_data.read_data_sets("/tmp/data",one_hot=True)
type(mnist)

sample = mnist.train.images[2].reshape(28,28)

import matplotlib.pyplot as plt
%matplotlib inline

plt.imshow(sample, cmap='Greys')

learning_rate = 0.001
training_epochs = 15
batch_size = 100

n_classes = 10
n_samples = mnist.train.num_examples

n_input = 784

n_hidden_1 = 256 #8 bit color storage - usually 256 for pics?
n_hidden_2 = 256

def multilayer_perceptron(x,weights,biases):
    """
    x: Placeholder for Data Input
    weights: dict of weights
    biases: dist of bias values

    """

# First hidden layer with RELU Activation
# X * W + B
layer_1 = tf.add(tf.matmul(x,weights['h1']),biases['b1'])
# Func(X * W + B) = RELU -> f(x) = max(0,x)
layer_1 = tf.nn.relu(layer_1)

# Second hidden layer with RELU Activation
# X * W + B
layer_2 = tf.add(tf.matmul(layer_1,weights['h2']),biases['b2'])
# Func(layer_1 * W + B) = RELU -> f(x) = max(0,x)
layer_1 = tf.nn.relu(layer_1)

# Last Output layer
out_layer = tf.matmul(layer_2,weights['out']) + bias['out']

return out_layer

weights = {
    'h1': tf.Variable(tf.random_normal([n_input,n_hidden_1]))
    'h2': tf.Variable(tf.random_normal([n_hidden_1,n_hidden_2]))
    'out': tf.Variable(tf.random_normal([n_hidden_2,n_classes]))
}

biases = {
    'b1': tf.Variable(tf.random_normal([n_hidden_1]))
    'b2': tf.Variable(tf.random_normal([n_hidden_2]))
    'out': tf.Variable(tf.random_normal([n_classes]))

}

x = tf.placeholder('float', [None,n_input])
y = tf.placeholder('float', [None,n_classes])

pred = multilayer_perceptron(x,weights,biases)

cost = tf.reduce_mean(tf.nn.softmax_cross_entropy_with_logits(pred,y))
optimizer = tf.train.AdamOptimiser(learning_rate=learning_Rate).minimize(cost)


# to run the session

sess = tf.InteractiveSession()

init = tf.initialise_all_variables()

sess.run(init)

#15 loops
for epoch in range(training_epochs):
    #cost
    avg_cost = 0.0
    
    total_batch = int(n_sample/batch_size)

    for i in range(total_batch):
        batch_x, batch_y = mnist.rain.next_batch(batch_size)
        _,c = sess.run([optimizer,cost],feed_dict={x:batch_x, y:batch_y})

        avg_cost += c/total_batch

    print("Epoch: {} cost{:.4f}".format(epoch+1,avg_cost))

print("Model has completed {} Epochs of training".format(training_epochs))

# model evaluation

correct_predictions = tf.equal(f.argmax(pred,1), tf.argmax(y,1))
correct_predictions = tf.cast(correct_predictions,'float')

accuracy = tf.reduce_mean(correct_predictions)
accuracy.eval({x:mnist.test.images, y:mnist.test.labels})

```

```python

# SKflow

from sklearn.datasets import load_iris
iris = load_iris()
iris.keys()

X = iris['data']
y = iris['target']

from sklearn.cross_validation import train_Test_split

X_train, X_test, y_train, y_test = train_test_split(X, y, test_Size=0.3)

import tensorflow.contrib.learn.python.learn as learn

classifier = learn.DNNClassifier(hidden_units=[10,20,10], n_classes=3)#hidden units is nodes per layer.

classifier.fit(X_train,y_train,steps=200,batch_size=32)

iris_predictions = classifier.predict(X_test)

from sklearn.metrics import classification_Report,confusion_matrix

print(classification_report(y_test,iris_predictions))

#tweak steps, hidden units or batch size to adjust
```



df_new = pd.merge(df, df_coo[['Carpark Name','Coord_rad']], on='Carpark Name')
df_new.tail()


left_on

right_on



---

csv[csv['Carpark Name'].str.contains("Sentosa")]