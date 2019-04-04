---
title: Problemi noti e risoluzione dei problemi
titleSuffix: Azure Machine Learning service
description: Ottenere un elenco dei problemi noti, delle soluzioni alternative e della risoluzione dei problemi per il servizio Azure Machine Learning.
services: machine-learning
author: j-martens
ms.author: jmartens
ms.reviewer: mldocs
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 03/29/2019
ms.custom: seodec18
ms.openlocfilehash: db0eccb542cb4253e6e891fa9fa51e60fb7951a1
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2019
ms.locfileid: "58892739"
---
# <a name="known-issues-and-troubleshooting-azure-machine-learning-service"></a>Problemi noti e risoluzione per il servizio di Azure Machine Learning

Questo articolo consente di trovare e correggere errori o guasti riscontrati durante l'uso del servizio di Azure Machine Learning.

## <a name="sdk-installation-issues"></a>Problemi di installazione dell'SDK

**Messaggio di errore: Non è possibile disinstallare 'PyYAML'**

Azure Machine Learning SDK per Python: PyYAML è un progetto installato da Distutils. Non è pertanto possibile stabilire in modo accurato quali file appartengono a esso in caso di disinstallazione parziale. Per continuare a installare l'SDK, ignorando l'errore, usare:

```Python
pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML
```

## <a name="trouble-creating-azure-machine-learning-compute"></a>Problemi durante la creazione dell'ambiente di calcolo di Azure Machine Learning

Esiste una rara possibilità per cui alcuni utenti che hanno creato l'area di lavoro di Azure Machine Learning dal portale di Azure prima della versione GA potrebbero non essere in grado di creare l'ambiente di calcolo di Azure Machine Learning nell'area di lavoro. È possibile generare una richiesta di supporto per il servizio o creare una nuova area di lavoro tramite il portale o il SDK per annullare il blocco immediatamente.

## <a name="image-building-failure"></a>Errore di compilazione di immagini

Errore di compilazione di immagini durante la distribuzione del servizio Web. Una soluzione alternativa consiste nell'aggiungere "pynacl==1.2.1" come dipendenza pip al file Conda per la configurazione dell'immagine.

## <a name="deployment-failure"></a>Errore di distribuzione

Se si osserva `['DaskOnBatch:context_managers.DaskOnBatch', 'setup.py']' died with <Signals.SIGKILL: 9>`, modificare lo SKU delle macchine virtuali usate nella distribuzione specificandone uno con maggiore quantità di memoria.

## <a name="fpgas"></a>FPGA

Non sarà possibile distribuire i modelli in FPGA fino a quando non viene richiesta e approvata la quota FPGA. Per richiedere l'accesso, compilare il modulo di richiesta della quota: https://aka.ms/aml-real-time-ai

## <a name="automated-machine-learning"></a>Funzionalità automatizzate di Machine Learning

Tensore flusso automatizzato apprendimento automatico attualmente non supporta la versione del flusso di tensore 1.13. Installare questa versione causerà le dipendenze dei pacchetti a smettere di funzionare. Stiamo lavorando per risolvere questo problema in una versione futura. 


## <a name="databricks"></a>Databricks

Problemi di Databricks e Azure Machine Learning.

### <a name="failure-when-installing-packages"></a>Errore durante l'installazione dei pacchetti

In Azure Databricks, installazione di Azure Machine Learning SDK ha esito negativo quando sono installati altri pacchetti. Alcuni pacchetti, come `psutil`, possono causare conflitti. Per evitare errori di installazione, installare i pacchetti bloccando la versione della libreria. Questo problema è correlato a Databricks e non per il SDK del servizio Azure Machine Learning. Questo problema con altre librerie, potrebbe verificarsi troppo. Esempio:

```python
psutil cryptography==1.5 pyopenssl==16.0.0 ipython==2.2.0
```

In alternativa, è possibile usare gli script di inizializzazione se si mantengano rivolta problemi di installazione con le librerie Python. Questo approccio non è ufficialmente supportato. Per altre informazioni, vedere [script con ambito Cluster init](https://docs.azuredatabricks.net/user-guide/clusters/init-scripts.html#cluster-scoped-init-scripts).

### <a name="cancel-an-automated-machine-learning-run"></a>Annullare un'esecuzione automatizzata di apprendimento

Quando si usa automatizzate funzionalità di machine learning in Azure Databricks, per annullare un'esecuzione e avviare un nuovo esperimento esegue, riavviare il cluster Azure Databricks.

### <a name="10-iterations-for-automated-machine-learning"></a>> 10 iterazioni per l'apprendimento automatico

In automatizzati machine learning le impostazioni, se si dispone di più di 10 iterazioni, impostare `show_output` a `False` quando si invia l'esecuzione.

### <a name="widget-for-the-azure-machine-learning-sdkautomated-machine-learning"></a>Widget per il machine learning di Azure Machine Learning a SDK/automatizzata

Poiché i notebook non è possibile analizzare i widget HTML, il widget di SDK di Azure Machine Learning non è supportato in un notebook di Databricks. È possibile visualizzare il widget nel portale usando il codice Python nella cella del notebook Azure Databricks:

```
displayHTML("<a href={} target='_blank'>Azure Portal: {}</a>".format(local_run.get_portal_url(), local_run.id))
```

### <a name="import-error-no-module-named-pandascoreindexes"></a>Errore di importazione: Nessun modulo denominato 'pandas.core.indexes'

Se viene visualizzato questo errore quando si usa automatico apprendimento:

1. Eseguire questo comando per installare due pacchetti nel cluster Azure Databricks: 

   ```
   scikit-learn==0.19.1
   pandas==0.22.0
   ```

1. Scollega e ricollega il cluster per il notebook. 

Se questi passaggi non risolvono il problema, provare a riavviare il cluster.

## <a name="azure-portal"></a>Portale di Azure

Se si passa direttamente a visualizzare l'area di lavoro a un collegamento di condivisione da SDK o dal portale, non sarà possibile visualizzare la pagina Panoramica normale con le informazioni sulla sottoscrizione nell'estensione. Inoltre non sarà possibile passare in un'altra area di lavoro. Se si desidera visualizzare un'altra area di lavoro, la soluzione alternativa consiste nel passare direttamente al [portale di Azure](https://portal.azure.com) e cercare il nome dell'area di lavoro.

## <a name="diagnostic-logs"></a>Log di diagnostica

In alcuni casi può essere utile fornire le informazioni di diagnostica quando si richiede supporto. Per visualizzare alcuni log, visitare [portale di Azure](https://portal.azure.com) e passare all'area di lavoro e selezionare **dell'area di lavoro > esperimento > esecuzione > log**.

## <a name="resource-quotas"></a>Quote di risorse

Informazioni sulle [quote di risorse](how-to-manage-quotas.md) che si potrebbero incontrare quando si lavora con Azure Machine Learning.

## <a name="authentication-errors"></a>Errori di autenticazione

Se si esegue un'operazione di gestione in una destinazione di calcolo da un processo remoto, si riceverà uno degli errori seguenti:

```json
{"code":"Unauthorized","statusCode":401,"message":"Unauthorized","details":[{"code":"InvalidOrExpiredToken","message":"The request token was either invalid or expired. Please try again with a valid token."}]}
```

```json
{"error":{"code":"AuthenticationFailed","message":"Authentication failed."}}
```

Ad esempio, si riceverà un errore se si prova a creare o collegare una destinazione di calcolo da una pipeline di Machine Learning che viene inviata per l'esecuzione remota.
