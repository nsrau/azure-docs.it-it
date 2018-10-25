---
title: Problemi noti e risoluzione per il servizio di Azure Machine Learning
description: Ottenere un elenco dei problemi, delle soluzioni alternative e delle risoluzioni
services: machine-learning
author: j-martens
ms.author: jmartens
ms.reviewer: mldocs
ms.service: machine-learning
ms.component: core
ms.topic: article
ms.date: 10/01/2018
ms.openlocfilehash: 02cee5a3e088c919ec94aee6f46ef6f428b9bb48
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/03/2018
ms.locfileid: "48249418"
---
# <a name="known-issues-and-troubleshooting-azure-machine-learning-service"></a>Problemi noti e risoluzione per il servizio di Azure Machine Learning
 
Questo articolo consente di trovare e correggere errori o guasti riscontrati durante l'uso del servizio di Azure Machine Learning. 

## <a name="sdk-installation-issues"></a>Problemi di installazione dell'SDK

**Messaggio di errore: Impossibile installare "PyYAML"** 

PyYAML è un progetto installato da Distutils. Pertanto, non è possibile stabilire in modo accurato quali file appartengono ad esso in caso di disinstallazione parziale. Per continuare a installare l'SDK, ignorando l'errore, usare:
```Python 
pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML
```

## <a name="image-building-failure"></a>Errore di compilazione di immagini

Errore di compilazione di immagini durante la distribuzione del servizio Web. Una soluzione alternativa consiste nell'aggiungere "pynacl==1.2.1" come dipendenza pip al file Conda per la configurazione dell'immagine.  

## <a name="pipelines"></a>Pipeline
Si verifica un errore durante la chiamata ripetuta di PythonScriptStep in una riga senza modificare lo script o i parametri. Una soluzione alternativa consiste nel ricompilare l'oggetto PipelineData.

## <a name="fpgas"></a>FPGA
Non sarà possibile distribuire i modelli in FPGA fino a quando non viene richiesta e approvata la quota FPGA. Per richiedere l'accesso, compilare il modulo di richiesta della quota: https://aka.ms/aml-real-time-ai

## <a name="databricks"></a>Databricks

Problemi di Databricks e Azure Machine Learning.

1. Raccomandazione cluster Databricks:
   
   Creare cluster Azure Databricks v.4.x con Python 3. Si consiglia un cluster di concorrenza elevata.
 
1. Errore di installazione di AML SDK in Databricks quando sono installati altri pacchetti.

   Alcuni pacchetti, come `psutil upgrade libs`, possono causare conflitti. Per evitare errori di installazione, installare i pacchetti con versione lib di blocco. Questo problema è correlato a Databricks e non ad AML SDK. Esempio:
   ```python
   pstuil cryptography==1.5 pyopenssl==16.0.0 ipython=2.2.0
   ```

## <a name="diagnostic-logs"></a>Log di diagnostica
In alcuni casi può essere utile fornire le informazioni di diagnostica quando si richiede supporto. Qui si trovano i file di log:

## <a name="resource-quotas"></a>Quote di risorse

Informazioni sulle [quote di risorse](how-to-manage-quotas.md) che si potrebbero incontrare quando si lavora con Azure Machine Learning.

## <a name="get-more-support"></a>Richiedere supporto tecnico

È possibile inviare le richieste di supporto e ottenere assistenza dal supporto tecnico, i forum e altro ancora. [Altre informazioni](support-for-aml-services.md)
