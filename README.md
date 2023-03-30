# HierSpeech

* This code is a unofficial implementation of HierSpeech.
* The algorithm is based on the following papers:

```
Lee, S. H., Kim, S. B., Lee, J. H., Song, E., Hwang, M. J., & Lee, S. W. HierSpeech: Bridging the Gap between Text and Speech by Hierarchical Variational Inference using Self-supervised Representations for Speech Synthesis. In Advances in Neural Information Processing Systems.
```

# Structure
* Structure is referred from the HierSpeech, but I changed several parts.
* The multi-head attention is changed to linearized attention in FFT Block.
* Discriminator
    * According to the advice of the paper [author](https://github.com/sh-lee-prml), **scale discriminator** and **multi stft discriminator** were applied as discriminators.
    * To prevent discriminator win, the gradient penalty is applied by R1 regularization.

# Supported dataset
* [LJ Dataset](https://keithito.com/LJ-Speech-Dataset/)

# Hyper parameters
Before proceeding, please set the pattern, inference, and checkpoint paths in [Hyper_Parameters.yaml](Hyper_Parameters.yaml) according to your environment.

* Sound
    * Setting basic sound parameters.

* Tokens
    * The number of token.

* Discriminator
    * If `Use_STFT` is `true`, model use period and stft discriminator, except scale.
    * If `Use_STFT` is `false`, model use period and scale discriminator, except stft.

* Train
    * Setting the parameters of training.

* Inference_Batch_Size
    * Setting the batch size when inference

* Inference_Path
    * Setting the inference path

* Checkpoint_Path
    * Setting the checkpoint path

* Log_Path
    * Setting the tensorboard log path

* Use_Mixed_Precision
    * Setting using mixed precision

* Use_Multi_GPU
    * Setting using multi gpu
    * By the nvcc problem, Only linux supports this option.
    * If this is `True`, device parameter is also multiple like '0,1,2,3'.
    * And you have to change the training command also: please check  [multi_gpu.sh](./multi_gpu.sh).

* Device
    * Setting which GPU devices are used in multi-GPU enviornment.
    * Or, if using only CPU, please set '-1'. (But, I don't recommend while training.)

# Generate pattern

```
python Pattern_Generate.py [parameters]
```
## Parameters
* -lj
    * The path of LJSpeech dataset
* -hp
    * The path of hyperparameter.

## About phonemizer
* To phoneme string generate, this repository uses phonimizer library.
* Please refer [here](https://bootphon.github.io/phonemizer/install.html) to install phonemizer and backend
* In Windows, you need more setting to use phonemizer.
    * Please refer [here](https://github.com/bootphon/phonemizer/issues/44)
    * In conda enviornment, the following commands are useful.
        ```bash
        conda env config vars set PHONEMIZER_ESPEAK_PATH='C:\Program Files\eSpeak NG'
        conda env config vars set PHONEMIZER_ESPEAK_LIBRARY='C:\Program Files\eSpeak NG\libespeak-ng.dll'
        ```
# Run

## Command

### Single GPU
```
python Train.py -hp <path> -s <int>
```

* `-hp <path>`
    * The hyper paramter file path
    * This is required.

* `-s <int>`
    * The resume step parameter.
    * Default is `0`.
    * If value is `0`, model try to search the latest checkpoint.

### Multi GPU
```
CUDA_VISIBLE_DEVICES=0,1,2,3,4,5,6,7 OMP_NUM_THREADS=32 python -m torch.distributed.launch --nproc_per_node=8 Train.py --hyper_parameters Hyper_Parameters.yaml --port 54322
```

* I recommend to check the [multi_gpu.sh](./multi_gpu.sh).

# TODO
* F0(pitch) apply