# Which Machines have GPUs

**LOFAR 3:** No

**LOFAR 4:** No

**LOFAR 5:** No

**LOFAR 6:** No

**LOFAR 7:** Yes

# Booking Time

Book time on the GPUs using the google calendar. 

Use the format: *LOFAR X - NAME* , when booking a day. 

# Versions of cuda installed on LOFAR machines

* cuda-10.1
* cuda-10.0
* cuda-9.0
* cuda-8.0
* cuda-7.5

# Run a python script using Singularity container

For training a neural network on the GPU we recommend using the singularity container provided at x. 
For information on Singularity see the [documentation](https://www.sylabs.io/docs/) or [click here for a tutorial](https://github.com/NIH-HPC/Singularity-Tutorial).
If the container is missing a python module or software that you need simply clone this repo and edit the %post
section of the recipe file in this repo to install the required software. You can then build a new container to use. 

To run a python script using the provided container first ssh to a LOFAR machine you have booked time on. 
Ensure you are in bash. (just type bash). In order to use singularity on the LOFAR machines you must add it to your path:

```bash
export PATH=/raid/singularity/bin:$PATH
```

To download the container to your working directory run


```bash
singularity pull --name container.simg shub://TMCantwell/FAW:cuda101 
```

From your working directory run the following command
```bash
export SINGULARITY_BINDPATH=$PWD:/mnt
```

This will bind your working directory to the folder /mnt in the singularity container when you run the container. 

To run your python script use the following command

```bash
singularity run container.simg /mnt/your_script.py
```

### Possible Errors
If you are using tensorflow and keras and you get the following error:


```bash
Could not create cudnn handle: CUDNN_STATUS_INTERNAL_ERROR
```

then you need to add the following lines of code to the start of your script
```python
import tensorflow as tf
from keras.backend.tensorflow_backend import set_session
config = tf.ConfigProto()
config.gpu_options.per_process_gpu_memory_fraction = 0.8
set_session(tf.Session(config=config))
```

# Run a jupyter notebook using Singularity container

The first thing you will need to do is set up an ssh tunnel. On your host machine add the following to your bashrc.
replace username with your username. Then source the bashrc. For more information see [here](https://medium.com/@sankarshan7/how-to-run-jupyter-notebook-in-server-which-is-at-multi-hop-distance-a02bc8e78314)

```bash
alias jupyter-on-lofar7='ssh -L 1893:localhost:1894 username@external.jb.man.ac.uk -t ssh -L 1894:localhost:1895 username@lofar7.jb.man.ac.uk'
```

Run jupter-on-lofar7. Ensure you are in bash. (just type bash). In order to use singularity on the LOFAR machines you must add it to your path:

```bash
export PATH=/raid/singularity/bin:$PATH
```

To download the container to your working directory run


```bash
singularity pull --name container.simg shub://TMCantwell/FAW:cuda101 
```

From your working directory run the following command
```bash
export SINGULARITY_BINDPATH=$PWD:/mnt
```

This will bind your working directory to the folder /mnt in the singularity container when you run the container. 

Next shell into the container and activate the tensorflow environment. Cd to your working directory in /mnt.
```bash
singularity shell container.simg
source /tensorflow/bin/activate
cd /mnt
```

Start jupyter notebook
```bash
jupyter notebook --no-browser --port=1895
```

On your local machine open your browser at http://localhost:1893. 

### Possible Errors

*You might see the following error:
```bash
jupyter notebook permission denied: '/run/user/
```

The solution is to run 
```bash
export XDG_RUNTIME_DIR=""
```


* you might see this error
```bash
Error : Failed to get convolution algorithm. This is probably because cuDNN failed to initialize, so try looking to see if a warning log message was printed above.
```

then you need to add the following lines of code to the start of your script
```python
import tensorflow as tf
from keras.backend.tensorflow_backend import set_session
config = tf.ConfigProto()
config.gpu_options.per_process_gpu_memory_fraction = 0.8
set_session(tf.Session(config=config))
```
