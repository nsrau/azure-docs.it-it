---
title: Framework di iA di & Deep Learning
titleSuffix: Azure Data Science Virtual Machine
description: Framework e strumenti di deep learning disponibili in Azure Data Science Virtual Machine.Available deep learning frameworks and tools on Azure Data Science Virtual Machine.
keywords: strumenti di analisi scientifica dei dati, macchina virtuale per l'analisi scientifica dei dati, strumenti per l'analisi scientifica dei dati, analisi scientifica dei dati per Linux
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: d8a5cf428f41b130e6faf68ac87a075c15211099
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270069"
---
# <a name="deep-learning-and-ai-frameworks-for-the-azure-data-science-vm"></a>Deep learning and AI frameworks for the Azure Data Science VM
I framework di deep learning su DSVM sono elencati di seguito.

## <a name="caffe"></a>[Caffe](https://github.com/BVLC/caffe)

|    |           |
| ------------- | ------------- |
| Versioni supportate | |
| Edizioni DSVM supportate      | Linux (Ubuntu)     |
| Come viene configurata o installata sulla macchina virtuale per data science?  | Caffe è installato in `/opt/caffe`.   I campioni `/opt/caffe/examples`sono in formato .|
| Come eseguirlo      | usare X2Go per accedere alla macchina virtuale, quindi avviare un nuovo terminale e immettere quanto segue:<br/>`cd /opt/caffe/examples`<br/>`source activate root`<br/>`jupyter notebook`<br/><br/>Si apre una nuova finestra del browser con i blocchi appunti di esempio. I file binari vengono installati in /opt/caffe/build/installazioni/bin.<br/><br/>La versione installata di Caffe richiede Python 2.7 e non funziona con Python 3.5, che viene attivato per impostazione predefinita. Per passare a Python 2.7, eseguire `source activate root` per passare all'ambiente Anaconda.|    

## <a name="caffe2"></a>[Caffè2](https://github.com/caffe2/caffe2)

|    |           |
| ------------- | ------------- |
| Versioni supportate | |
| Edizioni DSVM supportate      | Linux (Ubuntu)     |
| Come viene configurata o installata sulla macchina virtuale per data science?  | Caffe2 è installato nell'ambiente conda [Python 2.7 (radice). |
| Come eseguirlo      | Terminale: Avviare Python, e importare Caffe2. <br/> JupyterHub: [Connettersi a JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine), quindi passare alla directory Caffe2 per trovare blocchi appunti di esempio. Alcuni blocchi appunti richiedono l'impostazione della radice di Caffe2 nel codice Python. Immettere /opt/caffe2. |

## <a name="chainer"></a>[Chainer](https://chainer.org/)

|    |           |
| ------------- | ------------- |
| Versioni supportate | 5,2 |
| Edizioni DSVM supportate      | Linux (Ubuntu)     |
| Come viene configurata o installata sulla macchina virtuale per data science?  | Chainer viene installato in Python 3.5. |
| Come eseguirlo      | Terminale: Attivare l'ambiente `python`Python 3.5, eseguire , e quindi `import chainer`. <br/> JupyterHub: [Connettersi a JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine), quindi passare alla directory Chainer per trovare i blocchi appunti di esempio.| 

## <a name="cuda-cudnn-nvidia-driver"></a>[Driver CUDA, cuDNN, NVIDIA](https://developer.nvidia.com/cuda-toolkit)

|    |           |
| ------------- | ------------- |
| Versioni supportate | 10.0.130|
| Edizioni DSVM supportate      | Windows e Linux   |
| Come viene configurata o installata sulla macchina virtuale per data science?  |_nvidia-smi_ è disponibile nel percorso del sistema.  |
| Come eseguirlo      | Aprire un prompt dei comandi (in Windows) o un terminale (su Linux) e quindi eseguire _nvidia-smi_. |


## <a name="horovod"></a>[Horovod](https://github.com/uber/horovod)

|    |           |
| ------------- | ------------- |
| Versioni supportate | 0.16.1|
| Edizioni DSVM supportate      | Linux (Ubuntu)   |
| Come viene configurata o installata sulla macchina virtuale per data science?  | Horovod è installato in Python 3.5 |
| Come eseguirlo      | Attivare l'ambiente corretto nel terminale, quindi eseguire Python. |

## <a name="keras"></a>[Keras](https://keras.io/)

|    |           |
| ------------- | ------------- |
| Versioni supportate | 2.2.4 |
| Edizioni DSVM supportate      | Windows e Linux   |
| Come viene configurata o installata sulla macchina virtuale per data science?  | Keras è installato in Python 3.6 su Windows e in Python 3.5 in Linux |
| Come eseguirlo      | Attivare l'ambiente corretto nel terminale, quindi eseguire Python. |

## <a name="microsoft-cognitive-toolkit-cntk"></a>[Microsoft Cognitive Toolkit (CNTK)](https://docs.microsoft.com/cognitive-toolkit/)

|    |           |
| ------------- | ------------- |
| Versioni supportate | 2.5.1 |
| Edizioni DSVM supportate      | Windows e Linux   |
| Come viene configurata o installata sulla macchina virtuale per data science?  | CNTK è installato in Python 3.6 su [Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition) e in Python 3.5 su [Linux](./dsvm-tools-languages.md#python-linux-edition)) |
| Come eseguirlo      | Terminale: Attivare l'ambiente corretto ed eseguire Python. <br/>Jupyter: Connettersi a [Jupyter](provision-vm.md) o [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine), quindi aprire la directory CNTK per gli esempi. |

## <a name="mxnet"></a>[MXNet](https://mxnet.apache.org/)
|    |           |
| ------------- | ------------- |
| Versioni supportate | 1.3.0 |
| Edizioni DSVM supportate      | Windows e Linux   |
| Come viene configurata o installata sulla macchina virtuale per data science?  | MXNet è `C:\dsvm\tools\mxnet` installato su `/dsvm/tools/mxnet` Windows e su Ubuntu. Le associazioni Python vengono installate in Python 3.6 in [Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition) e in Python 3.5 su [Linux](./dsvm-tools-languages.md#python-linux-edition)) sono incluse anche nella dSVM di Ubuntu. |
| Come eseguirlo      | Terminale: attivare l'ambiente `import mxnet`conda corretto, quindi eseguire . <br/>Jupyter: Connettersi a [Jupyter](provision-vm.md#access-the-dsvm) o [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine), quindi aprire la directory per gli `mxnet` esempi. |

## <a name="mxnet-model-server"></a>[MXNet Model Server](https://github.com/awslabs/mxnet-model-server#quick-start)

|    |           |
| ------------- | ------------- |
| Versioni supportate | 1.0.1 |
| Edizioni DSVM supportate      | Windows e Linux   |
| Come viene configurata o installata sulla macchina virtuale per data science?  | MXNet Model Server è installato in Python 3.6 in [Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition) e in Python 3.5 su [Linux](./dsvm-tools-languages.md#python-linux-edition)) |
| Come eseguirlo      | Terminale: `sudo systemctl stop jupyterhub` eseguire per arrestare prima il servizio JupyterHub, perché entrambi ascoltano sulla stessa porta. Quindi attivare l'ambiente conda corretto ed eseguire`mxnet-model-server --start --models squeezenet=https://s3.amazonaws.com/model-server/model_archive_1.0/squeezenet_v1.1.mar` |

## <a name="nvidia-system-management-interface-nvidia-smi"></a>[NVidia System Management Interface (nvidia-smi)](https://developer.nvidia.com/nvidia-system-management-interface)

|    |           |
| ------------- | ------------- |
| Versioni supportate |  |
| Edizioni DSVM supportate      | Windows e Linux   |
| A cosa serve? | Lo strumento NVIDIA per l'esecuzione di query sull'attività GPU |
| Come viene configurata o installata sulla macchina virtuale per data science?  | `nvidia-smi`si trova sul percorso di sistema. |
| Come eseguirlo      | In una macchina virtuale **con GPU**, aprire un prompt dei comandi (in `nvidia-smi`Windows) o un terminale (su Linux) e quindi eseguire . |

## <a name="pytorch"></a>[PyTorch](https://pytorch.org/)

|    |           |
| ------------- | ------------- |
| Versioni supportate | 1.2.0 (Ubuntu 16.04, Windows 2016), 1.4.0 (Ubuntu 18.04, Windows 2019) |
| Edizioni DSVM supportate      | Linux |
| Come viene configurata o installata sulla macchina virtuale per data science?  | Installato in [Python 3.5](dsvm-tools-languages.md#python-linux-edition). Sono inclusi i notebook Jupyter di esempio e i campioni sono inclusi in /dsvm/samples/pytorch. |
| Come eseguirlo      | Terminale: attivare l'ambiente corretto, quindi eseguire Python.<br/>* [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine): Connettersi e quindi aprire la directory PyTorch per gli esempi.  |

## <a name="tensorflow"></a>[TensorFlow](https://www.tensorflow.org/)

|    |           |
| ------------- | ------------- |
| Versioni supportate | 1.13 |
| Edizioni DSVM supportate      | Windows, Linux |
| Come viene configurata o installata sulla macchina virtuale per data science?  | Installato in Python 3.5 su [Linux](dsvm-tools-languages.md#python-linux-edition) e Python 3.6 su [Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition) |
| Come eseguirlo      | Terminale: attivare l'ambiente corretto, quindi eseguire Python. <br/> Jupyter: Connettersi a [Jupyter](provision-vm.md) o [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine), quindi aprire la directory TensorFlow per i campioni.   |

## <a name="tensorflow-serving"></a>[TensorFlow Serving](https://www.tensorflow.org/serving/)

|    |           |
| ------------- | ------------- |
| Versioni supportate | 1.12 |
| Edizioni DSVM supportate      | Linux |
| Come viene configurata o installata sulla macchina virtuale per data science?  | tensorflow_model_server è disponibile nel terminale. |
| Come eseguirlo      |  Sono disponibili esempi [online](https://www.tensorflow.org/serving/).   |


## <a name="theano"></a>[Theano](https://github.com/Theano/Theano)

|    |           |
| ------------- | ------------- |
| Versioni supportate | 1.0.3 |
| Edizioni DSVM supportate      | Linux |
| Come viene configurata o installata sulla macchina virtuale per data science?  |Theano viene installato in Python 2.7 (_radice_) e nell'ambiente Python 3.5 (_py35_). |
| Come eseguirlo      |  Terminale: Attivare la versione Python desiderata (radice o py35), eseguire Python, quindi importare Theano.<br/>Jupyter: selezionare il kernel Python 2.7 o 3.5, quindi importare Theano.  <br/>Per aggirare un recente bug della libreria del kernel matematica (MKL), è necessario innanzitutto impostare il livello di threading MKL come segue:<br/><br/>`export MKL_THREADING_LAYER=GNU`  |