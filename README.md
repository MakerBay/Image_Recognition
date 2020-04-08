# Image_Recognition

this repository contains generic templates for image recognition such as Tensorflow scripts ... etc ...

There is *.py files to run directly from a computer, as well as their Colab Notebook equivalent *.ipynb to execute in the Cloud with Google Tensorflow servers and GP

The attached code is optimized for tensorflow 1.15 and may require an update to tensorflow 2.X

# Installation on Ubuntu

```python
# Tensorflow==1.15 is not available for python 3.8 so we downgrade to python3.7
# https://pypi.org/project/tensorflow/1.15.0/#files
sudo apt-get install python3.7
virtualenv -p /usr/bin/python3.7 venv
source venv/bin/activate
python3 -m pip install --upgrade pip
python3 -m pip install --upgrade setuptools
python3 -m pip install --upgrade tensorflow==1.15
python3 -m pip install --upgrade tensorflow_hub
```

# Training the model on a standalone computer

The file retrain.py allows you to train your model directly on your computer

Assuming the training images are located in your local folder `training_images`
```bash
time python3 retrain.py --image_dir ./training_images/
```
It can be very long ... Once trained, a graph model is created in the folder /tmp/retrain_tmp, in a form of a file .

# Image recognition on a standalone computer

The file predict.py allows you to perform ML image recognition on images stored in a 'unknow_images' folder. Each recognized images will be printed on the screen.

```bash
./predict.py
```

Having multiple and good GPUs boost the speed of image recognition

# Training the model on Google Colabolatory (Colab Notebook)

## Mounting the images drive on Colab Notebook

Colab Notebooks requires the images to be uploaded on Google Drive, then the Drive can be mounted and accessed by your ipynb scripts thanks to the below code:

```python
from google.colab import drive
drive.mount('/content/drive'
```
Mounting the drive for training purpose requires manual authorization
```bash
Go to this URL in a browser: https://accounts.google.com/o/oauth2/auth?client_id=947318989803-6bn6qk8qdgf4n4g3pfee6491hc0brc4i.apps.googleusercontent.com&redirect_uri=urn%3aietf%3awg%3aoauth%3a2.0%3aoob&response_type=code&scope=email%20https%3a%2f%2fwww.googleapis.com%2fauth%2fdocs.test%20https%3a%2f%2fwww.googleapis.com%2fauth%2fdrive%20https%3a%2f%2fwww.googleapis.com%2fauth%2fdrive.photos.readonly%20https%3a%2f%2fwww.googleapis.com%2fauth%2fpeopleapi.readonly

Enter your authorization code:
··········
Mounted at /content/drive
```

## File System structure

```bash
TensorFlow_model
├── cpu
│   ├── output_graph.pb
│   └── output_labels.txt
├── tmp
│   └── retrain_tmp
└── training_data
    ├── Name_of_Asset_Class_001
    ├── Name_of_Asset_Class_002
    ├── Name_of_Asset_Class_003
    ├── Name_of_Asset_Class_004
    └── Name_of_Asset_Class_005
```

1. cpu folder contains the list of labels `output_labels.txt` and the final ML model `output_graph.pb`
2. tmp folder is a temporary folder used by tensorflow to train the model (and therefore can be ignored)
3. training_data folder contains the images used for training the model.

Those training images are splitted into folders by asset class names. 

An asset class is the coral specie name and therefore above `Name_of_Asset_Class_00X` should be replaced by the name of the coral specie such as 'Montastrae_cavernosa'. Those Asset class names are the actual labels our machine learning model is trained to recognize.

# Prediction on Colab Notebooks

The prediction Notebook only requires the ML model `output_graph.pb` and the labels `output_labels.txt` to work.

```python
# Loading the Trained Machine Learning Model created from running retrain.py on the training_images directory
graph = load_graph('/content/drive/My Drive/TensorFlow_model/tmp/retrain_tmp/output_graph.pb')
labels = load_labels("//content/drive/My Drive/TensorFlow_model/tmp/retrain_tmp/output_labels.txt")
```

In predict.ipynb notebook, as opposed to the training process, images are not required to be hosted or mounted in a Google drive. Instead we use a process similar to CAPTCHA: images to be tested are passed to the prediction model in a form of a list of base64 images through an API call. Then base64 images are turned back into image binaries.

It is possible to change this behavior and have the image binaries mounted on the tensorflow server. Then a similar code as 'predict.py' can be used to label images in a folder called 'unknow_images'
