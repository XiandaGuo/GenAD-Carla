# GenAD: Generative End-to-End Autonomous Driving

### [Paper](https://arxiv.org/pdf/2402.11502)

> GenAD: Generative End-to-End Autonomous Driving

> [Wenzhao Zheng](https://wzzheng.net/)\*, [Ruiqi Song](https://scholar.google.com.hk/citations?user=hMSOTPoAAAAJ&hl=zh-CN)\*, [Xianda Guo](https://scholar.google.com/citations?user=jPvOqgYAAAAJ)\* $\dagger$, Chenming Zhang, [Long Chen](https://scholar.google.com/citations?user=jzvXnkcAAAAJ)$\dagger$

\* Equal contributions $\dagger$ Corresponding authors

**GenAD casts autonomous driving as a generative modeling problem.**

## News 

- **[2024/11/10]**  Closed-loop evaluation code has been released.

## Demo

![vis](./assets/carla.png)

## Overview

![framework](./assets/framework.png)

**Comparisons of the proposed generative end-to-end autonomous driving framework with the conventional pipeline.** Bench2Drive comprises the [Bench2Drive](https://github.com/Thinklab-SJTU/Bench2Drive) repository for closed-loop evaluation and the model repository [Bench2DriveZoo](https://github.com/Thinklab-SJTU/Bench2DriveZoo/tree/uniad/vad). The code in this repository integrates GenAD within the Bench2DriveZoo repository, with the majority of the code being identical to that in Bench2DriveZoo. This repository does not contain the code from the Bench2Drive repository, and no modifications were made to the closed-loop evaluation code. Only the execution scripts were adjusted, as detailed in the following description.

## Evaluation Results

| Method | Closed-loop Score | Col. (%) 3s | L2 (m) 3s | Notes |
| ------ | ----------------- | ----------- | --------- | ----- |
| VAD    | 39.42             | 0.10        | 0.91      |       |
| GenAD  | 44.81             | 0.159       | 0.78      |       |

## Code 
### installation

Clone this repository and configure it according to the *Getting Started* section in the [Bench2DriveZoo](https://github.com/Thinklab-SJTU/Bench2DriveZoo/tree/uniad/vad) repository documentation. Refer to the configuration documentation in the [Bench2Drive](https://github.com/Thinklab-SJTU/Bench2Drive)  repository to link this repository to the closed-loop evaluation repository.

Detailed package versions can be found in [requirements.txt](../requirements.txt).

### Getting Started

#### Model Training

``` 
sh ./adzoo/genad/dist_train.sh ./adzoo/genad/configs/VAD/GenAD_config_b2d.py 1
```

**Note:** Detailed training and evaluation methods can be found in the documentation of [Bench2DriveZoo](https://github.com/Thinklab-SJTU/Bench2DriveZoo/tree/uniad/vad).

#### Open-Loop Evaluation

```
sh ./adzoo/genad/dist_test.sh ./adzoo/genad/configs/VAD/GenAD_config_b2d.py ./work_dirs/GenAD_config_b2d/epoch_.pth 1
```

#### Closed-Loop Evaluation

Eval GenAD with 8 GPUs

```shell
leaderboard/scripts/run_evaluation_multi.sh
```

Eval GenAD with 1 GPU

```shell
leaderboard/scripts/run_evaluation_debug.sh
```

**Note:** Detailed training and evaluation methods can be found in the documentation of [Bench2DriveZoo](https://github.com/Thinklab-SJTU/Bench2DriveZoo/tree/uniad/vad).

## Citation

If you find this project helpful, please consider citing the following paper:
```
@article{zheng2024genad,
    title={GenAD: Generative End-to-End Autonomous Driving},
    author={Zheng, Wenzhao and Song, Ruiqi and Guo, Xianda and Zhang, Chenming and Chen, Long},
    journal={arXiv preprint arXiv: 2402.11502},
    year={2024}
}
```