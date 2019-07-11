---
title: python实现kmeans算法
date: 2018-09-13 19:48:00
tags:
---

这是我上学期的数据科学课程的一次 python 作业中的一道题，要求我们在只借助 numpy，pandas 这种基础库的情况下，自己实现 kmeans 算法。现在过了小半年了，重新回顾一下。
<!--more-->

## 原理解析

我跟我搭档的这份代码可以明确分为两个部分，一个是数据的初始化部分，一个是kmeans所利用到的Lloyd算法。

在kmeans初始化阶段采取了kmeans++的思想。具体原理如下，

>1. 从输入的数据点集合（要求有$k$个聚类）中随机选择一个点作为第一个聚类中心
>2. 对于数据集中的每一个点x，计算它与最近聚类中心(指已选择的聚类中心)的距离$D(x)$
>3. 选择一个新的数据点作为新的聚类中心，选择的原则是：$D(x)$较大的点，被选取作为聚类中心的概率较大
>4. 重复2和3直到$k$个聚类中心被选出来

这原理很抽象，下面介绍一下具体是如何实现的。在代码实现的过程中，聚类的个数k会作为函数的参数传进来。然后第一步，随机选取一点作为聚类中心，然后将所有点与该中心的距离进行相加得到距离之和$\sum_{i=0}^{n}D(x_i)$，同时用一个数组记录每个距离$D(x_i)$。然后在$[0,\sum_{i=0}^{n}D(x_i)]$这一区间随机选取一个数作为比较点p。这时遍历之前生成的数组，并且对距离再次进行累加，直到找到一个点m使得$\sum_{i=0}^{m}D(x_i)>p$, 将m点作为第一个完成初始化的中心。显然，如果$D(x)$越大，被选中的概率越大。

从第二个聚类中心开始，将前一个完成初始化的中心作为当前步骤的起始聚类中心。

这次作业中我干的事情是实现Lloyd算法。这个算法按照以下两个步骤交替进行，

>- 分配
>将每个观测分配到聚类中，使得组内平方和（WCSS）达到最小。因为这一平方和就是平方后的欧氏距离，所以很直观地把观测分配到离它最近得均值点即可$$S_i^{(t)}=\\{ x_p:||x_p-m_i^{(t)}||^2 \leq |x_p-m_j^{(t)}||^2 \forall j, 1 \leq j \leq k\\}$$
>- 更新
>对于上一步得到的每一个聚类，以聚类中观测值的图心，作为新的均值点$$m_i^{(t+1)}=\frac{1}{s_i^{(t)}} \sum_{x_j \in S_i^{(t)}} x_j$$

个人觉得这些步骤就比较直白了，先将所有点初始化完成的中心，以此找到自己属于的中心，再将每个聚类计算新的中心（大概率不是已知点）。具体实现起来，就是计算出每个点对应几个中心的距离，挑出最小的距离的中心进行划分，然后再计算出中心。重复n次即可。代码实现起来也是个人觉得看起来比较优美（臭不要脸一次）。

## 代码实现

```python
def kmeans(X, n_cluster, random_seed=2, n_init=100):
    '''
    Function calculates the centroids after performing k-means on the given dataset.
    Function returns two values new calculated centers and labels for each datapoint.
    If we have n_cluster = 4 then labels from algorithm will correspond to values 0,1,2 and 3

    Args:
        X: np.array representing set of input data
        n_cluster: number of clusters to use for clustering
        random_seed: random seed to use for calling random function in numpy
        n_inint: max number of iterations to use for k-means
    Returns:
        centers: np.array representing the centers for n_clusters
        labels: np.array containing a label for each datapoint in X
    '''
    # The code below is inspired and refered to the pseudocode in lecture "Clustering"
    centers = np.zeros((n_cluster,X.shape[1]))
    labels = np.zeros_like(X)
    # YOUR CODE HERE
    labels = np.zeros(X.shape[0])

    # initialise clusters by K-means++ thoughts

    initial_centers_ind = np.zeros(n_cluster)
    distances = np.zeros(X.shape[0])

    #randomly pick the first center
    initial_centers_ind[0] = np.random.randint(X.shape[0])

    for i in range(0, n_cluster-1):
        sum_distance = 0
        flag = 0
        #compute distances from i_th center to all other points.
        for j in range(0, len(X)):
            tmpMax = 0
            distances[j] = np.linalg.norm(X[j] - X[int(initial_centers_ind[i])])
            sum_distance += distances[j]

        # pick the next center based on the length of distance. longer distance has higher probility
        random_loc = np.random.uniform(0,sum_distance)
        for j in range(0, len(X)):
            flag += distances[j]
            if flag > random_loc and not(j in initial_centers_ind):
                initial_centers_ind[i+1] = j #pick j as the data index of the next center
                break
    print ('initial centers:', initial_centers_ind)

    # initialise centers
    for i in range(len(centers)):
        centers[i] = X[int(initial_centers_ind[i])]

    for i in range(0, n_init):
        # for each point, assign them to the nearest center
        for j in range(0, len(X)):
            distance = np.inf
            for k in range(0, len(centers)):
                if np.linalg.norm(X[j]-centers[k]) < distance:
                    labels[j] = k
                    distance = np.linalg.norm(X[j]-centers[k])
        # for each point group, calculate the new center
        for k in range(0, len(centers)):
            num_point = 0
            sum_point = np.zeros_like(X[0])
            for j in range(0, len(X)):
                if k == labels[j]:
                    sum_point = sum_point + X[j]
                    num_point = num_point + 1
            centers[k] = sum_point / num_point

    return centers,labels

## change the parameters of the function call to test your implementation
centers, labels = kmeans(X[:,:2],n_cluster=6, random_seed=5, n_init=100)
```
