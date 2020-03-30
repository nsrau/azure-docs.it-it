---
title: Eseguire carichi di lavoro di Azure Machine Learning in Apache Spark in HDInsightRun Azure Machine Learning workloads on Apache Spark in HDInsight
description: Informazioni su come eseguire carichi di lavoro di Azure Machine Learning con Machine Learning automatizzato su Apache Spark in Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/13/2019
ms.openlocfilehash: 6fc0d4cfe29e0fb189c44b307576bd08d2da8a31
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75638885"
---
# <a name="run-azure-machine-learning-workloads-with-automated-machine-learning-on-apache-spark-in-hdinsight"></a>Eseguire carichi di lavoro di Azure Machine Learning con l'apprendimento automatico automatico in Apache Spark in HDInsightRun Azure Machine Learning workloads with automated machine learning on Apache Spark in HDInsight

Azure Machine Learning semplifica e accelera la creazione, la formazione e la distribuzione dei modelli di apprendimento automatico. Nell'apprendimento automatico automatico (AutoML) si inizia con i dati di training che hanno una funzionalità di destinazione definita e quindi si esegue l'iterazione attraverso combinazioni di algoritmi e selezioni di funzionalità per selezionare automaticamente il modello migliore per i dati in base ai punteggi di training. HDInsight consente ai clienti di eseguire il provisioning dei cluster con centinaia di nodi. AutoML in esecuzione su Spark in un cluster HDInsight consente agli utenti di utilizzare la capacità di calcolo tra questi nodi per eseguire i processi di training in modo scale-out e per eseguire più processi di training in parallelo. Ciò consente agli utenti di eseguire esperimenti AutoML condividendo il calcolo con gli altri carichi di lavoro Big Data.

## <a name="install-azure-machine-learning-on-an-hdinsight-cluster"></a>Installare Azure Machine Learning in un cluster HDInsight

Per esercitazioni generali sull'apprendimento automatico automatico, vedere [Esercitazione: Usare l'apprendimento automatico automatico per creare il modello di regressione.](../../machine-learning/tutorial-auto-train-models.md)
Tutti i nuovi cluster HDInsight-Spark vengono preinstallati con AzureML-AutoML SDK.

> [!Note]
> I pacchetti di Azure Machine Learning vengono installati nell'ambiente Conda Python3 e il notebook di Jupyter installato deve essere eseguito con il kernel PySpark3.

Per utilizzare autoML, è possibile utilizzare i notebook .eppelin.

> [!Note]
> Il [problema di](https://community.hortonworks.com/content/supportkb/207822/the-livypyspark3-interpreter-uses-python-2-instead.html) PySpark3 non sceglie la versione corretta di Python. Si prega di utilizzare la sistema di lavoro documentata.

## <a name="authentication-for-workspace"></a>Autenticazione dell'area di lavoro

La creazione dell'area di lavoro e l'invio dell'esperimento richiedono un token di autenticazione, che può essere generato usando un'[applicazione di Azure AD](../../active-directory/develop/app-objects-and-service-principals.md). Se sull'account non è abilitata l'autenticazione a più fattori, per generare il token di autenticazione è possibile anche usare un [utente di Azure AD](/azure/python/python-sdk-azure-authenticate).  

Il frammento di codice seguente crea un token di autenticazione con un'**applicazione di Azure AD**.

```python
from azureml.core.authentication import ServicePrincipalAuthentication
auth_sp = ServicePrincipalAuthentication(
    tenant_id='<Azure Tenant ID>',
    service_principal_id='<Azure AD Application ID>',
    service_principal_password='<Azure AD Application Key>'
)
```

Il frammento di codice seguente crea un token di autenticazione con un **utente di Azure AD**.

```python
from azure.common.credentials import UserPassCredentials
credentials = UserPassCredentials('user@domain.com', 'my_smart_password')
```

## <a name="loading-dataset"></a>Caricamento di un set di dati

La tecnologia di Machine Learning automatizzato in Spark usa **flussi di dati**, ovvero operazioni sui dati non modificabili e con valutazione differita.  Un flusso di dati può caricare un set di dati da un BLOB con accesso in lettura pubblico o da un URL BLOB con un token di firma di accesso condiviso.

```python
import azureml.dataprep as dprep

dataflow_public = dprep.read_csv(
    path='https://commonartifacts.blob.core.windows.net/automl/UCI_Adult_train.csv')

dataflow_with_token = dprep.read_csv(
    path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv?st=2018-06-15T23%3A01%3A42Z&se=2019-06-16T23%3A01%3A00Z&sp=r&sv=2017-04-17&sr=b&sig=ugQQCmeC2eBamm6ynM7wnI%2BI3TTDTM6z9RPKj4a%2FU6g%3D')
```

È anche possibile registrare l'archivio dati nell'area di lavoro usando una registrazione una tantum.

## <a name="experiment-submission"></a>Invio di esperimento

Nella configurazione di apprendimento automatico, `spark_context` la proprietà deve essere [impostata](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)affinché il pacchetto venga eseguito in modalità distribuita. La proprietà `concurrent_iterations`, che corrisponde al numero massimo di iterazioni eseguite in parallelo, deve essere impostata su un numero inferiore rispetto ai core executor per l'app Spark.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sulla motivazione alla base dell'apprendimento automatico, vedere [Rilasciare i modelli al ritmo con l'apprendimento automatico di Microsoft.](https://azure.microsoft.com/blog/release-models-at-pace-using-microsoft-s-automl/)
* Per altre informazioni sull'uso delle funzionalità di Azure ML Automated ML, vedere Nuove funzionalità di [apprendimento automatico automatizzate in Azure Machine LearningFor](https://azure.microsoft.com/blog/new-automated-machine-learning-capabilities-in-azure-machine-learning-service/) more information on using Azure ML Automated ML capabilities, see New automated machine learning capabilities in Azure Machine Learning
* [Progetto di Machine Learning automatizzato sviluppato da Microsoft Research](https://www.microsoft.com/research/project/automl/)
