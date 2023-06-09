# LoopyBeliefPropagation
Disparity Map Calculation Using Belief Propagation
## Motivation
The field of stereo vision is still a fundamental challenge in computer vision and image processing that finds corresponding points between two stereo images. This repo contains my own implementation of the loopy belief propagation algorithm in python which was inspired and derived from this blog [post](https://nghiaho.com/?page_id=1366). Unfortunately, this implementation was incredibly slow. However, I found a repository with another implementation of the loopy belief propagation using max product algorithm which was much faster. These results were compared to the middlebury ground truth disparity maps and compared to a deep learning algorithm proposed by Jia Ren Chang and Yong-Sheng Chen. [GitHub](https://github.com/JiaRenChang/PSMNet/tree/master) with PSMNet, a pyramid stereo matching network. The level of comparision involves using RMSE. The data set used comes from [Middlebury](https://vision.middlebury.edu/stereo/data/scenes2005/)
## First Algorithm
The first algorithm was developed from this blog [post](https://nghiaho.com/?page_id=1366). An MRF was built to formulate the stereo problem into a graphical model involving probabilities. The energy function $$energy(Y,X) = \sum_i DataCost(y_i,x_i) + \sum_{j=neighbours of i}SmoothnessCost(x_i,x_j)$$
was used to calculate the energy of all costs that link an image given and some labeling. The aim is to find a labeling that produces the lowest cost in energy. The data cost returns the penalty of assinging a label. This function calculates the data cost for a pixel given the left and right images, its coordinates, and a label. It computes the absolute differences between corresponding pixel instesities in a local window around the pixel. The smoothness cost computes the absolute difference and scales it by some constant lambda. If the difference exceeds a threshold, it truncates the cost to the threshold. 
$$f(n) = \lambda \times \min(|n|,K)$$ The message is then sent by computing the minimum cost among all possible label combinations and stores it in the message bp algorithm is then applied, which iterates over all pixels in the MRF and calls send message to send messages to their neghboring pixels. The max product is the algorithm used for the message updating. It keeps track of the largest marginal probability. 
$$msg_{i \longrightarrow j}(l)=\max_{l^{\prime}\in\text{all labels}}\left[exp(-DataCost(y_i,l^{\prime}))exp(-SmoothCost(l,l^{\prime})) \times \prod_{k = \text{neighbours of i except j}} msg_{k \longrightarrow i}(l^{\prime})\right]$$ The results are then saved to an output png file. As you can see, it is blurry and quite innacurate.
### Sample Image of Books Using Loopy Belief Propagation
![Books](output.png)
## Belief Propagation Second Algorithm
There was open source [software](https://github.com/aperezlebel/StereoMatching/tree/master) for this exact problem which wound up being must faster than I had originally started off with. This implementation was used as an alternative comparison for faster and more accurate results. I was able to make a few adjustments to make it run quite a bit faster. In the update_msg function, I was able to replace a for loop with numpy broadcasting and shaved off a good 8-9 seconds during runtime. I also used scikit skopt Optimizer to find an optimal number of disparity values, a lambda scalar value, and a number of iterations. This allowed for more accurate results when comparing against the deep learning algorithm and the ground truth disparity maps. The energy function $$E(L) = \sum_{p \in P}D_p(l_p)+\lambda \cdot \sum_{p,q\in N}V(l_p-l_q)$$ The data cost $$D_p(l_p)=\min\left(\frac{1}{3}||I_{left}(y,x)-I_{right}(y,x-l_p)||,\tau\right)$$ The message pasing equation Potts model example
f(n) = {
    0,              if x = 0
    1,         otherwise
}
### Sample Image of Books Using the Second Implementation of Belief Propagation
![Books](disparity_map_1.png)
### Comparisons of Belief Propagation With Ground Truth
![Maps](compares_belief.PNG)
## Deep Learning Stereo Vision
Deep learning algorithm for stereo vision was used as a comparison. It did perform much faster than anticipated and was incredibly accurate. Again, it was compared to the previous algorithms via RMSE. [GitHub](https://github.com/JiaRenChang/PSMNet/tree/master) The model was pretrained using KITTI 2015 data set.
![Maps](compares_belief2.PNG)
## Results
The equation used to draw comparisons between the deep learning algorithm and the open source belief propagation software were quite close. On average, the RMSE score for belief propagation was 10.4568. The deep learning algorithm was averaged at 10.2238. However, the average time of the belief propagation algorithm was at around 9 seconds, while the deep learning was either 2 seconds or slightly under. This makes up for a drastic difference in time, but quite similar with this testing approach. In the future, I would find it helpful to test for other metrics such as occlusion which I didn't have time for. I would also like to use a larger data set and work more on my own implementation of the belief propagation algorithm. 
## Instructions to Run
Clone this repository to your local machine. Create a virtual environment for Python. `pip install -r requirements.txt` Start up a jupyter notebook `jupyter notebook` and start up BeliefPropagation.ipynb
## Resources
### Resource 1
[MRF in Images](https://nghiaho.com/?page_id=1366)
### Resource 2
[Arxiv](https://arxiv.org/abs/2209.12000v1)
### Resource 3
[Cornell](https://www.cs.cornell.edu/~dph/papers/bp-cvpr.pdf)
### Resource 4
[IEEE doc1](https://ieeexplore.ieee.org/document/6844383)
### Resource 5
[IEEE doc 2](https://ieeexplore.ieee.org/document/8654665)
### Resource 6
[Brown](https://cs.brown.edu/people/pfelzens/papers/bp-long.pdf)
