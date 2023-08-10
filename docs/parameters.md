---
title: Parameters
description: >-
    Pipeline parameters and configuration.
---

# Parameters

The NextITS pipeline is made up of multiple connected processes. 
Each of these processes is amenable to configuration, 
empowering users to tailor not just the process parameters 
but also the resources allocated for each one (e.g., the number of CPUs). 
When setting the pipeline parameter values, users have two choices: 
they can specify them directly from the command line using double dash arguments (for example, `--its_region "full"`) 
or opt for a simple parameters file. Utilizing a parameters file is especially advantageous 
when dealing with numerous settings, as it offers a cleaner command line interface. 
In addition to this, individual processes within the pipeline can be fine-tuned using a dedicated config file.  
