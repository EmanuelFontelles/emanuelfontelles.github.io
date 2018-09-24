---
title: "Manifold Learning"
subtitle: "t-SNE "
layout: post
tags : [machine-learning, manifold-learning, tsne, t-distributed Stochastic Neighbor Embedding, multidimensional-scaling]

---

# Using t-SNE to visualize similar words from texts


First of all install dependencies

```bash
$ pip install gensim numpy matplotlib sklearn
```

We want to visualize the classification cluster from a data set, in our case it's a text, and we expect to classify the words in the text to  clusters with same kind of information. Think of that, we can search for `computer` and we want to classify the words with same meaning, like `laptop`, `laptop-computer`, `pc`, even `Dell`.

For do that we can use ***t-Distributed Stochastic Neighbor Embedding (t-SNE)***. t-SNE technique for dimensionality reduction that is particularly well suited for the visualization of high-dimensional datasets. The technique can be implemented via Barnes-Hut approximations, allowing it to be applied on large real-world datasets.


```python
import gensim
import numpy as np
import matplotlib.pyplot as plt
 
from sklearn.manifold import TSNE
```

The data can be downloaded from command prompt:

```bash
$ wget -c "https://s3.amazonaws.com/dl4j-distribution/GoogleNews-vectors-negative300.bin.gz"
```
Now we can train the our model directly using `gensim` a python module with support to word2vec.

```python
model = gensim.models.KeyedVectors.load_word2vec_format('GoogleNews-vectors-negative300.bin.gz', binary=True)
```


```python
# Test the loaded word2vec model in gensim
# We will need the raw vector for a word
print(model['computer']) 

# We will also need to get the words closest to a word
model.similar_by_word('computer')
```

    [ 1.07421875e-01 -2.01171875e-01  1.23046875e-01  2.11914062e-01
     -9.13085938e-02  2.16796875e-01 -1.31835938e-01  8.30078125e-02
      2.02148438e-01  4.78515625e-02  3.66210938e-02 -2.45361328e-02
      2.39257812e-02 -1.60156250e-01 -2.61230469e-02  9.71679688e-02
     -6.34765625e-02  1.84570312e-01  1.70898438e-01 -1.63085938e-01
     -1.09375000e-01  1.49414062e-01 -4.65393066e-04  9.61914062e-02
      1.68945312e-01  2.60925293e-03  8.93554688e-02  6.49414062e-02
      3.56445312e-02 -6.93359375e-02 -1.46484375e-01 -1.21093750e-01
     -2.27539062e-01  2.45361328e-02 -1.24511719e-01 -3.18359375e-01
     -2.20703125e-01  1.30859375e-01  3.66210938e-02 -3.63769531e-02
     -1.13281250e-01  1.95312500e-01  9.76562500e-02  1.26953125e-01
      6.59179688e-02  6.93359375e-02  1.02539062e-02  1.75781250e-01
     -1.68945312e-01  1.21307373e-03 -2.98828125e-01 -1.15234375e-01
      5.66406250e-02 -1.77734375e-01 -2.08984375e-01  1.76757812e-01
      2.38037109e-02 -2.57812500e-01 -4.46777344e-02  1.88476562e-01
      5.51757812e-02  5.02929688e-02 -1.06933594e-01  1.89453125e-01
     -1.16210938e-01  8.49609375e-02 -1.71875000e-01  2.45117188e-01
     -1.73828125e-01 -8.30078125e-03  4.56542969e-02 -1.61132812e-02
      1.86523438e-01 -6.05468750e-02 -4.17480469e-02  1.82617188e-01
      2.20703125e-01 -1.22558594e-01 -2.55126953e-02 -3.08593750e-01
      9.13085938e-02  1.60156250e-01  1.70898438e-01  1.19628906e-01
      7.08007812e-02 -2.64892578e-02 -3.08837891e-02  4.06250000e-01
     -1.01562500e-01  5.71289062e-02 -7.26318359e-03 -9.17968750e-02
     -1.50390625e-01 -2.55859375e-01  2.16796875e-01 -3.63769531e-02
      2.24609375e-01  8.00781250e-02  1.56250000e-01  5.27343750e-02
      1.50390625e-01 -1.14746094e-01 -8.64257812e-02  1.19140625e-01
     -7.17773438e-02  2.73437500e-01 -1.64062500e-01  7.29370117e-03
      4.21875000e-01 -1.12792969e-01 -1.35742188e-01 -1.31835938e-01
     -1.37695312e-01 -7.66601562e-02  6.25000000e-02  4.98046875e-02
     -1.91406250e-01 -6.03027344e-02  2.27539062e-01  5.88378906e-02
     -3.24218750e-01  5.41992188e-02 -1.35742188e-01  8.17871094e-03
     -5.24902344e-02 -1.74713135e-03 -9.81445312e-02 -2.86865234e-02
      3.61328125e-02  2.15820312e-01  5.98144531e-02 -3.08593750e-01
     -2.27539062e-01  2.61718750e-01  9.86328125e-02 -5.07812500e-02
      1.78222656e-02  1.31835938e-01 -5.35156250e-01 -1.81640625e-01
      1.38671875e-01 -3.10546875e-01 -9.71679688e-02  1.31835938e-01
     -1.16210938e-01  7.03125000e-02  2.85156250e-01  3.51562500e-02
     -1.01562500e-01 -3.75976562e-02  1.41601562e-01  1.42578125e-01
     -5.68847656e-02  2.65625000e-01 -2.09960938e-01  9.64355469e-03
     -6.68945312e-02 -4.83398438e-02 -6.10351562e-02  2.45117188e-01
     -9.66796875e-02  1.78222656e-02 -1.27929688e-01 -4.78515625e-02
     -7.26318359e-03  1.79687500e-01  2.78320312e-02 -2.10937500e-01
     -1.43554688e-01 -1.27929688e-01  1.73339844e-02 -3.60107422e-03
     -2.04101562e-01  3.63159180e-03 -1.19628906e-01 -6.15234375e-02
      5.93261719e-02 -3.23486328e-03 -1.70898438e-01 -3.14941406e-02
     -8.88671875e-02 -2.89062500e-01  3.44238281e-02 -1.87500000e-01
      2.94921875e-01  1.58203125e-01 -1.19628906e-01  7.61718750e-02
      6.39648438e-02 -4.68750000e-02 -6.83593750e-02  1.21459961e-02
     -1.44531250e-01  4.54101562e-02  3.68652344e-02  3.88671875e-01
      1.45507812e-01 -2.55859375e-01 -4.46777344e-02 -1.33789062e-01
     -1.38671875e-01  6.59179688e-02  1.37695312e-01  1.14746094e-01
      2.03125000e-01 -4.78515625e-02  1.80664062e-02 -8.54492188e-02
     -2.48046875e-01 -3.39843750e-01 -2.83203125e-02  1.05468750e-01
     -2.14843750e-01 -8.74023438e-02  7.12890625e-02  1.87500000e-01
     -1.12304688e-01  2.73437500e-01 -3.26171875e-01 -1.77734375e-01
     -4.24804688e-02 -2.69531250e-01  6.64062500e-02 -6.88476562e-02
     -1.99218750e-01 -7.03125000e-02 -2.43164062e-01 -3.66210938e-02
     -7.37304688e-02 -1.77734375e-01  9.17968750e-02 -1.25000000e-01
     -1.65039062e-01 -3.57421875e-01 -2.85156250e-01 -1.66992188e-01
      1.97265625e-01 -1.53320312e-01  2.31933594e-02  2.06054688e-01
      1.80664062e-01 -2.74658203e-02 -1.92382812e-01 -9.61914062e-02
     -1.06811523e-02 -4.73632812e-02  6.54296875e-02 -1.25732422e-02
      1.78222656e-02 -8.00781250e-02 -2.59765625e-01  9.37500000e-02
     -7.81250000e-02  4.68750000e-02 -2.22167969e-02  1.86767578e-02
      3.11279297e-02  1.04980469e-02 -1.69921875e-01  2.58789062e-02
     -3.41796875e-02 -1.44042969e-02 -5.46875000e-02 -8.78906250e-02
      1.96838379e-03  2.23632812e-01 -1.36718750e-01  1.75781250e-01
     -1.63085938e-01  1.87500000e-01  3.44238281e-02 -5.63964844e-02
     -2.27689743e-05  4.27246094e-02  5.81054688e-02 -1.07910156e-01
     -3.88183594e-02 -2.69531250e-01  3.34472656e-02  9.81445312e-02
      5.63964844e-02  2.23632812e-01 -5.49316406e-02  1.46484375e-01
      5.93261719e-02 -2.19726562e-01  6.39648438e-02  1.66015625e-02
      4.56542969e-02  3.26171875e-01 -3.80859375e-01  1.70898438e-01
      5.66406250e-02 -1.04492188e-01  1.38671875e-01 -1.57226562e-01
      3.23486328e-03 -4.80957031e-02 -2.48046875e-01 -6.20117188e-02]

    [('computers', 0.79793781042099),
     ('laptop', 0.6640492081642151),
     ('laptop_computer', 0.6548866629600525),
     ('Computer', 0.6473335027694702),
     ('com_puter', 0.6082081198692322),
     ('technician_Leonard_Luchko', 0.5662747025489807),
     ('mainframes_minicomputers', 0.5617724061012268),
     ('laptop_computers', 0.5585451126098633),
     ('PC', 0.5539618134498596),
     ('maker_Dell_DELL.O', 0.5519253611564636)]




```python
def display_closestwords_tsnescatterplot(model, word):
    
    arr = np.empty((0,300), dtype='f')
    word_labels = [word]

    # get close words
    close_words = model.similar_by_word(word)
    
    # add the vector for each of the closest words to the array
    arr = np.append(arr, np.array([model[word]]), axis=0)
    for wrd_score in close_words:
        wrd_vector = model[wrd_score[0]]
        word_labels.append(wrd_score[0])
        arr = np.append(arr, np.array([wrd_vector]), axis=0)
        
    # find tsne coords for 2 dimensions
    tsne = TSNE(n_components=2, random_state=0)
    np.set_printoptions(suppress=True)
    Y = tsne.fit_transform(arr)

    x_coords = Y[:, 0]
    y_coords = Y[:, 1]
    # display scatter plot
    plt.scatter(x_coords, y_coords)

    for label, x, y in zip(word_labels, x_coords, y_coords):
        plt.annotate(label, xy=(x, y), xytext=(0, 0), textcoords='offset points')
    plt.xlim(x_coords.min()+0.00005, x_coords.max()+0.00005)
    plt.ylim(y_coords.min()+0.00005, y_coords.max()+0.00005)
    plt.show()
```


```python
display_closestwords_tsnescatterplot(model, 'life')
```

![png](/blog/figs/2018-09-03-Manifold-Learning/output_7_1.png)