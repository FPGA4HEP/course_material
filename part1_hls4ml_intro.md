1) Download and install the package

```
git clone https://github.com/FPGA4HEP/hls4ml.git
cd hls4ml
source install_miniconda3.sh
source install.sh
source activate hls4ml-env
```

2) Run the tool (with your favourite model)

cd keras-to-hls
python keras-to-hls.py -c keras-config-FAVOURITE-MODEL.yml

3) Readout resource usage and latency from Vivado HLS

cd test
./gather-reports.sh

