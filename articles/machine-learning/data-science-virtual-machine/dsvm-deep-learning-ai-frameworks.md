---
title: Deep Learning & Framework di intelligenza artificiale
titleSuffix: Azure Data Science Virtual Machine
description: Framework e strumenti per Deep learning disponibili in Azure Data Science Virtual Machine, tra cui TensorFlow, PyTorch, keras, caffe, MXNet, Horovod, Theano, Chainer e altro ancora.
keywords: strumenti di analisi scientifica dei dati, macchina virtuale per l'analisi scientifica dei dati, strumenti per l'analisi scientifica dei dati, analisi scientifica dei dati per Linux
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 08/11/2019
ms.openlocfilehash: f71a2cc5c0a430037b429aac4f3ec4dc5f5f933d
ms.sourcegitcommit: 5f67772dac6a402bbaa8eb261f653a34b8672c3a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/01/2019
ms.locfileid: "70208115"
---
# <a name="deep-learning-and-ai-frameworks-for-azure-data-science-vm"></a>Apprendimento avanzato e Framework di intelligenza artificiale per Azure Data Science VM
Il [Data Science Virtual Machine](https://aka.ms/dsvm) (DSVM) supporta una serie di Framework per l'apprendimento avanzato che consentono di creare applicazioni di intelligenza artificiale con analisi predittiva e funzionalità cognitive come la comprensione di immagini e linguaggi.

I Framework per Deep learning disponibili tramite DSVM includono:

+ TensorFlow
+ PyTorch
+ Keras
+ Caffe
+ Caffe2
+ Torch
+ MXNet Model Server
+ MXNet
+ Horovod
+ Theano
+ Chainer
+ Deep Water
+ NVIDIA DIGITS
+ nvidia-smi
+ TensorFlow Serving
+ TensorRT
+ Microsoft Cognitive Toolkit

|&nbsp;Strumenti&nbsp;diformazione&nbsp;approfondita su DSVM|Windows|Linux|Note&nbsp;sull'utilizzo|
|---------|-------------------|------------------|-----|
|[TensorFlow](https://www.tensorflow.org/) | Sì (Windows 2016) | Sì |Installato in Python 3,5 in [Linux e windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) e Python 3,6 in [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition). I notebook di Jupyter di esempio sono inclusi in DSVM.<br/><br/>**Per eseguirlo**:<br/>Terminal Attivare l'ambiente corretto, quindi eseguire python. <br/> Jupyter Connettersi a [Jupyter](provision-vm.md) o [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), quindi aprire la directory TensorFlow per gli esempi.  |
|[PyTorch](https://pytorch.org/)| No | Yes |Installato in [Python 3,5](dsvm-languages.md#python-linux-and-windows-server-2012-edition). Sono inclusi i notebook di Jupyter di esempio e gli esempi si trovano in/dsvm/Samples/pytorch.    <br/><br/>**Per eseguirlo**:<br/>Terminal Attivare l'ambiente corretto, quindi eseguire python.<br/>* [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux): Connettersi, quindi aprire la directory PyTorch per gli esempi.  |
|[Keras](https://keras.io/)| Sì | Sì |L'API è installata in Python 3,5 in [Linux e windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) e in Python 3,6 in [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition). [Vedere esempi](https://github.com/fchollet/keras/tree/master/examples).<br/><br/>**Per eseguirlo**:<br/>Terminal Attivare l'ambiente corretto, quindi eseguire python. <br/> Jupyter Scaricare gli esempi dal percorso di GitHub, connettersi a [Jupyter](provision-vm.md) o [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), quindi aprire la directory di esempio. |
|[Caffe](https://github.com/caffe2/caffe2) | No |Sì (Ubuntu)|Caffe è installato in `/opt/caffe`.   Gli esempi sono `/opt/caffe/examples`disponibili in. <br/><br/>**Per eseguirlo**, usare x2go per accedere alla macchina virtuale e quindi avviare un nuovo terminale e immettere quanto segue:<br/>`cd /opt/caffe/examples`<br/>`source activate root`<br/>`jupyter notebook`<br/><br/>Si apre una nuova finestra del browser con i blocchi appunti di esempio. I file binari vengono installati in /opt/caffe/build/installazioni/bin.<br/><br/>La versione installata di caffe richiede Python 2,7 e non funziona con Python 3,5, che è attivato per impostazione predefinita. Per passare a Python 2,7, eseguire `source activate root` per passare all'ambiente Anaconda.|
|[Caffe2](https://github.com/caffe2/caffe2) | No |Sì (Ubuntu)|Caffe2 viene installato nell'[ambiente Conda di Python 2.7, ovvero nella radice](dsvm-languages.md#python-linux-and-windows-server-2012-edition). L'origine è `/opt/caffe2`.<br/>In Jupyter sono inclusi i blocchi appunti di esempio.<br/><br/>**Per eseguirlo**:<br/>Terminal Attivare l' [ambiente Python radice](dsvm-languages.md#python-linux-and-windows-server-2012-edition), avviare Python e importare Caffe2. <br/> JupyterHub [Connettersi a JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux)e quindi passare alla directory Caffe2 per trovare i blocchi appunti di esempio. Alcuni blocchi appunti richiedono l'impostazione della radice di Caffe2 nel codice Python. Immettere /opt/caffe2. |
|[Torcia](http://torch.ch/) | No |Sì (Ubuntu)|Torch è installato in `/dsvm/tools/torch`. PyTorch viene installato in Python 2.7, _radice_, e nell'ambiente Python 3.5, _py35_. Gli esempi di torcia `/dsvm/samples/torch` sono in e gli esempi `/dsvm/samples/pytorch`di PyTorch sono disponibili in. |
|[MXNet](https://mxnet.io/) | Sì (Windows 2016) | Sì|MXNet è installato in `C:\dsvm\tools\mxnet` su Windows e in `/dsvm/tools/mxnet` su Linux. Le associazioni di Python vengono installate in Python 3.5 su [Linux e Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) e in Python 3.6 su [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition). Anche le associazioni di R vengono installate su Ubuntu.<br/><br/>Sono inclusi i blocchi appunti Jupyter di esempio. <br/><br/>**Per eseguirlo**:<br/>Terminal Attivare l'ambiente corretto, quindi eseguire python. <br/> Jupyter Connettersi a [Jupyter](provision-vm.md) o [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), quindi aprire la directory MxNet per gli esempi.|
|[Server modello MXNet](https://github.com/awslabs/mxnet-model-server) | No | Yes |Server per la creazione di endpoint HTTP per i modelli MXNet e ONNX. _MxNet-Model-server_ è disponibile nel terminale. Esempi nella [pagina del server del modello MXNet](https://github.com/awslabs/mxnet-model-server).|
|[Horovod](https://github.com/uber/horovod) | No | Sì (Ubuntu) |Framework per Deep learning distribuito per TensorFlow. Horovod viene installato in Python 3.5 su [Ubuntu](dsvm-languages.md#python-linux-and-windows-server-2012-edition).  [Vedere esempi](https://github.com/uber/horovod/tree/master/examples).<br/><br/>**Per eseguirlo**, attivare l'ambiente corretto nel terminale, quindi eseguire python. |
|[Theano](https://github.com/Theano/Theano) | No | Sì (Ubuntu) |Theano viene installato in Python 2,7 (_radice_) e nell'ambiente Python 3,5 (_PY35_).<br/><br/>**Per eseguirlo**: <br/>Terminal Attivare la versione di Python desiderata (root o PY35), eseguire python, quindi importare Theano.<br/>Jupyter Selezionare il kernel Python 2,7 o 3,5, quindi importare Theano.  <br/>Per aggirare un bug recente della libreria del kernel matematico (MKL), è necessario innanzitutto impostare il livello di threading MKL nel modo seguente:<br/><br/>`export MKL_THREADING_LAYER=GNU`|
|[Chainer](https://chainer.org/) |No | Yes |Chainer viene installato in [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition). Vengono installati anche ChainerRL e ChainerCV. <br/><br/>In Jupyter sono inclusi i blocchi appunti di esempio.<br/><br/>**Per eseguirlo**: <br/>Terminal Attivare l'ambiente [python 3,5](dsvm-languages.md#python-linux-and-windows-server-2012-edition) , eseguire _Python_, quindi importare Chainer. <br/> JupyterHub [Connettersi a JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux)e quindi passare alla directory chainer per trovare i blocchi appunti di esempio.|
|[Cifre NVidia](https://github.com/NVIDIA/DIGITS) | No | Sì (Ubuntu) |Sistema di apprendimento avanzato di NVIDIA per il training rapido dei modelli di apprendimento avanzato. Le cifre vengono installate `/dsvm/tools/DIGITS` in ed è disponibile come servizio denominato _digits_.  <br/><br/>**Per eseguirlo**: <br/>Accedere alla macchina virtuale con X2Go. Avviare il servizio ```sudo systemctl start digits```in un terminale eseguendo. <br/><br/>L'avvio del servizio richiede circa un minuto. Aprire un Web browser e passare a `http://localhost:5000`. Si noti che DIGITS non offre un accesso protetto ed è consigliabile che non venga esposto al di fuori della macchina virtuale.|
|[CUDA, cuDNN, driver NVIDIA](https://developer.nvidia.com/cuda-toolkit) |Yes | Sì | |
|nvidia-smi|Sì | Yes |Strumento NVIDIA per l'esecuzione di query sull'attività GPU; _NVIDIA-SMI_ è disponibile nel percorso di sistema. <br/><br/>Aprire un prompt dei comandi (in Windows) o un terminale (in Linux), quindi eseguire _NVIDIA-SMI_.|
|[Servizio TensorFlow](https://www.tensorflow.org/serving/) | No | Sì |Server da inferenza su un modello TensorFlow; tensorflow_model_server è disponibile nel terminale. Sono disponibili esempi [online](https://www.tensorflow.org/serving/).|
|[TensorRT](https://developer.nvidia.com/tensorrt) |  No | Sì (Ubuntu) |Server di inferenza Deep Learning di NVIDIA. TensorRT viene installato come un pacchetto _apt_. Sono disponibili esempi [online](https://docs.nvidia.com/deeplearning/sdk/tensorrt-developer-guide/index.html#samples).|
|[Microsoft Cognitive Toolkit (CNTK)](https://docs.microsoft.com/cognitive-toolkit/)|Sì | Yes | Installato in Python 3,5 in [Linux e windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) e Python 3,6 in [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition). I notebook di Jupyter di esempio sono inclusi in DSVM. <br/><br/>**Per eseguirlo**: <br/>Terminal Attivare l'ambiente corretto ed eseguire python. <br/>Jupyter Connettersi a [Jupyter](provision-vm.md) o [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), quindi aprire la directory CNTK per gli esempi. |
|Deep Water|No | Sì (Ubuntu) |Il Framework per l'apprendimento avanzato per H2O, Deep Water è installato in [Python 3,5](dsvm-languages.md#python-linux-and-windows-server-2012-edition) ed è disponibile `/dsvm/tools/deep_water`anche in. In Jupyter sono inclusi i blocchi appunti di esempio. Deep Water richiede CUDA 8 con cuDNN 5.1. Per impostazione predefinita, non si trova nel percorso di libreria, perché altri Framework di apprendimento avanzato usano CUDA 9 e cuDNN 7. Per usare, installare CUDA 8 + cuDNN 5,1 per Deep Water:<br/><br/>```export LD_LIBRARY_PATH=/usr/local/cuda-8.0/lib64:${LD_LIBRARY_PATH}```<br/>```export CUDA_ROOT=/usr/local/cuda-8.0```<br/><br/>Per usare Deep Water:<br/>Terminal Attivare l'ambiente [python 3,5](dsvm-languages.md#python-linux-and-windows-server-2012-edition) , quindi eseguire _Python_. <br/>JupyterHub [Connettersi a JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux)e quindi passare alla directory deep_water per trovare i blocchi appunti di esempio.|
