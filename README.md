# Bench2Drive-GenAD
## 概要
Bench2Drive包含进行闭环评测的[Bench2Drive](https://github.com/Thinklab-SJTU/Bench2Drive)这个仓库以及模型的仓库[Bench2DriveZoo](https://github.com/Thinklab-SJTU/Bench2DriveZoo/tree/uniad/vad)
，本仓库代码在Bench2DriveZoo仓库中集成了GenAD，绝大部分代码与Bench2DriveZoo相同

本仓库不包含Bench2Drive仓库的代码，闭环评测的代码未做改动，仅改动了运行的脚本，具体见后续说明</br>

服务器上项目路径：/mnt/nas/algorithm/hao.hong/code/Bench2Drive</br>
运行环境：/mnt/nas/algorithm/hao.hong/packages/miniconda3/envs/b2d_zoo</br>

## 环境配置
服务器上的环境和文件组织结构全都配置好了，不动就可以了

要重新配置可以拉取本仓库，参考Bench2DriveZoo仓库文档的Getting Started进行配置，然后参考Bench2Drive仓库的配置文档，将本仓库链接到闭环评测仓库中

需要注意的点：配置carla的python包时，要将carla文件夹中的.egg写入conda环境的path下，不然会有部分包读不到

## 模型训练与开环评测
训练与开环评测可以不需要配置carla，可以在集群上跑
训练脚本为adzoo/genad/dist_train.sh和adzoo/genad/dist_train_multi_nodes.sh
``` 
sh ./adzoo/genad/dist_train.sh ./adzoo/genad/configs/VAD/GenAD_config_b2d.py 1
```
```
sh ./adzoo/vad/multi_node_train.sh
```

开环评测：
```
sh ./adzoo/genad/dist_test.sh ./adzoo/genad/configs/VAD/GenAD_config_b2d.py ./work_dirs/GenAD_config_b2d/epoch_6.pth 1
```

详细训练和评测方法可见[Bench2DriveZoo](https://github.com/Thinklab-SJTU/Bench2DriveZoo/tree/uniad/vad)的文档

目前已经训练好的vad和genad的ckpt保存在路径:
/mnt/nas/algorithm/hao.hong/code/Bench2Drive/Bench2DriveZoo/work_dirs下

## 闭环评测
闭环评测需要用carla进行模拟，carla底层采用vulkan，在不进行x11转发时，vulkan识别不到显示就不会启动显卡，导致报错lavapipe is not a conformant vulkan implementation, testing use only.

因此闭环评测只能在开发机上做好x11转发才能跑

目前项目的bench2drive版本是旧版，与新版的评测脚本不同，以下内容可以作为参考，新版本可能不存在相同的bug：

目前项目中使用以下脚本可以启动评测：
- 多卡版本：leaderboard/scripts/run_evaluation_multi.sh
  
  脚本内的参数已经有注释出来

  多卡版本会将leaderboard/data/bench2drive220.xml这个路径信息文件按运行的卡的数量拆分成n个xml文件，然后分多个进程跑carla
  多卡版本不带debug信息，因此无法中断正在运行的路线，有些时候车辆卡住了，长时间动不了carla就会崩了，当单独一张卡上的程序崩了，还不能直接中断，因为其他进程还在运行，因此一般是等到崩了几张卡后再中断，重新执行脚本
- 单卡debug版本：leaderboard/scripts/run_evaluation_debug.sh
  
  单卡版本带debug信息，会显示carla的时间、系统运行时间、正在运行的线路，每条路线评测完也会显示评测结果

  单卡版本可以ctrl+c直接中断当前正在运行的路线，直接开始下一条路线

  由于有一部分路线会经常崩，因此评测的后期会有一部分卡的路线都跑完了，少量卡由于一直崩还差很多，我一般是最后再用单卡版本单独跑这些卡的任务，可以指定分割的xml文件，也在leaderboard/data下面，看到一个路线运行了很久，就可以从carla采集的相机影像看车是不是不动了，不动了就直接中断，然后把记录结果的json文件里对应路径的status改成“Failed - TickRuntime”（不改的话如果后面崩了下次运行读存档就又是从这里开始）
  
#### 其他
- 每次启动评测都要很久，是因为不知道为什么carla client连接服务器的carla server第一次连都会连不上，b2d里会一定延迟后重新再连接一次，第二次就可以正常连接上了，由于设置的延迟时间比较长，所以启动的比较慢，我尝试过把leaderboard/leaderboard/leaderboard_evaluator.py里的--timeout默认参数设为300(s)，就稍微快一点，但会出现其他的bug导致某些路线无法启动，因此还是默认的timeout时间比较合适
- 评测结果通过tools/merge_reoute_json.py将多卡运行的json文件合并，就可以看到最终的评分的成功率，通过tools/ability_benchmark.py计算每个子任务的能力值，用法见[Bench2Drive](https://github.com/Thinklab-SJTU/Bench2Drive)主页