---
title: Eseguire carichi di lavoro di Azure Machine Learning con Machine Learning automatizzato su Apache Spark in Azure HDInsight
description: Informazioni su come eseguire carichi di lavoro di Azure Machine Learning con Machine Learning automatizzato su Apache Spark in Azure HDInsight.
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: howto
ms.date: 01/14/2019
ms.openlocfilehash: 58852ee5de97bef043f8c0b67d9e90cdc057912a
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2019
ms.locfileid: "54440099"
---
# <a name="run-azure-machine-learning-workloads-with-automated-machine-learning-automl-on-apache-spark-in-azure-hdinsight"></a>Eseguire carichi di lavoro di Azure Machine Learning con Machine Learning automatizzato su Apache Spark in Azure HDInsight

Azure Machine Learning è uno strumento collaborativo con supporto per il trascinamento della selezione che consente di creare, testare e distribuire soluzioni di analisi predittiva sui dati. Azure Machine Learning pubblica i modelli come servizi Web che possono essere facilmente usati da app personalizzate o strumenti di Business Intelligence come Excel. La tecnologia di Machine Learning automatizzato consente di creare modelli di Machine Learning di alta qualità usando funzionalità di ottimizzazione e automazione intelligente. Questa tecnologia è in grado di individuare l'algoritmo e gli iperparametri appropriati da usare per specifici tipi di problemi.

## <a name="install-azure-machine-learning-on-an-hdinsight-cluster"></a>Installare Azure Machine Learning in un cluster HDInsight

> [!Note]
> L'area di lavoro di Azure Machine Learning è attualmente disponibile nelle aree seguenti: eastus, eastus2 e westcentralus. Anche il cluster HDInsight deve essere creato in una di queste aree.

Per esercitazioni generiche su Azure Machine Learning e Machine Learning automatizzato, vedere [Esercitazione: Creare il primo esperimento data science in Azure Machine Learning Studio](../../machine-learning/studio/create-experiment.md) e [Esercitazione: Usare il servizio Machine Learning automatizzato per creare il proprio modello di regressione](../../machine-learning/service/tutorial-auto-train-models.md).
Per installare il servizio Machine Learning automatizzato nel cluster HDInsight di Azure, eseguire l'azione script - [install_aml](https://commonartifacts.blob.core.windows.net/automl/install_aml.sh) - nei nodi head e nei nodi di lavoro di un cluster Spark 2.3.0 di HDInsight 3.6 (scelta consigliata). Questa azione script può essere eseguita nell'ambito del processo di creazione del cluster o in un cluster esistente tramite il portale di Azure.

Per altre informazioni sulle azioni script, vedere [Personalizzare cluster HDInsight basati su Linux tramite Azione script](../hdinsight-hadoop-customize-cluster-linux.md). Oltre a installare i pacchetti e le dipendenze di Azure Machine Learning, lo script scarica anche un notebook di Jupyter di esempio (nel percorso `HdiNotebooks/PySpark` dell'archivio predefinito), che illustra come usare un classificatore di Machine Learning automatizzato per un problema di classificazione semplice.

> [!Note]
> I pacchetti di Azure Machine Learning vengono installati nell'ambiente Conda Python3 e il notebook di Jupyter installato deve essere eseguito con il kernel PySpark3.

## <a name="authentication-for-workspace"></a>Autenticazione dell'area di lavoro

La creazione dell'area di lavoro e l'invio dell'esperimento richiedono un token di autenticazione, che può essere generato usando un'[applicazione di Azure AD](../../active-directory/develop/app-objects-and-service-principals.md). Se sull'account non è abilitata l'autenticazione a più fattori, per generare il token di autenticazione è possibile anche usare un [utente di Azure AD](https://docs.microsoft.com/python/azure/python-sdk-azure-authenticate?view=azure-python).  

Il frammento di codice seguente crea un token di autenticazione con un'**applicazione di Azure AD**.

```python
from azureml.core.authentication import ServicePrincipalAuthentication
auth_sp = ServicePrincipalAuthentication(
                tenant_id = '<Azure Tenant ID>',
                username = '<Azure AD Application ID>',
                password = '<Azure AD Application Key>'
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

Nella configurazione di Machine Learning automatizzato, la proprietà `spark_context` deve essere impostata in modo che il pacchetto venga eseguito in modalità distribuita. La proprietà `concurrent_iterations`, che corrisponde al numero massimo di iterazioni eseguite in parallelo, deve essere impostata su un numero inferiore rispetto ai core executor per l'app Spark.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sulla logica alla base della tecnologia di Machine Learning automatizzato, vedere [Release models at pace using Microsoft's automated machine learning!](https://azure.microsoft.com/blog/release-models-at-pace-using-microsoft-s-automl/) (Rilasciare modelli a intervalli regolari con la tecnologia di Machine Learning automatizzato Microsoft).
* [Progetto di Machine Learning automatizzato sviluppato da Microsoft Research](https://www.microsoft.com/research/project/automl/)