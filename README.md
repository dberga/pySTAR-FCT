
# pySTAR-FCT
pySTAR-FCT is an application for selecting task-relevant human fixation locations on arbitrary static images.

See [Eye Movements](https://jtl.lassonde.yorku.ca/eye-movements/), [Selective Tuning](https://jtl.lassonde.yorku.ca/selective-tuning/) and [Vision Architecture](https://jtl.lassonde.yorku.ca/vision-architecture/) posts in [Laboratory of Active and Attentive Vision (LAAV)](https://jtl.lassonde.yorku.ca/) from the Lassonde School of Engineering, [York University](https://www.yorku.ca/).

Preliminary tests were presesented in the Annual Meeting of the Vision Sciences Society 2019 by [Berga et al.](https://doi.org/10.1167/19.10.106c) (VSS 2019) and the prelimiary STAR-FC by [Wloka et al.](https://doi.org/10.1109/cvpr.2018.00336) in the Computer Vision and Pattern Recognition CVPR' 2018. This work is an extension of [Yuulia Kotseruba's](https://github.com/ykotseruba) [pySTAR-FC](https://github.com/ykotseruba/pySTAR-FC). *in any case of use, must cite these two articles*:

* David Berga, Calden Wloka, John K Tsotsos; Modeling task influences for saccade sequence and visual relevance prediction. Journal of Vision 2019;19(10):106c. [https://doi.org/10.1167/19.10.106c](https://doi.org/10.1167/19.10.106c). See [poster](https://doi.org/10.7490/f1000research.1116873.1).
* Wloka, C., Kotseruba, I., & Tsotsos, J. K. (2018). Active Fixation Control to Predict Saccade Sequences. In 2018 IEEE/CVF Conference on Computer Vision and Pattern Recognition (pp. 3184–3193). IEEE. 2018 IEEE/CVF Conference on Computer Vision and Pattern Recognition (CVPR). [https://doi.org/10.1109/cvpr.2018.00336](https://doi.org/10.1109/cvpr.2018.00336)

This work was also included in Chapter 6 of David Berga's PhD thesis, titled "Understanding Eye Movements: Psychophysics and a model of Primary Visual Cortex" (ISBN: 978-84-948531-8-0), see University's [record](https://ddd.uab.cat/record/217162) and [handle](https://hdl.handle.net/10803/667901), download latest version of the [book here](https://dialnet.unirioja.es/servlet/tesis?codigo=270828&orden=0&info=link). 

![pySTAR-FCT in action](examples/Yarbus.gif)

## Getting Started
<img width="994" height="463" alt="image" src="https://github.com/user-attachments/assets/f5d51907-59bc-489c-930e-fbd77c19a28e" />
<img width="753" height="707" alt="image" src="https://github.com/user-attachments/assets/eb3461f6-e453-4cb4-a038-8d67d60c3b57" />


### Authors

* **David Berga** - *integration and testing for Task-based tuning*
* **Iuliia Kotseruba** - *Original Python version of the code*
* **Calden Wloka** - *theory, original C++ implementation for TarzaNN*
* **John K. Tsotsos** - *theory, selective tuning ideation*
* **Toni Kunic** - *docker integration assistance*
  
### Installation

We tested this setup with NVIDIA Titan X on Ubuntu 16.04 with Python 3.5.

#### Docker (strongly recommended)

Install [nvidia-docker](https://github.com/NVIDIA/nvidia-docker) following the instructions in the official repository. There are also good resources elsewhere that describe Docker installation in more detail, for example [this one for Ubuntu 16.04](https://chunml.github.io/ChunML.github.io/project/Installing-NVIDIA-Docker-On-Ubuntu-16.04/).

Add your name to the docker group so you can run docker commands without sudo:
```
usermod -aG docker <yourLoginUsername>
```

After Docker is installed all you need to do is to build a container using the scripts in the ```docker_scripts``` folder:
```
cd pySTAR-FC
sh docker_scripts/build.sh
```

NOTE: You will need to install the same GPU driver as in your system inside the container. ```docker_scripts/build.sh``` outputs the version of the driver on your system. Obtain the link for the ```.run``` file from [NVIDIA](https://www.nvidia.com/Download/index.aspx?lang=en-us) and replace the ```DRIVER_LINK``` in ```build.sh```. Or download the driver manually, place it into ```pySTAR-FC``` directory and rename the file to ```NVIDIA-DRIVER.run```.

To run the container:

```
sudo script/run -v -c <path_to_config_file>
```
There are only two command line options:
* -v for visualization  (optional)
* -c for config file in .ini format

The code and files are mounted in `/opt/STAR-FC`, which you can edit from your host machine using your usual editor or from within the container. Remember that any files created within the container will belong to root, but there is no harm in `chown`ing them back to your host user.

### Manual installation

```
pip3 install -r requirements.txt
```
<!-- pip3 install pycuda==2017.1.1 -->


<!-- Install [CUDA 8.0](https://developer.nvidia.com/cuda-toolkit-archive), [TensorFlow](https://www.tensorflow.org/install/), [CuDNN 6.0](https://developer.nvidia.com/rdp/cudnn-archive) for CUDA 8.0 ([installation instructions](http://docs.nvidia.com/deeplearning/sdk/cudnn-install/index.html)). -->


<!-- install pyCUDA library:

If you are getting 'pycuda._driver.Error: cuInit failed: unknown error' when running the code, try rebooting the machine
 -->

### Additional dependencies

pySTAR-FCT relies on several saliency models that also need to be installed.

Download DeepGazeII and ICF models from [https://deepgaze.bethgelab.org/] and place the files into ```pySTAR_FC/contrib/DeepGazeII``` and ```pySTAR_FC/contrib/ICF``` folders respectively (only the checkpoint files (```ckpt.data```, ```ckpt.index``` and ```ckpt.meta```) and ```centerbias.npy``` for each model).

Download SALICONtf from [https://github.com/ykotseruba/SALICONtf] and place the files it in ```pySTAR-FC/contrib/SALICONtf```. Download pre-trained SALICONtf weights:
```
cd contrib/SALICONtf/models
sh download_pretrained_weights.sh
sh download_vgg_weights.sh
```
Also revise `ImageNet, COCO, PASCAL-S (VOC 2010)` datasets.

## Running STAR-FCT

Below are instructions on how to run a demo of STAR-FCT on a single image (```Yarbus_scaled.jpg``` in ```images``` folder).

If STAR-FCT was build using the recommended Dockerfile, use the following command:
```
sh docker_scripts/run.sh -v -c config_files/test.ini
```

Without Docker use the following command:
```
python3 src/STAR_FC.py -v -c config_files/test.ini
```

There are only two command line options available:
* -v for visualization (optional)
* -c for config file in .ini format

All internal parameters of the algorihtm are set via configuration file (for available options and purpose of each parameter see example config file `config_files/template_config.ini`).

Should you have any questions, feel free to raise an issue or email david.berga@enti.cat or yulia_k@eecs.yorku.ca.
  
### Citing us

If you find our work useful in your research, please consider citing:

```latex
@article{Berga2019a,
  title = {Modeling task influences for saccade sequence and visual relevance prediction},
  volume = {19},
  ISSN = {1534-7362},
  url = {http://dx.doi.org/10.1167/19.10.106c},
  DOI = {10.1167/19.10.106c},
  number = {10},
  journal = {Journal of Vision},
  publisher = {Association for Research in Vision and Ophthalmology (ARVO)},
  author = {Berga,  David and Wloka,  Calden and Tsotsos,  John K},
  year = {2019},
  month = Sept,
  pages = {106c}
}
@inproceedings{Wloka2018,
  title = {Active Fixation Control to Predict Saccade Sequences},
  url = {http://dx.doi.org/10.1109/CVPR.2018.00336},
  DOI = {10.1109/cvpr.2018.00336},
  booktitle = {2018 IEEE/CVF Conference on Computer Vision and Pattern Recognition},
  publisher = {IEEE},
  author = {Wloka,  Calden and Kotseruba,  Iuliia and Tsotsos,  John K.},
  year = {2018},
  month = June,
  pages = {3184–3193}
}
@inbook{Berga2019b_Chapter6,
 place={Barcelona, Spain},
 booktitle={Understanding Eye Movements: Psychophysics and a model of Primary Visual Cortex},
 publisher={Universitat Autònoma de Barcelona},
 year={2019},
 pages={127–134}
} 
```

# Further Literature

* For more detailed design of cognitive architecture, check Tsotsos and Kruijne's (Cognitive Programs)(https://doi.org/10.3389/fpsyg.2014.01260).
* Also check biologically-plausible IoR (Inhibition of Return) by Berga & Otazu's [NSWAM-CM](https://doi.org/10.1016/j.neucom.2020.07.047)
* Also see Navalpakkam & Itti's [model on task attention](https://doi.org/10.1016/j.visres.2004.07.042)
* For word taxonomies, we integrated [Wu & Palmer's semantic similarity](https://doi.org/10.3115/981732.981751) for testing semantic similarity weights.
* For further grammar tasks check Toni Kunic's [Cognitive program compiler (Chapter 2)](https://yorkspace.library.yorku.ca/items/77289451-886e-4c1f-88a7-f52d7482fc77).
* Also see Biparva & Tsotsos' StNet object localization and Rosenfeld et al's [Visual Priming Networks](https://openaccess.thecvf.com/content_cvpr_2018_workshops/papers/w39/Rosenfeld_Priming_Neural_Networks_CVPR_2018_paper.pdf).
* See Huth et al's [brain continuous semantic space categories](https://doi.org/10.1016/j.neuron.2012.10.014) through fMRI BOLD responses.
* Also see Meng & Huang [semantic similarity features with Wordnet model](https://gvpress.com/journals/IJHIT/vol6_no1/1.pdf).

