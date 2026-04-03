# G2 Cluster
[Cluster help page](https://it.coecis.cornell.edu/researchit/using-the-unicorn-cluster/)

We have access to the Unicorn Cluster at Cornell. It's great! But if you've never used a computing cluster before, it might be daunting to get started. We've all had to start somewhere. Here are some tips to get you started, and don't be afraid to ask for help as you need it.

## Setup Conda environment
In order to install and use python libraries, you should use conda
First create a new python environment:

    conda create --name CONDA_ENV_NAME python=3.9

When you want to use an environment, activate it:

    conda activate CONDA_ENV_NAME

Once activated, you can install python packages into the environment:

    conda install PACKAGENAME

Those packages are available anytime you run python while the environment is active. To activate a conda environment from an arbitrary node on the cluster, you need a little extra syntactic sugar (see [dynamic_arrays.sh](dynamic_arrays.sh)).

### Alternative method: using `uv`
Conda is a good place to start, but can be very annoying and slow. If you are frustrated with Conda and want to explore alternatives, [`uv`](https://docs.astral.sh/uv/getting-started/) is a replacement for `conda`/`pip`/`venv` that is ultimately faster and simpler to use, but has a slight learning curve. If you are pretty comfortable with Python, learning to use `uv` in each of your projects is probably the better long term choice. 

## Running jobs on the cluster

To access the cluster, you will ssh into a gateway node. You shouldn't run code on the gateway server because the gateways are underpowered and every other person logs into the cluster through the gateway, so using up its resources running jobs will prevent others from accessing the cluster. Instead, you can either:
* Run jobs interactively using an interactive session on a compute node. This is best for testing out small bits of code and debugging. To access an interactive node run this command from the gateway:  
    `srun -p PARTITION --pty /bin/bash`  
or  
    `srun --pty /bin/bash`  
* Submit jobs to a computing cluster via a SLURM script. This is the usual way to run code on the cluster. There is a demo SLURM script that documents several aspects of SLURM, which you can adapt to your jobs in this repo: [dynamic_arrays.sh](dynamic_arrays.sh).

### Multi-GPU Training 
There are some known gotchas with multi-GPU training on `compling` nodes. When using `accelerate` or `torchrun` to train with multiple GPUs, it is possible that you will run into NCCL errors that are difficult to parse. Below are some tips that can make your life slightly easier:
- Set `NCCL_P2P_DISABLE=1`. This fixes an issue where all the devices are not interconnected via NVLink (known issue on brandal).
- Set `NCCL_DEBUG=INFO`. This helps with debugging. Without it, it can be hard to trace errors caused by NCCL issues.
- Set `TORCH_DISTRIBUTED_DEBUG=DETAIL`. More verbose errors from PyTorch when running on multiple devices. 
- Kill zombie processes with `pkill`, especially in an interactive session if you are trying to diagnose an issue. `accelerate` and `torchrun` spawn separate processes per device, and they may not all shutdown if they fail on only one device.

[Back to Home](README.md)
