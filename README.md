# rand_opt
Random search optimization and experiment logging. Support async, fancy visualization, distributed execution.

## Example
Here's a short example on how to use `randopt`.

```python
#!/usr/bin/env python

import randopt as ro

def loss(x):
    return x**2

if __name__ == '__main__':

    e = ro.Experiment('myexp', {
            'alpha': ro.Gaussian(mean=0.0, std=1.0, dtype='float'),
        })

    # Sampling parameters
    for i in xrange(100):
        e.sample('alpha')
        res = loss(e.alpha)
        print 'Result: ', res
        e.add_result(res)

    # Manually setting parameters
    e.alpha = 0.00001
    res = loss(e.alpha)
    e.add_result(res)

    # Search over all experiments results, including ones from previous runs
    opt = e.minimum()
    print 'Best result: ', opt.value, ' with params: ', opt.params
```

## Visualization
`randopt` also supports basic HTML visualization. After running an experiment, and using the `add_result` function, the following code can create a table containing the results. The `roviz.py` script will automatically launch the webpage. However, if you wish to view the HTML file for whatever reason, it's saved as `randopt_results/expName/viz.html`.

`python randopt/roviz.py -e expName`

By default, the visualizer sorts in order of ascending result. If you wish to visualize the data in a descending order, use the following command line argument.

`python randopt/roviz.py -e expName -s max`

## TODO

Check each python file or grep `TODO:` for a complete list of todos. Here's an overview.

* Unit Tests
* Documentation
* Improve HyperBand implementation
* Bayesian optimizaiton on previously ran experiments ?
