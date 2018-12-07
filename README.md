# Ubuntu 18.04 + RTX 2080 Ti Driver + cuda-10.0 + cudnn-7.0 + tensorflow-gpu + Anaconda Tutorial

This is a tutorial to configure the environment.

Don't forget to give me a star if you like it, thank you!

The reason we configure the environment as following is that tensorflow r1.* only support cuda 9.0 while RTX-2080-Ti only support cuda 10.0. Accidentally using cuda 9.0 with RTX-2080-Ti with tensoflow-gpu will cause core dumped sometimes. So we alternatively choose to compile tensorflow-gpu with cuda 10.0 ourselves. Hope we can save you some time!

* # Before NVIDIA Driver Installation

    * Disable the Nouveau Drivers
    
        ``` sudo vim /etc/modprobe.d/blacklist-nouveau.conf ```
    
    * Add the following two lines to the file
    
        ``` blacklist nouveau ```
        
        ``` options nouveau modeset=0 ```
     
    * Regenerate the kernel initramfs
    
        ``` sudo update-initramfs -u ```
        
    * Reboot
    
        ``` reboot ```

* # NVIDIA Driver Installation

    * Version: 415 or 410
    
    * Link: https://www.nvidia.com/Download/index.aspx
    
    * Installation instructions

        ``` chmod +x NVIDIA-Linux-x86_64-410.78.run ```

        ``` sudo ./NVIDIA-Linux-x86_64-410.78.run ```
        
    * Reboot
    
        ``` reboot ```
        
    * Test if driver is successfully installed, run:
    
        ``` nvidia-smi ```
        
        ``` nvidia-settings ```
        
        If there are some troubles, remove the driver, and run the installer:
        
        ``` sudo apt-get remove --purge nvidia* ```
        
        ``` sudo ./NVIDIA-Linux-x86_64-410.78.run --no-opengl-files ```
        
* # CUDA
    
    * Version: CUDA 10.0
    
    * Cuda link: https://developer.nvidia.com/cuda-downloads
    
    * Cuda installation instructions

        ``` sudo sh cuda_10.0.130_410.48_linux.run ```
    
    * Set up the development environment
    
        ``` sudo vim ~/.bashrc ```

        ``` export PATH=/usr/local/cuda/bin${PATH:+:${PATH}}```
        
        ``` export LD_LIBRARY_PATH=/usr/local/cuda/lib64\${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}} ```
        
        ``` source ~/.bashrc ```
        
    * Test if CUDA is successfully installed, run:
    
        ``` nvcc -V ```

        If there are some troubles, remove CUDA, and run the installer:
        
        ``` cd /usr/local/cuda/bin ```
        
        ``` sudo ./uninstall_cuda_10.0.pl ```
        
        ``` sudo ./NVIDIA-Linux-x86_64-410.78.run --no-opengl-libs ```

* # cuDNN

    * Version: cuDNN v7.4.1

    * cuDNN link: https://developer.nvidia.com/rdp/cudnn-download
    
    * Unzip the cuDNN package
        
        ``` tar -xzvf cudnn-10.0-linux-x64-v7.4.1.5 ```
        
    * Copy the files into the CUDA Toolkit directory
    
        ``` sudo cp cuda/include/cudnn.h /usr/local/cuda/include ```
        
        ``` sudo cp cuda/lib64/libcudnn* /usr/local/cuda/lib64 ```
        
        ``` sudo chmod a+r /usr/local/cuda/include/cudnn.h /usr/local/cuda/lib64/libcudnn* ```

* # Hey, Just Wait. 
    
    * Up to now, if everything is ok, Go Fighting to the end!!!

* # Anaconda
    * Download and Install Anaconda
        * Download Anaconda
        
            ``` wget https://repo.anaconda.com/archive/Anaconda3-5.3.1-Linux-x86_64.sh -P ~/Downloads ```

            ``` cd ~/Downloads ```

        * Install Anaconda
        
            ``` chmod +x Anaconda3-5.3.1-Linux-x86_64.sh ```

            ``` ./Anaconda3-5.3.1-Linux-x86_64.sh ```
            
        * Update .bashrc
        
            ``` source ~/.bashrc ```
            
    * Create a virtual environment
            
        * Create a python environment with version 3.5

            ``` conda create --name py35 python=3.5 ``` or ``` conda create -n py35 python=3.5 ```

* # Build TensorFlow from source
    * ### Install TensorFlow Package Dependencies 
        * Activate your virtual python environment named 'py35'
        
            ``` conda activate py35 ```
            
        * Install the TensorFlow pip package dependencies 
        
            ``` pip install -U pip six numpy wheel mock```
            
            ``` pip install -U keras_applications==1.0.5 --no-deps```
            
            ``` pip install -U keras_preprocessing==1.0.3 --no-deps```
        
    * ### Install Bazel ([Official Bazel Installation Documentation](https://docs.bazel.build/versions/master/install-ubuntu.html))
        
        * Install required packages
        
            ``` sudo apt-get install pkg-config zip g++ zlib1g-dev unzip python```
            
        * Download Bazel Installer
            Note: We use version 0.18.0 here, using version 0.19.0 or higher has caused some problems when we're trying. But you can still try if you like to.    
        
            ```
            wget https://github.com/bazelbuild/bazel/releases/download/0.18.0/bazel-0.18.0-installer-linux-x86_64.sh -P ~/Downloads
            ```
            ``` cd ~/Downloads ```
            
        * Run the installer
        
            ``` chmod +x bazel-0.18.0-installer-linux-x86_64.sh```
            
            ``` ./bazel-0.18.0-installer-linux-x86_64.sh --user```
            
        * Set up your environment
       
            ``` sudo vim ~/.bashrc ```
       
            Add the following one to your file
       
            ``` export PATH="$PATH:$HOME/bin"```
            
            Update .bashrc
            
            ``` source ~/.bashrc ```
            
    * ### Download the TensorFlow source code
        * Use Git to clone the TensorFlow repository
            
            ``` git clone https://github.com/tensorflow/tensorflow.git ```
            
            ``` cd tensorflow ```
            
        * Checkout r1.11 to build
        
            ``` git checkout r1.11 ``` 
            
    * ### Configure the build
        
        * Configure your system build
        
            ``` ./configure ```
        
        * A sample run with ./configure
          
          Note: [Y/n] means the default is Y, and vice versa.
         
          Note: The questions I do not mention are default, just press Enter to choose the default. 
          
          Note: There may be two possible locations of python.
          
                1. /home/YourUserAccount/anaconda3/envs/py35/bin/python3.5
                
                2. /home/YourUserAccount/.conda/envs/py35/bin/python3.5
                
                The first one often happens in desktop computers, while the second one in laptops.
          Note: It seems that python3.7 is not compatible, we use python3.5 here.
        
            ```
            $ ./configure

            Please specify the location of python. [Default is /home/YourUserAccount/anaconda3/bin/python]: /home/YourUserAccount/anaconda3/envs/py35/bin/python3.5

            Please input the desired Python library path to use.  Default is [/home/YourUserAccount/anaconda3/envs/py35/lib/python3.5/dist-packages]

            Do you wish to build TensorFlow with CUDA support? [y/N]: Y

            Please specify the CUDA SDK version you want to use: 10.0

            Please specify the cuDNN version you want to use: 7.0

            Please specify the NCCL version you want to use. If NCLL 2.2 is not installed, then you can use version 1.3 that can be fetched automatically but it may have worse performance with multiple GPUs. [Default is 2.2]: 1.3

            Please specify a list of comma-separated Cuda compute capabilities you want to build with.
            You can find the compute capability of your device at: https://developer.nvidia.com/cuda-gpus.
            Please note that each additional compute capability significantly increases your
            build time and binary size. [Default is: 6.1] 6.1
            ``` 
        
    * ### Build the pip package
        * Bazel build
        
            If you use GCC 5 and later, run it
            
            ```
            bazel build --config=opt --config=cuda //tensorflow/tools/pip_package:build_pip_package --cxxopt="-D_GLIBCXX_USE_CXX11_ABI=0"
            ```
            
            If you use GCC 4, run it
            
            ```
            bazel build --config=opt --config=cuda //tensorflow/tools/pip_package:build_pip_package
            ```
            
        * Build the package
        
            ```
            ./bazel-bin/tensorflow/tools/pip_package/build_pip_package /tmp/tensorflow_pkg
            ```
            
        * Install the package
            
            ```pip install /tmp/tensorflow_pkg/tensorflow-version-tags.whl```
            
* # Congratulations! 

* I'm new here. Your star is the best encouragement! 
