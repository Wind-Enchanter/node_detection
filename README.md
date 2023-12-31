# THREATRACE_based_train

## 概述

基于 https://github.com/threaTrace-detector/threaTrace 中的darpatc cadets处理与检测部分，修改了输入输出和模型训练的部分代码，使用自建数据集重新训练了模型并验证了效果

项目介绍见博客 https://blog.csdn.net/weixin_43832093/article/details/133088528

## 实现



### Clone和安装准备

  1. clone本项目到名为ROOT的文件夹中

  2. 部署 PyG，引导: https://pytorch-geometric.readthedocs.io/en/latest/notes/installation.html.
老版本pyg在这里下载：https://data.pyg.org/whl/

  4. 其他需要的Python包: 
  
    numpy, pandas, argparse, subprocess, os, sys, time, psutil, random, csv, re
    
  3. Python和一些包的版本: 
  
    Python 3.6.13
    pytorch 1.9.1
    torch-cluster 1.5.9
    torch-geometric 1.4.3
    torch-scatter 2.0.9
    torch-sparse 0.6.12
    torch-spline-conv 1.2.1

  4. 参考安装过程

    pip install torch_scatter torch_sparse torch_cluster torch_spline_conv -f https://data.pyg.org/whl/torch-1.9.1%2Bcpu.html
    pip install torch==1.9.1
    pip install torch_geometric==1.4.3 
    pip list  #(检查一下）

  5. 可能遇到的问题及解决

> AttributeError: 'NoneType' object has no attribute 'origin'

  参考https://zhuanlan.zhihu.com/p/407535625，重新离线安装torch相关
  
> ImportError: cannot import name 'container_abcs'

  修改报错的文件anaconda3/envs/node/lib/python3.6/site-packages/torch_geometric/data/dataloader.py
  
    # from torch._six import container_abcs, string_classes, int_classes
    import collections.abc as container_abcs
    string_classes = str
    int_classes = int
    
  
### 数据集准备

#### Darpa OpTC

https://github.com/FiveDirections/OpTC-data/tree/master


#### 自建数据集

    # 每条数据都处理成如下格式
    /* {
        "srcmsg":{"PROCESS": "MsMpEng.exe"}, 
        "dstmsg":{"FILE": "SOFTWARE\\Microsoft\\Windows Defender\\"}, 
        "edge_type": "READ", 
        "time": 1698285448637422700
        } */



### 使用Darpa OpTC训练模型

> 处理训练数据
    
    cd /ROOT/node_detection/parse_log
    python parse_darpa.py
    # 处理成如下格式
        /* {
        "srcmsg":{"PROCESS": "MsMpEng.exe"}, 
        "dstmsg":{"FILE": "SOFTWARE\\Microsoft\\Windows Defender\\"}, 
        "edge_type": "READ", 
        "time": 1698285448637422700
        } */

> 训练模型

    cd ROOT/threaTrace/scripts/train
    python train_model.py


### 使用自建数据集进行检测


模型训练好后，可以使用模型进行检测，输出到ROOT/threaTrace/scripts/output的三个文件中

    cd ROOT/threaTrace/scripts/detection
    python check_node.py --log <log_path> # log_path是指定要被检测的日志文件

**（如果被检测的日志无标记，复现到这就可以结束了）**

（如果被检测的日志有标记，可以放到ROOT/threaTrace/scripts/train/groundtruth里进行下一步评估）

检测结束后，评估模型效果

    cd ROOT/threaTrace/scripts/train
    python evaluate_darpatc.py



## License

This project is licensed under the MIT License - see the LICENSE file for details
