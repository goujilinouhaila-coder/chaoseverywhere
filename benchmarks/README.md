# Benchmarking chaoseverywhere

:warning: :information_source: To have a better visualization of the time managment, you can run the file `run_to_visualize.py` and wait a few seconds. We recommend setting the *depth* to `5` and the *Cutoff* to `1/100`.

In order to see the memory impact, please run the `memory_graph.py` file. Before using the `memory_graph.py` file, one must replace at lines 19, 41, 58, 65, 86, 116 and 136 the `#` with a `@` so that the `memory_profiler` and github can both be fully used.

A summary of the results is presented below in order to have an idea of the time-memory consumption before (maybe) downloading the package. Please note that a modelisation of the time consumption for the two functions created to build the 2D-mandelbrot set is present in the `report` folder at the root.
:information_source: :warning:

**Chosen functions** : We chose to only run functions that are the root of this module. Indeed, in most of the animations, we create the background once and then only add points or move around the structure. So the main question is not *How long does it take to render the movie?* but rather *How long does it take to produce each part of the movie?* We only applied the saying: *a chain is only as strong as its weakest link*.

## Time elapsed

We ran the creation of the Mandelbrot set with a number of iterations of 300 and a precision of 400. Note that in the animations, the parameters used are way lower and the difference between the two functions is almost non-existent. As for the `mandel_branch_points` functions, we used for parameters `(.01, -.5, 1000)` where `1000` is the number of horizontal lines (and also vertical lines) created in the plot. In reality, don't need more than `100` branches.

|      | mandel_loop | mandelbrot | mandel_branch_points |
|------|-------------|------------|----------------------|
| time | 12.3 s      | 9.79 s     |0.002 s               |

The most time consuming functions are the first two : the ones creating the actual Mandelbrot set.
Let's consider the logistic submodule now.

|      | logi_branch_points(.1,.3,1000) | bifurcation | logistic_draw(.1,3.4,300,300) |
|------|--------------------------------|-------------|-------------------------------|
| time | 0.016 s                        | 26.1 s      |11.81 s                        |

Just like before, the main (and static) pieces of the animations take more time to be generated but the moving parts of the scenes aren't time consuming at all considering that we don't even need that much points).

**Note** : Using numba on the Mandelbrot set function did not make a big difference. It can be a little useful only for the matplotlib animation rendering a zoom on the set.

## Memory use

The memory use flame graph can be visualized by running the `memory_graph.py` file.

:warning: We removed the `bifurcation` function in the memory graph because it was very time consuming in order to get the visualization. The main reason for that is the mainy calls to the `logistic` function which are necessary. We also only kept two memory graphs for the animations and deleted (on the fly) the folder they created (also on the fly). Other animations aren't shown here, because either it was done using mayavi, either the animations were done using matplotlib, and we made one of each.

On the flame-graph, we can see that the `mandel_loop` and the `mandelbrot` function are almost the same in a memory point of view with both just a little over `200` MiB. 

The construction of the animation using Mayavi is quite interesting because we can see the call at the beginning in order to create the `3D` Mandelbrot set and then the small oscillations very high that are generated by the camera change and the saving of each frame.

In comparison, the matplotlib animation has quite the same behaviour, only during a lot more time. And oscillates just under the `350` MiB.
