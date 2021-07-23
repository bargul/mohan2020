# Moving in the Right Direction: A Regularization for Deep Metric Learning

This readme file is an outcome of the [CENG501 (Spring 2021)](http://kovan.ceng.metu.edu.tr/~sinan/DL/) project for reproducing a paper without an implementation. See [CENG501 (Spring 2021) Project List](https://github.com/sinankalkan/CENG501-Spring2021) for a complete list of all paper reproduction projects.

# 1. Introduction

This repository is the implementation of the CVPR 2020 paper written by Deen Dayal Mohan et al. Please refer to [the paper](https://openaccess.thecvf.com/content_CVPR_2020/html/Mohan_Moving_in_the_Right_Direction_A_Regularization_for_Deep_Metric_CVPR_2020_paper.html) for a detailed explanation. Moving in Right Direction (MVR) paper tries to solve the metric learning problem, which aims to construct embedding space where distance is inversely correlated with semantic similarity. Metric learning has many practical applications such as image retrieval, face verification, person re-identification, few-shot learning, etc. Image retrieval aims to bring samples as same class as the given query image. MVR paper introduces direction regularization to prevent the unaware movement of samples. Therefore, their methodology improves the performance of retrieval tasks compared to a method without regularization. We aim to quantitatively validate retrieval performance increases and visualize retrieval results to see the capability of deep metric learning during this repository.

## 1.1. Paper summary

Summarize the paper, the method & its contributions in relation with the existing literature.

# 2. The method and my interpretation

## 2.1. The original method

Explain the original method.

## 2.2. My interpretation 

Explain the parts that were not clearly explained in the original paper and how you interpreted them.

# 3. Experiments and results

## 3.1. Experimental setup

As model, MVR paper utilizes pretrained GoogleNet with Batch Normalization on ImageNet. Although they do not express which pretrained model they use, we choose caffe pretrained model due to superiority over pytorch pretrained model. The caffe model only perform zero mean preprocessing to the dataset compared to torch model that applies not only zero mean but also scaling of the dataset as a preprocessing. As mentioned in the paper, we augment train dataset with random cropping and random horizontal flip while test set is center cropped. We evaluate performance on CUB-200-2011 dataset but it is easily generalizable to other dataset. CUB dataset is split into two equal part as train and test set in the MVR paper ;however, they do not mention existence of validation set. Therefore, we assume that they do not use validation set. This fact is mentioned in Metric Learning Reality Check paper that majority of paper do not use validation set. We choose embedding dimension size as 64 like the paper. MVR paper do not share margin and regularization parameters of triplet. Therefore, we have to optimize this hyperparameter with OPTUNA. As results of optimization, we find margin and regularization as 0.2781877469005122 and 0.4919607680052035 respectively. We don't also have no information about batch size. Since higher batches results with diverse pairs and triplets, we try to keep batch size respectively high. So we choose batch size as 128 for direction regularized triplet.

## 3.2. Running the code

Explain your code & directory structure and how other people can run it. 

```
.
├── dataloader
│      ├── cub_dataset.py
│      ├── sampler.py
│      ├── trsfrms.py
├── evaluation
│      ├── recall.py
├── loss
|      ├── deneme.py
|      ├── mvrloss.py
├── model
|      ├── bn_inception.py
├── data
|      ├── CUB_200_2011
|          ├── images
├── hyper_train.py
├── test.py
├── train.py
└── README.md
```
Dataloader:\
"cub_dataset.py" splits dataset into 3 set, namely train, trainval, and test. It is responsible of loading images corresponding to choosen set. It takes transformations that is applied to image as parameter.\
"trsfrms.py" includes common transformation that is caffe type mean substraction belonging to Imagenet.

Evaluation:\
"recall.py" include function that evaluate performance of image retrieval and perform visaulization of retrieval results.

Loss:\
"mvrloss.py" includes class of loss of direction regularized triplet, proxynca, and multi-similarity. You can change margin, regularization constant, adn other hyperparameter.

Model:\
"bn_inception.py" includes forward pass for embedding extraction. It takes L2 normalization of embeddings based on chosen parameter.

Main:\
"hyper_train.py" includes wrapper function that defines objective and optimize hyper-parameters with optuna based on this objective. \
"train.py" includes full pipeline for training. It also test retrieval performance without visualization. \
"test.py" tests the trained model again with visualization.

1. Download dataset and put into folder named 'data'.
2.
DR-TRIPLET:
```
python train.py --batch_size 128 --patience 25 --mvr_reg 0.4919607680052035 --margin 0.2781877469005122 --loss mvr_triplet --tnsrbrd_dir ./runs/exp_trp --model_save_dir ./MVR_Triplet/exp  --exp_name mvr_triplet
```
DR-PROXYNCA:
```
python train.py --batch_size 196 --patience 25 --mvr_reg 0.45 --loss mvr_proxy --tnsrbrd_dir ./runs/exp_proxy --model_save_dir ./MVR_Proxy/exp --exp_name mvr_proxy 
```
For visualization
Create folder with name you desired inside log directory. Please change name of 'proxy_exp20' with name you assing for log folder. 

DR-Triplet:
```
python test.py --exp_name mvr_triplet --model_save_dir ./MVR_Triplet/exp
```
DR-PROXYNCA:
```
python test.py --exp_name mvr_proxy --model_save_dir ./MVR_Proxy/exp
```

## 3.3. Results

Present your results and compare them to the original paper. Please number your figures & tables as if this is a paper.

Retrieval performance of the DR is evaluated with Recall @K. Recall @K computes percentage of images whose k neighborhood contains at least one sample as same class as query image. It is worth to express that recall metric in image retrieval is different from those in recommendation.

TABLE 1: Recall Results on CUB-200 Dataset


| Recall@K | 1 | 2 | 4 | 8 |
|:----------:|---|---|---|---|
| Triplet|  51.9 | 64.0 | 70.3  | 74.1 | 
| DR-Triplet| 54.49 | 66.22 | 77.5 | 85.79 |
| ProxyNCA | 49.2 |61.9 | 67.90 | 72.4 |
| DR-ProxyNCA | 52.43 | 63.74 | 74.05 | 83.37 |


![kuslar3](https://user-images.githubusercontent.com/50836811/126769870-e177fe7f-10ea-46c3-9418-6796a23c101c.png)


<p align="center">
Figure 1: Qualitative results of Image retrieval.
</p>

In figure 1, first column of each row shows unique query image. On the other hand, other columns in certain row corresponds to retrieved images corresponding to query image in that row. Model can distinguish between two similar bird species in terms of appearance as shown in second row, where it miss only one prediction at 4th retrieved result.
# 4. Conclusion

Discuss the paper in relation to the results in the paper and your results.

# 5. References

Provide your references here.

# Contact

Alper Kayabasi - alperkayabasi97@gmail.com
Baran Gulmez - baran.gulmez07@gmail.com
