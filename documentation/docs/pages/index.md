
# Home

!!! warning "Under construction"
    This documentation is still under construction. It will be developed during the alpha testing. We welcome any [contributions](/contributing).

Welcome to the documentation for the [ACCESS-AM3 model configurations](https://github.com/ACCESS-NRI/access-am3-configs)! 

!!! info "Licensing information"
    This model uses components and science configurations from the UK Met Office (UKMO), and as such are license restricted. This means the associated repositories are required to be private. If you would like to be granted access to these repositories, please get in touch [on the ACCESS-Hive forum](https://forum.access-hive.org.au/t/request-access-to-am3-configurations/5580).

## ACCESS-AM3 Documentation Overview

In this documentation, you will find:

- information on how to contribute to the development of ACCESS-AM3, its configurations and their documentation
- details of the supported configurations, including information on the provenance of the inputs and their creation, on some evaluation and on the outputs from the model
- technical details of the model's architecture and the build, deployment and testing infrastructure of the model.

## access-am3-configs Overview
ACCESS-AM3 configurations are provided via branches in the [access-am3-configs](https://github.com/ACCESS-NRI/access-am3-configs) GitHub repository, which is currently private. This repository contains several configurations using the following components:

- [UM](https://github.com/ACCESS-NRI/UM) atmosphere model
- [CABLE](https://github.com/CABLE-LSM/CABLE) land model
- [JULES](https://github.com/ACCESS-NRI/JULES) land model

All configurations use the [_Rose/Cylc_ workflow management tools](https://docs.access-hive.org.au/models/run_a_model/rose_cylc/) and pre-built executables available on [NCI](https://nci.org.au/).

### Repository structure

Each configuration in [github.com/ACCESS-NRI/access-am3-configs](https://github.com/ACCESS-NRI/access-am3-configs) repository is stored as a git branch. Most of the ACCESS-NRI supported branches are named according to the following naming scheme:

* {dev|release}-{nominal_resolution}

where {nominal_resolution} is the spectral resolution following the conventions from the UKMO.

#### Supported configurations

* dev-n96e: global coupled atmosphere-land at N96e resolution.

#### How to use this repository to run a model

The [access-am3-configs](https://github.com/ACCESS-NRI/access-am3-configs) repository contains many related experimental configurations to facilitate support and discovery. Users will not need all the configurations at once, but rather a single one. Use the information in this documentation to choose the configuration most appropriate to your work, then follow the instructions in the [Run ACCESS-AM3](https://docs.access-hive.org.au/pr-previews/1058/models/run_a_model/run_access-am3/) guide to retrieve and run your chosen configuration.

#### CI and Reproducibility Checks

This repository will make use of GitHub Actions to perform reproducibility checks on supported configuration branches.
