### Clone hls4ml wrapper for SDAccel

```
git clone https://github.com/FPGA4HEP/hls4ml_c.git -b fml
cd hls4ml_c
git pull #to fetch the latest changes
export FAVOURITEMODEL=1layer # define your favorite model
mkdir -p ~/.aws
printf '[default]\nregion = us-west-2\noutput = json\n' > ~/.aws/config
```

Note the `Makefile` in the `hls4ml_c` directory uses the `FAVOURITEMODEL` environment variable in the input directory name:

```
HLS4ML_PROJECT := my-hls-test-$${FAVOURITEMODEL}
```

### Check out SDAccel and setup environment

```
git clone https://github.com/aws/aws-fpga.git $AWS_FPGA_REPO_DIR  
```

Every time you login do:

```
cd $AWS_FPGA_REPO_DIR                                         
source sdaccel_setup.sh
```

More detailed information [here](https://github.com/aws/aws-fpga/tree/master/SDAccel)

### Run software simulation, hardware emulation and build FPGA binary

```
cd ~/hls4ml_c/
make clean                                                                 
make check TARGETS=sw_emu DEVICES=$AWS_PLATFORM all                 #software emulation
make check TARGETS=hw_emu DEVICES=$AWS_PLATFORM all                 #hardware emulation
make TARGETS=hw DEVICES=$AWS_PLATFORM all && ./create.sh            #firmware building
```
To check it that the AFI was created properly:

```
./check.sh
```

### Run on real FPGA

If using a T2 instance previously, then  copy the `host` and binary files (`.awsxclbin`) from the T2 to the F1 (nb, first copy to your laptop/another server). 

Setup the SDAccel environment on the F1 as well:

```
git clone https://github.com/aws/aws-fpga.git $AWS_FPGA_REPO_DIR
cd $AWS_FPGA_REPO_DIR 
source sdaccel_setup.sh
```

Otherwise, just run
```
cd ~/hls4ml_c/
sudo sh
export VIVADO_TOOL_VERSION=2018.3
source /home/centos/src/project_data/aws-fpga/sdaccel_runtime_setup.sh
fpga-describe-local-image -S 0 -H # check current local image
fpga-clear-local-image -S 0 # clear current local image
fpga-describe-local-image -S 0 -H # check current local image again
``` 

Now copy the input features and keras prediction files from your hls4ml project directory on the T2 (`my-hls-test-${FAVOURITEMODEL}/tb_data/`) to the F1 to pass it to the FPGA. 

Finally, you can accelerate your NN inference on the FPGA running on the input features:

```
./host N data_dir
```

where N is number of batches of 32 events (suggest use N=6168 if use the provided input features list), and data_dir is the directory with input features and keras predictions files.

The application will produce a file with the predictions from the FPGA run. Compare it with HLS and Keras calculations using the extract_roc.py script in the hls4ml directory on the T2 instance (nb, copy the `tb_output_data.dat` from the F1 to `hls4ml/keras-to-hls` directory on the T2)

```
python extract_roc.py -c keras-config-${FAVOURITEMODEL}.yml -f tb_output_data.dat
```
