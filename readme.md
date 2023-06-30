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

We get finally the nice normal distibution we were looking for FOR THIS SPECIFIC DATASET (the first one, $(\mu_1, \mu_2) = (10,90)$ ), of course we are seeing it after the relu. QUESTION: should we try to compute Wassersein before the relu? ANSWER: no! The estimated parameters are going to be $\mu = 0$ and $\sigma=1$ because that's literally the point of the BN. If we apply BN adaptation after, the parameters are going to be once again $\mu = 0$ and $\sigma=1$ and we get Wasserstein $\approx$ 0. BUT it may make sense to check the Wasserstein on test without BN adaptation!? In this second case I fear that we do not have a normal behaviour anymore. For now I'll focus on verifying latent space on other datasets.