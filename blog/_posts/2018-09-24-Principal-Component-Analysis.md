## Principal Component Analysis an aproach 

This post will be commit until the end of month


<h1 align="center"> Principle Component Analysis (PCA) for Data Visualization </h1>


```python
import pandas as pd 
import numpy as np
import matplotlib.pyplot as plt
from sklearn.decomposition import PCA
from sklearn.preprocessing import StandardScaler
%matplotlib inline
plt.style.use('seaborn')
```

## Load Iris Dataset


```python
url = "https://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data"
```


```python
# loading dataset into Pandas DataFrame
df = pd.read_csv(url
                 , names=['sepal length','sepal width','petal length','petal width','target'])
```


```python
df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>sepal length</th>
      <th>sepal width</th>
      <th>petal length</th>
      <th>petal width</th>
      <th>target</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>5.1</td>
      <td>3.5</td>
      <td>1.4</td>
      <td>0.2</td>
      <td>Iris-setosa</td>
    </tr>
    <tr>
      <th>1</th>
      <td>4.9</td>
      <td>3.0</td>
      <td>1.4</td>
      <td>0.2</td>
      <td>Iris-setosa</td>
    </tr>
    <tr>
      <th>2</th>
      <td>4.7</td>
      <td>3.2</td>
      <td>1.3</td>
      <td>0.2</td>
      <td>Iris-setosa</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4.6</td>
      <td>3.1</td>
      <td>1.5</td>
      <td>0.2</td>
      <td>Iris-setosa</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5.0</td>
      <td>3.6</td>
      <td>1.4</td>
      <td>0.2</td>
      <td>Iris-setosa</td>
    </tr>
  </tbody>
</table>
</div>



## Standardize the Data

Since PCA yields a feature subspace that maximizes the variance along the axes, it makes sense to standardize the data, especially, if it was measured on different scales. Although, all features in the Iris dataset were measured in centimeters, let us continue with the transformation of the data onto unit scale (mean=0 and variance=1), which is a requirement for the optimal performance of many machine learning algorithms.


```python
features = ['sepal length', 'sepal width', 'petal length', 'petal width']
x = df.loc[:, features].values
```


```python
y = df.loc[:,['target']].values
```


```python
x = StandardScaler().fit_transform(x)
```


```python
pd.DataFrame(data = x, columns = features).head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>sepal length</th>
      <th>sepal width</th>
      <th>petal length</th>
      <th>petal width</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>-0.900681</td>
      <td>1.032057</td>
      <td>-1.341272</td>
      <td>-1.312977</td>
    </tr>
    <tr>
      <th>1</th>
      <td>-1.143017</td>
      <td>-0.124958</td>
      <td>-1.341272</td>
      <td>-1.312977</td>
    </tr>
    <tr>
      <th>2</th>
      <td>-1.385353</td>
      <td>0.337848</td>
      <td>-1.398138</td>
      <td>-1.312977</td>
    </tr>
    <tr>
      <th>3</th>
      <td>-1.506521</td>
      <td>0.106445</td>
      <td>-1.284407</td>
      <td>-1.312977</td>
    </tr>
    <tr>
      <th>4</th>
      <td>-1.021849</td>
      <td>1.263460</td>
      <td>-1.341272</td>
      <td>-1.312977</td>
    </tr>
  </tbody>
</table>
</div>



## PCA Projection to 2D


```python
pca = PCA(n_components=2)
```


```python
principalComponents = pca.fit_transform(x)
```


```python
principalDf = pd.DataFrame(data = principalComponents
             , columns = ['principal component 1', 'principal component 2'])
```


```python
principalDf.head(5)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>principal component 1</th>
      <th>principal component 2</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>-2.264542</td>
      <td>0.505704</td>
    </tr>
    <tr>
      <th>1</th>
      <td>-2.086426</td>
      <td>-0.655405</td>
    </tr>
    <tr>
      <th>2</th>
      <td>-2.367950</td>
      <td>-0.318477</td>
    </tr>
    <tr>
      <th>3</th>
      <td>-2.304197</td>
      <td>-0.575368</td>
    </tr>
    <tr>
      <th>4</th>
      <td>-2.388777</td>
      <td>0.674767</td>
    </tr>
  </tbody>
</table>
</div>




```python
df[['target']].head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>target</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Iris-setosa</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Iris-setosa</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Iris-setosa</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Iris-setosa</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Iris-setosa</td>
    </tr>
  </tbody>
</table>
</div>




```python
finalDf = pd.concat([principalDf, df[['target']]], axis = 1)
finalDf.head(5)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>principal component 1</th>
      <th>principal component 2</th>
      <th>target</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>-2.264542</td>
      <td>0.505704</td>
      <td>Iris-setosa</td>
    </tr>
    <tr>
      <th>1</th>
      <td>-2.086426</td>
      <td>-0.655405</td>
      <td>Iris-setosa</td>
    </tr>
    <tr>
      <th>2</th>
      <td>-2.367950</td>
      <td>-0.318477</td>
      <td>Iris-setosa</td>
    </tr>
    <tr>
      <th>3</th>
      <td>-2.304197</td>
      <td>-0.575368</td>
      <td>Iris-setosa</td>
    </tr>
    <tr>
      <th>4</th>
      <td>-2.388777</td>
      <td>0.674767</td>
      <td>Iris-setosa</td>
    </tr>
  </tbody>
</table>
</div>



## Visualize 2D Projection

Use a PCA projection to 2d to visualize the entire data set. You should plot different classes using different colors or shapes. Do the classes seem well-separated from each other? 


```python
fig = plt.figure(figsize = (8,8))
ax = fig.add_subplot(1,1,1) 
ax.set_xlabel('Principal Component 1', fontsize = 15)
ax.set_ylabel('Principal Component 2', fontsize = 15)
ax.set_title('2 Component PCA', fontsize = 20)


targets = ['Iris-setosa', 'Iris-versicolor', 'Iris-virginica']
colors = ['r', 'g', 'b']
for target, color in zip(targets,colors):
    indicesToKeep = finalDf['target'] == target
    ax.scatter(finalDf.loc[indicesToKeep, 'principal component 1']
               , finalDf.loc[indicesToKeep, 'principal component 2']
               , c = color
               , s = 50)
ax.legend(targets)
ax.grid()
```


![png](/blog/figs/2018-09-11-Principal-Component-Analysis/output_21_0.png)


The three classes appear to be well separated! 

iris-virginica and iris-versicolor could be better separated, but still good!

## Explained Variance

The explained variance tells us how much information (variance) can be attributed to each of the principal components.


```python
pca.explained_variance_ratio_
```




    array([0.72770452, 0.23030523])



Together, the first two principal components contain 95.80% of the information. The first principal component contains 72.77% of the variance and the second principal component contains 23.03% of the variance. The third and fourth principal component contained the rest of the variance of the dataset. 

## What are other applications of PCA (other than visualizing data)?

If your learning algorithm is too slow because the input dimension is too high, then using PCA to speed it up is a reasonable choice. (<b>most common application in my opinion</b>). We will see this in the MNIST dataset. 

If memory or disk space is limited, PCA allows you to save space in exchange for losing a little of the data's information. This can be a reasonable tradeoff.

## What are the limitations of PCA? 

- PCA is not scale invariant. check: we need to scale our data first. 
- The directions with largest variance are assumed to be of the most interest 
- Only considers orthogonal transformations (rotations) of the original variables 
- PCA is only based on the mean vector and covariance matrix. Some distributions (multivariate normal) are characterized by this, but some are not. 
- If the variables are correlated, PCA can achieve dimension reduction. If not, PCA just orders them according to their variances. 
