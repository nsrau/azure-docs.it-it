---
title: Procedure dettagliate & esempi
titleSuffix: Azure Data Science Virtual Machine
description: Con questi esempi e procedure dettagliate, viene illustrato come gestire le attività e gli scenari comuni con i Data Science Virtual Machine.
keywords: strumenti di analisi scientifica dei dati, macchina virtuale per l'analisi scientifica dei dati, strumenti per l'analisi scientifica dei dati, analisi scientifica dei dati per Linux
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: ad58adb85077a27bce65595738ffdbd92bace9bd
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71802445"
---
# <a name="samples-on-azure-data-science-virtual-machines"></a>Esempi sulle macchine virtuali di Data Science di Azure

Le macchine virtuali di Data Science di Azure (Dsvm) includono un set completo di codice di esempio. Questi esempi includono notebook e script di Jupyter in linguaggi come Python e R.
> [!NOTE]
> Per altre informazioni su come eseguire Jupyter notebook in data science macchine virtuali, vedere la sezione accedere a [Jupyter](#access-jupyter) .

## <a name="prerequisites"></a>Prerequisiti

Per eseguire questi esempi, è necessario avere effettuato il provisioning di un Data Science Virtual Machine. Vedere le guide introduttive per [Windows](./provision-vm.md) e [Ubuntu](./dsvm-ubuntu-intro.md).

## <a name="available-samples"></a>Esempi disponibili
| Categoria di esempi | Descrizione | Località |
| ------------- | ------------- | ------------- |
| Linguaggio di programmazione di R  | Gli esempi illustrano scenari come la connessione con gli archivi dati cloud basati su Azure e la modalità di confronto tra R e Microsoft Machine Learning Server Open Source. Spiegano inoltre come rendere operativo i modelli in Microsoft Machine Learning Server e SQL Server. <br/> [Linguaggio di programmazione di R](#r-language) | <br/>`~notebooks` <br/> <br/> `~samples/MicrosoftR` <br/> <br/> `~samples/RSqlDemo` <br/> <br/> `~samples/SQLRServices`<br/> <br/>|
| Linguaggio di programmazione di Python  | Gli esempi illustrano scenari come la connessione con gli archivi dati cloud basati su Azure e come usare Azure Machine Learning.  <br/> [Linguaggio di programmazione di Python](#python-language) | <br/>`~notebooks` <br/><br/>|
| Linguaggio di programmazione di Julia  | Fornisce una descrizione dettagliata del tracciato e dell'apprendimento avanzato in Julia. Viene inoltre illustrato come chiamare C e Python da Julia. <br/> [Linguaggio di programmazione di Julia](#julia-language) |<br/> Windows:<br/> `~notebooks/Julia_notebooks`<br/><br/> Linux:<br/> `~notebooks/julia`<br/><br/> |
| Azure Machine Learning  | Viene illustrato come creare modelli di apprendimento automatico e Deep Learning con Machine Learning. Distribuire i modelli ovunque. Usare le funzionalità automatizzate di Machine Learning e l'ottimizzazione intelligente degli iperparametri. Usare la gestione modelli e il training distribuito. <br/> [Machine Learning](#azure-machine-learning) | <br/>`~notebooks/AzureML`<br/> <br/>|
| Notebook PyTorch  | Esempi di apprendimento avanzato che usano reti neurali basate su PyTorch. I livelli dei notebook vanno dallo scenario per principianti agli scenari avanzati.  <br/> [Notebook PyTorch](#pytorch) | <br/>`~notebooks/Deep_learning_frameworks/pytorch`<br/> <br/>|
| TensorFlow  |  Una serie di esempi e tecniche di rete neurale implementate tramite TensorFlow Framework. <br/> [TensorFlow](#tensorflow) | <br/>`~notebooks/Deep_learning_frameworks/tensorflow`<br/><br/> |
| Microsoft Cognitive Toolkit <br/>   | Esempi di apprendimento approfondito pubblicati dal team di Cognitive Toolkit in Microsoft.  <br/> [Cognitive Toolkit](#cntk) | <br/> `~notebooks/DeepLearningTools/CNTK/Tutorials`<br/><br/> Linux:<br/> `~notebooks/CNTK`<br/> <br/>|
| Caffe2 | Esempi di apprendimento avanzato che usano reti neurali basate su Caffe2. Diversi notebook consentono di acquisire familiarità con Caffe2 e come utilizzarlo in modo efficace. Gli esempi includono la pre-elaborazione di immagini e la creazione di set di dati. Includono anche la regressione e come usare i modelli con training. <br/> [Caffe2](#caffe2) | <br/>`~notebooks/Deep_learning_frameworks/caffe2`<br/><br/> |
| H2O   | Esempi basati su Python che usano H2O per scenari di problemi reali. <br/> [H2O](#h2o) | <br/>`~notebooks/h2o`<br/><br/> |
| Linguaggio di programmazione di SparkML  | Esempi che usano le funzionalità di Apache Spark MLLib Toolkit tramite pySpark e MMLSpark: Microsoft Machine Learning per Apache Spark su Apache Spark 2. x.  <br/> [Linguaggio di programmazione di SparkML](#sparkml) | <br/>`~notebooks/SparkML/pySpark`<br/>`~notebooks/MMLSpark`<br/><br/>  |
| XGBoost | Esempi di Machine Learning standard in XGBoost per scenari come la classificazione e la regressione. <br/> [XGBoost](#xgboost) | <br/>Windows:<br/>`\dsvm\samples\xgboost\demo`<br/><br/> |

<br/>

## <a name="access-jupyter"></a>Accesso a Jupyter 

Per accedere a Jupyter, selezionare l'icona **Jupyter** nel menu desktop o applicazione. È anche possibile accedere a Jupyter in un'edizione Linux di un DSVM. Per accedere in remoto da un Web browser, passare a `https://<Full Domain Name or IP Address of the DSVM>:8000` in Ubuntu.

Per aggiungere eccezioni e rendere disponibile l'accesso a Jupyter tramite un browser, usare le linee guida seguenti:


![Abilitare l’eccezione di Jupyter](./media/ubuntu-jupyter-exception.png)


Accedere con la stessa password usata per accedere al Data Science Virtual Machine.
<br/>

**Pagina iniziale di Jupyter**
<br/>![Pagina iniziale di Jupyter](./media/jupyter-home.png)<br/>

## <a name="r-language"></a>Linguaggio di programmazione di R 
<br/>![Esempi di R](./media/r-language-samples.png)<br/>

## <a name="python-language"></a>Linguaggio di programmazione di Python
<br/>![Esempi per Python](./media/python-language-samples.png)<br/>

## <a name="julia-language"></a>Linguaggio di programmazione di Julia 
<br/>![Esempi di Julia](./media/julia-samples.png)<br/>

## <a name="azure-machine-learning"></a>Azure Machine Learning 
<br/>![Esempi di Azure Machine Learning](./media/azureml-samples.png)<br/>

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

