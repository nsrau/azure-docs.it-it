---
title: Apprendimento avanzato e framework IA in Azure | Microsoft Docs
description: Apprendimento avanzato e framework AI
keywords: strumenti di analisi scientifica dei dati, macchina virtuale per l'analisi scientifica dei dati, strumenti per l'analisi scientifica dei dati, analisi scientifica dei dati per Linux
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/11/2017
ms.author: gokuma;bradsev
ms.openlocfilehash: 46662c15374f781a6527bb1435d883ae060c63f4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="deep-learning-and-ai-frameworks"></a>Apprendimento avanzato e framework AI
La [macchina virtuale per data science](http://aka.ms/dsvm) e la [macchina virtuale per l'apprendimento avanzato](http://aka.ms/dsvm/deeplearning) supportano diversi framework per consentire la creazione delle applicazioni di Intelligenza artificiale con analisi predittiva e funzionalità cognitive come comprensione delle immagini e del linguaggio. 

Di seguito sono riportati i dettagli su tutti i framework di apprendimento avanzato disponibili nella macchina virtuale per data science.

## <a name="microsoft-cognitive-toolkit"></a>Microsoft Cognitive Toolkit

|    |           |
| ------------- | ------------- |
| Che cos'è?   | Framework per l'apprendimento avanzato      |
| Edizioni supportate della macchina virtuale per data science      | Windows, Linux     |
| Come viene configurato o installato sulla macchina virtuale per data science?  | Microsoft Cognitive Toolkit viene installato in Python 2.7, nell'ambiente _radice_, in Python 3.5, nell'ambiente _py35_.   |
| Collegamenti agli esempi      | Sono inclusi i blocchi appunti Jupyter di esempio.     |
| Strumenti correlati nella macchina virtuale per data science      | Keras      |
| Come usarlo o eseguirlo?    | Aprire Jupyter, quindi cercare la cartella CNTK  |

## <a name="tensorflow"></a>TensorFlow

|    |           |
| ------------- | ------------- |
| Che cos'è?   | Framework per l'apprendimento avanzato      |
| Edizioni supportate della macchina virtuale per data science      | Windows, Linux     |
| Come viene configurato o installato sulla macchina virtuale per data science?  | In Linux TensorFlow viene installato in Python 2.7, _radice_ e nell'ambiente Python 3.5 (_py35_). In Windows Tensorflow viene installato nell'ambiente Python 3.5 (_py35_).  |
| Collegamenti agli esempi      | Sono inclusi i blocchi appunti Jupyter di esempio.     |
| Strumenti correlati nella macchina virtuale per data science      | Keras      |
| Come usarlo o eseguirlo?    | Aprire Jupyter, quindi cercare la cartella TensorFlow.  |

## <a name="keras"></a>Keras

|    |           |
| ------------- | ------------- |
| Che cos'è?   | Framework per l'apprendimento avanzato      |
| Edizioni supportate della macchina virtuale per data science      | Windows, Linux     |
| Come viene configurato o installato sulla macchina virtuale per data science?  | Keras viene installato in Python 2.7, _radice_, e nell'ambiente Python 3.5, _py35_.   |
| Collegamenti agli esempi      | https://github.com/fchollet/keras/tree/master/examples      |
| Strumenti correlati nella macchina virtuale per data science      | Microsoft Cognitive Toolkit, TensorLlow, Theano      |
| Come usarlo o eseguirlo?    | Scaricare gli esempi dal percorso di Github, copiarli in una directory in ~/notebooks e aprirlo in Jupyter   |




## <a name="caffe"></a>Caffe

|    |           |
| ------------- | ------------- |
| Che cos'è?   | Framework per l'apprendimento avanzato      |
| Edizioni supportate della macchina virtuale per data science      | Linux     |
| Come viene configurato o installato sulla macchina virtuale per data science?  | Caffe è installato in `/opt/caffe`.    |
| Collegamenti agli esempi      | Gli esempi sono inclusi in `/opt/caffe/examples`.      |
| Strumenti correlati nella macchina virtuale per data science      | Caffe2      |
### <a name="how-to-use--run-it"></a>Come usarlo o eseguirlo?  

Usare X2Go per accedere alla macchina virtuale, quindi avviare un nuovo terminale e immettere

```
cd /opt/caffe/examples
jupyter notebook
```

Si apre una nuova finestra del browser con i blocchi appunti di esempio.

## <a name="caffe2"></a>Caffe2

|    |           |
| ------------- | ------------- |
| Che cos'è?   | Framework per l'apprendimento avanzato      |
| Edizioni supportate della macchina virtuale per data science      | Linux     |
| Come viene configurato o installato sulla macchina virtuale per data science?  | Caffe2 è installato in `/opt/caffe2`. È anche disponibile per l'ambiente conda Python 2.7, _radice_.     |
| Collegamenti agli esempi      | Sono inclusi i blocchi appunti Jupyter di esempio     |
| Strumenti correlati nella macchina virtuale per data science      | Caffe      |
| Come usarlo o eseguirlo?    | Aprire Jupyter, quindi passare alla directory Caffe2 per trovare i blocchi appunti di esempio. Alcuni blocchi appunti richiedono l'impostazione della radice di Caffe2 nel codice Python. Immettere /opt/caffe2.   |


## <a name="chainer"></a>Chainer

|    |           |
| ------------- | ------------- |
| Che cos'è?   | Framework per l'apprendimento avanzato      |
| Edizioni supportate della macchina virtuale per data science      | Windows, Linux     |
| Come viene configurato o installato sulla macchina virtuale per data science?  | Chainer viene installato in Python 2.7, _radice_, e nell'ambiente Python 3.5, _py35_. Vengono installati anche ChainerRL e ChainerCV.   |
| Collegamenti agli esempi      | Sono inclusi i blocchi appunti Jupyter di esempio.      |
| Strumenti correlati nella macchina virtuale per data science      | Caffe      |

### <a name="how-to-use--run-it"></a>Come usarlo o eseguirlo?  

In un terminale attivare la versione di Python desiderata, ovvero _radice_ o _py35_, eseguire _python_, quindi importare Chainer. In Jupyter selezionare il kernel Python 2.7 o 3.5, quindi importare Chainer.


## <a name="deep-water"></a>Deep Water

|    |           |
| ------------- | ------------- |
| Che cos'è?   | Framework per l'apprendimento avanzato per H2O      |
| Edizioni supportate della macchina virtuale per data science      | Linux     |
| Come viene configurato o installato sulla macchina virtuale per data science?  | Deep Water viene installato in `/dsvm/tools/deep_water`.   |
| Collegamenti agli esempi      | Gli esempi sono disponibili nel server di Deep Water.      |
| Strumenti correlati nella macchina virtuale per data science      | H2O, Sparkling Water      |

### <a name="how-to-use--run-it"></a>Come usarlo o eseguirlo?  

Connettersi alla macchina virtuale con X2Go. In un terminale, avviare il server di Deep Water:

    java -jar /dsvm/tools/deep_water/h2o.jar

Quindi aprire un browser e connettersi a `http://localhost:54321`.



## <a name="mxnet"></a>MXNet

|    |           |
| ------------- | ------------- |
| Che cos'è?   | Framework per l'apprendimento avanzato      |
| Edizioni supportate della macchina virtuale per data science      | Windows, Linux     |
| Come viene configurato o installato sulla macchina virtuale per data science?  | MXNet è installato in `C:\dsvm\tools\mxnet` su Windows e in `/dsvm/tools/mxnet` su Linux. Le associazioni di Python vengono installate in Python 2.7, _radice_, e nell'ambiente Python 3.5, _py35_. Vengono installate anche le associazioni R.   |
| Collegamenti agli esempi      | Sono inclusi i blocchi appunti Jupyter di esempio.    |
| Strumenti correlati nella macchina virtuale per data science      | Keras      |
| Come usarlo o eseguirlo?    | Aprire Jupyter, quindi cercare la cartella mxnet  |

## <a name="nvidia-digits"></a>NVIDIA DIGITS

|    |           |
| ------------- | ------------- |
| Che cos'è?   | Sistema di apprendimento avanzato di NVIDIA per eseguire un training rapido sui modelli di apprendimento avanzato      |
| Edizioni supportate della macchina virtuale per data science      | Linux     |
| Come viene configurato o installato sulla macchina virtuale per data science?  | DIGITS viene installato in `/dsvm/tools/DIGITS` ed è disponibile come servizio denominato _digits_.   |
### <a name="how-to-use--run-it"></a>Come usarlo o eseguirlo?  

Accedere alla macchina virtuale con X2Go. In un terminale avviare il servizio:

    sudo systemctl start digits

L'avvio del servizio richiede circa un minuto. Aprire un Web browser e passare all'indirizzo `http://localhost:5000`.



## <a name="nvdia-smi"></a>nvdia-smi

|    |           |
| ------------- | ------------- |
| Che cos'è?   | Lo strumento NVIDIA per l'esecuzione di query sull'attività GPU      |
| Edizioni supportate della macchina virtuale per data science      | Windows, Linux     |
| Come viene configurato o installato sulla macchina virtuale per data science?  | _nvidia-smi_ è disponibile nel percorso del sistema.   |
| Come usarlo o eseguirlo? | Avviare un prompt dei comandi in Windows o un terminale in Linux, quindi eseguire _nvidia-smi_.



## <a name="theano"></a>Theano

|    |           |
| ------------- | ------------- |
| Che cos'è?   | Framework per l'apprendimento avanzato      |
| Edizioni supportate della macchina virtuale per data science      | Linux     |
| Come viene configurato o installato sulla macchina virtuale per data science?  | Theano viene installato in Python 2.7, _radice_, e nell'ambiente Python 3.5, _py35_.   |
| Strumenti correlati nella macchina virtuale per data science      | Keras      |
| Come usarlo o eseguirlo?    | In un terminale attivare la versione di Python desiderata, ovvero radice o py35, eseguire python, quindi importare Theano. In Jupyter selezionare il kernel Python 2.7 o 3.5, quindi importare Theano.  |



## <a name="torch"></a>Torch

|    |           |
| ------------- | ------------- |
| Che cos'è?   | Framework per l'apprendimento avanzato      |
| Edizioni supportate della macchina virtuale per data science      | Linux     |
| Come viene configurato o installato sulla macchina virtuale per data science?  | Torch è installato in `/dsvm/tools/torch`. PyTorch viene installato in Python 2.7, _radice_, e nell'ambiente Python 3.5, _py35_.   |
| Collegamenti agli esempi      | Gli esempi di Torch si trovano in `/dsvm/samples/torch`. Gli esempi di PyTorch si trovano in `/dsvm/samples/pytorch`.      |

