# IntelOneAPI

# Use SJSU HPC to install Intel OneAPI AI Analytics Toolkit
Refer [Install Intel AI Analytics Toolkit via Conda](https://www.intel.com/content/www/us/en/develop/documentation/installation-guide-for-intel-oneapi-toolkits-linux/top/installation/install-using-package-managers/conda/install-intel-ai-analytics-toolkit-via-conda.html), create a new conda virtual environment named "intelaikit" via intel channel ("-c intel") and install Intel Modin:
```bash
(base) [xxx@coe-hpc1 ~]$ conda create -n intelaikit -c intel intel-aikit-modin
.....
Installed package of scikit-learn can be accelerated using scikit-learn-intelex.
    More details are available here: https://intel.github.io/scikit-learn-intelex
    For example:
        $ conda install scikit-learn-intelex
        $ python -m sklearnex my_application.py
done
#
# To activate this environment, use
#
#     $ conda activate intelaikit
#
# To deactivate an active environment, use
#
#     $ conda deactivate
```

Install Intel optimized scikit: conda install scikit-learn-intelex and update conda (optional): conda update -n base -c defaults conda. After activate the virtual environment: conda activate intelaikit, you can see the intel distribution of python:
```bash
(intelaikit) [xxx@coe-hpc1 ~]$ python -V
Python 3.7.12 :: Intel Corporation
```
You can check numpy configuration to see the MKL is linked:
![image](https://user-images.githubusercontent.com/6676586/190889824-024486bc-8820-4e1f-979f-8df8ffce6227.png)

# Install Intel OneAPI in local Linux machine
## Intel OneAPI base kit
Get the [Intel oneAPI Base Toolkit](https://www.intel.com/content/www/us/en/developer/tools/oneapi/base-toolkit-download.html?operatingsystem=linux&distributions=webdownload&options=online), select the online/offline distribution and online installation. Get the installation command line:
```bash
wget https://registrationcenter-download.intel.com/akdlm/irc_nas/18673/l_BaseKit_p_2022.2.0.262.sh

sudo sh ./l_BaseKit_p_2022.2.0262.sh
```
The installation GUI will display the options
![image](https://user-images.githubusercontent.com/6676586/190890088-51aa7e75-21db-45cb-a697-a5785ccf3034.png)

After the installation, run the following command to setup the environment:
```bash
. /opt/intel/oneapi/setvars.sh

:: initializing oneAPI environment ...
   bash: BASH_VERSION = 5.1.16(1)-release
```

Run the "oneapi-cli" in the command line to check the sample project, ref [run a sample](https://www.intel.com/content/www/us/en/develop/documentation/get-started-with-intel-oneapi-base-linux/top/run-a-sample-project-using-the-command-line.html). You can see Python samples
![image](https://user-images.githubusercontent.com/6676586/190890230-099018ec-c19f-4bee-b565-dd8e972d42b9.png)

You can also check C++ samples:
![image](https://user-images.githubusercontent.com/6676586/190890242-63606399-3738-47f5-9b64-d5dd2d48c682.png)

Using the matrix multiply example, do make all and make run:
```bash
kaikai@kaikai-i9new:~/Documents/inteloneapi$ cd matrix_mul/
kaikai@kaikai-i9new:~/Documents/inteloneapi/matrix_mul$ make all
dpcpp -std=c++17 -g -o matrix_mul_dpc src/matrix_mul_dpcpp.cpp 
kaikai@kaikai-i9new:~/Documents/inteloneapi/matrix_mul$ make run
./matrix_mul_dpc
Device: Intel(R) Core(TM) i9-9900K CPU @ 3.60GHz
Problem size: c(150,600) = a(150,300) * b(300,600)
Result of matrix multiplication using DPC++: Success - The results are correct!
```
## Install AI Analytic Kit
Using conda to [install AI Analytic Kit](https://www.intel.com/content/www/us/en/develop/documentation/installation-guide-for-intel-oneapi-toolkits-linux/top/installation/install-using-package-managers/conda/install-intel-ai-analytics-toolkit-via-conda.html)
```bash
(Newenv) kaikai@kaikai-i9new:~$ conda install modin-all -c intel -y
(Newenv) kaikai@kaikai-i9new:~$ conda install matplotlib -c intel -y
```
Install [intel distribution for python](https://www.intel.com/content/www/us/en/developer/articles/technical/get-started-with-intel-distribution-for-python.html) and run the numpy example:
```bash
(Newenv) kaikai@kaikai-i9new:~$ conda install intelpython3_core python=3.9 -c intel
(Newenv) kaikai@kaikai-i9new:~$ python3 --version
Python 3.9.10 :: Intel Corporation
(Newenv) kaikai@kaikai-i9new:~$ python3 intelnumpy.py 
Time Consuming: 0.043503761291503906
```
The numpy configuration results show that Intel MKL is utilized:
![image](https://user-images.githubusercontent.com/6676586/190890442-295069ca-ab79-46d1-9d23-e675e5427326.png)


# Use Intel DevCloud for oneAPI
Apply for the Intel DevCloud four month access for a range of Intel® CPUs, GPUs, and FPGAs using oneAPI software. After the account has been activated, you can get your credentials, unique Access URL, user id, and UUID key.

Intel oneAPI provides a cross-architecture language called Data Parallel C++, with best-in-class compilers, performance libraries, and analysis tools for high-performance heterogeneous code. Getting started by visiting (https://devcloud.intel.com/oneapi/get_started) where you can learn how to connect with an SSH client terminal or learn the essentials of Data Parallel C++ using Jupyter Lab.

## Step1 DevCloud access
Visit [https://devcloud.intel.com/oneapi/get_started/baseToolkitSamples/](!https://devcloud.intel.com/oneapi/get_started/baseToolkitSamples/) to access the Intel DevCloud and to try Intel oneAPI Base Toolkit Samples.

* Download and save the SSH access key for Linux/macOS to the local .ssh folder: kaikai@kaikai-i9new:~/Documents/MyRepo/inteloneapi$ mv ./devcloud-access-key-71793.txt ~/.ssh/. Add the following lines to the file ~/.ssh/config:
```bash
Host devcloud
User u71793
IdentityFile ~/.ssh/devcloud-access-key-71793.txt
ProxyCommand ssh -T -i ~/.ssh/devcloud-access-key-71793.txt guest@ssh.devcloud.intel.com
```
* Set the permission of the file
```bash
chmod 600 ~/.ssh/devcloud-access-key-71793.txt
chmod 600 ~/.ssh/config
```
* use "ssh devcloud" to login to the Intel DevCloud:

![image](https://user-images.githubusercontent.com/6676586/122686092-4a94e800-d1c4-11eb-8ae8-74ea3253ceaa.png)

* Use "lscpu" and "lspci -v | less" to show the CPU and hardware devices of the VM. The host VM does not have GPU and any FPGA devices.

## Step2 Test CPU/GPU Vector-Add sample
* Download sample code: git clone https://github.com/oneapi-src/oneAPI-samples.git
* Go to the vector-add sample: cd oneAPI-samples/DirectProgramming/DPC++/DenseLinearAlgebra/vector-add/
* sd
```bash
#!/bin/bash
source /opt/intel/inteloneapi/setvars.sh
make clean
make all
```
* Submit job: https://devcloud.intel.com/oneapi/documentation/job-submission/
* Error: run any qsub will have the following error
```bash
u71793@login-2:~$ qsub -l nodes=1:gpu:ppn=2 -d . job.sh
/usr/local/sbin/torque_submitfilter: line 67: echo: write error: No space left on device
tr: write error: No space left on device
/usr/local/sbin/torque_submitfilter: line 149: echo: write error: No space left on device
/usr/local/sbin/torque_submitfilter: line 149: echo: write error: No space left on device
/usr/local/sbin/torque_submitfilter: line 149: echo: write error: No space left on device
qsub: submit filter returned an error code, aborting job submission.
```
The reason for this error is no space left in the root folder:
```bash
u71793@login-2:/$ df .
Filesystem                    1K-blocks     Used Available Use% Mounted on
/dev/mapper/login--2--vg-root  24643364 23389416         0 100% /
```

