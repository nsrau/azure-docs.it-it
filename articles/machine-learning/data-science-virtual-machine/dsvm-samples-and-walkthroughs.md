---
title: Esempi e procedure dettagliate per Data Science Virtual Machine - Azure | Microsoft Docs
description: Altre informazioni su esempi e procedure dettagliate che illustrano come eseguire attività e scenari comuni con la Data Science Virtual Machine.
keywords: strumenti di analisi scientifica dei dati, macchina virtuale per l'analisi scientifica dei dati, strumenti per l'analisi scientifica dei dati, analisi scientifica dei dati per Linux
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: gokuma
ms.openlocfilehash: e61f0f4ba30b29fea1b2fd5f2a2ab253d3a6710c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60502867"
---
# <a name="samples-on-data-science-virtual-machines"></a>Esempi nelle Data Science Virtual Machine

Data Science Virtual Machine di Azure include un set completo di codice di esempio. Il codice di esempio è in formato di Jupyter Notebook e di script in linguaggi come Python e R. 
> [!NOTE]
> Per altre informazioni su come eseguire i Jupyter Notebook in macchine virtuali di data science, vedere la sezione [Accesso a Jupyter](#access-jupyter).

## <a name="quick-reference-of-samples"></a>Avvio rapido per gli esempi
| Categoria di esempi | Descrizione | Località |
| ------------- | ------------- | ------------- |
| Linguaggio di programmazione di R  | Gli esempi in R illustrano gli scenari, ad esempio la modalità di connessione con gli archivi dati di cloud di Azure. Viene inoltre illustrato come confrontare R open source e Microsoft R. Viene anche spiegato come rendere operativi i modelli in Microsoft R Server o SQL Server. <br/> [Linguaggio di programmazione di R](#r-language) | <br/>`~notebooks` <br/> <br/> `~samples/MicrosoftR` <br/> <br/> `~samples/RSqlDemo` <br/> <br/> `~samples/SQLRServices`<br/> <br/>|
| Linguaggio di programmazione di Python  | Gli esempi in Python descrivono scenari quali come eseguire la connessione con gli archivi dati di cloud di Azure e l'uso di Azure Machine Learning.  <br/> [Linguaggio di programmazione di Python](#python-language) | <br/>`~notebooks` <br/><br/>|
| Linguaggio di programmazione di Julia  | L'esempio di Julia che illustra in dettaglio il tracciamento e l'apprendimento avanzato in Julia. Viene inoltre spiegato come richiamare C e Python da Julia. <br/> [Linguaggio di programmazione di Julia](#julia-language) |<br/> Windows:<br/> `~notebooks/Julia_notebooks`<br/><br/> Linux:<br/> `~notebooks/julia`<br/><br/> |
| Azure Machine Learning  | Creare modelli di Machine Learning e di apprendimento avanzato con Machine Learning. Distribuire i modelli ovunque. Usare le funzionalità automatizzate di Machine Learning e l'ottimizzazione intelligente degli iperparametri. Usare la gestione modelli e il training distribuito. <br/> [Machine Learning](#azureml) | <br/>`~notebooks/AzureML`<br/> <br/>|
| Notebook PyTorch  | Esempi di apprendimento avanzato che usano reti neurali basate su PyTorch. I livelli dei notebook vanno dallo scenario per principianti agli scenari avanzati.  <br/> [Notebook PyTorch](#pytorch) | <br/>`~notebooks/Deep_learning_frameworks/pytorch`<br/> <br/>|
| TensorFlow  |  Esempi diversi di rete neurale e tecniche implementate usando il framework TensorFlow. <br/> [TensorFlow](#tensorflow) | <br/>`~notebooks/Deep_learning_frameworks/tensorflow`<br/><br/> |
| Microsoft Cognitive Toolkit <br/>   | Degli esempi di apprendimento avanzato pubblicati dal team di Cognitive Toolkit in Microsoft.  <br/> [Cognitive Toolkit](#cntk) | <br/> `~notebooks/DeepLearningTools/CNTK/Tutorials`<br/><br/> Linux:<br/> `~notebooks/CNTK`<br/> <br/>|
| caffe2 | Esempi di apprendimento avanzato che usano reti neurali basate su caffe2. Vari notebook consentono agli utenti di acquisire familiarità con caffe2 e di usarlo in modo efficace. Gli esempi includono la creazione di set di dati e la pre-elaborazione di immagini. Includono anche la regressione e come usare i modelli con training preliminare. <br/> [caffe2](#caffe2) | <br/>`~notebooks/Deep_learning_frameworks/caffe2`<br/><br/> |
| H2O   | Esempi basati su Python che usano H2O per numerosi problemi di uno scenario reale. <br/> [H2O](#h2o) | <br/>`~notebooks/h2o`<br/><br/> |
| Linguaggio di programmazione di SparkML  | Esempio che usa le funzionalità del toolkit MLLib di Spark attraverso pySpark e MMLSpark - Microsoft Machine Learning per Apache Spark su Apache Spark 2.x.  <br/> [Linguaggio di programmazione di SparkML](#sparkml) | <br/>`~notebooks/SparkML/pySpark`<br/>`~notebooks/MMLSpark`<br/><br/>  |
| XGBoost | Esempi di Machine Learning standard in XGBoost per scenari come la classificazione e la regressione. <br/> [XGBoost](#xgboost) | <br/>Windows:<br/>`\dsvm\samples\xgboost\demo`<br/><br/> |

<br/>

## <a name="access-jupyter"></a>Accesso a Jupyter 

Per accedere a Jupyter fare clic sull'icona `Jupyter` sul desktop o nel menu dell'applicazione. È anche possibile accedere a Jupyter su edizioni Linux di Data Science Virtual Machine. Per accedere in remoto da un Web browser, visitare il sito `https://<Full Domain Name or IP Address of the DSVM>:8000` in Ubuntu.

Per aggiungere le eccezioni e rendere l'accesso a Jupyter disponibile da un browser, vedere lo screenshot seguente.


![Abilitare l’eccezione di Jupyter](./media/ubuntu-jupyter-exception.png)


Accedere con la stessa password usata per l'accesso a Data Science Virtual Machine.
<br/>

**Pagina iniziale di Jupyter**
<br/>![Pagina iniziale di Jupyter](./media/jupyter-home.png)<br/>

## <a name="r-language"></a>Linguaggio di programmazione di R 
<br/>![Esempi di R](./media/r-language-samples.png)<br/>

## <a name="python-language"></a>Linguaggio di programmazione di Python
<br/>![Esempi per Python](./media/python-language-samples.png)<br/>

## <a name="julia-language"></a>Linguaggio di programmazione di Julia 
<br/>![Esempi di Julia](./media/julia-samples.png)<br/>

## <a name="azureml"></a>AzureML 
<br/>![Esempi di AzurekML](./media/azureml-samples.png)<br/>

## <a name="pytorch"></a>PyTorch
<br/>![Esempi di PyTorch](./media/pytorch-samples.png)<br/>

## <a name="tensorflow"></a>TensorFlow 
<br/>![Esempi di TensorFlow](./media/tensorflow-samples.png)<br/>


## <a name="cntk"></a>CNTK 
<br/>![Esempi di CNTK](./media/cntk-samples.png)<br/>


## <a name="caffe2"></a>caffe2 
<br/>![Esempi di caffe2](./media/caffe2-samples.png)<br/>

## <a name="h2o"></a>H2O 
<br/>![Esempi di H2O](./media/h2o-samples.png)<br/>

## <a name="sparkml"></a>SparkML 
<br/>![Esempi di SparkML](./media/sparkml-samples.png)<br/>

## <a name="xgboost"></a>XGBoost 
<br/>![Esempi di XGBoost](./media/xgboost-samples.png)<br/>

