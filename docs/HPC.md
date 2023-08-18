---
title: HPC
description: >-
    Learn how to use NextITS on HPC cluster.
---

# Usage on HPC cluster

NextITS leverages the capabilities of the [Nextflow](https://www.nextflow.io/) workflow manager, 
designed to maximize the efficiency and flexibility of your analyses by fully utilizing available computational resources. 
This means that you can seamlessly deploy the pipeline on high-performance computing (HPC) clusters, 
making it especially beneficial for large-scale datasets.  

Nextflow is compatible with all major job schedulers, including 
[Slurm](https://slurm.schedmd.com/documentation.html), 
[OpenPBS](https://www.openpbs.org/), 
[TORQUE](https://adaptivecomputing.com/cherry-services/torque-resource-manager/), 
and [Apache Ignite](https://ignite.apache.org/), 
to name a few. 
In the upcoming section, we'll guide you through the process of setting up and initiating NextITS on a cluster environment, 
like your University's computing cluster.  

!!! info "Tips for optimizing pipeline performance on HPC clusters"
    For an in-depth exploration, including details on specific workload manager features, please refer to  
    [https://www.nextflow.io/blog/2023/best-practices-deploying-pipelines-with-hpc-workload-managers.html].  

## Usage scenarios

Three potential usage scenarios are:  

- **Simple Mode**:  
    This scenario involves a single, self-contained job. 
    Here, Nextflow initiates all tasks on the same computing node it operates on. 
    The total amount of requested resources is cumulatively allocated to all tasks.  

- **Cluster Mode**:  
    In this scenario, Nextflow operates on a computing node, using a long-duration task with low resource allocation. 
    From this node, it begins dispatching separate jobs (e.g., using Slurm). 
    It's important to configure individual resource requirements for each job in this scenario, as they are autonomous of each other. 
    This mode is ideal for handling extensive datasets. 
    However, the turnaround time might increase based on the cluster's activity, since each task might have to wait its turn in the queue. 
    Despite this, the mode offers heightened flexibility because it allows for precise resource allocation and scaling based on the demands of individual tasks.  
    For a clearer understanding of this scenario, consider the following illustration:  
    ![The anatomy of an HPC cluster](https://github.com/nextflow-io/website/blob/master/assets/img/nextflow-on-big-iron-twelve-tips-for-improving-the-effectiveness-of-pipelines-on-hpc-clusters-1.jpg?raw=true)  
    (Image source: https://www.nextflow.io/blog/2023/best-practices-deploying-pipelines-with-hpc-workload-managers.html).  

- **Hybrid Workload**:  
    This versatile scenario integrates various resources (local/HPC/cloud). 
    Specific tasks, depending on their requirements, might be delegated to another infrastructure boasting superior hardware. 
    For instance, a sequence clustering task might be resource-intensive but can efficiently use multiple CPUs. 
    Such a task can be dispatched to a cloud service (e.g., AWS Batch), and once processed, 
    the results can be retrieved and the pipeline can continue executing other tasks using the primary executor. 
    This approach taps into the best of both worlds by using specialized resources for demanding tasks and local or primary resources for regular tasks.  


In this guide, we'll concentrate on the **simple scenario**, 
ideal for managing medium-to-large-sized datasets and aligning with the needs of most users. 
We'll primarily focus on configurations and commands tailored for **Slurm**, 
given its popularity in academic institutions. 
If you're uncertain about the workload manager employed on your HPC, please reach out to your IT department.  

## Resource requirements

When initiating a job on a cluster, consider specifying these resource requirements:

- **CPUs**: The required number of processor cores for the task.  
- **Memory**: Different processes might have distinct RAM needs. For instance, sequence clustering often demands more memory.  
- **Time**: The maximum duration allowed for the pipeline to run.  

The HPC scheduler utilizes these details to allocate your task to a suitable computing node. 
Determining the right resources often varies based on the dataset's size. 
Additionally, your system may have some restrictions based on its HPC policies. 
Overallocating can lead to underutilized resources, longer wait times, and reduced cluster efficiency for other users. 
Conversely, underallocating risks task failures.

