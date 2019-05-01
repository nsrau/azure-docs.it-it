---
title: Eseguire carichi di lavoro di Azure Machine Learning con Machine Learning automatizzato su Apache Spark in Azure HDInsight
description: Informazioni su come eseguire carichi di lavoro di Azure Machine Learning con Machine Learning automatizzato su Apache Spark in Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/14/2019
ms.openlocfilehash: 5135de0fc87af227073f96c653d928ace1a50fd0
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64917051"
---
# <a name="run-azure-machine-learning-workloads-with-automated-machine-learning-automl-on-apache-spark-in-azure-hdinsight"></a>Eseguire carichi di lavoro di Azure Machine Learning con Machine Learning automatizzato su Apache Spark in Azure HDInsight

Azure Machine Learning semplifica e accelera la creazione, training e la distribuzione di modelli di machine learning. In automatizzati di machine learning (AutoML), si inizia con dati di training che include una funzionalità di destinazione definito e quindi eseguire l'iterazione attraverso le combinazioni di algoritmi e selezione di funzionalità per selezionare automaticamente il modello migliore per i dati per i punteggi di training. HDInsight consente ai clienti di effettuare il provisioning di cluster con centinaia di nodi. AutoML in esecuzione in Spark in un cluster HDInsight consente agli utenti di usare la capacità di calcolo tra i nodi per eseguire processi di training in una modalità di scalabilità orizzontale e per eseguire più processi di training in parallelo. Ciò consente agli utenti di eseguire esperimenti AutoML pur condividendo le risorse di calcolo con loro altri carichi di lavoro di big data.
 

## <a name="install-azure-machine-learning-on-an-hdinsight-cluster"></a>Installare Azure Machine Learning in un cluster HDInsight

Per esercitazioni generale dell'apprendimento automatico, vedere [esercitazione: Usare il servizio Machine Learning automatizzato per creare il proprio modello di regressione](../../machine-learning/service/tutorial-auto-train-models.md).
Tutti i nuovi cluster HDInsight Spark è preinstallato con Azure ml-AutoML SDK. È possibile iniziare a usare AutoML in HDInsight con questo [notebook di Jupyter di esempio](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-hdi). che illustra come usare un classificatore di Machine Learning automatizzato per un problema di classificazione semplice.

> [!Note]
> I pacchetti di Azure Machine Learning vengono installati nell'ambiente Conda Python3 e il notebook di Jupyter installato deve essere eseguito con il kernel PySpark3.

In alternativa, è possibile usare i notebook di Zeppelin usare AutoML anche.

> [!Note]
> Zeppelin con un [problema noto](https://community.hortonworks.com/content/supportkb/207822/the-livypyspark3-interpreter-uses-python-2-instead.html) dove PySpark3 non selezionare la versione corretta di Python. Usare documentata risolvere.

## <a name="authentication-for-workspace"></a>Autenticazione dell'area di lavoro

La creazione dell'area di lavoro e l'invio dell'esperimento richiedono un token di autenticazione, che può essere generato usando un'[applicazione di Azure AD](../../active-directory/develop/app-objects-and-service-principals.md). Se sull'account non è abilitata l'autenticazione a più fattori, per generare il token di autenticazione è possibile anche usare un [utente di Azure AD](https://docs.microsoft.com/python/azure/python-sdk-azure-authenticate?view=azure-python).  

Il frammento di codice seguente crea un token di autenticazione con un'**applicazione di Azure AD**.

```python
from azureml.core.authentication import ServicePrincipalAuthentication
auth_sp = ServicePrincipalAuthentication(
                tenant_id = '<Azure Tenant ID>',
                service_principal_id = '<Azure AD Application ID>',
                service_principal_password = '<Azure AD Application Key>'
                )
```
Il frammento di codice seguente crea un token di autenticazione con un **utente di Azure AD**.

```python
from azure.common.credentials import UserPassCredentials
credentials = UserPassCredentials('user@domain.com','my_smart_password')
```

## <a name="loading-dataset"></a>Caricamento di un set di dati

La tecnologia di Machine Learning automatizzato in Spark usa **flussi di dati**, ovvero operazioni sui dati non modificabili e con valutazione differita.  Un flusso di dati può caricare un set di dati da un BLOB con accesso in lettura pubblico o da un URL BLOB con un token di firma di accesso condiviso.

```python
import azureml.dataprep as dprep

dataflow_public = dprep.read_csv(path='https://commonartifacts.blob.core.windows.net/automl/UCI_Adult_train.csv')

dataflow_with_token = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv?st=2018-06-15T23%3A01%3A42Z&se=2019-06-16T23%3A01%3A00Z&sp=r&sv=2017-04-17&sr=b&sig=ugQQCmeC2eBamm6ynM7wnI%2BI3TTDTM6z9RPKj4a%2FU6g%3D')
```

È anche possibile registrare l'archivio dati nell'area di lavoro usando una registrazione una tantum.

## <a name="experiment-submission"></a>Invio di esperimento

Nel [learning machineconfiguration automatizzati](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig), la proprietà `spark_context` deve essere impostato per il pacchetto da eseguire in modalità distribuita. La proprietà `concurrent_iterations`, che corrisponde al numero massimo di iterazioni eseguite in parallelo, deve essere impostata su un numero inferiore rispetto ai core executor per l'app Spark.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sulla motivazione alla base di apprendimento automatico, vedere [i modelli di rilascio a in base alla velocità usando Microsoft automatico di apprendimento automatico.](https://azure.microsoft.com/blog/release-models-at-pace-using-microsoft-s-automl/)
* Per altre informazioni sull'utilizzo delle funzionalità di Azure Machine Learning automatizzati di Machine Learning, vedere [New automatizzata funzionalità di machine learning nel servizio Azure Machine Learning](https://azure.microsoft.com/blog/new-automated-machine-learning-capabilities-in-azure-machine-learning-service/)
* [Progetto di Machine Learning automatizzato sviluppato da Microsoft Research](https://www.microsoft.com/research/project/automl/)
