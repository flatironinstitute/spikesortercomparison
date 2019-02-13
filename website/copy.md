# Copy for SpikeForest website

[this doc is a mix of text, markdown, and latex math between $$, plan to use w/ MathML]

[notice it includes advice for how to interact - something I believe is needed for users who are older than Gen Z.]

* [version 0.7 by Alex 2/7/19]

* [edited by jfm 2/13/19]
    * removed the manual line breaks and changed headings and other parts to #markdown style
    * changed "Tools" in About page to a new page called "Internals" -- we can discuss
    * drafted "Internals" page -- outline form for now
    * various edits throughout



### TODO:

* complete the names of collaborators who contributed recordings.

* explain the grouping into study sets

* when latex MathML understood, fix the subscript mbox's to be right size (eg insert \tiny \rm).

* Provide [link to details] from the Internals page




## Header

header [for all pages]:  Home  Recordings  Sorters  Metrics  Internals  About

[The copy for each of these is given in the ## sections below]


## Main Page (Home)

Electrical recording from extracellular probes is a popular and affordable method to capture the simultaneous activity of many neurons in the brain or retina. There is a great need to quantify the reliability of the firing events extracted from the recordings by _spike sorting_ algorithms. This SpikeForest website addresses this need, assessing many popular spike sorting codes via "ground truth" recordings, which are the gold standard in terms of accuracy. We host a variety of experimental paired ground truth recordings from the community, and also many _in silico_ synthetic recordings. Each sorter is run on all recordings, and the resulting accuracies for the ground truth units are updated on a daily basis as needed. Approximate CPU/GPU run times are also reported.

This software is open source: you can browse all datasets, algorithms, sorting results, and comparisons, and inspect the source code used to generate these data. One goal is for neuroscientists to best choose the spike sorter appropriate for their probe type and brain region.

Please click on any entry of the performance matrix below for more details.  Use the links above to learn about recordings, sorters, metric definitions, and website.

[heatmap text]: Results overview

[in top-left corner of heatmap axes:] study \ sorter

[radio buttons]:
accuracy [mouseover: balances false positive and false negatives]
precision [mouseover: penalizes false positives only]
recall [mouseover: penalizes false negatives, ie missed spikes, only]

## Recordings

Our hosted recordings include many popular probe geometries and types, and fall into two categories:

1. "Paired" recordings from various laboratories where an independent intra- or juxta-cellular probe provides reliable ground-truth firing events, usually for one neuron per recording.
2. Simulated (in silico) recordings from neural simulator codes with various degrees of fidelity, with known firing events for large numbers of neurons.
3. We also host a small set of expert human-curated sorting results.

Recordings are grouped into "studies".  Each study contains a set of real or synthesized recordings sharing a common source (probe and brain region, or simulation code settings). It is appropriate to aggregate the statistics from all recordings within a particular study.

In turn, studies are grouped into "study sets".  Each study set is a collection of studies which have different parameters but share some common features.

Below is the current list of studies in SpikeForest.  Click on each to see an expanded list of the recordings within each study.

[are study sets used to group here?]

[now auto-gen table...]

## Sorters

Generally speaking, spike sorting algorithms take in an unfiltered multi-channel timeseries (aka, recording) and a list of algorithm parameters, and output a list of firing times and associated integer unit labels.

This page lists the spike sorting codes we run, as well as some that have yet to be incorporated.  Most of the codes were developed at other institutions; two of them are in-house.  Click on the links in [column x] to get to the original git repo, and [column y] for a docker image that enables you to install it easily.

[some info re bundled packages - Jeremy]

[now auto-gen table...]

## Metrics

Here we define the accuracy and other metrics used throughout SpikeForest.  Different accuracy metrics may be selected via radio buttons on the main page.

### Matching of firing events

We first describe how labeled firing events output by a sorter are matched to a given list of ground truth events, with firing times $s_i$, $i=1,\dots,n_{GT}$.  Consider the sorted unit labeled $k$.  Let $t_j^k$ for $j=1,\dots,n_k$ be the firing times of this unit from the sorter.  Let $\Delta t$ be an acceptable firing time error, which we assume is shorter than half the refractory period of a true neuron. The number of matches of unit $k$ to the ground truth is

$n_\mbox{match}^k := \# \{i: |t_j^k-s_i| < \Delta t \mbox{ for some } j\}$

Note that if more than one sorted event falls within $\pm \Delta t$ of a true event, only one is matched. The converse cannot happen, by assumption.

The number of missed events is $n_\mbox{miss}^k := n_GT - n_\mbox{match}^k$.

The number of false positive events is $n_\mbox{fp}^k := n_k - n_\mbox{match}^k$.

To translate to the notation of Jun et al 2017,
$n_1 = n_\mbox{miss}^k$,
$n_2 = n_\mbox{match}^k$,
$n_3 = n_\mbox{fp}^k$.

For code implementation, this matching of sorted events to ground-truth events uses a [routine from
`spiketoolkit`](https://github.com/flatironinstitute/spikeforest2/blob/dev/repos/spiketoolkit/spiketoolkit/comparison/sortingcomparison.py)

The default $\Delta t$ is set as `delta_tp` there, in sample units (note: the actual time thus depends on the sample rate).

### Accuracy metrics

For a given ground truth unit, and sorted unit $k$, the metrics are as follows, in terms of the above counts:

Precision $p^k := \frac{n_\mbox{match}^k}{n_\mbox{match}^k+n_\mbox{fp}^k}
 = \frac{n_\mbox{match}^k}{n^k} = \frac{n_2}{n_2+n_3}$

Note: precision = 1 - false positive rate.

Recall $r_k := \frac{n_\mbox{match}^k}{n_\mbox{miss}^k+n_\mbox{match}^k}
 = \frac{n_\mbox{match}^k}{n_{GT}} = \frac{n_2}{n_1+n_2}$

Note: recall  = 1 - false negative rate.

Accuracy $a_k := \frac{n_\mbox{match}^k}{n_\mbox{miss}^k+n_\mbox{match}^k+n_\mbox{fp}^k} = \frac{n_\mbox{match}^k}{n^k + n_{GT}-n_\mbox{match}} = \frac{n_2}{n_1+n_2+n_3}$

Accuracy balances precision and recall.  It is similar, but not identical, to the clustering metric called the $F_1$ score.

Code: see `GenSortingComparisonTable` in [compare_with_truth.py](https://github.com/flatironinstitute/spikeforest2/blob/dev/spikeforest_analysis/compare_with_truth.py">compare_with_truth.py)

### Best matching unit

For each ground truth unit, the _best matching_ unit $k$ to the ground truth firing time list $s_i$ is the one with the highest accuracy $a_k$, as defined above. Only results for this unit are reported.

### Other metrics of a unit

SNR (signal-to-noise ratio) is a property of a single neural unit, either a ground truth unit or a unit as output by a sorter. To compute it, the filtered recording $y_m(t)$ where $m = 1,\dots,M$ is the channel index and $t$ the time point, is needed, and the list of firing times.

[say something about standardized filtering].

Firstly the mean waveforms $w_m^k(t)$ are extracted for each unit label $k$. Here the time index is in the sample range $1\le t \le T$.

Then,

$\mbox{SNR} := \frac{\max_{m=1,\dots,M, \, t=1,\dots,T} |w_m^k(t)|}{\sigma_m}$

where a robust estimate of the (Gaussian-standardized) standard deviation for the $m$th channel is,

$\sigma_m = \frac{\mbox{MAD}_m}{0.6745}$,

where, for each channel $m$, the median absolute deviation of the filtered data is defined by 

$\mbox{MAD}_m = \mbox{median}_{(y_m(t) - \overline{y_m})
$

where $\overline{y_m} := \frac{1}{T} \sum_{t=1}^T y_m(t)$ is the channel average.

The above are estimated by stochastic sampling in large datasets.

For the scaling factor for the MAD, see [here](https://en.wikipedia.org/wiki/Median_absolute_deviation)

Here is the code we use for computing SNR: [compute_units_info.py](href="https://github.com/flatironinstitute/spikeforest2/blob/dev/spikeforest_analysis/compute_units_info.py)


## Internals

All code used internally by SpikeForest is open source and independently run-able. The system consists of the following:

* A set of spike sorting algorithms
    * Each algorithm consists of the following:
        * Environment: A docker file and image representing the environment required for running the software. [link to details]
        * Python wrapper: A MountainLab processor used to execute the software. [link to details]
    * As a secondary goal, reseachers may use the SpikeForest infrastructure to run spike sorting on their own computers. [link to details]

* A set of registerd ground truth recordings organized into studies and study sets
    * Recordings are represented in a simple flat format and are publicly available. [link to details]
    * Recordings or portions thereof may be downloaded using the kbucket/cairio python interface. [link to details]
    * The SpikeInterface [link] tools provide a means to convert electrophysiology recordings from various formats into the format used by SpikeForest. [link to details]

* Analysis pipelines
   * SpikeForest includes scripts for running the registered spike sorting algorithms (with various parameters) on the registered recordings, comparing the sorting results with ground truth, and assembling the results into publicly-accessible result files that provide the content for the front-end website. [link to details]
   * To promote reproducibility and scientific transparency, all analysis pipelines are open source, publicly available, and may be run outside the SpikeForest infrastructure in order to independently reproduce the findings of this website. [link to details]

* This front-end website for exploring recordings, spike sorting algorithms, and sorting results
    * Liz to describe more here

TODO: diagram of dependence of repos and tools.



## About

headings:   Credits     History and Future    Related Projects      Contact

jfm: note that I moved "Tools" to a new "Internals" page

Put this somewhere: Please use git issues to report problems - and see Contact.



Credits
-------

SpikeForest is a project of the Center for Computational Mathematics,
Flatiron Institute, NYC.
[include links].

Jeremy Magland (chief infrastructure developer, back-end, recording
simulation)
Liz Lovero, Scientific Computing Core (site design and visualizations, front-end)
James Jun (infrastructure, testing, and recording wrangling and simulation)
Alex Barnett (concept and site design, testing)

[please edit if inaccurate] ETC

[do as 2-col table?]

We are grateful for many collaborators at other institutions for
their vital help:

Loren Frank (UCSD)
Jason Chung (UCSD)
Catalin Mitelut (Columbia)
Dan English (...)
[please add!]
ETC


History and future
------------------

This project is the fruition of a long-term goal within the spike sorting effort of CCM, starting in 2014 at what was then the SCDA. The original spike sorting effort comprised Jeremy Magland, Alex Barnett, and Leslie Greengard, and collaborators in Loren Frank's lab.

Early design principles were outlined in our [white paper](https://github.com/flatironinstitute/spikesortercomparison) of May 2018. This was inspired in part a spike-sorting community discussion at the Janelia spike sorting meeting of 3/22/18.

Influences for the concept and site design include:

* [ClustEval website](https://clusteval.sdu.dk).
  Wiwie, C., Röttger, R. & Baumbach, J. Comparing the performance of biomedical clustering methods. Nature Methods (2015).

* <a href="http://neurofinder.codeneuro.org/">NeuroFinder</a>
  by Jeremy Freeman, for calcium imaging spatial neuron extraction comparison.

* <a href="http://spikefinder.codeneuro.org">SpikeFinder</a>,
  by P. Berens, for extracting spikes from calcium imaging
  fluorescence curves.

* <a href="http://spike.g-node.org">G-Node</a>.
  A now-defunct 2011-2012 project wher the user
  uploads sorted data, which is compared against a hidden ground truth
  sorting and optionally published.

* <a href="http://phy.cortexlab.net/data/sortingComparison">cortexlab</a>,
  by N. Steinmetz. Comparison of several algorithms on hybrid data.

* <a href="http://www.spikesortingtest.com">Spikesortingtest</a> by
  C. Mitelut.

* <a href="http://simonster.github.io/SpikeSortingSoftware">SpikeSortingSoftware</a>.
  Older list of spike sorting codes and their features.


#### Changelog for website:

June 2019 : release v 1.0



#### Future plans:

Hybrid recordings.

Expansion of SNR to other surrogate metrics: noise overlap, etc.

Stability-based quality metrics.


Related Projects
----------------

Links to other software repositories



Contact
-------

If you have questions, comments, requests, new algorithms or recordings that we should host or incorporate, please use the following tool: [Give the URL for posting comments.]

If you would like to help develop SpikeForest, please contact Jeremy Magland directly, or use its github repo.

