---
title: Apprendimento avanzato e framework IA in Azure | Microsoft Docs
description: Informazioni su strumenti e framework di Deep Learning supportati in Data Science Virtual Machine.
keywords: strumenti di analisi scientifica dei dati, macchina virtuale per l'analisi scientifica dei dati, strumenti per l'analisi scientifica dei dati, analisi scientifica dei dati per Linux
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2017
ms.author: gokuma
ms.openlocfilehash: d266ee48a4248584ae22a9ba678d516fe28cd712
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2018
ms.locfileid: "53259716"
---
# <a name="deep-learning-and-ai-frameworks"></a>Apprendimento avanzato e framework AI
La [macchina virtuale per data science](https://aka.ms/dsvm) e la [macchina virtuale per l'apprendimento avanzato](https://aka.ms/dsvm/deeplearning) supportano diversi framework per consentire la creazione delle applicazioni di Intelligenza artificiale con analisi predittiva e funzionalità cognitive come comprensione delle immagini e del linguaggio.

Di seguito sono riportati i dettagli su tutti i framework di apprendimento avanzato disponibili nella macchina virtuale per data science.

## <a name="microsoft-cognitive-toolkit"></a>Microsoft Cognitive Toolkit

|    |           |
| ------------- | ------------- |
| Che cos'è?   | Framework per l'apprendimento avanzato      |
| Edizioni supportate della macchina virtuale per data science      | Windows, Linux     |
| Come viene configurata o installata sulla macchina virtuale per data science?  | Microsoft Cognitive Toolkit, ovvero CNTK, viene installato in Python 3.5 su [Linux e Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) e in Python 3.6 su [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition).   |
| Collegamenti agli esempi      | Sono inclusi i blocchi appunti Jupyter di esempio.     |
| Strumenti correlati nella macchina virtuale per data science      | Keras      |
| Come usarla o eseguirla?    | * In un terminale: attivare l'ambiente corretto, quindi eseguire Python. <br/> * In Jupyter: connettersi a [Jupyter](provision-vm.md#tools-installed-on-the-microsoft-data-science-virtual-machine) o a [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), quindi aprire la directory CNTK per gli esempi. |

## <a name="tensorflow"></a>TensorFlow

|    |           |
| ------------- | ------------- |
| Che cos'è?   | Framework per l'apprendimento avanzato      |
| Edizioni supportate della macchina virtuale per data science      | Windows, Linux     |
| Come viene configurata o installata sulla macchina virtuale per data science?  | TensorFlow viene installato in Python 3.5 su [Linux e Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) e in Python 3.6 su [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition).  |
| Collegamenti agli esempi      | Sono inclusi i blocchi appunti Jupyter di esempio.     |
| Strumenti correlati nella macchina virtuale per data science      | Keras      |
| Come usarla o eseguirla?    | * In un terminale: attivare l'ambiente corretto, quindi eseguire Python. <br/> * In Jupyter: connettersi a [Jupyter](provision-vm.md#tools-installed-on-the-microsoft-data-science-virtual-machine) oppure a [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), quindi aprire la directory di TensorFlow per gli esempi.  |

## <a name="horovod"></a>Horovod

|    |           |
| ------------- | ------------- |
| Che cos'è?   | Framework di apprendimento avanzato distribuito per TensorFlow      |
| Edizioni supportate della macchina virtuale per data science      | Ubuntu     |
| Come viene configurata o installata sulla macchina virtuale per data science?  | Horovod viene installato in Python 3.5 su [Ubuntu](dsvm-languages.md#python-linux-and-windows-server-2012-edition).  |
| Collegamenti agli esempi      | [https://github.com/uber/horovod/tree/master/examples](https://github.com/uber/horovod/tree/master/examples)     |
| Strumenti correlati nella macchina virtuale per data science      | TensorFlow      |
| Come usarla o eseguirla?    | In un terminale: attivare l'ambiente corretto, quindi eseguire Python. |

## <a name="keras"></a>Keras

|    |           |
| ------------- | ------------- |
| Che cos'è?   | API di alto livello di apprendimento avanzato      |
| Edizioni supportate della macchina virtuale per data science      | Windows, Linux     |
| Come viene configurata o installata sulla macchina virtuale per data science?  | TensorFlow viene installato in Python 3.5 su [Linux e Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) e in Python 3.6 su [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition). |
| Collegamenti agli esempi      | https://github.com/fchollet/keras/tree/master/examples      |
| Strumenti correlati nella macchina virtuale per data science      | Microsoft Cognitive Toolkit, TensorFlow, Theano      |
| Come usarla o eseguirla?    | * In un terminale: attivare l'ambiente corretto, quindi eseguire Python. <br/> * In Jupyter: scaricare gli esempi dal percorso di GitHub, connettersi a [Jupyter](provision-vm.md#tools-installed-on-the-microsoft-data-science-virtual-machine) oppure a [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), quindi aprire la directory per gli esempi. |

## <a name="caffe"></a>Caffe

|    |           |
| ------------- | ------------- |
| Che cos'è?   | Framework per l'apprendimento avanzato      |
| Edizioni supportate della macchina virtuale per data science      | Ubuntu     |
| Come viene configurata o installata sulla macchina virtuale per data science?  | Caffe è installato in `/opt/caffe`.    |
| Come passare a Python 2.7 | Eseguire `source activate root` |
| Collegamenti agli esempi      | Gli esempi sono inclusi in `/opt/caffe/examples`.      |
| Strumenti correlati nella macchina virtuale per data science      | Caffe2      |

### <a name="how-to-use--run-it"></a>Come usarla o eseguirla?

Usare X2Go per accedere alla macchina virtuale, quindi avviare un nuovo terminale e immettere

```
cd /opt/caffe/examples
source activate root
jupyter notebook
```

Si apre una nuova finestra del browser con i blocchi appunti di esempio.

I file binari vengono installati in /opt/caffe/build/installazioni/bin.

La versione installata di Caffe richiede Python 2.7 e non funziona con Python 3.5 attivato per impostazione predefinita. Eseguire `source activate root` per attivare l'ambiente Anaconda.

## <a name="caffe2"></a>Caffe2

|    |           |
| ------------- | ------------- |
| Che cos'è?   | Framework per l'apprendimento avanzato      |
| Edizioni supportate della macchina virtuale per data science      | Ubuntu     |
| Come viene configurata o installata sulla macchina virtuale per data science?  | Caffe2 viene installato nell'[ambiente Conda di Python 2.7, ovvero nella radice](dsvm-languages.md#python-linux-and-windows-server-2012-edition). L'origine è `/opt/caffe2`. |
| Collegamenti agli esempi      | In Jupyter sono inclusi i blocchi appunti di esempio. |
| Strumenti correlati nella macchina virtuale per data science      | Caffe      |
| Come usarla o eseguirla?    | * Nel terminale: attivare l'[ambiente di Python radice](dsvm-languages.md#python-linux-and-windows-server-2012-edition), avviare Python e importare caffe2. <br/> * In JupyterHub: [connettersi a JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), quindi passare alla directory Caffe2 per trovare i blocchi appunti di esempio. Alcuni blocchi appunti richiedono l'impostazione della radice di Caffe2 nel codice Python. Immettere /opt/caffe2. |
| Note sulla build | Caffe2 viene creato dalle origini in Linux e include CUDA, cuDNN e Intel MKL. Il commit corrente è 0d9c0d48c6f20143d6404b99cc568efd29d5a4be, che è stato scelto per la stabilità in tutti i GPU e negli esempi testati. |

## <a name="chainer"></a>Chainer

|    |           |
| ------------- | ------------- |
| Che cos'è?   | Framework per l'apprendimento avanzato      |
| Edizioni supportate della macchina virtuale per data science      | Windows, Linux     |
| Come viene configurata o installata sulla macchina virtuale per data science?  | Chainer viene installato in [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition). Vengono installati anche ChainerRL e ChainerCV.   |
| Collegamenti agli esempi      | In Jupyter sono inclusi i blocchi appunti di esempio. |
| Strumenti correlati nella macchina virtuale per data science      | Caffe      |
| Come usarla o eseguirla?  | * Nel terminale: attivare l'ambiente [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition), eseguire _python_, quindi importare Chainer. <br/> * In JupyterHub: [connettersi a JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), quindi passare alla directory di Chainer per trovare i notebook di esempio.


## <a name="deep-water"></a>Deep Water

|    |           |
| ------------- | ------------- |
| Che cos'è?   | Framework per l'apprendimento avanzato per H2O      |
| Edizioni supportate della macchina virtuale per data science      | Ubuntu     |
| Come viene configurata o installata sulla macchina virtuale per data science?  | Deep Water viene installato in [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition) ed è anche disponibile in `/dsvm/tools/deep_water`.   |
| Collegamenti agli esempi      | In Jupyter sono inclusi i blocchi appunti di esempio.      |
| Strumenti correlati nella macchina virtuale per data science      | H2O, Sparkling Water      |

### <a name="how-to-use--run-it"></a>Come usarla o eseguirla?

Deep Water richiede CUDA 8 con cuDNN 5.1. Non si trova nel percorso della libreria per impostazione predefinita, perché altri framework di apprendimento avanzato usano CUDA 9 e cuDNN 7. Per usare CUDA 8 + cuDNN 5.1 per Deep Water:

```
export LD_LIBRARY_PATH=/usr/local/cuda-8.0/lib64:${LD_LIBRARY_PATH}
export CUDA_ROOT=/usr/local/cuda-8.0
```

Per usare Deep Water:
* Nel terminale: attivare l'ambiente [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition), quindi eseguire _python_. <br/>
* In JupyterHub: [connettersi a JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), quindi passare alla directory deep_water per trovare i blocchi appunti di esempio.

## <a name="mxnet"></a>MXNet

|    |           |
| ------------- | ------------- |
| Che cos'è?   | Framework per l'apprendimento avanzato      |
| Edizioni supportate della macchina virtuale per data science      | Windows, Linux     |
| Come viene configurata o installata sulla macchina virtuale per data science?  | MXNet è installato in `C:\dsvm\tools\mxnet` su Windows e in `/dsvm/tools/mxnet` su Linux. Le associazioni di Python vengono installate in Python 3.5 su [Linux e Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) e in Python 3.6 su [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition). Anche le associazioni di R vengono installate su Ubuntu.   |
| Collegamenti agli esempi      | Sono inclusi i blocchi appunti Jupyter di esempio.    |
| Strumenti correlati nella macchina virtuale per data science      | Keras      |
| Come usarla o eseguirla?    | * In un terminale: attivare l'ambiente corretto, quindi eseguire Python. <br/> * In Jupyter: connettersi a [Jupyter](provision-vm.md#tools-installed-on-the-microsoft-data-science-virtual-machine) oppure a [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), quindi aprire la directory di MXNet per gli esempi.  |
 | Note sulla build | MXNet viene creata dalle origini di Linux. Questa build include CUDA, cuDNN, NCCL e MKL. |

## <a name="nvidia-digits"></a>NVIDIA DIGITS

|    |           |
| ------------- | ------------- |
| Che cos'è?   | Sistema di apprendimento avanzato di NVIDIA per eseguire un training rapido sui modelli di apprendimento avanzato      |
| Edizioni supportate della macchina virtuale per data science      | Ubuntu     |
| Come viene configurata o installata sulla macchina virtuale per data science?  | DIGITS viene installato in `/dsvm/tools/DIGITS` ed è disponibile come servizio denominato _digits_.   |

### <a name="how-to-use--run-it"></a>Come usarla o eseguirla?

Accedere alla macchina virtuale con X2Go. In un terminale avviare il servizio:

    sudo systemctl start digits

L'avvio del servizio richiede circa un minuto. Aprire un Web browser e passare all'indirizzo `http://localhost:5000`. Si noti che DIGITS non offre un accesso protetto ed è consigliabile che non venga esposto al di fuori della macchina virtuale.



## <a name="nvidia-smi"></a>nvidia-smi

|    |           |
| ------------- | ------------- |
| Che cos'è?   | Lo strumento NVIDIA per l'esecuzione di query sull'attività GPU      |
| Edizioni supportate della macchina virtuale per data science      | Windows, Linux     |
| Come viene configurata o installata sulla macchina virtuale per data science?  | _nvidia-smi_ è disponibile nel percorso del sistema.   |
| Come usarla o eseguirla? | Avviare un prompt dei comandi in Windows o un terminale in Linux, quindi eseguire _nvidia-smi_.



## <a name="theano"></a>Theano

|    |           |
| ------------- | ------------- |
| Che cos'è?   | Framework per l'apprendimento avanzato      |
| Edizioni supportate della macchina virtuale per data science      | Ubuntu     |
| Come viene configurata o installata sulla macchina virtuale per data science?  | Theano viene installato in Python 2.7, _radice_, e nell'ambiente Python 3.5, _py35_.   |
| Strumenti correlati nella macchina virtuale per data science      | Keras      |
| Come usarla o eseguirla?    | * In un terminale attivare la versione di Python desiderata, ovvero radice o py35, eseguire python, quindi importare Theano. <br/> * In Jupyter selezionare il kernel Python 2.7 o 3.5, quindi importare Theano.  <br/>Per risolvere un bug di MKL recente, è necessario innanzitutto impostare il livello di threading di MKL:<br/><br/>_export MKL_THREADING_LAYER=GNU_|



## <a name="torch"></a>Torch

|    |           |
| ------------- | ------------- |
| Che cos'è?   | Framework per l'apprendimento avanzato      |
| Edizioni supportate della macchina virtuale per data science      | Ubuntu     |
| Come viene configurata o installata sulla macchina virtuale per data science?  | Torch è installato in `/dsvm/tools/torch`. PyTorch viene installato in Python 2.7, _radice_, e nell'ambiente Python 3.5, _py35_.   |
| Collegamenti agli esempi      | Gli esempi di Torch si trovano in `/dsvm/samples/torch`. Gli esempi di PyTorch si trovano in `/dsvm/samples/pytorch`.      |


## <a name="pytorch"></a>PyTorch

|    |           |
| ------------- | ------------- |
| Che cos'è?   | Framework per l'apprendimento avanzato      |
| Edizioni supportate della macchina virtuale per data science      | Linux     |
| Come viene configurata o installata sulla macchina virtuale per data science?  | PyTorch viene installato in [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition).  |
| Collegamenti agli esempi      | Sono inclusi notebook di Jupyter di esempio e altri esempi sono disponibili in /dsvm/samples/pytorch.      |
| Strumenti correlati nella macchina virtuale per data science      | Torch      |
| Come usarla o eseguirla? |
* In un terminale: attivare l'ambiente corretto, quindi eseguire Python. <br/> * In Jupyter: connettersi a [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), quindi aprire la directory di PyTorch per gli esempi.  |

## <a name="mxnet-model-server"></a>MXNet Model Server

|    |           |
| ------------- | ------------- |
| Che cos'è?   | Un server per la creazione di endpoint HTTP per i modelli MXNet e ONNX      |
| Edizioni supportate della macchina virtuale per data science      | Linux     |
| Come viene configurata o installata sulla macchina virtuale per data science?  | _mxnet-model-server_ è disponibile nel terminale.   |
| Collegamenti agli esempi      | Cercare gli esempi aggiornati nella [pagina di MXNet Model Server](https://github.com/awslabs/mxnet-model-server).    |
| Strumenti correlati nella macchina virtuale per data science      | MXNet      |

## <a name="tensorflow-serving"></a>TensorFlow Serving

|    |           |
| ------------- | ------------- |
| Che cos'è?   | Un server per l'esecuzione di inferenza in un modello TensorFlow      |
| Edizioni supportate della macchina virtuale per data science      | Linux     |
| Come viene configurata o installata sulla macchina virtuale per data science?  | _tensorflow_model_server_ è disponibile nel terminale.   |
| Collegamenti agli esempi      | Sono disponibili esempi [online](https://www.tensorflow.org/serving/).      |
| Strumenti correlati nella macchina virtuale per data science      | TensorFlow      |

## <a name="tensorrt"></a>TensorRT

|    |           |
| ------------- | ------------- |
| Che cos'è?   | Server di inferenza per l'apprendimento avanzato da NVIDIA. |
| Edizioni supportate della macchina virtuale per data science      | Ubuntu     |
| Come viene configurata o installata sulla macchina virtuale per data science?  | TensorRT viene installato come un pacchetto _apt_.   |
| Collegamenti agli esempi      | Sono disponibili esempi [online](https://docs.nvidia.com/deeplearning/sdk/tensorrt-developer-guide/index.html#samples).      |
| Strumenti correlati nella macchina virtuale per data science      | TensorFlow Serving, MXNet Model Server  |



