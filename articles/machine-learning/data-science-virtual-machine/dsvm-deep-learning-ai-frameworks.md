---
title: Deep Learning & Framework di intelligenza artificiale
titleSuffix: Azure Data Science Virtual Machine
description: Framework e strumenti per Deep learning disponibili in Data Science Virtual Machine di Azure.
keywords: strumenti di analisi scientifica dei dati, macchina virtuale per l'analisi scientifica dei dati, strumenti per l'analisi scientifica dei dati, analisi scientifica dei dati per Linux
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: conceptual
ms.date: 10/1/2019
ms.openlocfilehash: db95788b0f2c041157bdc16000d0328c042e86d5
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973683"
---
# <a name="deep-learning-and-ai-frameworks-for-the-azure-data-science-vm"></a>Apprendimento avanzato e Framework di intelligenza artificiale per Azure Data Science VM
I Framework per Deep Learning in DSVM sono elencati di seguito.

## <a name="caffehttpsgithubcombvlccaffe"></a>[Caffe](https://github.com/BVLC/caffe)

|    |           |
| ------------- | ------------- |
| Versioni supportate | |
| Edizioni DSVM supportate      | Linux (Ubuntu)     |
| Come viene configurata o installata sulla macchina virtuale per data science?  | Caffe è installato in `/opt/caffe`.   Gli esempi sono disponibili in `/opt/caffe/examples`.|
| Come eseguirla      | usare X2Go per accedere alla macchina virtuale e quindi avviare un nuovo terminale e immettere quanto segue:<br/>`cd /opt/caffe/examples`<br/>`source activate root`<br/>`jupyter notebook`<br/><br/>Si apre una nuova finestra del browser con i blocchi appunti di esempio. I file binari vengono installati in /opt/caffe/build/installazioni/bin.<br/><br/>La versione installata di caffe richiede Python 2,7 e non funziona con Python 3,5, che è attivato per impostazione predefinita. Per passare a Python 2,7, eseguire `source activate root` per passare all'ambiente Anaconda.|    

## <a name="caffe2httpsgithubcomcaffe2caffe2"></a>[Caffe2](https://github.com/caffe2/caffe2)

|    |           |
| ------------- | ------------- |
| Versioni supportate | |
| Edizioni DSVM supportate      | Linux (Ubuntu)     |
| Come viene configurata o installata sulla macchina virtuale per data science?  | Caffe2 viene installato nell'ambiente conda [Python 2,7 (radice). |
| Come eseguirla      | Terminal Avviare Python e importare Caffe2. <br/> JupyterHub [Connettersi a JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)e quindi passare alla directory Caffe2 per trovare i blocchi appunti di esempio. Alcuni blocchi appunti richiedono l'impostazione della radice di Caffe2 nel codice Python. Immettere /opt/caffe2. |

## <a name="chainerhttpschainerorg"></a>[Chainer](https://chainer.org/)

|    |           |
| ------------- | ------------- |
| Versioni supportate | 5,2 |
| Edizioni DSVM supportate      | Linux (Ubuntu)     |
| Come viene configurata o installata sulla macchina virtuale per data science?  | Chainer viene installato in Python 3,5. |
| Come eseguirla      | Terminal Attivare l'ambiente Python 3,5, eseguire `python` e quindi `import chainer`. <br/> JupyterHub [Connettersi a JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)e quindi passare alla directory chainer per trovare i blocchi appunti di esempio.| 

## <a name="cuda-cudnn-nvidia-driverhttpsdevelopernvidiacomcuda-toolkit"></a>[CUDA, cuDNN, driver NVIDIA](https://developer.nvidia.com/cuda-toolkit)

|    |           |
| ------------- | ------------- |
| Versioni supportate | 10.0.130|
| Edizioni DSVM supportate      | Windows e Linux   |
| Come viene configurata o installata sulla macchina virtuale per data science?  |_nvidia-smi_ è disponibile nel percorso del sistema.  |
| Come eseguirla      | Aprire un prompt dei comandi (in Windows) o un terminale (in Linux), quindi eseguire _NVIDIA-SMI_. |


## <a name="horovodhttpsgithubcomuberhorovod"></a>[Horovod](https://github.com/uber/horovod)

|    |           |
| ------------- | ------------- |
| Versioni supportate | 0.16.1|
| Edizioni DSVM supportate      | Linux (Ubuntu)   |
| Come viene configurata o installata sulla macchina virtuale per data science?  | Horovod è installato in Python 3,5 |
| Come eseguirla      | Attivare l'ambiente corretto nel terminale, quindi eseguire python. |

## <a name="kerashttpskerasio"></a>[Keras](https://keras.io/)

|    |           |
| ------------- | ------------- |
| Versioni supportate | 2.2.4 |
| Edizioni DSVM supportate      | Windows e Linux   |
| Come viene configurata o installata sulla macchina virtuale per data science?  | Keras viene installato in Python 3,6 in Windows e in Python 3,5 in Linux |
| Come eseguirla      | Attivare l'ambiente corretto nel terminale, quindi eseguire python. |

## <a name="microsoft-cognitive-toolkit-cntkhttpsdocsmicrosoftcomcognitive-toolkit"></a>[Microsoft Cognitive Toolkit (CNTK)](https://docs.microsoft.com/cognitive-toolkit/)

|    |           |
| ------------- | ------------- |
| Versioni supportate | 2.5.1 |
| Edizioni DSVM supportate      | Windows e Linux   |
| Come viene configurata o installata sulla macchina virtuale per data science?  | CNTK viene installato in Python 3,6 in [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition) e in Python 3,5 in [Linux](./dsvm-languages.md#python-linux-edition)) |
| Come eseguirla      | Terminal Attivare l'ambiente corretto ed eseguire python. <br/>Jupyter Connettersi a [Jupyter](provision-vm.md) o [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine), quindi aprire la directory CNTK per gli esempi. |

## <a name="mxnethttpsmxnetapacheorg"></a>[MXNet](https://mxnet.apache.org/)
|    |           |
| ------------- | ------------- |
| Versioni supportate | 1.3.0 |
| Edizioni DSVM supportate      | Windows e Linux   |
| Come viene configurata o installata sulla macchina virtuale per data science?  | MXNet viene installato in `C:\dsvm\tools\mxnet` in Windows e `/dsvm/tools/mxnet` in Ubuntu. Le associazioni di Python vengono installate in Python 3,6 in [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition) e in Python 3,5 in [Linux](./dsvm-languages.md#python-linux-edition)) i binding R sono inclusi anche in Ubuntu DSVM. |
| Come eseguirla      | Terminal Attivare l'ambiente conda corretto, quindi eseguire `import mxnet`. <br/>Jupyter Connettersi a [Jupyter](provision-vm.md#access-the-dsvm) o [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine), quindi aprire la directory `mxnet` per gli esempi. |

## <a name="mxnet-model-serverhttpsgithubcomawslabsmxnet-model-serverquick-start"></a>[Server modello MXNet](https://github.com/awslabs/mxnet-model-server#quick-start)

|    |           |
| ------------- | ------------- |
| Versioni supportate | 1.0.1 |
| Edizioni DSVM supportate      | Windows e Linux   |
| Come viene configurata o installata sulla macchina virtuale per data science?  | MXNet Model server è installato in Python 3,6 in [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition) e in Python 3,5 in [Linux](./dsvm-languages.md#python-linux-edition)) |
| Come eseguirla      | Terminal Eseguire `sudo systemctl stop jupyterhub` per arrestare prima il servizio JupyterHub, perché entrambi sono in ascolto sulla stessa porta. Attivare quindi l'ambiente conda corretto ed eseguire `mxnet-model-server --start --models squeezenet=https://s3.amazonaws.com/model-server/model_archive_1.0/squeezenet_v1.1.mar` |

## <a name="nvidia-system-management-interface-nvidia-smihttpsdevelopernvidiacomnvidia-system-management-interface"></a>[NVIDIA System Management Interface (NVIDIA-SMI)](https://developer.nvidia.com/nvidia-system-management-interface)

|    |           |
| ------------- | ------------- |
| Versioni supportate |  |
| Edizioni DSVM supportate      | Windows e Linux   |
| A cosa serve? | Lo strumento NVIDIA per l'esecuzione di query sull'attività GPU |
| Come viene configurata o installata sulla macchina virtuale per data science?  | `nvidia-smi` si trova nel percorso di sistema. |
| Come eseguirla      | In una macchina virtuale **con GPU**aprire un prompt dei comandi (in Windows) o un terminale (in Linux), quindi eseguire `nvidia-smi`. |

## <a name="pytorchhttpspytorchorg"></a>[PyTorch](https://pytorch.org/)

|    |           |
| ------------- | ------------- |
| Versioni supportate | 1.2.0 |
| Edizioni DSVM supportate      | Linux |
| Come viene configurata o installata sulla macchina virtuale per data science?  | Installato in [Python 3,5](dsvm-languages.md#python-linux-edition). Sono inclusi i notebook di Jupyter di esempio e gli esempi si trovano in/dsvm/Samples/pytorch. |
| Come eseguirla      | Terminal Attivare l'ambiente corretto, quindi eseguire python.<br/>* [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine): Connettersi, quindi aprire la directory PyTorch per gli esempi.  |

## <a name="tensorflowhttpswwwtensorfloworg"></a>[TensorFlow](https://www.tensorflow.org/)

|    |           |
| ------------- | ------------- |
| Versioni supportate | 1,13 |
| Edizioni DSVM supportate      | Windows, Linux |
| Come viene configurata o installata sulla macchina virtuale per data science?  | Installato in Python 3,5 in [Linux](dsvm-languages.md#python-linux-edition) e Python 3,6 in [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition) |
| Come eseguirla      | Terminal Attivare l'ambiente corretto, quindi eseguire python. <br/> Jupyter Connettersi a [Jupyter](provision-vm.md) o [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine), quindi aprire la directory TensorFlow per gli esempi.   |

## <a name="tensorflow-servinghttpswwwtensorfloworgserving"></a>[Servizio TensorFlow](https://www.tensorflow.org/serving/)

|    |           |
| ------------- | ------------- |
| Versioni supportate | 1,12 |
| Edizioni DSVM supportate      | Linux |
| Come viene configurata o installata sulla macchina virtuale per data science?  | tensorflow_model_server è disponibile nel terminale. |
| Come eseguirla      |  Sono disponibili esempi [online](https://www.tensorflow.org/serving/).   |


## <a name="theanohttpsgithubcomtheanotheano"></a>[Theano](https://github.com/Theano/Theano)

|    |           |
| ------------- | ------------- |
| Versioni supportate | 1.0.3 |
| Edizioni DSVM supportate      | Linux |
| Come viene configurata o installata sulla macchina virtuale per data science?  |Theano viene installato in Python 2,7 (_radice_) e nell'ambiente Python 3,5 (_PY35_). |
| Come eseguirla      |  Terminal Attivare la versione di Python desiderata (root o PY35), eseguire python, quindi importare Theano.<br/>Jupyter Selezionare il kernel Python 2,7 o 3,5, quindi importare Theano.  <br/>Per aggirare un bug recente della libreria del kernel matematico (MKL), è necessario innanzitutto impostare il livello di threading MKL nel modo seguente:<br/><br/>`export MKL_THREADING_LAYER=GNU`  |