{
 "cells": [
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "# About\n",
    "\n",
    "\n",
    "- **Purpose** : Prediction of Close price of Tesla Stocks\n",
    "\n",
    "\n",
    "- **Data Source** : [Yahoo Finance](https://finance.yahoo.com/quote/TSLA/history/)\n",
    "\n",
    "- **Dependencies** : \n",
    "    - Pandas\n",
    "    - Numpy\n",
    "    - Plotly\n",
    "    - Matplotlib\n",
    "    - Sklearn\n",
    "\n",
    "\n",
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "# Result : Best Models for Close price prediction\n",
    "**- Only Date as Input Feature**\n",
    "    - Polynomial Regression with Degree=3\n",
    "**- Open Price and Date as Input Feature**\n",
    "    - Ridge Regression + Polynomial Regression with Degree=3\n"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 1,
   "metadata": {},
   "outputs": [],
   "source": [
    "import pandas as pd\n",
    "import numpy as np\n",
    "import matplotlib.pyplot as plt\n",
    "%matplotlib inline\n",
    "\n",
    "import plotly.graph_objs as go\n",
    "\n",
    "#for online plotting\n",
    "import plotly.plotly as py \n",
    "\n",
    "#for offline plotting\n",
    "#from plotly.offline import download_plotlyjs, init_notebook_mode, plot, iplot\n",
    "#init_notebook_mode(connected=True) "
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "# Data Cleaning"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 2,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<style>\n",
       "    .dataframe thead tr:only-child th {\n",
       "        text-align: right;\n",
       "    }\n",
       "\n",
       "    .dataframe thead th {\n",
       "        text-align: left;\n",
       "    }\n",
       "\n",
       "    .dataframe tbody tr th {\n",
       "        vertical-align: top;\n",
       "    }\n",
       "</style>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>Date</th>\n",
       "      <th>Open</th>\n",
       "      <th>High</th>\n",
       "      <th>Low</th>\n",
       "      <th>Close</th>\n",
       "      <th>Adj Close</th>\n",
       "      <th>Volume</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>0</th>\n",
       "      <td>2010-06-29</td>\n",
       "      <td>19.000000</td>\n",
       "      <td>25.00</td>\n",
       "      <td>17.540001</td>\n",
       "      <td>23.889999</td>\n",
       "      <td>23.889999</td>\n",
       "      <td>18766300</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>1</th>\n",
       "      <td>2010-06-30</td>\n",
       "      <td>25.790001</td>\n",
       "      <td>30.42</td>\n",
       "      <td>23.299999</td>\n",
       "      <td>23.830000</td>\n",
       "      <td>23.830000</td>\n",
       "      <td>17187100</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>2</th>\n",
       "      <td>2010-07-01</td>\n",
       "      <td>25.000000</td>\n",
       "      <td>25.92</td>\n",
       "      <td>20.270000</td>\n",
       "      <td>21.959999</td>\n",
       "      <td>21.959999</td>\n",
       "      <td>8218800</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>3</th>\n",
       "      <td>2010-07-02</td>\n",
       "      <td>23.000000</td>\n",
       "      <td>23.10</td>\n",
       "      <td>18.709999</td>\n",
       "      <td>19.200001</td>\n",
       "      <td>19.200001</td>\n",
       "      <td>5139800</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>4</th>\n",
       "      <td>2010-07-06</td>\n",
       "      <td>20.000000</td>\n",
       "      <td>20.00</td>\n",
       "      <td>15.830000</td>\n",
       "      <td>16.110001</td>\n",
       "      <td>16.110001</td>\n",
       "      <td>6866900</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "</div>"
      ],
      "text/plain": [
       "         Date       Open   High        Low      Close  Adj Close    Volume\n",
       "0  2010-06-29  19.000000  25.00  17.540001  23.889999  23.889999  18766300\n",
       "1  2010-06-30  25.790001  30.42  23.299999  23.830000  23.830000  17187100\n",
       "2  2010-07-01  25.000000  25.92  20.270000  21.959999  21.959999   8218800\n",
       "3  2010-07-02  23.000000  23.10  18.709999  19.200001  19.200001   5139800\n",
       "4  2010-07-06  20.000000  20.00  15.830000  16.110001  16.110001   6866900"
      ]
     },
     "execution_count": 2,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "data = pd.read_csv('data/tesla.csv') #reading stock prices\n",
    "data.head() #checking out data"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 3,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "<class 'pandas.core.frame.DataFrame'>\n",
      "RangeIndex: 2193 entries, 0 to 2192\n",
      "Data columns (total 7 columns):\n",
      "Date         2193 non-null object\n",
      "Open         2193 non-null float64\n",
      "High         2193 non-null float64\n",
      "Low          2193 non-null float64\n",
      "Close        2193 non-null float64\n",
      "Adj Close    2193 non-null float64\n",
      "Volume       2193 non-null int64\n",
      "dtypes: float64(5), int64(1), object(1)\n",
      "memory usage: 120.0+ KB\n"
     ]
    }
   ],
   "source": [
    "data.info() #to check datatypes and null values"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 4,
   "metadata": {},
   "outputs": [],
   "source": [
    "#need to fix type of Date column\n",
    "data['Date'] = pd.to_datetime(data['Date'])"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "- No Missing values\n",
    "- No Duplicates"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "# Exploratly Data Analysis"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 5,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "Dataframe contains stock prices between 2010-06-29 00:00:00 2019-03-15 00:00:00\n",
      "Total days=3181 days\n"
     ]
    }
   ],
   "source": [
    "print(f'Dataframe contains stock prices between {data.Date.min()} {data.Date.max()}') \n",
    "print(f'Total days={(data.Date.max()  - data.Date.min()).days} days')"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 6,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<style>\n",
       "    .dataframe thead tr:only-child th {\n",
       "        text-align: right;\n",
       "    }\n",
       "\n",
       "    .dataframe thead th {\n",
       "        text-align: left;\n",
       "    }\n",
       "\n",
       "    .dataframe tbody tr th {\n",
       "        vertical-align: top;\n",
       "    }\n",
       "</style>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>Open</th>\n",
       "      <th>High</th>\n",
       "      <th>Low</th>\n",
       "      <th>Close</th>\n",
       "      <th>Adj Close</th>\n",
       "      <th>Volume</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>count</th>\n",
       "      <td>2193.000000</td>\n",
       "      <td>2193.000000</td>\n",
       "      <td>2193.000000</td>\n",
       "      <td>2193.000000</td>\n",
       "      <td>2193.000000</td>\n",
       "      <td>2.193000e+03</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>mean</th>\n",
       "      <td>175.652882</td>\n",
       "      <td>178.710262</td>\n",
       "      <td>172.412075</td>\n",
       "      <td>175.648555</td>\n",
       "      <td>175.648555</td>\n",
       "      <td>5.077449e+06</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>std</th>\n",
       "      <td>115.580903</td>\n",
       "      <td>117.370092</td>\n",
       "      <td>113.654794</td>\n",
       "      <td>115.580771</td>\n",
       "      <td>115.580771</td>\n",
       "      <td>4.545398e+06</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>min</th>\n",
       "      <td>16.139999</td>\n",
       "      <td>16.629999</td>\n",
       "      <td>14.980000</td>\n",
       "      <td>15.800000</td>\n",
       "      <td>15.800000</td>\n",
       "      <td>1.185000e+05</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>25%</th>\n",
       "      <td>33.110001</td>\n",
       "      <td>33.910000</td>\n",
       "      <td>32.459999</td>\n",
       "      <td>33.160000</td>\n",
       "      <td>33.160000</td>\n",
       "      <td>1.577800e+06</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>50%</th>\n",
       "      <td>204.990005</td>\n",
       "      <td>208.160004</td>\n",
       "      <td>201.669998</td>\n",
       "      <td>204.990005</td>\n",
       "      <td>204.990005</td>\n",
       "      <td>4.171700e+06</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>75%</th>\n",
       "      <td>262.000000</td>\n",
       "      <td>265.329987</td>\n",
       "      <td>256.209991</td>\n",
       "      <td>261.739990</td>\n",
       "      <td>261.739990</td>\n",
       "      <td>6.885600e+06</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>max</th>\n",
       "      <td>386.690002</td>\n",
       "      <td>389.609985</td>\n",
       "      <td>379.350006</td>\n",
       "      <td>385.000000</td>\n",
       "      <td>385.000000</td>\n",
       "      <td>3.716390e+07</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "</div>"
      ],
      "text/plain": [
       "              Open         High          Low        Close    Adj Close  \\\n",
       "count  2193.000000  2193.000000  2193.000000  2193.000000  2193.000000   \n",
       "mean    175.652882   178.710262   172.412075   175.648555   175.648555   \n",
       "std     115.580903   117.370092   113.654794   115.580771   115.580771   \n",
       "min      16.139999    16.629999    14.980000    15.800000    15.800000   \n",
       "25%      33.110001    33.910000    32.459999    33.160000    33.160000   \n",
       "50%     204.990005   208.160004   201.669998   204.990005   204.990005   \n",
       "75%     262.000000   265.329987   256.209991   261.739990   261.739990   \n",
       "max     386.690002   389.609985   379.350006   385.000000   385.000000   \n",
       "\n",
       "             Volume  \n",
       "count  2.193000e+03  \n",
       "mean   5.077449e+06  \n",
       "std    4.545398e+06  \n",
       "min    1.185000e+05  \n",
       "25%    1.577800e+06  \n",
       "50%    4.171700e+06  \n",
       "75%    6.885600e+06  \n",
       "max    3.716390e+07  "
      ]
     },
     "execution_count": 6,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "data.describe() # to check for outliers"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 7,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "<matplotlib.axes._subplots.AxesSubplot at 0x1139de4a8>"
      ]
     },
     "execution_count": 7,
     "metadata": {},
     "output_type": "execute_result"
    },
    {
     "data": {
      "image/png": "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\nL/2dkzyf3nViXwA8A/irJM+tqu0LWbg0iKX2J7o0nUGuGXsPcE9b/nGS24AVM2yyFrigqh4G7mgX\nCT8E+PoC1CvNyVL7E12azpzOo0+ymt6Fwq9tTe9McmOSc5M8tbWtAO7u22wrM78wSJIW0cBBn+TJ\nwOeBd1fVj4CzgWcDa+iN+D8ylydOsi7JRJKJycnJuWwqSZqDgYI+yZ70Qv5TVfUFgKq6t6q2V9Uv\ngI/Rm54B2Aas6tt8ZWt7lKraUFXjVTU+NjY2zM8gSZrBIGfdBDgHuK2q/qSvfXlftzcCN7fli4Hj\nkuyV5FnAQcB1C1eyJGkuBjnr5lXAW4Gbkmxube8Hjk+yBijgTuDtAFV1S5ILgVvpnbFzimfcSNLo\nDHLWzdeATLPq0hm2OR04fYi6JEkLxG+vlKSOM+glqeMMeknqOINekjrOoJekjjPoJanjDHpJ6jiD\nXpI6zqCXpI4z6CWp4wx6Seo4g16SOs6gl6SOM+glqeMMeknqOINekjrOoJekjhvkUoJ6nNnnN9bz\nwo3rR1wDwFEjrUFSz6xBn2QVcD5wAL3rw26oqrOS7Ad8FlhN75qxx1bV/e1i4mcBRwIPASdW1fWL\nU76m8+PbzuDOM0YbsqvXf2mkzy9pyiAj+keAP6iq65PsA2xKchlwInB5VZ2RZD2wHngf8HrgoHZ7\nOXB2+3dROYqVZuYxMmWp7YtBLg5+D3BPW/5xktuAFcBa4NDWbSNwJb2gXwucX1UFXJNk3yTL2+Ms\nGkexms5SO6Bn4jEyZantiznN0SdZDbwEuBY4oC+8v0dvagd6LwJ39222tbU9KuiTrAPWARx44IFz\nLFsazFI7oKXpDHzWTZInA58H3l1VP+pf10bvNZcnrqoNVTVeVeNjY2Nz2VSSNAcDBX2SPemF/Keq\n6gut+d4ky9v65cB9rX0bsKpv85WtTZI0ArMGfTuL5hzgtqr6k75VFwMntOUTgIv62t+WnlcADyz2\n/LwkadcGmaN/FfBW4KYkm1vb+4EzgAuTnAzcBRzb1l1K79TKLfROrzxpQSuWJM3JIGfdfA3ILlYf\nPk3/Ak4Zsi5J0gLxKxAkqeMMeknqOINekjrOoJekjjPoJanjDHpJ6jiDXpI6zqCXpI4z6CWp4wx6\nSeo4g16SOs6gl6SOM+glqeMMeknqOINekjrOoJekjjPoJanjBrlm7LlJ7ktyc1/bB5JsS7K53Y7s\n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\nQS9JHff/AO4emsFOyrI2AAAAAElFTkSuQmCC\n",
      "text/plain": [
       "<matplotlib.figure.Figure at 0x1062c8160>"
      ]
     },
     "metadata": {},
     "output_type": "display_data"
    }
   ],
   "source": [
    "data[['Open','High','Low','Close','Adj Close']].plot(kind='box') #box plot to visually check for outliers"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 8,
   "metadata": {},
   "outputs": [],
   "source": [
    "#plotly figure prepration\n",
    "#setting a layout for figure. will be used later again. \n",
    "layout = go.Layout(\n",
    "    title='Stock Prices of Tesla',\n",
    "    xaxis=dict(\n",
    "        title='Date',\n",
    "        titlefont=dict(\n",
    "            family='Courier New, monospace',\n",
    "            size=18,\n",
    "            color='#7f7f7f'\n",
    "        )\n",
    "    ),\n",
    "    yaxis=dict(\n",
    "        title='Price',\n",
    "        titlefont=dict(\n",
    "            family='Courier New, monospace',\n",
    "            size=18,\n",
    "            color='#7f7f7f'\n",
    "        )\n",
    "    )\n",
    ")\n",
    "\n",
    "#data to be plot\n",
    "data_lst = [{'x':data['Date'], 'y':data['Close']}]\n",
    "fig = go.Figure(data=data_lst, layout=layout)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 9,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/html": [
       "<iframe id=\"igraph\" scrolling=\"no\" style=\"border:none;\" seamless=\"seamless\" src=\"https://plot.ly/~blueshriyan/12.embed\" height=\"525px\" width=\"100%\"></iframe>"
      ],
      "text/plain": [
       "<plotly.tools.PlotlyDisplay object>"
      ]
     },
     "execution_count": 9,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "py.iplot(fig) #plotting online\n",
    "\n",
    "#iplot(fig) #plotting offline"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "# Regression for Stock Price Prediction"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 10,
   "metadata": {},
   "outputs": [],
   "source": [
    "#For corss validation and hyper params selection\n",
    "from sklearn.model_selection import train_test_split\n",
    "from sklearn.model_selection import GridSearchCV\n",
    "\n",
    "#For preprocessing\n",
    "from sklearn.preprocessing import MinMaxScaler\n",
    "from sklearn.preprocessing import StandardScaler\n",
    "\n",
    "#For model evaluation\n",
    "from sklearn.metrics import mean_squared_error as mse\n",
    "from sklearn.metrics import r2_score"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 11,
   "metadata": {},
   "outputs": [],
   "source": [
    "#spliting data into train and test sets\n",
    "X = np.array(data.index).reshape(-1,1)\n",
    "Y = data['Close']\n",
    "X_train, X_test, Y_train, Y_test = train_test_split(X, Y, test_size=0.33, random_state=42)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 12,
   "metadata": {},
   "outputs": [
    {
     "name": "stderr",
     "output_type": "stream",
     "text": [
      "/Library/Frameworks/Python.framework/Versions/3.6/lib/python3.6/site-packages/sklearn/utils/validation.py:444: DataConversionWarning:\n",
      "\n",
      "Data with input dtype int64 was converted to float64 by StandardScaler.\n",
      "\n"
     ]
    }
   ],
   "source": [
    "# For feature scaling\n",
    "scaler = StandardScaler().fit(X_train)\n",
    "#scaler.transform(...)"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "## 1. Simple Linear Regression (Without Scaling) "
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 13,
   "metadata": {},
   "outputs": [],
   "source": [
    "from sklearn.linear_model import LinearRegression"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 14,
   "metadata": {},
   "outputs": [
    {
     "name": "stderr",
     "output_type": "stream",
     "text": [
      "/Library/Frameworks/Python.framework/Versions/3.6/lib/python3.6/site-packages/scipy/linalg/basic.py:1226: RuntimeWarning:\n",
      "\n",
      "internal gelsd driver lwork query error, required iwork dimension not returned. This is likely the result of LAPACK bug 0038, fixed in LAPACK 3.2.2 (released July 21, 2010). Falling back to 'gelss' driver.\n",
      "\n"
     ]
    },
    {
     "data": {
      "text/plain": [
       "LinearRegression(copy_X=True, fit_intercept=True, n_jobs=1, normalize=False)"
      ]
     },
     "execution_count": 14,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "#creating a linear model\n",
    "lreg = LinearRegression()\n",
    "lreg.fit(X_train, Y_train)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 15,
   "metadata": {},
   "outputs": [],
   "source": [
    "#plotting actual and predicted values for train dataset\n",
    "trace0 = go.Scatter(\n",
    "    x = X_train.T[0],\n",
    "    y = Y_train,\n",
    "    mode = 'markers',\n",
    "    name = 'Actual'\n",
    ")\n",
    "trace1 = go.Scatter(\n",
    "    x = X_train.T[0],\n",
    "    y = lreg.predict(X_train).T,\n",
    "    mode = 'lines',\n",
    "    name = 'Predicted'\n",
    ")\n",
    "data_lst = [trace0,trace1]\n",
    "layout.xaxis.title.text = 'Day'\n",
    "fig2 = go.Figure(data=data_lst, layout=layout)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 16,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/html": [
       "<iframe id=\"igraph\" scrolling=\"no\" style=\"border:none;\" seamless=\"seamless\" src=\"https://plot.ly/~blueshriyan/14.embed\" height=\"525px\" width=\"100%\"></iframe>"
      ],
      "text/plain": [
       "<plotly.tools.PlotlyDisplay object>"
      ]
     },
     "execution_count": 16,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "py.iplot(fig2)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 17,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "\n",
      "Metric           Train                Test        \n",
      "r2_score  0.8621706890708688\t0.8687654977642258\n",
      "MSE       1832.5856512729763\t1761.28951759388\n",
      "\n"
     ]
    }
   ],
   "source": [
    "#calculating scores for model evaluation\n",
    "scores = f'''\n",
    "{'Metric'.ljust(10)}{'Train'.center(20)}{'Test'.center(20)}\n",
    "{'r2_score'.ljust(10)}{r2_score(Y_train, lreg.predict(X_train))}\\t{r2_score(Y_test, lreg.predict(X_test))}\n",
    "{'MSE'.ljust(10)}{mse(Y_train, lreg.predict(X_train))}\\t{mse(Y_test, lreg.predict(X_test))}\n",
    "'''\n",
    "print(scores)"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "## 2. Simple Linear Regression (With Feature Scaling)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 18,
   "metadata": {},
   "outputs": [
    {
     "name": "stderr",
     "output_type": "stream",
     "text": [
      "/Library/Frameworks/Python.framework/Versions/3.6/lib/python3.6/site-packages/sklearn/utils/validation.py:444: DataConversionWarning:\n",
      "\n",
      "Data with input dtype int64 was converted to float64 by StandardScaler.\n",
      "\n"
     ]
    }
   ],
   "source": [
    "#feature scaling\n",
    "X_train_scaled = scaler.transform(X_train)\n",
    "X_test_scaled = scaler.transform(X_test)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 19,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "LinearRegression(copy_X=True, fit_intercept=True, n_jobs=1, normalize=False)"
      ]
     },
     "execution_count": 19,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "#model training\n",
    "lreg_scaled = LinearRegression()\n",
    "lreg_scaled.fit(X_train_scaled, Y_train)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 20,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "\n",
      "Metric           Train                Test        \n",
      "r2_score  0.8621706890708688\t0.8687654977642258\n",
      "MSE       1832.5856512729767\t1761.28951759388\n",
      "\n"
     ]
    }
   ],
   "source": [
    "#calculating scores\n",
    "scores = f'''\n",
    "{'Metric'.ljust(10)}{'Train'.center(20)}{'Test'.center(20)}\n",
    "{'r2_score'.ljust(10)}{r2_score(Y_train, lreg_scaled.predict(X_train_scaled))}\\t{r2_score(Y_test, lreg_scaled.predict(X_test_scaled))}\n",
    "{'MSE'.ljust(10)}{mse(Y_train, lreg_scaled.predict(X_train_scaled))}\\t{mse(Y_test, lreg_scaled.predict(X_test_scaled))}\n",
    "'''\n",
    "print(scores)"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "## 3. Polynomial Regression (Degree=3)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 21,
   "metadata": {},
   "outputs": [],
   "source": [
    "from sklearn.preprocessing import PolynomialFeatures"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 22,
   "metadata": {},
   "outputs": [],
   "source": [
    "#feature prepration \n",
    "poly = PolynomialFeatures(3)\n",
    "X_train_poly = poly.fit_transform(X_train)\n",
    "X_test_poly = poly.fit_transform(X_test)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 23,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "LinearRegression(copy_X=True, fit_intercept=True, n_jobs=1, normalize=False)"
      ]
     },
     "execution_count": 23,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "#model training\n",
    "lreg3 = LinearRegression()\n",
    "lreg3.fit(X_train_poly, Y_train)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 24,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "\n",
      "Metric           Train                Test        \n",
      "r2_score  0.8793012254409129\t0.8872216199767171\n",
      "MSE       1604.8171531304124\t1513.5911300929192\n",
      "\n"
     ]
    }
   ],
   "source": [
    "#calculating scores\n",
    "scores = f'''\n",
    "{'Metric'.ljust(10)}{'Train'.center(20)}{'Test'.center(20)}\n",
    "{'r2_score'.ljust(10)}{r2_score(Y_train, lreg3.predict(X_train_poly))}\\t{r2_score(Y_test, lreg3.predict(X_test_poly))}\n",
    "{'MSE'.ljust(10)}{mse(Y_train, lreg3.predict(X_train_poly))}\\t{mse(Y_test, lreg3.predict(X_test_poly))}\n",
    "'''\n",
    "print(scores)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 25,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/html": [
       "<iframe id=\"igraph\" scrolling=\"no\" style=\"border:none;\" seamless=\"seamless\" src=\"https://plot.ly/~blueshriyan/16.embed\" height=\"525px\" width=\"100%\"></iframe>"
      ],
      "text/plain": [
       "<plotly.tools.PlotlyDisplay object>"
      ]
     },
     "execution_count": 25,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "#plotting actual and predicted values for train dataset\n",
    "trace0 = go.Scatter(\n",
    "    x = X_train.T[0],\n",
    "    y = Y_train,\n",
    "    mode = 'markers',\n",
    "    name = 'Actual'\n",
    ")\n",
    "trace1 = go.Scatter(\n",
    "    x = X_train.T[0],\n",
    "    y = lreg3.predict(X_train_poly).T,\n",
    "    mode = 'markers',\n",
    "    name = 'Predicted'\n",
    ")\n",
    "data_lst = [trace0,trace1]\n",
    "layout.xaxis.title.text = 'Day'\n",
    "fig3 = go.Figure(data=data_lst, layout=layout)\n",
    "py.iplot(fig3)"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "## 4. Ridge Regression + Polynomial Regression (with Date and Open Price as input features)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 26,
   "metadata": {},
   "outputs": [],
   "source": [
    "from sklearn.linear_model import Ridge"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 27,
   "metadata": {},
   "outputs": [],
   "source": [
    "#spliting data into \n",
    "X = np.array([data.index, data['Open']]).T\n",
    "Y = data['Close']\n",
    "X_train, X_test, Y_train, Y_test = train_test_split(X, Y, test_size=0.33, random_state=42)\n",
    "\n",
    "#transforming input features\n",
    "poly = PolynomialFeatures(3)\n",
    "X_train_poly = poly.fit_transform(X_train)\n",
    "X_test_poly = poly.fit_transform(X_test)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 28,
   "metadata": {},
   "outputs": [
    {
     "name": "stderr",
     "output_type": "stream",
     "text": [
      "/Library/Frameworks/Python.framework/Versions/3.6/lib/python3.6/site-packages/scipy/linalg/basic.py:40: RuntimeWarning:\n",
      "\n",
      "scipy.linalg.solve\n",
      "Ill-conditioned matrix detected. Result is not guaranteed to be accurate.\n",
      "Reciprocal condition number/precision: 6.3631197869480334e-24 / 1.1102230246251565e-16\n",
      "\n"
     ]
    },
    {
     "data": {
      "text/plain": [
       "Ridge(alpha=0.1, copy_X=True, fit_intercept=True, max_iter=None,\n",
       "   normalize=False, random_state=None, solver='auto', tol=0.001)"
      ]
     },
     "execution_count": 28,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "#training model\n",
    "clf = Ridge(alpha=0.1)\n",
    "clf.fit(X_train_poly, Y_train) "
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 29,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "\n",
      "Metric           Train                Test        \n",
      "r2_score  0.9981624609030438\t0.9980694836336763\n",
      "MSE       24.432014932342298\t25.909331628663445\n",
      "\n"
     ]
    }
   ],
   "source": [
    "#calculating scores for model evaluation\n",
    "scores = f'''\n",
    "{'Metric'.ljust(10)}{'Train'.center(20)}{'Test'.center(20)}\n",
    "{'r2_score'.ljust(10)}{r2_score(Y_train, clf.predict(X_train_poly))}\\t{r2_score(Y_test, clf.predict(X_test_poly))}\n",
    "{'MSE'.ljust(10)}{mse(Y_train, clf.predict(X_train_poly))}\\t{mse(Y_test, clf.predict(X_test_poly))}\n",
    "'''\n",
    "print(scores)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 30,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/html": [
       "<iframe id=\"igraph\" scrolling=\"no\" style=\"border:none;\" seamless=\"seamless\" src=\"https://plot.ly/~blueshriyan/18.embed\" height=\"525px\" width=\"100%\"></iframe>"
      ],
      "text/plain": [
       "<plotly.tools.PlotlyDisplay object>"
      ]
     },
     "execution_count": 30,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "# plotting predicted and actual values for train dataset\n",
    "trace0 = go.Scatter(\n",
    "    x = X_train.T[0],\n",
    "    y = Y_train,\n",
    "    mode = 'markers',\n",
    "    name = 'Actual'\n",
    ")\n",
    "trace1 = go.Scatter(\n",
    "    x = X_train.T[0],\n",
    "    y = clf.predict(X_train_poly).T,\n",
    "    mode = 'markers',\n",
    "    name = 'Predicted'\n",
    ")\n",
    "data_lst = [trace0,trace1]\n",
    "layout.xaxis.title.text = 'Day'\n",
    "fig3 = go.Figure(data=data_lst, layout=layout)\n",
    "py.iplot(fig3)"
   ]
  }
 ],
 "metadata": {
  "kernelspec": {
   "display_name": "Python 3",
   "language": "python",
   "name": "python3"
  },
  "language_info": {
   "codemirror_mode": {
    "name": "ipython",
    "version": 3
   },
   "file_extension": ".py",
   "mimetype": "text/x-python",
   "name": "python",
   "nbconvert_exporter": "python",
   "pygments_lexer": "ipython3",
   "version": "3.6.2"
  }
 },
 "nbformat": 4,
 "nbformat_minor": 2
}
