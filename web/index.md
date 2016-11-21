% randopt
% 
% November 20, 2016

# Introduction

`randopt` is a python package that will help you manage and run experiments. On top of managing and visualizing experiments, it provides functionalities for hyper-parameter search. It's still in its early days, and we are working on adding more features without clutter the current workflow.

# Installation

To install `randopt` execute

```shell
pip install randopt
```

source are also available on the GitHub repo

>[https://github.com/seba-1511/randopt/](https://github.com/seba-1511/randopt/)

# Overview

`randopt` provides two main utilities: a programmatic interface to experiments and a visualization tool. Here's what a typical randopt workflow looks like

1. Write (or wrap) your experiment with `randopt.Experiment`
2. Run your experiments (several times if you are searching for best hyper-parameters)
3. Analyse the results using `roviz.py`

### Simple Example

TL;DR: We're working through [this](https://github.com/seba-1511/randopt/blob/master/examples/simple.py) easy example.

Let's work through a [simple](https://github.com/seba-1511/randopt/blob/master/examples/simple.py) example; trying to find the minimum value of $x^2$ by random search. We first define our **loss function**, which will return the value we're trying to optimize for. 

```python

def loss(x):
    return x**2

```

Our example is trivial, and a real-world application will more likely be along the lines of minimizing the validation accuracy of a machine learning algorithm.

Now that we have a loss function we create an experiment using `randopt.Experiment(name, params)`.

```python

import randopt as ro
experiment = ro.Experiment('simple_example', {
    'alpha': ro.Gaussian(mean=0.0, std=0.5)
})

```

In the above snippet, `'simple_example'` is the name of the experiment, and `alpha` is the parameter we will sample. Note

1. you could have used any name (eg, `param_name`) instead of `alpha` except for `result`,
2. you can define any number of parameters, and
3. once sampled, their values are accessible at `experiment.param_name`.

Finally, we decided to sample the value of `alpha` according to a Gaussian distribution, with mean 0 and standard deviation 0.5. `randopt` provides several distributions, and it is easy to also add you own.

It is now time for us to sample, run our experiment, and save the result.

```python

for i in range(100):
    # Could use experiment.sample_all_params()
    experiment.sample('alpha')
    result = loss(experiment.alpha)
    experiment.add_result(result)

```

Here we decided to run our experiment a 100 times. As noted in the comment we could have used `experiment.sample_all_params()` instead of `experiment.sample('alpha')`. This is particularly useful when dealing with a large number of hyper-parameters. 

The crucial line is the call to `experiment.add_result(result)`. This will save a JSON file with with the current hyper-parameters and the obtained result. You can also choose to add more information in the JSON dump by passing a dictionary as the second parameter to `add_result`. For example, you could pass the list of validation errors of your training algorithm.

### Retrieving Results

There are two *preferred* ways to retrieve results from randopt. The first one is using `roviz.py`, which will generate a web-page with a summary of all results.

```
roviz.py -e simple_example
```

The an example of the web page generated by the above line is [available here](./simple_example/viz.html). Try clicking on the values of `alpha` !

The second option is to explore your results using the programmatic API. The following snippet demonstrates some of the available options to search through your results.

```python

mini = experiment.minimum() # Returns the exp. with minimum result
print 'Minimum result: ', mini.value, ', with params: ', mini.params

maxi = experiment.maximum() # Returns the exp. with maximum result
print 'Maximum result: ', maxi.value, ', with params: ', maxi.params

leq = lambda x, y: x <= y
top = experiment.top(3, fn=leq) # Best n according to fn. Default: leq

```

Of course, you can easily access each experiment run individually through its JSON dump, which is available in the `randopt_result/simple_example/` folder. 

### What's next

* Read the [doc]()
* Check out [more](https://github.com/seba-1511/randopt/blob/master/examples/hb_example.py) [complex](https://github.com/seba-1511/randopt/blob/master/examples/hb_vs_random.py) [examples](https://github.com/seba-1511/randopt/blob/master/examples/multi_params.py)
* Contribute !

# Roadmap
Here's a list of functionalities we would like to add to `randopt`

* More samplers
* HyperBand support
* Fancier HTML visualization - in particular wrt to plotting result, confidence intervals, etc...

# Contributors
The following people have made contributions to randopt

* Séb Arnold
* Noel Trivedi
* Cyrus Jia