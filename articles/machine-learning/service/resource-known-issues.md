---
title: Problemi noti e risoluzione dei problemi
titleSuffix: Azure Machine Learning service
description: Ottenere un elenco dei problemi noti, delle soluzioni alternative e della risoluzione dei problemi per il servizio Azure Machine Learning.
services: machine-learning
author: j-martens
ms.author: jmartens
ms.reviewer: mldocs
ms.service: machine-learning
ms.component: core
ms.topic: article
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: e143c0c8ef09af49aed656d479bcad4dd35e2211
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/16/2019
ms.locfileid: "54351799"
---
# <a name="known-issues-and-troubleshooting-azure-machine-learning-service"></a>Problemi noti e risoluzione per il servizio di Azure Machine Learning
 
Questo articolo consente di trovare e correggere errori o guasti riscontrati durante l'uso del servizio di Azure Machine Learning. 

## <a name="sdk-installation-issues"></a>Problemi di installazione dell'SDK

**Messaggio di errore: Impossibile installare "PyYAML"** 

Azure Machine Learning SDK per Python: PyYAML è un progetto installato da Distutils. Pertanto, non è possibile stabilire in modo accurato quali file appartengono ad esso in caso di disinstallazione parziale. Per continuare a installare l'SDK, ignorando l'errore, usare:
```Python 
pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML
```

## <a name="trouble-creating-azure-machine-learning-compute"></a>Problemi durante la creazione dell'ambiente di calcolo di Azure Machine Learning
Esiste una rara possibilità per cui alcuni utenti che hanno creato l'area di lavoro di Azure Machine Learning dal portale di Azure prima della versione GA potrebbero non essere in grado di creare l'ambiente di calcolo di Azure Machine Learning nell'area di lavoro. È possibile generare una richiesta di supporto per il servizio o creare una nuova area di lavoro tramite il portale o il SDK per annullare il blocco immediatamente. 

## <a name="image-building-failure"></a>Errore di compilazione di immagini

Errore di compilazione di immagini durante la distribuzione del servizio Web. Una soluzione alternativa consiste nell'aggiungere "pynacl==1.2.1" come dipendenza pip al file Conda per la configurazione dell'immagine.  

## <a name="fpgas"></a>FPGA
Non sarà possibile distribuire i modelli in FPGA fino a quando non viene richiesta e approvata la quota FPGA. Per richiedere l'accesso, compilare il modulo di richiesta della quota: https://aka.ms/aml-real-time-ai

## <a name="databricks"></a>Databricks

Problemi di Databricks e Azure Machine Learning.
 
1. Errore di installazione di AML SDK in Databricks quando sono installati altri pacchetti.

   Alcuni pacchetti, come `psutil`, possono causare conflitti. Per evitare errori di installazione, installare i pacchetti con versione lib di blocco. Questo problema è correlato a Databricks e nemmeno al SDK di Azure Machine Learning: è possibile incontrarlo anche con altre librerie. Esempio:
   ```python
   pstuil cryptography==1.5 pyopenssl==16.0.0 ipython==2.2.0
   ```
   In alternativa, è possibile usare gli script di inizializzazione se si riscontrano problemi di installazione con le librerie Python. Questo approccio non è supportato ufficialmente. È possibile fare riferimento a [questo documento](https://docs.azuredatabricks.net/user-guide/clusters/init-scripts.html#cluster-scoped-init-scripts).

2. Quando si usa Machine Learning automatizzata in Databricks e si desidera annullare un'esecuzione per avviare un nuova esecuzione dell'esperimento, riavviare il cluster Azure Databricks.

3. Quando si dispone di inferiore a 10 iterazioni nelle impostazioni di machine learning automatizzata, impostare show_output come False durante l'invio dell'esecuzione.


## <a name="azure-portal"></a>Portale di Azure
Se si passa direttamente a visualizzare l'area di lavoro a un collegamento di condivisione da SDK o dal portale, non sarà possibile visualizzare la pagina Panoramica normale con le informazioni sulla sottoscrizione nell'estensione. Inoltre non sarà possibile passare in un'altra area di lavoro. Se si desidera visualizzare un'altra area di lavoro, la soluzione alternativa consiste nel passare direttamente al [portale di Azure](https://portal.azure.com) e cercare il nome dell'area di lavoro.

## <a name="diagnostic-logs"></a>Log di diagnostica
In alcuni casi può essere utile fornire le informazioni di diagnostica quando si richiede supporto. Qui si trovano i file di log:

## <a name="resource-quotas"></a>Quote di risorse

Informazioni sulle [quote di risorse](how-to-manage-quotas.md) che si potrebbero incontrare quando si lavora con Azure Machine Learning.

## <a name="get-more-support"></a>Richiedere supporto tecnico

È possibile inviare le richieste di supporto e ottenere assistenza dal supporto tecnico, i forum e altro ancora. [Altre informazioni](support-for-aml-services.md)
