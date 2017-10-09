---
title: Esempi e procedure dettagliate per la macchina virtuale di data science - Azure | Microsoft Docs
description: Esempi e procedure dettagliate per la macchina virtuale di data science.
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
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 89bb5d255db9ab266d04169a3101e2b694236029
ms.contentlocale: it-it
ms.lasthandoff: 09/25/2017

---


# <a name="samples-on-the-data-science-virtual-machines-dsvm"></a>Esempi nelle macchine virtuali di data science (DSVM)

I DSVMs includono degli esempi completamente lavorati sotto forma di Jupyter Notebook a alcuni che non sono basati su Jupyter. È possibile accedere a Jupyter facendo clic sull'icona `Jupyter` sul desktop o nel menu dell'applicazione.  
> [!NOTE]
> Fare riferimento alla sezione [Access Jupyter](#access-jupyter) (Accesso a Jupyter) per abilitare Jupyter Notebook sulle DSVM.

## <a name="quick-reference-of-samples"></a>Riferimento rapido agli esempi
| Categoria di esempi | Descrizione | Località |
| ------------- | ------------- | ------------- |
| Linguaggio di **R**  | Esempi in **R** che descrivono scenari come la connessione con l'archivio dati di cloud di Azure, il confronto Apri origine R e Microsoft R e l'operatività dei modelli in Microsoft R Server o SQL Server. <br/> [Schermata](#r-language) | <br/>`~notebooks` <br/> <br/> `~samples/MicrosoftR` <br/> <br/> `~samples/RSqlDemo` <br/> <br/> `~samples/SQLRServices`<br/> <br/>|
| Linguaggio di **Python**  | Esempi in **Python** che descrivono scenari come la connessione con gli archivi dati di cloud di Azure e l'uso di **Azure Machine Learning**.  <br/> [Schermata](#python-language) | <br/>`~notebooks` <br/><br/>|
| Linguaggio di **Julia**  | Esempio in **Julia** che descrive nel dettaglio il tracciamento in Julia, l'apprendimento avanzato in Julia, la chiamata di C e Python da Julia ecc. <br/> [Schermata](#julia-language) |<br/> **Windows**:<br/> `~notebooks/Julia_notebooks`<br/><br/>`~notebooks`<br/><br/> **Linux**:<br/> `~notebooks/julia`<br/><br/> |
| **CNTK** <br/> (Microsoft Cognitive Toolkit)  | Degli esempi di apprendimento avanzato pubblicati dal team di Cognitive Toolkit in Microsoft.  <br/> [Schermata](#cntk) | <br/>**Windows**:<br/> `~notebooks/CNTK/Tutorials`<br/><br/>`~/samples/CNTK-Samples-2-0/Examples`<br/><br/> **Linux**:<br/> `~notebooks/CNTK`<br/> <br/>|
| Notebook di **MXnet**  | Degli esempi di apprendimento avanzato che usano delle reti neurali basate su **MXnet**. Sono disponibili molti notebook che comprendono scenari che vanno dai principianti agli scenari.  <br/> [Schermata](#mxnet) | <br/>`~notebooks/mxnet`<br/> <br/>|
| AzureML **Azure Machine Learning**  | L'interazione con **Azure Machine Learning** Studio e la creazione di endpoint del servizio web dai modelli su cui è stato localmente eseguito il training, per flussi di lavoro con punteggio basato su cloud. <br/> [Schermata](#azureml) | <br/>`~notebooks/azureml`<br/> <br/>|
| **caffe2** | Degli esempi di apprendimento avanzato che usano reti neurali basate su **caffe2**. Sono presenti numerosi notebook progettati per consentire agli utenti di familiarizzare con caffe2 e scoprire come usarlo in modo efficace, con inclusi degli esempi come la pre-elaborazione di immagini, la creazione di set di dati, la regressione e l'uso di modelli che hanno precedentemente eseguito il training. <br/> [Schermata](#caffe2) | <br/>`~notebooks/caffe2`<br/><br/> |
| **H2O**   | Esempi basati su Python che usano **H2O** per numerosi problemi di uno scenario reale. <br/> [Schermata](#h2o) | <br/>`~notebooks/h2o`<br/><br/> |
| Linguaggio di programmazione di **SparkML**  | Esempio che usa le funzionalità e le capacità del toolkit **MLlib** di Spark tramite **pySpark 2.0** su **Apache Spark 2.0**.  <br/> [Schermata](#sparkml) | <br/>`~notebooks/SparkML/pySpark`<br/><br/> |
| Linguaggio di programmazione di **MMLSpark**  | Una serie di esempi che usano **MMLSpark - Microsoft Machine Learning per Apache Spark**, ovvero un framework che fornisce una serie di apprendimenti avanzati e di strumenti di data science per **Apache Spark**. <br/> [Schermata](#sparkml) | <br/>`~notebooks/MMLSpark`<br/><br/> |
| **TensorFlow**  | Più esempi diversi di rete neurale e tecniche implementate usando il framework **TensorFlow**. <br/> [Schermata](#tensorflow) | <br/>`~notebooks/tensorflow`<br/><br/> |
| **XGBoost** | Esempi di Machine Learning standard in **XGBoost** per scenari come la classificazione, la regressione e così via. <br/> [Schermata](#xgboost) | <br/>`~samples/xgboost/demo`<br/><br/> |

<br/>

## <a name="access-jupyter"></a>Accesso a Jupyter 

Visitare Jupyter Home passando a **`https://localhost:9999`** su Windows o a **`https://localhost:8000`** su Ubuntu.


### <a name="enabling-jupyter-access-from-browser"></a>Abilitazione dell'accesso a Jupyter dal Browser

**DSVM di Windows**

Eseguire **`Jupyter SetPassword`** dal collegamento sul desktop e seguire il prompt per impostare/reimpostare la password per Jupyter e avviare il processo di Jupyter. 
<br/>![Abilitare l'eccezione di Jupyter](./media/jupyter-setpassword.png)<br/>
Una volta che il processo Jupyter è stato avviato con successo nella VM, è possibile accedere a Jupyter Home visitando **`https://localhost:9999`** nel browser. Vedere la schermata per aggiungere l'eccezione e abilitare l'accesso di Jupyter tramite il browser
<br/>![Abilitare l'eccezione di Jupyter](./media/windows-jupyter-exception.png)<br/>
Accedere con la nuova password che è stata appena impostata.
<br/>
**DSVM di Linux**

È possibile accedere a Jupyter Home nella VM visitando **`https://localhost:8000`** nel browser. Vedere la schermata per aggiungere l'eccezione e abilitare l'accesso di Jupyter tramite il browser.
<br/>![Abilitare l'eccezione di Jupyter](./media/ubuntu-jupyter-exception.png)<br/>
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

## <a name="cntk"></a>CNTK 
<br/>![Esempi di CNTK](./media/cntk-samples2.png)<br/>
<br/>![Esempi di CNTK](./media/cntk-samples.png)<br/>

## <a name="mxnet"></a>MXNet
<br/>![Esempi di MXnet](./media/mxnet-samples.png)<br/>

## <a name="azureml"></a>AzureML 
<br/>![Esempi di AzurekML](./media/azureml-samples.png)<br/>

## <a name="caffe2"></a>caffe2 
<br/>![Esempi di caffe2](./media/caffe2-samples.png)<br/>

## <a name="h2o"></a>H2O 
<br/>![Esempi di H2O](./media/h2o-samples.png)<br/>

## <a name="sparkml"></a>SparkML 
<br/>![Esempi di SparkML](./media/sparkml-samples.png)<br/>

## <a name="tensorflow"></a>TensorFlow 
<br/>![Esempi di TensorFlow](./media/tensorflow-samples.png)<br/>

## <a name="xgboost"></a>XGBoost 
<br/>![Esempi di XGBoost](./media/xgboost-samples.png)<br/>


