### Download and install the package

```
git clone https://github.com/FPGA4HEP/hls4ml.git -b paris
cd hls4ml
source install_miniconda3.sh
source setup_hls4ml.sh
source install.sh
```

Every time you log in do:

```
source setup_hls4ml.sh
conda activate hls4ml-env
```

Run as well ```git pull``` to fetch the latest changes.

### Run the tool (with your favourite model, e.g. 1-layer)

```
cd keras-to-hls
export FAVOURITEMODEL=1layer
python keras-to-hls.py -c keras-config-${FAVOURITEMODEL}.yml
```

This will create a folder called `my-hls-test-${FAVOURITEMODEL}`. If you want to change the projectory directory name edit the yml configuration file.

### Run project design synthesis with Vivado HLS

```
cd my-hls-test-${FAVOURITEMODEL}
vivado_hls -f build_prj.tcl
```


### Readout resource usage and latency from the synthesis report

```
cd ..
./print-reports.sh my-hls-test-${FAVOURITEMODEL}
```

### Extract and compare area under the ROC curve from keras (floating point calculations) and HLS (fixed point calculations)

```
python extract_roc.py -c keras-config-${FAVOURITEMODEL}.yml
```

### EXERCISE:

Change precision of calculations and reuse factor in the keras configuration file and check effect on NN performance (AUC) and FPGA resource usage using the scripts above.

```
ReuseFactor: N  # N = number of times a multiplier is used to do a computation 
DefaultPrecision: ap_fixed<X,Y>  # X = total number of bits, Y = number of integer bits, X-Y = number of decimal bits
```

NB: suggest to change the project output directory in the keras configuration for each test to avoid overwriting of previous projects.
