# Weight decay = 0

I reorganized the code in such a way that I have one single, nice file displaying all parameters clearly. These are the current parameters:

![alt text](https://github.com/MarcoFurlan99/3_no_weight_decay/blob/master/images/parameters.png?raw=true)

As you can see I set weight decay to 0.

Let's compare first and foremost how the results changed. These results are referred to a SINGLE set of datasets, specifically the one where $\mu_2 - \mu_1 = 80$, and therefore should NOT be taken too generally. So, in this specific case shown here:

<img src="https://github.com/MarcoFurlan99/3_no_weight_decay/blob/master/images/samples.png?raw=true" width=50% height=50%>

There are the results:

WITH weight decay:

![alt text](https://github.com/MarcoFurlan99/3_no_weight_decay/blob/master/images/with_weight_decay.png?raw=true)

WITHOUT weight decay:

![alt text](https://github.com/MarcoFurlan99/3_no_weight_decay/blob/master/images/without_weight_decay.png?raw=true)

WITH weight decay:

<img src="https://github.com/MarcoFurlan99/3_no_weight_decay/blob/master/images/pred_with_wd.png?raw=true" width=30% height=30%>

WITHOUT weight decay: (note: dataset is different but with same parameters)

<img src="https://github.com/MarcoFurlan99/3_no_weight_decay/blob/master/images/pred_without_wd.png?raw=true" width=30% height=30%>

So results seem to be more or less analogous in this specific example.

Latent space, on the other hand, stabilized. Here is a per-image latent space (16 samples from the 0-th dimension of the 4x4x1024 latent space):

![alt text](https://github.com/MarcoFurlan99/3_no_weight_decay/blob/master/images/per_image_ls.png?raw=true)

And here is the combined graph, always relative to the 0-th dimension:

![alt text](https://github.com/MarcoFurlan99/3_no_weight_decay/blob/master/images/dim0.png?raw=true)

And here are the first 12 dimensions:

![alt text](https://github.com/MarcoFurlan99/3_no_weight_decay/blob/master/images/per_dimension_ls.png?raw=true)

We get finally the nice normal distibution we were looking for FOR THIS SPECIFIC DATASET (the first one, $(\mu_1, \mu_2) = (10,90)$ ), of course we are seeing it after the ReLU. I will check with more datasets.

# Looking for a distance to predict BN adaptation

It came natural to check the behaviour right before the ReLU. After a little more coding here's what I found: the following are the first 12 dimensions of the latent space obtained feeding the 500 test images with parameters $(\mu_1, \mu_2) = (10,90)$ to the UNet traied on 5000 training images of parameters $(\mu_1, \mu_2) = (10,90)$:

![alt text](https://github.com/MarcoFurlan99/3_no_weight_decay/blob/master/images/per_dimension_noReLU.png?raw=true)

Very nice looking distributions! Just for clarity: we are in the last latent space, right AFTER the batch norm and right BEFORE the ReLU.

So where do we go from here? Here's my idea: we take the latent space histograms for not only the source dataset, but also for the target datasets and see if they behave differently. The following results are all taken WITHOUT batch norm adaptation (will do results also with).

dimension 0

<img src="https://github.com/MarcoFurlan99/3_no_weight_decay/blob/master/images/hists_dim0.png?raw=true">

dimension 1

<img src="https://github.com/MarcoFurlan99/3_no_weight_decay/blob/master/images/hists_dim1.png?raw=true">

dimension 2

<img src="https://github.com/MarcoFurlan99/3_no_weight_decay/blob/master/images/hists_dim2.png?raw=true">

dimension 3

<img src="https://github.com/MarcoFurlan99/3_no_weight_decay/blob/master/images/hists_dim3.png?raw=true">

For reference, the above graphs correspond index by index to these graphs:

<img src="https://github.com/MarcoFurlan99/3_no_weight_decay/blob/master/images/without_weight_decay.png?raw=true">

So my idea is now to compute some distance between the histograms to find a correlation with the BN improvement (right-most graph). The histograms show a difference which looks more and more evident the further the target dataset is from the source dataset; let's try and see how far we get.

I did not consider the Wasserstein for clear reasons (the histograms above are not normal in general, and are nothing but the projections of the 1024-dim distributions). One intuitive candidate that I found was the Bhattacharyya distance, which has a simple and intuitive way to calculate distance between sampled distributions using histograms (see [here](https://en.wikipedia.org/wiki/Bhattacharyya_distance) under "Applications"). Essentially it works like this:

Given two distributions $P,Q$, bin them into $n$ buckets, and let the frequency of samples from $P$ in bucket $i$ be $p_i$, and similarly $q_i$, then we can compute the **sample Bhattacharyya coefficient**:

$BC(p,q) = \sum\limits_{i=1}^{n} \sqrt{p_i q_i}$

The **sample Bhattacharyya distance** is then defined as:

$D_B(p,q) = -\ln (BC(p,q)) $

In my case more specifically I compute it in a slightly different but equivalent way: call $P_i,Q_i$ the amount of samples falling in the $i$-th bin, then it holds that $p_i = P_i / \sum_j P_j$ and $q_i = Q_i / \sum_j Q_j$. Consequently:

$BC(p,q) = \sum\limits_{i=1}^{n} \sqrt{p_i q_i} = \sum\limits_{i=1}^{n} \sqrt{P_i Q_i (\sum_j P_j \sum_j Q_j)^{-1}} = (\sum_j P_j \sum_j Q_j)^{-1} \sum\limits_{i=1}^{n} \sqrt{P_i Q_i}$

The last expression is how I computed it.

Following are some results. I took the log(1+distance*100) for simple rescaling purposes.

dim3

<img src="https://github.com/MarcoFurlan99/3_no_weight_decay/blob/master/images/Bhattacharyya_proc_dim3.png?raw=true" width=50% height=50%>

dim19

<img src="https://github.com/MarcoFurlan99/3_no_weight_decay/blob/master/images/Bhattacharyya_proc_dim19.png?raw=true" width=50% height=50%>

dim121

<img src="https://github.com/MarcoFurlan99/3_no_weight_decay/blob/master/images/Bhattacharyya_proc_dim121.png?raw=true" width=50% height=50%>

dim131

<img src="https://github.com/MarcoFurlan99/3_no_weight_decay/blob/master/images/Bhattacharyya_proc_dim131.png?raw=true" width=50% height=50%>

dim171

<img src="https://github.com/MarcoFurlan99/3_no_weight_decay/blob/master/images/Bhattacharyya_proc_dim171.png?raw=true" width=50% height=50%>

Recall that we want a predictor for the "Difference between the two graphs" graph. So we are close but not quite there. I'm working more on this, more results coming soon!

 # Extra: There is no Bessel's correction in BatchNorm2d

 With a simple example (file bessel.py) I verified that the batch norm implementation in torch.nn.BatchNorm2d does not use the Bessel's correction in the computation of the standard deviation, essentially it means that the std is computed like this:

 $\sigma = \sqrt{\frac{1}{N}\sum\limits_{i=0}^{N-1}(x_i - \bar x)^2}$

 and not like this:

 $\sigma = \sqrt{\frac{1}{N-1}\sum\limits_{i=0}^{N-1}(x_i - \bar x)^2}$

See bessel.ipynb for a simple model that shows it.

This implies that the std of the output, computed per-channel (aka per-feature, aka on the C dimension in  a BxCxWxH tensor), is NOT 1 (as would be with Bessel's correction). This is not a problem as far as I know, just a thing to keep in mind.

