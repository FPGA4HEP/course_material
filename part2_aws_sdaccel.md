### Check out SDAccel and setup environment

```
git clone https://github.com/aws/aws-fpga.git $AWS_FPGA_REPO_DIR  
cd $AWS_FPGA_REPO_DIR                                         
source sdaccel_setup.sh #every time you log in
```

More detailed information [here](https://github.com/aws/aws-fpga/tree/master/SDAccel)

### Clone hls4ml wrapper for SDAccel

```
cd ../../
git clone https://github.com/FPGA4HEP/hls4ml_c.git
```

Edit Makefile in the hls4ml_c directory to change default input directory name.


### Run software simulation, hardware emulation and build FPGA binary

```
cd ~/hls4ml
source deactivate hls4ml-env
cd ~/hls4ml_c
make clean                                                                 
make check TARGETS=sw_emu DEVICES=$AWS_PLATFORM all   
make check TARGETS=hw_emu DEVICES=$AWS_PLATFORM all
make TARGETS=hw DEVICES=$AWS_PLATFORM all
```

