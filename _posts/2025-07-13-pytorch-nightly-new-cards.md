---
title: "[til] pytorch nightly with uv for nvidia blackwell"
date: 2025-07-13
tags: nvidia til torch blackwell uv
---

Today I learned some things about installing a nightly build of pytorch on Ubuntu 24.04.
I did this to support an Nvidia GeForce RTX 5070 laptop card.
The 50 series cards are based on the Blackwell architecture. 

# Nvidia driver

I installed the nvidia driver with apt. It was important to use the open version,

```bash
sudo apt install nvidia-driver-570-open
```

> For cutting-edge platforms such as NVIDIA Grace Hopper or NVIDIA Blackwell, you must use the open-source GPU kernel modules. The proprietary drivers are unsupported on these platforms. <cite>[nvidia-transitions-fully-towards-open-source-gpu-kernel-modules](https://developer.nvidia.com/blog/nvidia-transitions-fully-towards-open-source-gpu-kernel-modules/)


# Nvidia compute capability

> Compute capability (CC) defines the hardware features and supported instructions for each NVIDIA GPU architecture.
> <cite>[https://developer.nvidia.com/cuda-gpus](https://developer.nvidia.com/cuda-gpus)</cite>

The site referenced above lists the compute capability of various cards.
The compute capability is expressed as a version number. 
The GeForce RTX 5070 has compute capability 12.0.

# torch and Nvidia compute capability

At the time of writing, the stable version of [torch](https://pytorch.org/get-started/locally/) is 2.7.1.
On a system that supports cuda, we can check the compute capabilities supported by a version of torch.

```python
> import torch
> print(torch.cuda.get_arch_list())
['sm_50', 'sm_60', 'sm_70', 'sm_80', 'sm_86', 'sm_90']
```

This indicates that torch 2.7.1 supports Nvidia compute capabilities 5.0, 6.0, 7.0, 8.0, 8.6, and 9.0.
The datacenter cards (GH200, H200, H100) use compute capability 9.0.
The datacenter cards (GB200, B200) use compute capability 10.0.
The 5070 we are interested in uses compute capability 12.0.

This means we are going to have to install a more recent version than 2.7.1 

# torch Preview (nightly) builds with uv

> Stable represents the most currently tested and supported version of PyTorch. This should be suitable for many users. Preview is available if you want the latest, not fully tested and supported, builds that are generated nightly. <cite>[torch](https://pytorch.org/get-started/locally/)</cite>

In order to setup a uv environment with pytorch nightly I did the following,

```bash
mkdir tmp
cd tmp
uv init
uv add ipython
uv add numpy
```

This results in a `pyproject.toml` file like this,

```toml
[project]
name = "tmp"
version = "0.1.0"
description = "Add your description here"
readme = "README.md"
requires-python = ">=3.12"
dependencies = [
    "ipython>=9.4.0",
    "numpy>=2.3.1",
]
```

Next I used the [uv pip interface](https://docs.astral.sh/uv/pip/) to install the nightly build,

```bash
uv pip install --pre --force-reinstall --index-url https://download.pytorch.org/whl/nightly/cu128 torch
```

This allowed me to check that the 12.0 compute capability was supported and to start using the card with torch.

```python
> import torch
> torch.__version__
2.9.0.dev20250712+cu128
> print(torch.cuda.get_arch_list())
['sm_70', 'sm_75', 'sm_80', 'sm_86', 'sm_90', 'sm_100', 'sm_120']
```




