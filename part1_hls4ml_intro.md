### Download and install the package

```
git clone https://github.com/FPGA4HEP/hls4ml.git
cd hls4ml
source install_miniconda3.sh
source install.sh
source activate hls4ml-env
```

### Run the tool (with your favourite model)

```
cd keras-to-hls
python keras-to-hls.py -c keras-config-FAVOURITE-MODEL.yml
```

This will create a folder called "my-hls-test". If you want to change the projectory directory name edit the yml configuration file.

### Run project design synthesis with Vivado HLS

```
cd my-hls-test
vivado_hls -f build_prj.tcl
```

### Readout resource usage and latency from the synthesis report

```
./gather-reports.sh -b
```
