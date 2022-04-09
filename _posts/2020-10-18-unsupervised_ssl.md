---
layout: post
title: Self-Supervised Learning for Large-Scale Unsupervised Image Clustering
date: 2020-10-18 17:08:00+0300
description: Short summary of our latest paper
---

In this post I'll summarize our latest paper, ["Self-Supervised Learning for Large-Scale Unsupervised Image Clustering"](https://arxiv.org/abs/2008.10312). In this work we found out that self-supervised learning is a strong baseline for fully unsupervised clustering of ImageNet (39% accuracy with 1000 clusters and 46% with 1500).

{% twitter https://twitter.com/evgeniyzhe/status/1298136113639497729 %}


Self-supervised learning became very popular recently, especially among large companies who have access to large amount of unlabelled data. In approximately last year, contrasitive losses have significantly boosed the performance of the self-supervised computer vision (you can get more details in blog posts in [English](https://lilianweng.github.io/lil-log/2019/11/10/self-supervised-learning.html) or [Russian](https://dyakonov.org/2020/06/03/%d1%81%d0%b0%d0%bc%d0%be%d0%be%d0%b1%d1%83%d1%87%d0%b5%d0%bd%d0%b8%d0%b5-self-supervision/), which unfortunately age very fast).

In particular, we try to review current state-of-the-art in the paper introduction. Lets me remind you how self-supervised approaches are usually evaluated. There are two main apporaches: either train a linear classifier on extracted features (some use k-NN instead), or fine-tune the network (usually on 1-10% of ImageNet, or on some segmentation dataset, e.g., COCO). Both have thier problem: first approach may fail if our features are good but not suitable for linear (or some other simple) classification; second one strongly depends on the way of fine tuning (cf. SimCLR v2). I was wondering for quite some time why nobody tried to employ a fully unsupervised pipeline to evaluate those methods. Since there appeared some attempts on unsupervised ImageNet clustering, I felt it is a good time to implement this idea. Since most of self-supervised methods publish both codes and models, we were able to perform extensive evaluation on relatively scarce compute.

First of all, we needed to choose metrics, and evaluation of unsupervised learning is a hard task. Luckily, we have labels and thus we can calculate accuracy. In addition, we calculate some metrics based on pairwise accuracy (i.e., a pair is counted as positive if we managed to predict whether this pair belongs to the same cluster or to two different ones). We also adjust those metrics to random chance, acquiring two main metrics: adjusted Rand Index (ARI) and adjusted mutual information (AMI). We also added non-adjusted MI since it was used in some previous works.

Since our goal is to create baseline, the approach is as simple as possible: we extract features from trainign and validation, apply PCA, and then train k-means. Linear assignment is used to calculate accuracy. This alone allows to achieve nearly SOTA results for unsupervised clustering. We also compared to top supervised CNNs. The results are highly correlated with linear classifier performance. One interesting exception is networks with high dimension of embedding space, which might be a shortcoming of k-means (or might be not). 

Moreover, we tested our approach on ObjectNet. Unfortunately, if trained on ImageNet no network performed better than random, even if we used validation set for label assignment (one notable exception is BigBiGAN). Moreover, supervised networks have similar problems.

Nevertheless, if we train k-means on ObjectNet itself, we can get some non-trivial results (6.5% accuracy). Interestingly, there is no significant accuracy difference between classes include in ImageNet and ones that are not.

Finally we performed small ablation study: checked the influence of dimentions of input (after PCA) and number of clusters (for less that 2000 clusters label-independent metrics increase).

The paper provides some interesting insights and poses some questions which can not be answered that fast and require more fundamental work to answer.