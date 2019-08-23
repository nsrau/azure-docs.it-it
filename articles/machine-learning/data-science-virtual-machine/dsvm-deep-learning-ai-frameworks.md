---
title: Deep Learning & Framework di intelligenza artificiale
titleSuffix: Azure Data Science Virtual Machines
description: Framework e strumenti per Deep Learning in Data Science Virtual Machine di Azure, tra cui TensorFlow, PyTorch, keras, caffe, MXNet, Horovod, Theano, Chainer e altro ancora.
keywords: strumenti di analisi scientifica dei dati, macchina virtuale per l'analisi scientifica dei dati, strumenti per l'analisi scientifica dei dati, analisi scientifica dei dati per Linux
services: machine-learning
documentationcenter: ''
author: vijetajo
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/11/2019
ms.author: vijetaj
ms.openlocfilehash: adf6a68d6da4236874dce403bb4d7043087049b2
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69970356"
---
# <a name="deep-learning-and-ai-frameworks"></a>Apprendimento avanzato e framework AI
Il [Data Science Virtual Machine](https://aka.ms/dsvm) (DSVM) supporta una serie di Framework di apprendimento avanzato che consentono di creare applicazioni di intelligenza artificiale con analisi predittiva e funzionalità cognitive come la comprensione delle immagini e della lingua.

I Framework per Deep learning disponibili in DSVM includono:

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

|Strumenti&nbsp;dl&nbsp;inDSVM&nbsp;|Windows|Linux|Note&nbsp;sull'utilizzo|
|---------|:-------------------:|:------------------:|-----|
|[TensorFlow](https://www.tensorflow.org/) | Sì (Windows 2016) | Sì |Installato in Python 3,5 in [Linux e windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) e Python 3,6 in [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition). I notebook di Jupyter di esempio sono inclusi in DSVM.<br/><br/>**Per eseguirlo**:<br/>* Terminale: attivare l'ambiente corretto, quindi eseguire python. <br/> Jupyter connettersi a [Jupyter](provision-vm.md#tools-installed-on-the-microsoft-data-science-virtual-machine) oppure a [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), quindi aprire la directory di TensorFlow per gli esempi.  |
|[PyTorch](https://pytorch.org/)| No | Sì |Installato in [Python 3,5](dsvm-languages.md#python-linux-and-windows-server-2012-edition). Sono inclusi i notebook di Jupyter di esempio e gli esempi si trovano in/dsvm/Samples/pytorch.    <br/><br/>**Per eseguirla**<br/>* Terminale: attivare l'ambiente corretto, quindi eseguire python.<br/>* [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux): Connettersi, quindi aprire la directory PyTorch per gli esempi.  |
|[Keras](https://keras.io/)| Sì | Yes |L'API è installata in Python 3,5 in [Linux e windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) e Python 3,6 in [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition). [Esempi](https://github.com/fchollet/keras/tree/master/examples)<br/><br/>**Per eseguirlo**:<br/>* Terminale: attivare l'ambiente corretto, quindi eseguire python. <br/> Jupyter scaricare gli esempi dal percorso di GitHub, connettersi a [Jupyter](provision-vm.md#tools-installed-on-the-microsoft-data-science-virtual-machine) oppure a [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), quindi aprire la directory per gli esempi. |
|[Caffe](https://github.com/caffe2/caffe2) | No |Sì (Ubuntu)|Caffe è installato in `/opt/caffe`.   Gli esempi sono `/opt/caffe/examples`disponibili in. <br/><br/>**Per eseguirlo**, usare x2go per accedere alla macchina virtuale, quindi avviare un nuovo terminale e immettere:<br/>`cd /opt/caffe/examples`<br/>`source activate root`<br/>`jupyter notebook`<br/><br/>Si apre una nuova finestra del browser con i blocchi appunti di esempio. I file binari vengono installati in /opt/caffe/build/installazioni/bin.<br/><br/>La versione installata di Caffe richiede Python 2.7 e non funziona con Python 3.5 attivato per impostazione predefinita. Per passare a Python 2,7, eseguire `source activate root` per cambiare ambiente Anaconda.|
|[Caffe2](https://github.com/caffe2/caffe2) | No |Sì (Ubuntu)|Caffe2 viene installato nell'[ambiente Conda di Python 2.7, ovvero nella radice](dsvm-languages.md#python-linux-and-windows-server-2012-edition). L'origine è `/opt/caffe2`.<br/>In Jupyter sono inclusi i blocchi appunti di esempio.<br/><br/>**Per eseguirlo**:<br/>* Nel terminale: attivare l'[ambiente di Python radice](dsvm-languages.md#python-linux-and-windows-server-2012-edition), avviare Python e importare caffe2. <br/> * In JupyterHub: [connettersi a JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), quindi passare alla directory Caffe2 per trovare i blocchi appunti di esempio. Alcuni blocchi appunti richiedono l'impostazione della radice di Caffe2 nel codice Python. Immettere /opt/caffe2. |
|[Torcia](http://torch.ch/) | No |Sì (Ubuntu)|Torch è installato in `/dsvm/tools/torch`. PyTorch viene installato in Python 2.7, _radice_, e nell'ambiente Python 3.5, _py35_. Gli esempi di torcia `/dsvm/samples/torch` sono in e gli esempi `/dsvm/samples/pytorch`di PyTorch sono disponibili in. |
|[MXNet](https://mxnet.io/) | Sì (Windows 2016) | Sì|MXNet è installato in `C:\dsvm\tools\mxnet` su Windows e in `/dsvm/tools/mxnet` su Linux. Le associazioni di Python vengono installate in Python 3.5 su [Linux e Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) e in Python 3.6 su [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition). Anche le associazioni di R vengono installate su Ubuntu.<br/><br/>Sono inclusi i blocchi appunti Jupyter di esempio. <br/><br/>**Per eseguirlo**:<br/>Terminal Attivare l'ambiente corretto, quindi eseguire python. <br/> Jupyter connettersi a [Jupyter](provision-vm.md#tools-installed-on-the-microsoft-data-science-virtual-machine) oppure a [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), quindi aprire la directory di MXNet per gli esempi.|
|[Server modello MXNet](https://github.com/awslabs/mxnet-model-server) | No | Sì |Server per la creazione di endpoint HTTP per i modelli MXNet e ONNX. _mxnet-model-server_ è disponibile nel terminale. Esempi nella [pagina del server del modello MXNet](https://github.com/awslabs/mxnet-model-server).|
|[Horovod](https://github.com/uber/horovod) | No | Sì (Ubuntu) |Framework per Deep learning distribuito per TensorFlow. Horovod viene installato in Python 3.5 su [Ubuntu](dsvm-languages.md#python-linux-and-windows-server-2012-edition).  [Vedere gli esempi](https://github.com/uber/horovod/tree/master/examples)<br/><br/>**Per eseguirlo**, attivare l'ambiente corretto nel terminale, quindi eseguire python. |
|[Theano](https://github.com/Theano/Theano) | No | Sì (Ubuntu) |Theano viene installato in Python 2.7, _radice_, e nell'ambiente Python 3.5, _py35_.<br/><br/>**Per eseguirlo**: <br/>Terminal Attivare la versione di Python desiderata (root o PY35), eseguire python, quindi importare Theano.<br/>Jupyter Selezionare il kernel Python 2,7 o 3,5, quindi importare Theano.  <br/>Per risolvere un bug di MKL recente, è necessario innanzitutto impostare il livello di threading di MKL:<br/><br/>_export MKL_THREADING_LAYER=GNU_|
|[Chainer](https://chainer.org/) |No | Yes |Chainer viene installato in [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition). Vengono installati anche ChainerRL e ChainerCV. <br/><br/>In Jupyter sono inclusi i blocchi appunti di esempio.<br/><br/>**Per eseguirlo**: <br/>Terminal Attivare l'ambiente [python 3,5](dsvm-languages.md#python-linux-and-windows-server-2012-edition) , eseguire _Python_, quindi importare Chainer. <br/> JupyterHub [Connettersi a JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), quindi passare alla directory chainer per trovare i blocchi appunti di esempio.|
|[Cifre NVidia](https://github.com/NVIDIA/DIGITS) | No | Sì (Ubuntu) |Sistema di apprendimento avanzato di NVIDIA per il training rapido dei modelli di apprendimento avanzato. DIGITS viene installato in `/dsvm/tools/DIGITS` ed è disponibile come servizio denominato _digits_.  <br/><br/>**Per eseguirlo**: <br/>Accedere alla macchina virtuale con X2Go. Avviare il servizio ```sudo systemctl start digits```in un terminale. <br/><br/>L'avvio del servizio richiede circa un minuto. Aprire un Web browser e passare all'indirizzo `http://localhost:5000`. Si noti che DIGITS non offre un accesso protetto ed è consigliabile che non venga esposto al di fuori della macchina virtuale.|
|[CUDA, cuDNN, driver NVIDIA](https://developer.nvidia.com/cuda-toolkit) |Yes | Sì | |
|nvidia-smi|Sì | Yes |Strumento NVIDIA per l'esecuzione di query sull'attività GPU. _nvidia-smi_ è disponibile nel percorso del sistema. <br/><br/>Avviare un prompt dei comandi in Windows o un terminale in Linux, quindi eseguire _nvidia-smi_.|
|[Servizio TensorFlow](https://www.tensorflow.org/serving/) | No | Sì |Server da inferenza su un modello TensorFlow. _tensorflow_model_server_ è disponibile nel terminale. Sono disponibili esempi [online](https://www.tensorflow.org/serving/).|
|[TensorRT](https://developer.nvidia.com/tensorrt) |  No | Sì (Ubuntu) |Server di inferenza per l'apprendimento avanzato da NVIDIA. TensorRT viene installato come un pacchetto _apt_. Sono disponibili esempi [online](https://docs.nvidia.com/deeplearning/sdk/tensorrt-developer-guide/index.html#samples).|
|[Microsoft Cognitive Toolkit (CNTK)](https://docs.microsoft.com/cognitive-toolkit/)|Yes | Yes | Installato in Python 3,5 in [Linux e windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) e Python 3,6 in [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition). I notebook di Jupyter di esempio sono inclusi in DSVM. <br/><br/>**Per eseguirlo**: <br/>Terminal Attivare l'ambiente corretto ed eseguire python. <br/>Jupyter connettersi a [Jupyter](provision-vm.md#tools-installed-on-the-microsoft-data-science-virtual-machine) o a [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), quindi aprire la directory CNTK per gli esempi. |
|Deep Water|No | Sì (Ubuntu) |Framework per Deep learning per H2O, Deep Water è installato in [Python 3,5](dsvm-languages.md#python-linux-and-windows-server-2012-edition) ed è disponibile anche `/dsvm/tools/deep_water`in. In Jupyter sono inclusi i blocchi appunti di esempio. Deep Water richiede CUDA 8 con cuDNN 5.1. Non si trova nel percorso della libreria per impostazione predefinita, perché altri framework di apprendimento avanzato usano CUDA 9 e cuDNN 7. Per usare CUDA 8 + cuDNN 5.1 per Deep Water:<br/><br/>```export LD_LIBRARY_PATH=/usr/local/cuda-8.0/lib64:${LD_LIBRARY_PATH}```<br/>```export CUDA_ROOT=/usr/local/cuda-8.0```<br/><br/>Per usare Deep Water:<br/>* Terminale: attivare l'ambiente [python 3,5](dsvm-languages.md#python-linux-and-windows-server-2012-edition) , quindi eseguire _Python_. <br/>* JupyterHub: [connettersi a JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), quindi passare alla directory deep_water per trovare i blocchi appunti di esempio.|
