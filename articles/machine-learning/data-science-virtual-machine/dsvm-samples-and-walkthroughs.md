---
title: Programmi di esempio & procedure dettagliate di MLSample programs & ML walkthroughs
titleSuffix: Azure Data Science Virtual Machine
description: Tramite questi esempi e procedure dettagliate viene illustrato come gestire attività e scenari comuni con la macchina virtuale di data science.
keywords: strumenti di analisi scientifica dei dati, macchina virtuale per l'analisi scientifica dei dati, strumenti per l'analisi scientifica dei dati, analisi scientifica dei dati per Linux
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: d064e940997d13e51be4fd38e9f678b778ce31f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74900055"
---
# <a name="samples-on-azure-data-science-virtual-machines"></a>Esempi in macchine virtuali di analisi scientifica dei dati di AzureSamples on Azure Data Science Virtual Machines

Le macchine virtuali di analisi scientifica dei dati di Azure includono un set completo di codice di esempio. Questi esempi includono taccuini Jupyter e script in linguaggi come Python e R.
> [!NOTE]
> Per altre informazioni su come eseguire blocchi appunti Jupyter nelle macchine virtuali di data science, vedere la sezione [Esegui presentazione dell'accesso.](#access-jupyter)

## <a name="prerequisites"></a>Prerequisiti

Per eseguire questi esempi, è necessario avere eseguito il provisioning di una macchina virtuale di data science. Vedere le guide introduttive per [Windows](./provision-vm.md) e [Ubuntu](./dsvm-ubuntu-intro.md).

## <a name="available-samples"></a>Esempi disponibili
| Categoria di esempi | Descrizione | Percorsi |
| ------------- | ------------- | ------------- |
| Linguaggio R  | Gli esempi illustrano scenari come la connessione con gli archivi dati cloud basati su Azure e come confrontare R open source e Microsoft Machine Learning Server.Samples illustrate scenarios such as how to connect with Azure-based cloud data stores and how to compare open-source R and Microsoft Machine Learning Server. Viene inoltre illustrato come utilizzare i modelli in Microsoft Machine Learning Server e SQL Server. <br/> [Lingua R](#r-language) | <br/>`~notebooks` <br/> <br/> `~samples/MicrosoftR` <br/> <br/> `~samples/RSqlDemo` <br/> <br/> `~samples/SQLRServices`<br/> <br/>|
| Linguaggio di programmazione di Python  | Gli esempi illustrano scenari come la connessione agli archivi dati cloud basati su Azure e l'utilizzo di Azure Machine Learning.Samples explain scenarios like how to connect with Azure-based cloud data stores and how to work with Azure Machine Learning.  <br/> [Linguaggio Python](#python-language) | <br/>`~notebooks` <br/><br/>|
| Linguaggio di programmazione di Julia  | Fornisce una descrizione dettagliata della trama e del deep learning in Julia. Spiega anche come chiamare C e Python da Julia. <br/> [Lingua di Julia](#julia-language) |<br/> Windows:<br/> `~notebooks/Julia_notebooks`<br/><br/> Linux:<br/> `~notebooks/julia`<br/><br/> |
| Azure Machine Learning  | Illustra come creare modelli di apprendimento automatico e deep learning con Machine Learning.Illustrates how to build machine-learning and deep-learning models with Machine Learning. Distribuire i modelli ovunque. Usare le funzionalità automatizzate di Machine Learning e l'ottimizzazione intelligente degli iperparametri. Usare la gestione modelli e il training distribuito. <br/> [Apprendimento automatico](#azure-machine-learning) | <br/>`~notebooks/AzureML`<br/> <br/>|
| Notebook PyTorch  | Esempi di deep learning che usano reti neurali basate su PyTorch.Deep-learning samples that use PyTorch-based neural networks. I livelli dei notebook vanno dallo scenario per principianti agli scenari avanzati.  <br/> [Quaderni PyTorch](#pytorch) | <br/>`~notebooks/Deep_learning_frameworks/pytorch`<br/> <br/>|
| TensorFlow  |  Una varietà di esempi di rete neurale e tecniche implementate utilizzando il framework TensorFlow. <br/> [TensorFlow](#tensorflow) | <br/>`~notebooks/Deep_learning_frameworks/tensorflow`<br/><br/> |
| Microsoft Cognitive Toolkit <br/>   | Esempi di deep learning pubblicati dal team di Cognitive Toolkit presso Microsoft.  <br/> [Cognitive Toolkit](#cntk) | <br/> `~notebooks/DeepLearningTools/CNTK/Tutorials`<br/><br/> Linux:<br/> `~notebooks/CNTK`<br/> <br/>|
| Caffe2 | Esempi di deep learning che usano reti neurali basate su Caffe2. Diversi blocchi appunti familiarizzano gli utenti con Caffe2 e come usarlo in modo efficace. Gli esempi includono la pre-elaborazione delle immagini e la creazione di set di dati. Includono anche la regressione e come utilizzare i modelli con training preliminare. <br/> [Caffè2](#caffe2) | <br/>`~notebooks/Deep_learning_frameworks/caffe2`<br/><br/> |
| H2O   | Esempi basati su Python che usano H2O per scenari di problemi reali. <br/> [H2O](#h2o) | <br/>`~notebooks/h2o`<br/><br/> |
| Linguaggio di programmazione di SparkML  | Esempi che usano funzionalità del toolkit Apache Spark MLLib tramite pySpark e MMLSpark: Microsoft Machine Learning per Apache Spark in Apache Spark 2.x.  <br/> [Lingua SparkML](#sparkml) | <br/>`~notebooks/SparkML/pySpark`<br/>`~notebooks/MMLSpark`<br/><br/>  |
| XGBoost | Esempi standard di apprendimento automatico in XGBoost per scenari come la classificazione e la regressione. <br/> [XGBoost](#xgboost) | <br/>Windows:<br/>`\dsvm\samples\xgboost\demo`<br/><br/> |

<br/>

## <a name="access-jupyter"></a>Accesso a Jupyter 

Per accedere a Jupyter, selezionare l'icona **Jupyter** nel menu del desktop o dell'applicazione. È inoltre possibile accedere a Jupyter su un'edizione Linux di una DSVM. Per accedere in remoto da un `https://<Full Domain Name or IP Address of the DSVM>:8000` browser web, vai a su Ubuntu.

Per aggiungere eccezioni e rendere disponibile l'accesso a Jupyter su un browser, utilizzare le indicazioni seguenti:


![Abilitare l’eccezione di Jupyter](./media/ubuntu-jupyter-exception.png)


Accedere con la stessa password utilizzata per accedere alla macchina virtuale di analisi scientifica dei dati.
<br/>

**Jupyter casa**
<br/>![Pagina iniziale di Jupyter](./media/jupyter-home.png)<br/>

## <a name="r-language"></a>Linguaggio R 
<br/>![Esempi di R](./media/r-language-samples.png)<br/>

## <a name="python-language"></a>Linguaggio di programmazione di Python
<br/>![Esempi per Python](./media/python-language-samples.png)<br/>

## <a name="julia-language"></a>Linguaggio di programmazione di Julia 
<br/>![Esempi di Julia](./media/julia-samples.png)<br/>

## <a name="azure-machine-learning"></a>Azure Machine Learning 
<br/>![Esempi di Azure Machine LearningAzure Machine Learning samples](./media/azureml-samples.png)<br/>

## <a name="pytorch"></a>PyTorch
<br/>![Esempi di PyTorch](./media/pytorch-samples.png)<br/>

## <a name="tensorflow"></a>TensorFlow 
<br/>![Esempi di TensorFlow](./media/tensorflow-samples.png)<br/>


## <a name="cntk"></a>CNTK 
<br/>![Esempi di CNTK](./media/cntk-samples.png)<br/>


## <a name="caffe2"></a>Caffe2 
<br/>![Esempi di caffe2](./media/caffe2-samples.png)<br/>

## <a name="h2o"></a>H2O 
<br/>![Esempi di H2O](./media/h2o-samples.png)<br/>

## <a name="sparkml"></a>SparkML 
<br/>![Esempi di SparkML](./media/sparkml-samples.png)<br/>

## <a name="xgboost"></a>XGBoost 
<br/>![Esempi di XGBoost](./media/xgboost-samples.png)<br/>

