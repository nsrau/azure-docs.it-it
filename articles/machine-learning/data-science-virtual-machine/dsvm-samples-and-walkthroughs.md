---
title: Esempi e procedure dettagliate per la macchina virtuale di data science - Azure | Microsoft Docs
description: Esempi e procedure dettagliate per la macchina virtuale di data science.
keywords: strumenti di analisi scientifica dei dati, macchina virtuale per l'analisi scientifica dei dati, strumenti per l'analisi scientifica dei dati, analisi scientifica dei dati per Linux
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: gokuma
ms.openlocfilehash: a1f15b805d2f27152d9ba85608ce0dc1d1aac21e
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/27/2018
ms.locfileid: "47392569"
---
# <a name="samples-on-the-data-science-virtual-machines-dsvm"></a>Esempi nelle macchine virtuali di data science (DSVM)

Il DSVM include un set completo di codice di esempio sotto forma di Jupyter Notebook e di script in linguaggi come Python e R.    
> [!NOTE]
> Fare riferimento alla sezione [Accesso a Jupyter](#access-jupyter) per abilitare il Jupyter Notebook sulle DSVM.

## <a name="quick-reference-of-samples"></a>Riferimento rapido agli esempi
| Categoria di esempi | DESCRIZIONE | Località |
| ------------- | ------------- | ------------- |
| Linguaggio di **R**  | Esempi in **R** che descrivono scenari come la connessione con l'archivio dati di cloud di Azure, il confronto Apri origine R e Microsoft R e l'operatività dei modelli in Microsoft R Server o SQL Server. <br/> [Schermata](#r-language) | <br/>`~notebooks` <br/> <br/> `~samples/MicrosoftR` <br/> <br/> `~samples/RSqlDemo` <br/> <br/> `~samples/SQLRServices`<br/> <br/>|
| Linguaggio di **Python**  | Esempi in **Python** che descrivono scenari come la connessione con gli archivi dati di cloud di Azure e l'uso di **Azure Machine Learning**.  <br/> [Schermata](#python-language) | <br/>`~notebooks` <br/><br/>|
| Linguaggio di **Julia**  | Esempio in **Julia** che descrive nel dettaglio il tracciamento in Julia, l'apprendimento avanzato in Julia, la chiamata di C e Python da Julia ecc. <br/> [Schermata](#julia-language) |<br/> **Windows**:<br/> `~notebooks/Julia_notebooks`<br/><br/> **Linux**:<br/> `~notebooks/julia`<br/><br/> |
| AzureML **Azure Machine Learning**  | Costruire modelli di ML e modelli di apprendimento avanzato con il servizio **Azure Machine Learning** e modelli di distribuzione ovunque. Sfruttare le funzionalità, ad esempio ML automatizzata, sintonizzazione del parametro hyper intelligente, Gestione di modelli, formazione distribuita. <br/> [Schermata](#azureml) | <br/>`~notebooks/AzureML`<br/> <br/>|
| **PyTorch** Notebook  | Esempi di apprendimento avanzato che usano **PyTorch** basato su reti neurali. Sono disponibili molti notebook che comprendono scenari che vanno dai principianti agli scenari.  <br/> [Schermata](#pytorch) | <br/>`~notebooks/Deep_learning_frameworks/pytorch`<br/> <br/>|
| **TensorFlow**  | Più esempi diversi di rete neurale e tecniche implementate usando il framework **TensorFlow**. <br/> [Schermata](#tensorflow) | <br/>`~notebooks/Deep_learning_frameworks/tensorflow`<br/><br/> |
| **CNTK** <br/> (Microsoft Cognitive Toolkit)  | Degli esempi di apprendimento avanzato pubblicati dal team di Cognitive Toolkit in Microsoft.  <br/> [Schermata](#cntk) | <br/> `~notebooks/DeepLearningTools/CNTK/Tutorials`<br/><br/> **Linux**:<br/> `~notebooks/CNTK`<br/> <br/>|
| **caffe2** | Degli esempi di apprendimento avanzato che usano reti neurali basate su **caffe2**. Sono presenti numerosi notebook progettati per consentire agli utenti di familiarizzare con caffe2 e scoprire come usarlo in modo efficace, con inclusi degli esempi come la pre-elaborazione di immagini, la creazione di set di dati, la regressione e l'uso di modelli che hanno precedentemente eseguito il training. <br/> [Schermata](#caffe2) | <br/>`~notebooks/Deep_learning_frameworks/caffe2`<br/><br/> |
| **H2O**   | Esempi basati su Python che usano **H2O** per numerosi problemi di uno scenario reale. <br/> [Schermata](#h2o) | <br/>`~notebooks/h2o`<br/><br/> |
| Linguaggio di programmazione di **SparkML**  | Esempio che usa le funzionalità e le capacità di Spark **MLlib** toolkit attraverso **pySpark** e **MMLSpark - Microsoft Machine Learning per Apache Spark** su **Apache Spark 2.x**.  <br/> [Schermata](#sparkml) | <br/>`~notebooks/SparkML/pySpark`<br/>`~notebooks/MMLSpark`<br/><br/>  |
| **XGBoost** | Esempi di Machine Learning standard in **XGBoost** per scenari come la classificazione, la regressione e così via. <br/> [Schermata](#xgboost) | <br/>**Windows**:<br/>`\dsvm\samples\xgboost\demo`<br/><br/> |

<br/>

## <a name="access-jupyter"></a>Accesso a Jupyter 

È possibile accedere a Jupyter facendo clic sull'icona `Jupyter` sul desktop o nel menu dell'applicazione. È anche possibile accedere a Jupyter su edizioni di Linux di DSVM in modalità remota da un Web browser visitando **`https://<Full Domain Name or IP Address of the DSVM>:8000`** in Ubuntu.

Vedere la schermata per aggiungere l'eccezione e abilitare l'accesso di Jupyter tramite il browser.


![Abilitare l’eccezione di Jupyter](./media/ubuntu-jupyter-exception.png)


Accedere con la stessa password usata per l'accesso a DSVM.
<br/>

**Home di Jupyter**
<br/>![Home di Jupyter](./media/jupyter-home.png)<br/>

## <a name="r-language"></a>Linguaggio di R 
<br/>![Esempi di R](./media/r-language-samples.png)<br/>

## <a name="python-language"></a>Linguaggio di Python
<br/>![Esempi di Python](./media/python-language-samples.png)<br/>

## <a name="julia-language"></a>Linguaggio di Julia 
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

