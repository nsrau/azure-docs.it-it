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
ms.date: 04/30/2019
ms.custom: seodec18
ms.openlocfilehash: 7d1bce7575272b7df185c4e261685d989f49436c
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68716542"
---
# <a name="known-issues-and-troubleshooting-azure-machine-learning-service"></a>Problemi noti e risoluzione per il servizio di Azure Machine Learning

Questo articolo consente di trovare e correggere errori o guasti riscontrati durante l'uso del servizio di Azure Machine Learning.

## <a name="visual-interface-issues"></a>Problemi relativi all'interfaccia visiva

Interfaccia visiva per i problemi del servizio Machine Learning.

### <a name="long-compute-preparation-time"></a>Tempo di preparazione del calcolo lungo

La creazione di un nuovo calcolo o di un'evocazione per l'uscita richiede tempo, può essere di pochi minuti o addirittura più lungo. Il team sta lavorando per l'ottimizzazione.


### <a name="cannot-run-an-experiment-only-contains-dataset"></a>Non è possibile eseguire un esperimento contiene solo il set di dati 

Potrebbe essere necessario eseguire un esperimento contenente solo il set di dati per visualizzare il set di dati. Tuttavia, non è consentita l'esecuzione di un esperimento che contiene solo il set di dati. Questo problema è stato risolto attivamente.
 
Prima della correzione, è possibile connettere il set di dati a qualsiasi modulo di trasformazione dati (selezionare le colonne nel set di dati, modificare i metadati, suddividere i dati e così via) ed eseguire l'esperimento. Sarà quindi possibile visualizzare il set di dati. 

Nell'immagine seguente viene illustrato ![come: visulize-data](./media/resource-known-issues/aml-visualize-data.png)

## <a name="sdk-installation-issues"></a>Problemi di installazione dell'SDK

**Messaggio di errore: Impossibile installare "PyYAML"**

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

## <a name="automated-machine-learning"></a>Machine Learning automatizzato

Il flusso tensore automatico Machine Learning non supporta attualmente il flusso tensore versione 1,13. Se si installa questa versione, le dipendenze del pacchetto smetteranno di funzionare. Ci stiamo impegnando per risolvere questo problema in una versione futura. 

### <a name="experiment-charts"></a>Grafici degli esperimenti

I grafici di classificazione binaria (precisione-richiamo, ROC, curva di guadagno e così via) mostrati nelle iterazioni dell'esperimento di ML automatizzato non eseguono il rendering corecto nell'interfaccia utente a partire da 4/12. I tracciati del grafico mostrano attualmente risultati inversi, in cui i modelli con prestazioni migliori vengono visualizzati con risultati inferiori. Una soluzione è in fase di analisi.

## <a name="databricks"></a>Databricks

Problemi di Databricks e Azure Machine Learning.

### <a name="failure-when-installing-packages"></a>Errore durante l'installazione dei pacchetti

L'installazione di Azure Machine Learning SDK non riesce in Azure Databricks quando sono installati altri pacchetti. Alcuni pacchetti, come `psutil`, possono causare conflitti. Per evitare errori di installazione, installare i pacchetti bloccando la versione della libreria. Questo problema è correlato a databricks e non a Azure Machine Learning Service SDK. Questo problema può verificarsi anche con altre librerie. Esempio:

```python
psutil cryptography==1.5 pyopenssl==16.0.0 ipython==2.2.0
```

In alternativa, è possibile usare gli script init se si continuano a risolvere i problemi di installazione con le librerie di Python. Questo approccio non è ufficialmente supportato. Per altre informazioni, vedere [script init con ambito cluster](https://docs.azuredatabricks.net/user-guide/clusters/init-scripts.html#cluster-scoped-init-scripts).

### <a name="cancel-an-automated-machine-learning-run"></a>Annulla un'esecuzione automatizzata di Machine Learning

Quando si usano le funzionalità automatiche di Machine Learning in Azure Databricks, per annullare un'esecuzione e avviare una nuova esecuzione dell'esperimento, riavviare il cluster di Azure Databricks.

### <a name="10-iterations-for-automated-machine-learning"></a>> 10 iterazioni per Machine Learning automatizzato

Nelle impostazioni automatiche di Machine Learning, se sono presenti più di 10 iterazioni `show_output` , `False` impostare su quando si invia l'esecuzione.

### <a name="widget-for-the-azure-machine-learning-sdkautomated-machine-learning"></a>Widget per Azure Machine Learning SDK/Machine Learning automatizzato

Il widget Azure Machine Learning SDK non è supportato in un notebook di databricks perché i notebook non possono analizzare i widget HTML. È possibile visualizzare il widget nel portale usando questo codice Python nella cella Azure Databricks notebook:

```
displayHTML("<a href={} target='_blank'>Azure Portal: {}</a>".format(local_run.get_portal_url(), local_run.id))
```

### <a name="import-error-no-module-named-pandascoreindexes"></a>Errore di importazione: Nessun modulo denominato ' Pandas. Core. Indexes '

Se questo errore viene visualizzato quando si usa Machine Learning automatico:

1. Eseguire questo comando per installare due pacchetti nel cluster Azure Databricks: 

   ```
   scikit-learn==0.19.1
   pandas==0.22.0
   ```

1. Scollegare e quindi riconnettere il cluster al notebook. 

Se questi passaggi non risolvono il problema, provare a riavviare il cluster.

### <a name="failtosendfeather"></a>FailToSendFeather

Se viene visualizzato un `FailToSendFeather` errore durante la lettura dei dati in Azure Databricks cluster, fare riferimento alle soluzioni seguenti:

* Aggiornare `azureml-sdk[automl_databricks]` il pacchetto alla versione più recente.
* Aggiungere `azure-dataprep` la versione 1.1.8 o successiva.
* Aggiungere `pyarrow` la versione 0,11 o successiva.

## <a name="azure-portal"></a>Portale di Azure

Se si passa direttamente a visualizzare l'area di lavoro a un collegamento di condivisione da SDK o dal portale, non sarà possibile visualizzare la pagina Panoramica normale con le informazioni sulla sottoscrizione nell'estensione. Inoltre non sarà possibile passare in un'altra area di lavoro. Se si desidera visualizzare un'altra area di lavoro, la soluzione alternativa consiste nel passare direttamente al [portale di Azure](https://portal.azure.com) e cercare il nome dell'area di lavoro.

## <a name="diagnostic-logs"></a>Log di diagnostica

In alcuni casi può essere utile fornire le informazioni di diagnostica quando si richiede supporto. Per visualizzare alcuni log, visitare [portale di Azure](https://portal.azure.com) e passare all'area di lavoro e selezionare **area di lavoro > esperimento > Esegui log >** .

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

## <a name="overloaded-azurefile-storage"></a>Archiviazione AzureFile di overload

Se viene visualizzato l'errore "Impossibile caricare i file di progetto nella directory di lavoro in AzureFile perché lo spazio di archiviazione è sovraccarico", applicare le soluzioni alternative seguenti.

Se si usa una condivisione file per altri carichi di lavoro, ad esempio il trasferimento dei dati, si consiglia di usare i BLOB in modo che la condivisione file sia disponibile per l'invio di esecuzioni. È inoltre possibile suddividere il carico di lavoro tra due aree di lavoro diverse.
