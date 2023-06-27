# Weight decay = 0

I reorganized the code in such a way that I have one single, nice file displaying all parameters clearly. These are the current parameters:

![alt text](https://github.com/MarcoFurlan99/3_no_weight_decay/blob/master/images/parameters.png?raw=true)

As you can see I set weight decay to 0.

Let's compare first and foremost how the results changed. These results are referred to a SINGLE set of datasets, specifically the one where $\mu_2 - \mu_1 = 80$, and therefore should NOT be taken too generally. So, in this specific case shown here:


![alt text](https://github.com/MarcoFurlan99/3_no_weight_decay/blob/master/images/samples.png?raw=true)

There are the results:

WITH weight decay:

![alt text](https://github.com/MarcoFurlan99/3_no_weight_decay/blob/master/images/with_weight_decay.png?raw=true)

WITHOUT weight decay:

![alt text](https://github.com/MarcoFurlan99/3_no_weight_decay/blob/master/images/without_weight_decay.png?raw=true)

WITH weight decay:

<img src="https://github.com/MarcoFurlan99/3_no_weight_decay/blob/master/images/pred_with_wd.png?raw=true" width=50% height=50%>

![alt text](https://github.com/MarcoFurlan99/3_no_weight_decay/blob/master/images/pred_with_wd.png?raw=true)

WITHOUT weight decay:

![alt text](https://github.com/MarcoFurlan99/3_no_weight_decay/blob/master/images/pred_without_wd.png?raw=true)

So results seem to be more or less analogous in this specific example.

Latent space, on the other hand, stabilized. Here is a per-image latent space (16 samples from the 0-th dimension of the 4x4x1024 latent space):

![alt text](https://github.com/MarcoFurlan99/3_no_weight_decay/blob/master/images/per_image_ls.png?raw=true)

And here is the combined graph, always relative to the 0-th dimension:

![alt text](https://github.com/MarcoFurlan99/3_no_weight_decay/blob/master/images/dim0.png?raw=true)

And here are the first 12 dimensions:

![alt text](https://github.com/MarcoFurlan99/3_no_weight_decay/blob/master/images/per_dimension_ls.png?raw=true)