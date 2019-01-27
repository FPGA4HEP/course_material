### Checkout the keras training package and setup the environment (assume local installation of python data analysis packages: keras, tensorflow, ...)

```
git clone https://github.com/FPGA4HEP/keras-training.git
cp keras-training/install_miniconda.sh ~/
cd ~
source install_miniconda.sh
source ~/.bashrc
cd keras-training
source install.sh
source setup.sh # every time you log in
```

Download the dataset from here: https://cernbox.cern.ch/index.php/s/jvFd5MoWhGs1l5v
and copy it to a folder inside the keras-training directory:

```
cd keras-training
mkdir data --> put the dataset file here
```

### Training and evaluation of the [3-layer Dense NN](https://github.com/FPGA4HEP/keras-training/blob/master/models/models.py#L63-L76):

```
cd ~/keras-training/train
python train.py -t t_allpar_new -i ../data/processed-pythia82-lhc13-all-pt1-50k-r1_h022_e0175_t220_nonu_truth.z -c train_config_threelayer.yml -o train_3layer/
```

The NN inputs/outputs and training configuration are specified in the config file train_config_threelayer.yml. Note from the config file that we are training the model with L1 regularization = 0.0001.

After the training, find the final weights in the train_3layer output folder. You can now evaluate the performance of the NN:

```
python eval.py -t t_allpar_new -i ../data/processed-pythia82-lhc13-all-pt1-50k-r1_h022_e0175_t220_nonu_truth.z -m train_3layer/KERAS_check_best_model.h5 -c train_config_threelayer.yml -o eval_3layer/
```

Find training history (loss and accuracy), ROC curve and confusion matrix in the output folder eval_3layer.

### Pruning and retraining

To prune the trained model by removing weights below a certain threshold (relative weight < 0.004):

```
mkdir prune_3layer_relwmax4e-3
python prune.py -m train_3layer/KERAS_check_best_model.h5 --relative-weight-max 4e-3 -o prune_3layer_relwmax4e-3/pruned_model.h5
```

Check the output folder prune_3layer_relwmax4e-3 for plots of weights and quantiles.

Now evaluate the pruned model:

```
python eval.py -t t_allpar_new -i ../data/processed-pythia82-lhc13-all-pt1-50k-r1_h022_e0175_t220_nonu_truth.z -m prune_3layer_relwmax4e-3/pruned_model.h5 -c train_config_threelayer.yml -o eval_3layer_relwmax4e-3/
```

Check the performance of the pruned model in the eval_3layer_relwmax4e-3 output folder and compare with previous performance of the non pruned model.

Now retrain the model (keeping the pruned weights fixed to 0) and evaluate the performance:

```
python retrain.py -t t_allpar_new -i ../data/processed-pythia82-lhc13-all-pt1-50k-r1_h022_e0175_t220_nonu_truth.z -o retrain_3layer_relwmax4e-3 -m prune_3layer_relwmax4e-3/pruned_model.h5 -c train_config_threelayer.yml -d prune_3layer_relwmax4e-3/pruned_model_drop_weights.h5
python eval.py -t t_allpar_new -i ../data/processed-pythia82-lhc13-all-pt1-50k-r1_h022_e0175_t220_nonu_truth.z -m retrain_3layer_relwmax4e-3/KERAS_check_best_model.h5 -c train_config_threelayer.yml -o eval_retrain_3layer_relwmax4e-3/
```

Repeat the full procedure few times and copy the final compressed model (pruned weights .h5 file) to the hls4ml directory. Synthetise the FPGA project and compare resources and latency with what obtained with the full 3-layer model.
NB: when repeating the pruning+evaluation+retrain procedure remember to change the name of the output directory and input model.
