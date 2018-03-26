Wrapping spike sorters
======================

In Alex's draft (https://github.com/flatironinstitute/spikesortercomparison/blob/master/metrics_datasets_notes.pdf) it is indicated that we have already wrapped several popular spike sorting algorithms as processors in MountainLab. This should be clarified... yes they have been wrapped (to some extent), but there are technical issues that need to be addressed.

* **MountainSort** -- this has been wrapped

* **JRClust** -- only a proof-of-principle wrapping. James and Jeremy are working on making a suitable wrapper. The main issue is how to pass in and work with parameter files.

* **Spyking Circus** -- made a preliminary wrapper (http://github.com/magland/ml_spyking_circus), but it needs to be improved in order to expose some important parameters, to make sure we are doing the right thing w.r.t. probe files, and to address an issue relating to MPI running in docker containers. Also, the installation instructions are out-dated -- need to update those.

* **KiloSort** -- made a preliminary wrapper (https://github.com/magland/ml_kilosort), but it needs to be improved. A snapshot of the source code is included in the source code of the wrapper. But this is perhaps not the most up-to-date version of the software. We need to figure out how to deal with this issue -- one possibility is to include the KiloSort code as a git submodule. But then the question is, do we always update this submodule when changes are made to KiloSort?

Need to think about versioning of wrapped processing servers.