### Download and install the package

```
git clone https://github.com/hls-fpga-machine-learning/hls4ml -b tutorial
cd hls4ml
pip install . # --user
```

### Run the tool (with your favourite model, e.g. 1-layer)

```
cd example-models
export FAVOURITEMODEL=1layer
hls4ml convert -c keras-config-${FAVOURITEMODEL}.yml
```

This will create a folder called `my-hls-test-${FAVOURITEMODEL}`. If you want to change the projectory directory name edit the yml configuration file.

### Run project design synthesis with Vivado HLS

```
hls4ml build -p my-hls-test-${FAVOURITEMODEL} -a
```
or alternatively:
```
cd my-hls-test-${FAVOURITEMODEL}
vivado_hls -f build.tcl "csim=1 synth=1 cosim=1 export=1"
```


### Readout resource usage and latency from the synthesis report

```
./print-reports.sh -d ./
```

### Extract and compare area under the ROC curve from keras (floating point calculations) and HLS (fixed point calculations)

```
pip install scikit-learn matplotlib # --user
python extract_roc.py -c keras-config-${FAVOURITEMODEL}.yml
```

### EXERCISE:

Change precision of calculations and reuse factor in the keras configuration file and check effect on NN performance (AUC) and FPGA resource usage using the scripts above.

```
ReuseFactor: N  # N = number of times a multiplier is used to do a computation 
DefaultPrecision: ap_fixed<X,Y>  # X = total number of bits, Y = number of integer bits, X-Y = number of decimal bits
```

NB: suggest to change the project output directory in the keras configuration for each test to avoid overwriting of previous projects.
