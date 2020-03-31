---
title: Data ingestion with Azure Data Factory
titleSuffix: Azure Machine Learning
description: Informazioni su come creare una pipeline di inserimento dati con Azure Data Factory.Learn how to build a data ingestion pipeline with Azure Data Factory.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: iefedore
author: eedorenko
manager: davete
ms.reviewer: larryfr
ms.date: 03/01/2020
ms.openlocfilehash: 5b4ed40048aab815397c9726098880b2125b732c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78274788"
---
# <a name="data-ingestion-with-azure-data-factory"></a>Data ingestion with Azure Data Factory

In questo articolo viene illustrato come compilare una pipeline di inserimento dati con Azure Data Factory (ADF). Questa pipeline viene usata per l'inserimento di dati da usare con Azure Machine Learning.This pipeline is used to ingest data for use with Azure Machine Learning. Azure Data Factory consente di estrarre, trasformare e caricare facilmente i dati (ETL). Una volta che i dati sono stati trasformati e caricati in archiviazione, possono essere usati per eseguire il training dei modelli di apprendimento automatico.

La semplice trasformazione dei dati può essere gestita con attività e strumenti ADF nativi, ad esempio [il flusso di dati.](https://docs.microsoft.com/azure/data-factory/control-flow-execute-data-flow-activity) Quando si tratta di scenari più complessi, i dati possono essere elaborati con codice personalizzato. Ad esempio, codice Python o R.For example, Python or R code.

Esistono diverse tecniche comuni di utilizzo di Azure Data Factory per trasformare i dati durante l'inserimento. Ogni tecnica ha pro e contro che determinano se è una buona misura per un caso d'uso specifico:

| Tecnica | Vantaggi | Svantaggi |
| ----- | ----- | ----- |
| ADF - Funzioni di Azure | Bassa latenza, calcolo senza server</br>Funzioni stateful</br>Funzioni riutilizzabili | Buono solo per l'elaborazione a breve distanza |
| ADF - componente personalizzato | Calcolo parallelo su larga scala</br>Adatto per algoritmi pesanti | Wrapping di codice in un eseguibileWrapping code into an executable</br>Complessità della gestione delle dipendenze e I/O |
| Blocco appunti ADF - Azure Databricks | Apache Spark</br>Ambiente Python nativo | Può essere costoso</br>La creazione di cluster richiede inizialmente tempo e aggiunge latenza

## <a name="adf-with-azure-functions"></a>ADF con funzioni di AzureADF with Azure functions

![adf-funzione](media/how-to-data-ingest-adf/adf-function.png)

Funzioni di Azure consente di eseguire piccole parti di codice (funzioni) senza preoccuparsi dell'infrastruttura dell'applicazione. In this option, the data is processed with custom Python code wrapped into an Azure Function. 

La funzione viene richiamata con [l'attività Funzione di Azure ADF.](https://docs.microsoft.com/azure/data-factory/control-flow-azure-function-activity) Questo approccio è una buona opzione per le trasformazioni dei dati leggere. 

* Vantaggi:
    * I dati vengono elaborati su un calcolo senza server con una latenza relativamente bassa
    * La pipeline ADF può richiamare una funzione di [Azure durevole](/azure/azure-functions/durable/durable-functions-overview) che può implementare un flusso di trasformazione dei dati sofisticatoADF pipeline can invoke a Durable Azure Function that may implement a sophisticated data transformation flow 
    * I dettagli della trasformazione dei dati vengono astratti dalla funzione di Azure che può essere riutilizzata e richiamata da altre posizioni
* Svantaggi:
    * Le funzioni di Azure devono essere create prima dell'uso con ADFThe Azure Functions must be created before use with ADF
    * Funzioni di Azure è valido solo per l'elaborazione di dati a esecuzione breveAzure Functions is good only for short running data processing

## <a name="adf-with-custom-component-activity"></a>ADF con attività del componente personalizzato

![adf-customcomponent (componente adf-personalizzato)](media/how-to-data-ingest-adf/adf-customcomponent.png)

In questa opzione, i dati vengono elaborati con codice Python personalizzato di cui è stato eseguito il wrapping in un eseguibile. Viene richiamato con [un'attività ADF Custom Component](https://docs.microsoft.com/azure/data-factory/transform-data-using-dotnet-custom-activity). Questo approccio è più adatto per i dati di grandi dimensioni rispetto alla tecnica precedente.

* Vantaggi:
    * I dati vengono elaborati nel pool Batch di [Azure,](https://docs.microsoft.com/azure/batch/batch-technical-overview) che fornisce calcoli paralleli e ad alte prestazioni su larga scala
    * Può essere utilizzato per eseguire algoritmi pesanti ed elaborare quantità significative di dati
* Svantaggi:
    * Azure Batch pool must be created before use with ADF
    * Oltre la progettazione relativa al wrapping di codice Python in un eseguibile. Complessità della gestione delle dipendenze e dei parametri di input/output

## <a name="adf-with-azure-databricks-python-notebook"></a>BLOCCO appunti di ADF con Azure Databricks Python

![adf-databricks](media/how-to-data-ingest-adf/adf-databricks.png)

[Azure Databricks](https://azure.microsoft.com/services/databricks/) è una piattaforma di analisi basata su Apache Spark nel cloud Microsoft.Azure Databricks is an Apache Spark-based analytics platform in the Microsoft cloud.

In questa tecnica, la trasformazione dei dati viene eseguita da un [blocco appunti Python](https://docs.microsoft.com/azure/data-factory/transform-data-using-databricks-notebook), in esecuzione in un cluster Di Azure Databricks. Questo è probabilmente l'approccio più comune che sfrutta tutta la potenza di un servizio Azure Databricks.This is probably, the most common approach that leverages the full power of an Azure Databricks service. È progettato per l'elaborazione distribuita dei dati su larga scala.

* Vantaggi:
    * I dati vengono trasformati nel servizio di Azure di elaborazione dati più potente, il cui backup è stato eseguito dall'ambiente Apache Spark
    * Supporto nativo di Python insieme a framework e librerie di analisi scientifica dei dati, tra cui TensorFlow, PyTorch e scikit-learn
    * Non è necessario eseguire il wrapping del codice Python in funzioni o moduli eseguibili. Il codice funziona così com'è.
* Svantaggi:
    * Azure Databricks infrastructure must be created before use with ADF
    * Può essere costoso a seconda della configurazione di Azure Databricks
    * La rotazione dei cluster di calcolo dalla modalità "freddo" richiede del tempo che porta un'elevata latenza alla soluzione 
    

## <a name="consuming-data-in-azure-machine-learning-pipelines"></a>Utilizzo di dati nelle pipeline di Azure Machine LearningConsuming data in Azure Machine Learning pipelines

![aml-dataset](media/how-to-data-ingest-adf/aml-dataset.png)

The transformed data from the ADF pipeline is saved to data storage (such as Azure Blob). Azure Machine Learning può accedere a questi dati usando [datastore](https://docs.microsoft.com/azure/machine-learning/how-to-access-data#create-and-register-datastores) e [set di dati.](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets)

Ogni volta che viene eseguita la pipeline ADF, i dati vengono salvati in un percorso diverso nell'archiviazione. Per passare il percorso ad Azure Machine Learning, la pipeline ADF chiama una pipeline di Azure Machine Learning.To pass the location to Azure Machine Learning, the ADF pipeline calls an Azure Machine Learning pipeline. Quando si chiama la pipeline di Machine IL, il percorso dei dati e l'ID di esecuzione vengono inviati come parametri. La pipeline di ML può quindi creare un archivio dati/set di dati usando il percorso dei dati. 

> [!TIP]
> I set di dati [supportano](https://docs.microsoft.com/azure/machine-learning/how-to-version-track-datasets)il controllo delle versioni, in modo che la pipeline di Configurazione guidata servizi può registrare una nuova versione del set di dati che punta ai dati più recenti della pipeline ADF.

Una volta che i dati sono accessibili tramite un datastore o un set di dati, è possibile usarli per eseguire il training di un modello di ML. Il processo di training potrebbe far parte della stessa pipeline di ML chiamata da ADF. Oppure potrebbe essere un processo separato come la sperimentazione in un taccuino Jupyter.

Poiché i set di dati supportano il controllo delle versioni e ogni esecuzione eseguita dalla pipeline crea una nuova versione, è facile comprendere quale versione dei dati è stata usata per eseguire il training di un modello.

## <a name="next-steps"></a>Passaggi successivi

* [Eseguire un blocco appunti di Databrick in Azure Data FactoryRun a Databricks notebook in Azure Data Factory](https://docs.microsoft.com/azure/data-factory/transform-data-using-databricks-notebook)
* [Accedere ai dati nei servizi di archiviazione di AzureAccess data in Azure storage services](https://docs.microsoft.com/azure/machine-learning/how-to-access-data#create-and-register-datastores)
* [Eseguire il training di modelli con set di dati in Azure Machine LearningTrain models with datasets in Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/how-to-train-with-datasets)
* [DevOps per una pipeline di inserimento datiDevOps for a data ingestion pipeline](https://docs.microsoft.com/azure/machine-learning/how-to-cicd-data-ingestion)

