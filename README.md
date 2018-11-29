<meta name="image" property="og:image" content="https://github.com/CalculatedContent/PredictingTestAccuracies/blob/master/img/vgg-w_alphas.png">

## Weight Watcher

**Weight Watcher** analyzes the Fat Tails in the  weight matrices of Deep Neural Networks (DNNs).

This tool can predict the trends in the generalization accuracy of a series of DNNs, such as VGG11, VGG13, ...,
or even the entire series of ResNet models--without needing a test set !

This relies upon recent research into the [Heavy (Fat) Tailed Self Regularization in DNNs](https://openreview.net/forum?id=SJeFNoRcFQ)
 
The tool lets one compute a averager capacity, or quality, metric for a series of  DNNs, trained on the same data, but with different hyperparameters, or even different but related architectures. For example, it can predict that VGG19_BN generalizes better than VGG19, and better than VGG16_BN, VGG16, etc.  



### Types of Capacity Metrics:
There are 2 metrics availabe. The average **log Norm**, which is much faster but less accurate.
The average **weighted alpha** is more accurate but much slower because it needs to both compute the SVD of the layer weight matrices, and thenaa
fit the singluar/eigenvalues to a power law.

- log Norm (default, fast, less accurate)
- weighted alpaha (slow, more accurate)

Here is an example of the **Weighted Alpha** capacity metric for all the current pretrained VGG models.
![alt text](https://github.com/CalculatedContent/PredictingTestAccuracies/blob/master/img/vgg-w_alphas.png)

Notice: we *did not peek* at the ImageNet test data to build this plot.

### Frameworks supported

- Keras
- PyTorch


### Layers supported 

- Dense / Linear / Fully Connected (and Conv1D)
- Conv2D



## Installation

```sh
pip install weightwatcher
```

## Usage

Weight Watcher works with both Keras and pyTorch models.

```python
import weightwatcher as ww
watcher = ww.WeightWatcher(model=model)
results = watcher.analyze()

watcher.get_summary()
watcher.print_results()
```

## Advanced Usage 

The analyze function has several features described below

```python
def analyze(self, model=None, layers=[], min_size=50, max_size=0,
                compute_alphas=False, compute_lognorms=True,
                plot=False):
...
```

and in the [Demo Notebook](https://github.com/CalculatedContent/WeightWatcher/blob/master/WeightWatcher.ipynb)


### weighted alpha (SLOW)
Power Law fit, here with pyTorch example

```python
import weightwatcher as ww
import torchvision.models as models

model = models.vgg19_bn(pretrained=True)
watcher = ww.WeightWatcher(model=model)
results = watcher.analyze(compute_alphas=True)
data.append({"name": "vgg19bntorch", "summary": watcher.get_summary()})


### data:
{'name': 'vgg19bntorch',
  'summary': {'lognorm': 0.81850576,
   'lognorm_compound': 0.9365272010550088,
   'alpha': 2.9646726379493287,
   'alpha_compound': 2.847975521455623,
   'alpha_weighted': 1.1588882728052485,
   'alpha_weighted_compound': 1.5002343912892515}},
```


#### Capacity Metrics (evarages over all layers):
- lognorm:  average log norm, fast
- alpha_weight:  average weighted alpha, slow

- alpha:  average alpha, not weighted  (slow, not as useful)

Compound averages: 

  Same as above, but averages are computed slightly differently. This will be desrcibed in an upcoming paper.

Results are also provided for every layer; see [Demo Notebook](https://github.com/CalculatedContent/WeightWatcher/blob/master/WeightWatcher.ipynb)

### Additional options
 
#### filter by layer types 

```python
results = watcher.analyze(layers=ww.LAYER_TYPE.CONV1D|ww.LAYER_TYPE.DENSE)

```

#### filter by ids

```python
results = watcher.analyze(layers=[20])
```

#### minimum, maximum size of weight matrix

Sets the minimum and maximum size of the weight matrices analyzed.
Setting max is useful for a quick debugging.

```python
results = watcher.analyze(min_size=50, max_size=500)
```

#### plots (for weight_alpha=True)

Create log-log plots for each layer weight matrix to observe how well
the power law fits work

```python
results = watcher.analyze(compute_alphas=True, plot=True)
```


## Links

[Demo Notebook](https://github.com/CalculatedContent/WeightWatcher/blob/master/WeightWatcher.ipynb)

[Calculation Consulting homepage](https://calculationconsulting.com)

[Calculated Content Blog](https://calculatedcontent.com)

[Orginal research: 
Implicit Self-Regularization in Deep Neural Networks: Evidence from Random Matrix Theory and Implications for Learning](https://arxiv.org/abs/1810.01075)

[Early YouTube research talk](https://www.youtube.com/watch?v=_Ni5UDrVwYU)

[Current ICLR 2019 submnission](https://openreview.net/forum?id=SJeFNoRcFQ)

Latest R&D Talk (to be presented at UC Berkeley/ICSI 12/13/2018)  N/A yet

## License

[Apache License 2.0](LICENSE)
