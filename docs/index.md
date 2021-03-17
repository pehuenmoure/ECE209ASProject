## Spatial-temporal Predictions with Sensor Dropout for Topology-Aware IoT Applications

### Overview
#### Abstract
The goal of the project is to augment spatial-temporal predictions for topology aware IoT applications wherein the sensors are mobile. The specific case study analyzed is traffic forecasting. Traffic forecasting fits well as a cast study as there is static topology with high spatial dependency. This work primarily expands on the implementation Li et.al. [1] and Zhang et.al.[2]. by incorportating the graph recovery module from Liu et.al[3]. The typical traffic optimization problem is reformulated to include the potential sensor dropout. This is impactful for two purposes: 
1. Wireless traffic sensors may have some dropout
2. There may be more desired prediction edges than there are available sensor location

Thus this works goal is to be able to integrate the graph recovery module so that a "virtual sensor" can be dropped along any edge of the network by reconstructing the full potentional input space from a limited number of sensors. This work will focus on predicting over all edges at curent timestep $t$ and $t+1$ ignoring future $t>1$ timesteps for now.

#### Related Work
The spatio-temporal traffic forecasting is discussed in transportation literature, Cascetta [4]. In this domaain typically the data is generated and evaluated via simulations, see Mirzae et. al. for an example [5]. Some data driven methods have been presented to evaluate traffic sensor data [6]. Rose Yu's group proposed using a known underlying topology and graph convolutions to help in predictions [2]. The model architecture contained a encoder-decoder framework with scheduled samping to improve training. The main contribution worth note is the Diffusion Convoluational Recurrent Layer (DCRL). The model architecture can be seen below.

![dcrnn_model_architecture](https://user-images.githubusercontent.com/17114651/111532550-25638a00-873c-11eb-87c4-0240c28c660f.jpeg) [1]

This work was followed Zhang et al. work using gated graph attention layers to improve the underlying performance on the DCRN performance. This approach follows the success of attention mechanisms in transformer networks [7]. Functionally the proposed additional layers combine the gated mechanisms from RNN with the multi headed dot prodcut attention mechanisms using graph convolutions. An example of a three-head gated attention aggregator with two center nodes in a mini-batch can be seen below

<img width="546" alt="Screen Shot 2021-03-17 at 1 31 30 PM" src="https://user-images.githubusercontent.com/17114651/111534366-4b8a2980-873e-11eb-9b10-08e9fe35122c.png"> [2]

Finally this study seeks to incorporate the graph recovery module from Liu et al. [3] for feature reconstruction of missing sensors. The authors use a graph convolutions over known topologies to reproduce the missing data points. They show that there approach outperforms on baselines and can better handle sensors that are entirely missing during testing. An outline of the graph recovery module can be seen below:

<img width="546" alt="Screen Shot 2021-03-17 at 2 14 27 PM" src="https://user-images.githubusercontent.com/17114651/111539426-40d29300-8744-11eb-871f-531b9aa5f247.png"> [3]

This graph recovery module was trained in an end-to-end fashion.

### Technical Approach
Our proposal is to evaluate the use of the graph recovery module in the encoder-decoder architecture used in [1] and [2]. The intuition is that a missing sensor and sensor that moves to a subset of edges is functionally the same. Thus the success of the Graph Recovery Module in handling missing sensor data should allow for evaluating a "virtual sensor" over any location where the sensor was placed during training. The approach is outlined in the image below: 



In order to evaluate the approach we will use L2 loss on the output directly from decoder. This baseline spatio-temporal model will be trained with the diffusion and attention modules. All will be trained with simulated sensor dropout, as if they were part of smaller subseet `$\delta$` sensors. This `$\delta$` will be drawn from a gaussian distribution `$~N(\mu, \sigma)$`, these two parameters will be specified by the engineer during training time but should be used to estimate the runtime sensor availability. 

### Results
#### Experimental Setup
The proposed models are evaluated on the METR-LA dataset. This dataset has 207 sensors collected over 4 months of data ranging from March 1st 2012 collected from Los Angeles County [3, 8].

The dataset can be downloaded here: https://drive.google.com/drive/folders/10FOTa6HXPqX8Pf5WRoRwcFnW9BrNZEIX

#### Evaluation Metrics
For now we will use the `L2 - loss` for each sensor signal which was not dropped out in the input.

### Future Work
We see this 

### References
[1] Diffusion Convolutional Recurrent Neural Network: Data-Driven Traffic Forecasting, Yaguang Li, Rose Yu, Cyrus Shahabi and Yan Liu: https://arxiv.org/abs/1707.01926v2

[2] GaAN: Gated Attention Networks for Learning on Large and Spatiotemporal Graphs, Jiani Zhang, Xingjian Shi, Junyuan Xie, Hao Ma, Irwin King1, Dit-Yan Yeung: https://arxiv.org/pdf/1803.07294.pdf

[3] Handling Missing Sensors in Topology-Aware IoT Applications with Gated Graph Neural Network, Shengzhone Liu, et. al. : https://dl.acm.org/doi/pdf/10.1145/3411818

[4] Transportation systems engineering: theory and methods, Ennio Cascetta: https://www.springer.com/gp/book/9781475768732

[5] Enhanced Delta-tolling: Traffic Optimization via Policy Gradient Reinforcement Learning, Hamid Mirzaei, Guni Sharon, Stephen Boyles, Tony Givargis, and Peter Stone: https://www.ics.uci.edu/~givargis/pubs/C69.pdf

[6] Learning traffic as images: a deep convolutional neural network for large-scale transportation network speed
prediction, Xiaolei Ma, Zhuang Dai, Zhengbing He, Jihui Ma, Yong Wang, and Yunpeng Wang: https://arxiv.org/pdf/1701.04245.pdf

[7] Attention Is All You Need, Ashish Vaswani, Noam Shazeer, Niki Parmar, Jakob Uszkoreit, Llion Jones, Aidan N. Gomez, Lukasz Kaiser, Illia Polosukhin: https://arxiv.org/abs/1706.03762v5

[8] Big data and its technical challenges, H. V. Jagadish, Johannes Gehrke, Alexandros Labrinidis, Yannis Papakonstantinou, Jignesh M. Patel,
Raghu Ramakrishnan, and Cyrus Shahabi.
