---
title: Dati in Azure Machine LearningData in Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Informazioni su come Azure Machine Learning si connette in modo sicuro ai dati e usa tali dati per le attività di Apprendimento automatico.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
author: nibaccam
ms.author: nibaccam
ms.date: 03/20/2020
ms.openlocfilehash: 982c9c9eadec4403c8116430e1e25092de99f1d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128481"
---
# <a name="data-access-in-azure-machine-learning"></a>Accesso ai dati in Azure Machine LearningData access in Azure Machine Learning

Azure Machine Learning semplifica la connessione ai dati nel cloud.  Fornisce un livello di astrazione sul servizio di archiviazione sottostante, in modo da poter accedere in modo sicuro e lavorare con i dati senza dover scrivere codice specifico per il tipo di archiviazione. Azure Machine Learning offre anche le funzionalità dei dati seguenti:Azure Machine Learning also provides the following data capabilities:

*    Controllo delle versioni e rilevamento della derivazione dei dati
*    Etichettatura dei dati 
*    Monitoraggio della deriva dei dati
*    Interoperabilità con Panda e Spark DataFrames

## <a name="data-workflow"></a>Flusso di lavoro dei dati

Quando si è pronti a usare i dati nella soluzione di archiviazione basata su cloud, è consigliabile il flusso di lavoro di recapito dei dati seguente. Questo flusso di lavoro presuppone la disponibilità di un account di archiviazione di Azure e dei dati in un servizio di archiviazione basato su cloud in Azure.This workflow assumes you have an [Azure storage account](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal) and data in a cloud-based storage service in Azure. 

1. Creare un archivio dati di Azure Machine Learning per archiviare le informazioni di connessione all'archiviazione di Azure.Create an [Azure Machine Learning datastore](#datastores) to store connection information to your Azure storage.

2. Da tale archivio dati creare un set di dati di [Azure Machine Learning](#datasets) in modo che punti a uno o più file specifici nell'archiviazione sottostante. 

3. Per usare il set di dati nell'esperimento di apprendimento automatico, è possibile
    1. Montarlo sulla destinazione di calcolo dell'esperimento per il training del modello.

        **O** 

    1. In questo modo viene eseguita l'esperimento automatizzato di apprendimento automatico (Machine Learning), nelle pipeline di apprendimento automatico o nella finestra di progettazione di [Azure Machine Learning.](concept-designer.md)

4. Creare [monitoraggi del set di dati](#data-drift) per il set di dati di output del modello per rilevare la deriva dei dati. 

5. Se viene rilevata una deriva dei dati, aggiornare il set di dati di input e riqualificare il modello di conseguenza.

Il diagramma seguente fornisce una dimostrazione visiva di questo flusso di lavoro consigliato.

![Diagramma dei concetti di dati](./media/concept-data/data-concept-diagram.svg)

## <a name="datastores"></a>Datastore

Gli archivi dati di Azure Machine Learning mantengono in modo sicuro le informazioni di connessione all'archiviazione di Azure, in modo da non doverle codificarle negli script. [Registrare e creare un archivio dati](how-to-access-data.md) per connettersi facilmente all'account di archiviazione e accedere ai dati nel servizio di archiviazione di Azure sottostante. 

Servizi di archiviazione basati su cloud supportati in Azure che possono essere registrati come datastore:Supported cloud-based storage services in Azure that can be registered as datastores:

+ Contenitore BLOB di AzureAzure Blob Container
+ Condivisione file di Azure
+ Azure Data Lake
+ Azure Data Lake Gen2
+ database SQL di Azure
+ Database di Azure per PostgreSQL
+ File system di Databricks
+ Database di Azure per MySQL

## <a name="datasets"></a>Set di dati

I set di dati di Azure Machine Learning sono riferimenti che puntano ai dati nel servizio di archiviazione. Non sono copie dei tuoi dati, quindi non viene sostenuto costi di archiviazione aggiuntivi. Per interagire con i dati nell'archiviazione, [creare un set di dati](how-to-create-register-datasets.md) per creare un pacchetto dei dati in un oggetto utilizzabile per le attività di apprendimento automatico. Registrare il set di dati nell'area di lavoro per condividerlo e riutilizzarlo in diversi esperimenti senza complessità di inserimento dati.

I set di dati possono essere creati da file locali, URL pubblici, set di dati aperti di [Azure](https://azure.microsoft.com/services/open-datasets/)o servizi di archiviazione di Azure tramite archivi dati. Per creare un set di dati da un frame di dati pandas in memoria, scrivere i dati in un file locale, ad esempio un parquet, e creare il set di dati da tale file.  

Sono supportati 2 tipi di set di dati:We support 2 types of datasets: 
+ TabularDataset rappresenta [i](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) dati in formato tabulare analizzando il file o l'elenco di file fornito. È possibile caricare un TabularDataset in un Panda o Spark DataFrame per ulteriori manipolazioni e pulizia. Per un elenco completo dei formati di dati da cui è possibile creare TabularDatasets, vedere la [classe TabularDatasetFactory](https://aka.ms/tabulardataset-api-reference).

+ Un [FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py) fa riferimento a uno o più file negli archivi dati o in URL pubblici. È possibile [scaricare o montare](how-to-train-with-datasets.md#option-2--mount-files-to-a-remote-compute-target) i file a cui fa riferimento FileDatasets nella destinazione di calcolo.

Ulteriori funzionalità di set di dati sono disponibili nella documentazione seguente:Additional datasets capabilities can be found in the following documentation:

+ [Versione e tenere traccia della](how-to-version-track-datasets.md) derivazione del set di dati.
+ [Monitorare il set di dati](how-to-monitor-datasets.md) per facilitare il rilevamento della deriva dei dati.    

## <a name="work-with-your-data"></a>Lavorare con i dati

Con i set di dati, è possibile eseguire una serie di attività di machine learning tramite l'integrazione perfetta con le funzionalità di Azure Machine Learning.With datasets, you can accomplish a number of machine learning tasks through seamless integration with Azure Machine Learning features. 

+ Creare un [progetto di etichettatura dati.](#label)
+ Addestrare i modelli di apprendimento automatico:
     + [esperimenti automatizzati di ML](how-to-use-automated-ml-for-ml-models.md)
     + il [progettista](tutorial-designer-automobile-price-train-score.md#import-data)
     + [Notebook](how-to-train-with-datasets.md)
     + [Pipeline di Azure Machine LearningAzure Machine Learning pipelines](how-to-create-your-first-pipeline.md)
+ Accedere ai set di dati per il punteggio con [l'inferenza](how-to-use-parallel-run-step.md) di batch nelle pipeline di [Machine Learning.](how-to-create-your-first-pipeline.md)
+ Configurare un monitoraggio del set di dati per il rilevamento della deriva dei [dati.](#drift)

<a name="label"></a>

## <a name="data-labeling"></a>Etichettatura dei dati

L'etichettatura di grandi quantità di dati nei progetti di Machine Learning è spesso stata considerata un'attività molto complessa. Quelli con un componente di visione artificiale, come la classificazione delle immagini o il rilevamento di oggetti, generalmente richiedono migliaia di immagini ed etichette corrispondenti.

Azure Machine Learning offre una posizione centrale per creare, gestire e monitorare i progetti di etichettatura. I progetti di etichettatura consentono di coordinare i dati, le etichette e i membri del team, per una gestione più efficiente delle attività di etichettatura. Le attività attualmente supportate sono la classificazione delle immagini, multi-etichetta o multi-classe, e l'identificazione di oggetti tramite i riquadri di selezione.

Creare un progetto di etichettatura dei dati e generare un set di dati da usare negli esperimenti di Machine Learning.Create a [data labeling project](how-to-create-labeling-projects.md), and output a dataset for use in machine learning experiments.

<a name="drift"></a>

## <a name="data-drift"></a>Deriva dei dati

Nel contesto dell'apprendimento automatico, la deriva dei dati è la modifica dei dati di input del modello che porta alla riduzione delle prestazioni del modello. È uno dei motivi principali per cui l'accuratezza del modello si riduce nel tempo, quindi il monitoraggio della deriva dei dati aiuta a rilevare i problemi di prestazioni del modello.

Per altre informazioni su come rilevare e avvisare i dati derivanti sui nuovi dati in un set di dati, vedere l'articolo [Creare un monitoraggio del set](how-to-monitor-datasets.md) di dati.

## <a name="next-steps"></a>Passaggi successivi 

+ Creare un set di dati in Azure Machine Learning Studio o con Python SDK [usando questi passaggi.](how-to-create-register-datasets.md)
+ Provare esempi di training sui set di dati con [i blocchi appunti di esempio.](https://aka.ms/dataset-tutorial)
+ Per esempi di deriva dei dati, vedere questa [esercitazione sulla deriva dei dati](https://aka.ms/datadrift-notebook).
