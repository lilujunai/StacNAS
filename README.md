# [StacNAS: Towards Stable and Consistent Optimization for Differentiable Architecture Search](https://openreview.net/forum?id=rygpAnEKDH)

The algorithm is based on continuous relaxation and gradient descent in the architecture space. It is able to efficiently design high-performance convolutional architectures for image classification (on CIFAR-10, CIFAR-100 and ImageNet). Only a single GPU is required.

For the experiments on ImageNet, see [this codebase](https://github.com/susan0199/stacnas)

<p align="center">
  <img src="images/two_stage.png" alt="two_stage" width="60%">
  <br/>Two Stages
</p>

## Requirements
```
Python >= 3.5.5, PyTorch >= 1.0.0, torchvision >= 0.2.0
```

## Datasets
CIFAR-10 and CIFAR-100 can be automatically downloaded by torchvision, ImageNet needs to be manually downloaded (preferably to a SSD) following the instructions [here](https://github.com/pytorch/examples/tree/master/imagenet).

## Pretrained models
The easiest way to get started is to evaluate our pretrained StacNAS models.

**CIFAR-10** ([cifar10.pth.tar](models/cifar10.pth.tar))
```
python test.py \
    --name job_name \
    --dataset cifar10 \
    --data_dir /path/to/dataset/ \
    --save_dir /path/to/results/ \
    --seed 1 \
    --stage test \
    --batch_size 96 \
    --load_model_dir "models/cifar10.pth.tar"
```
* Expected result: 2.48% test error rate with 3.9M model params.

Similar for CIFAR-100 and ImageNet.

## Architecture search stage1
To carry out architecture search using 1st-order approximation, run
```
python search.py \
    --name job_name \
    --dataset cifar10 \
    --train_ratio 1.0 \
    --data_dir /path/to/dataset/ \
    --save_dir /path/to/results/ \
    --seed 1 \
    --stage search1 \
    --batch_size 64 \
    --init_channels 16 \
    --num_cells 14 \
    --grad_clip 3 \
    --aux_weight 0.0 \
    --epochs 80 \
    --alpha_share
```
Note that train_ratio is the train-valid split ratio, train_ratio=1 means we use all the 50000 training images to search the architecure and use the final result as the best results.

## Architecture search stage2
To carry out architecture search2, run
```
python search.py \
    --name job_name \
    --dataset cifar10 \
    --train_ratio 1.0 \
    --data_dir /path/to/dataset/ \
    --save_dir /path/to/results/ \
    --seed 1 \
    --stage search2 \
    --batch_size 64 \
    --init_channels 16 \
    --num_cells 20 \
    --grad_clip 3 \
    --aux_weight 0.0 \
    --epochs 80 \
    --alpha_share
```
Note that train_ratio is the train-valid split ratio, train_ratio=1 means we use all the 50000 training images to search the architecure and use the final result as the best results.

## Architecture evaluation (using full-sized models)
To evaluate our best cells by training from scratch, run
```
python augment.py \
    --name job_name \
    --dataset cifar10 \
    --train_ratio 1.0 \
    --data_dir /path/to/dataset/ \
    --save_dir /path/to/results/ \
    --seed 1 \
    --stage augment \
    --batch_size 96 \
    --init_channels 36 \
    --num_cells 20 \
    --grad_clip 5 \
    --aux_weight 0.4 \
    --epochs 600 \
    --alpha_share
```
Note that aux_weight=0.4 means add an auxiliary head at the 2/3 position of the network, and add aux_weight * auxiliary_loss to the final loss.

## Feature clustering
To visualizatize feature clustering, run
```
python feature.py \
    --name job_name \
    --dataset cifar10 \
    --data_dir /path/to/dataset/ \
    --save_dir /path/to/results/ \
    --seed 1 \
    --stage search1 \
    --batch_size 64
```
Note that feature.py will load the saved model of search stage1.

## Run all
To run all the above directly, run
```
python run_all.py
```
Note that you should modify the parameters in file run_all.py before running.

## Found architectures

<p align="center">
  <img src="images/cifar10_normal.png" alt="cifar10_normal" width="45%">
  <img src="images/cifar10_reduce.png" alt="cifar10_reduce" width="45%">
  <br/>CIFAR-10
</p>

<p align="center">
  <img src="images/cifar100_normal.png" alt="cifar100_normal" width="45%">
  <img src="images/cifar100_reduce.png" alt="cifar100_reduce" width="45%">
  <br/>CIFAR-100
</p>

<p align="center">
  <img src="images/imagenet_normal.png" alt="imagenet_normal" width="45%">
  <img src="images/imagenet_reduce.png" alt="imagenet_reduce" width="45%">
  <br/>ImageNet
</p>


## Reference
https://github.com/quark0/darts
