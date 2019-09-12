### Checkout the mnist-training package and setup the environment

Setup:
```
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
bash Miniconda3-latest-Linux-x86_64.sh
git clone https://github.com/jmduarte/mnist-training.git
cd mnist-training
source setup.sh
```

### Training and evaluation of the MNIST MLP

Train an MNIST MLP model:
```
python mnist_mlp.py
```

After the training, you can now evaluate the performance of the NN and output text files containing inputs, truth labels, and predictions (which will be needed in `hls4ml`).
```
python mnist_mlp_pred.py
```


### Pruning and retraining

To gradually prune the trained model by removing weights below a certain threshold following a schedule according to polynomial decay (see https://www.tensorflow.org/model_optimization/api_docs/python/tfmot/sparsity/keras/PolynomialDecay and https://arxiv.org/abs/1710.01878).
```
python mnst_mlp_prune.py
```

Now evaluate the pruned model:
```
python mnist_mlp_pred.py -p
```

And check the level of compression achieved:
```
python check_compression.py
```
