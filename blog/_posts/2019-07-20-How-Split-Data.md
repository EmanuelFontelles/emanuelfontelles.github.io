---
title: "How split data can affect you data evaluation"
subtitle: "Random Split, Stratified Slit in 'How your metrics could be affect'"
layout: post
tags : [archlinux]
---

## From a binary class pespective

1+1=3

## As simple as Logistic Regression


## Validation model

> When to split

### Preprocessing



```python
import numpy as np
import seaborn as sns;
import matplotlib.pyplot as plt; plt.style.use('ggplot')
```


```python
N = int(10e6)
```


```python
log_normal = np.random.lognormal(size=N)
```


```python
var_log, mean_log = log_normal.var(), log_normal.mean()
```


```python
var_log, mean_log
```




    (4.658509594268878, 1.6486913090041344)




```python
dist = sns.distplot(log_normal)
```


![png](output_5_0.png)



```python
log_normal_sample = np.random.choice(log_normal, size=int(0.3*N))
```


```python
var_log_sample, mean_log_sample = log_normal_sample.var(), log_normal_sample.mean()
```


```python
var_log_sample, mean_log_sample
```




    (4.617970633233202, 1.6470042949353507)




```python
dist_sample = sns.distplot(log_normal_sample)
```


![png](output_9_0.png)



```python

```


### Trainnig

### Metrics

### Fine Tunning
