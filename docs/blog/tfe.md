---
layout: default
title: Introducing TF-Encrypted
parent: Blogs
nav_order: 991
---

# Introducing TF-Encrypted

[TF-Encrypted/TFE](https://tf-encrypted.io/) is an open-source framework for Secure Multi-party Computation (MPC) machine learning. The advantage of TFE is that it's built on top of TensorFlow, allowing non-cryptographic experts to quickly experiment MPC machine learning, while leveraging all the advantages of TensorFlow's optimizations, including graph compilation and distributed orchestration. 

We have developed several projects based on TFE (e.g. The solution winning the iDASH2019 competition), and are actively contributing to TFE. In this blog we will describe a proof-of-concept use case, and give a walkthrough on how to do it with TFE.

# MPC machine learning training in five minutes with TF-Encrypted

## The use case: Collaborative fraud detection

Suppose Alice is a bank, Bob is a government. Alice and Bob know many individuals in common, and both parties knows some information about the individuals from different aspects (e.g., Alice knows their credit card bills, while Bob knows their tax information), but only Bob knows wether the individuals have some fraud history or not (denoted by label =1 or 0). Now Bob wants to build a fraud detection model with the help of Alice. Alice is willing to collaborate, but she consider her part of user information sensitive and is not willing to share them directly.

This problem could be summarized as a how to do secure collaborative machine learning training on a vertically split dataset. Here's a simple walkthrough showing how it could be done efficiently using TFE. 


Suppose the dataset contains 7000 samples with 32 features, 16 of which is held by Alice, and the other 16 (and the label) is held by Bob. A random generated example dataset could be found here: [aliceTrainFile.csv](https://raw.githubusercontent.com/Alibaba-Gemini-Lab/tf-encrypted/master/examples/logistic/aliceTrainFile.csv)  and [bobTrainFileWithLabel.csv](https://raw.githubusercontent.com/Alibaba-Gemini-Lab/tf-encrypted/master/examples/logistic/bobTrainFileWithLabel.csv). 


## Step 1. Prepare three machines and set up environments

Check python3 and pip3 is correctly installed, then install TensorFlow and TFE on the three machines.

```shell
# python3 --version
Python 3.6.9
# pip3 --version
pip 9.0.1 from /usr/lib/python3/dist-packages (python 3.6)
#pip3 install tensorflow==1.13.2
#pip3 install tf-encrypted
```


## Step 2. Edit the following file config.json

Replace machine:port with your own IP:Port. Make sure the three machines are able to access each other via IP:Port.

```json
{
    "alice": "machine1:port1",
    "bob": "machine2:port2",
    "crypto-producer": "machine3:port3"
}
```

## Step 3. Write TFE training code
We provide an example for Logistic Regression :  [common.py](https://github.com/Alibaba-Gemini-Lab/tf-encrypted/blob/744fe406dfd1c80c08d86e0d83e8e7258d9b9459/examples/logistic/common.py) , [training_alice.py](https://raw.githubusercontent.com/Alibaba-Gemini-Lab/tf-encrypted/master/examples/logistic/training_alice.py) , [training_bob.py](https://raw.githubusercontent.com/Alibaba-Gemini-Lab/tf-encrypted/master/examples/logistic/training_bob.py)  and [training_server.py](https://raw.githubusercontent.com/Alibaba-Gemini-Lab/tf-encrypted/master/examples/logistic/training_server.py). 

## Step 4. Copy the files to the same directory

Copy config.json , common.py , training_alice.py , aliceTrainFile.csv to  machine1; 

Copy config.json ,  training_bob.py , bobTrainFileWithLabel.csv to machine2;

Copy config.json ,  training_server.py to machine3;

## Step 5. Run!

Run the following command on the three machines, and the trained logistic regression model (the weights for each feature) will be printed on machine1. 
```shell
python3 training_bob.py
python3 training_server.py
python3 training_alice.py
```

# Extra notes

## Using ABY3

The above example uses the default protocol of TFE, which is **POND**.   Change the files to the corresponding ones here https://github.com/Alibaba-Gemini-Lab/tf-encrypted/tree/master/examples/logistic/aby3 to use **ABY3**.


## Production usage
TFE is an experimental software and must be hardened before used in production environments.  
