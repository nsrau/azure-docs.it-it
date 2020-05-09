---
title: Proteggere l'accesso ai dati nel cloud
titleSuffix: Azure Machine Learning
description: Informazioni su come connettersi in modo sicuro ai dati da Azure Machine Learning e come usare i set di dati e gli archivi dati per le attività ML. Gli archivi dati possono archiviare i dati da un BLOB di Azure, Azure Data Lake generazione 1 & 2, database SQL, databricks,...
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
author: nibaccam
ms.author: nibaccam
ms.date: 04/24/2020
ms.openlocfilehash: e41d00a31a0065e2923259cbb0bb36986cbb2ec8
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2020
ms.locfileid: "82995059"
---
# <a name="secure-data-access-in-azure-machine-learning"></a>Proteggere l'accesso ai dati in Azure Machine Learning

Azure Machine Learning semplifica la connessione ai dati nel cloud.  Fornisce un livello di astrazione sul servizio di archiviazione sottostante, quindi è possibile accedere in modo sicuro ai dati e utilizzarli senza dover scrivere codice specifico per il tipo di archiviazione. Azure Machine Learning fornisce anche le funzionalità dei dati seguenti:

*    Controllo delle versioni e rilevamento della derivazione dei dati
*    Assegnazione di etichette ai dati 
*    Monitoraggio della deriva dei dati
*    Interoperabilità con Pandas e dataframe di Spark

## <a name="data-workflow"></a>Flusso di lavoro dei dati

Quando si è pronti per usare i dati nella soluzione di archiviazione basata su cloud, si consiglia il seguente flusso di lavoro di distribuzione dei dati. Questo flusso di lavoro presuppone che si disponga di un [account di archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal) e di dati in un servizio di archiviazione basato su cloud in Azure. 

1. Creare un [archivio dati Azure Machine Learning](#datastores) per archiviare le informazioni di connessione nell'archiviazione di Azure.

2. Da tale archivio dati, creare un [set di dati Azure Machine Learning](#datasets) in modo che punti a uno o più file specifici nell'archivio sottostante. 

3. Per usare tale set di dati nell'esperimento di Machine Learning, è possibile
    1. Montarlo nella destinazione di calcolo dell'esperimento per il training del modello.

        **OR** 

    1. È sufficiente utilizzarlo direttamente in soluzioni Azure Machine Learning come, esecuzioni di esperimenti automatizzati di Machine Learning (automatico ML), pipeline di Machine Learning o [progettazione Azure Machine Learning](concept-designer.md).

4. Creare [monitoraggi del set](#data-drift) di dati per il set di dati di output del modello per rilevare la tendenza dei dati. 

5. Se viene rilevata la deriva dei dati, aggiornare il set di dati di input e ripetere il training del modello di conseguenza.

Il diagramma seguente fornisce una dimostrazione visiva di questo flusso di lavoro consigliato.

![Data-Concept-diagramma](./media/concept-data/data-concept-diagram.svg)

## <a name="datastores"></a>Archivi dati

Azure Machine Learning archivi dati conservano in modo sicuro le informazioni di connessione per l'archiviazione di Azure, quindi non è necessario codificarle negli script. [Registrare e creare un archivio](how-to-access-data.md) dati per connettersi facilmente all'account di archiviazione e accedere ai dati nel servizio di archiviazione di Azure sottostante. 

Servizi di archiviazione basati su cloud supportati in Azure che possono essere registrati come archivi dati:

+ Contenitore BLOB di Azure
+ Condivisione file di Azure
+ Azure Data Lake
+ Azure Data Lake Gen2
+ database SQL di Azure
+ Database di Azure per PostgreSQL
+ File system di Databricks
+ Database di Azure per MySQL

## <a name="datasets"></a>Set di dati

Azure Machine Learning set di dati sono riferimenti che puntano ai dati nel servizio di archiviazione. Non si tratta di copie dei dati, quindi non sono previsti costi di archiviazione aggiuntivi. Per interagire con i dati nell'archiviazione, [creare un set](how-to-create-register-datasets.md) di dati per creare un pacchetto dei dati in un oggetto utilizzabile per le attività di machine learning. Registrare il set di dati nell'area di lavoro per condividerlo e riutilizzarlo in diversi esperimenti senza complessi di inserimento di dati.

I set di dati possono essere creati da file locali, URL pubblici, set di dati [aperti di Azure](https://azure.microsoft.com/services/open-datasets/)o servizi di archiviazione di Azure tramite archivi dati. Per creare un set di dati da un dataframe Pandas in memoria, scrivere i dati in un file locale, ad esempio un parquet, e creare il set di dati da tale file.  

Sono supportati 2 tipi di set di impostazioni: 
+ Un [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) rappresenta i dati in formato tabulare analizzando il file o l'elenco di file fornito. È possibile caricare un TabularDataset in un dataframe Pandas o Spark per eseguire ulteriori operazioni di manipolazione e pulizia. Per un elenco completo dei formati di dati da cui è possibile creare TabularDatasets, vedere la [classe TabularDatasetFactory](https://aka.ms/tabulardataset-api-reference).

+ Un [filedataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py) fa riferimento a uno o più file negli archivi dati o negli URL pubblici. È possibile [scaricare o montare i file](how-to-train-with-datasets.md#mount-files-to-remote-compute-targets) a cui fa riferimento filedatasets nella destinazione di calcolo.

Le funzionalità di set di impostazioni aggiuntive sono disponibili nella documentazione seguente:

+ Derivazione del set [di dati Version e Track](how-to-version-track-datasets.md) .
+ Consente di [monitorare il set](how-to-monitor-datasets.md) di dati per semplificare il rilevamento della tendenza.    

## <a name="work-with-your-data"></a>Usare i dati

Con i set di elementi, è possibile eseguire numerose attività di machine learning grazie a una perfetta integrazione con Azure Machine Learning funzionalità. 

+ Creare un [progetto di assegnazione di etichette dati](#label).
+ Eseguire il training di modelli di Machine Learning:
     + [esperimenti di Machine Learning automatici](how-to-use-automated-ml-for-ml-models.md)
     + [finestra di progettazione](tutorial-designer-automobile-price-train-score.md#import-data)
     + [Notebook](how-to-train-with-datasets.md)
     + [Pipeline di Azure Machine Learning](how-to-create-your-first-pipeline.md)
+ Accedere ai set di impostazioni per l'assegnazione dei punteggi con l' [inferenza batch](how-to-use-parallel-run-step.md) nelle [pipeline di Machine Learning](how-to-create-your-first-pipeline.md).
+ Configurare un monitoraggio del set di [dati](#drift) per il rilevamento della deriva.

<a name="label"></a>

## <a name="data-labeling"></a>Assegnazione di etichette ai dati

L'etichettatura di grandi quantità di dati nei progetti di Machine Learning è spesso stata considerata un'attività molto complessa. Quelli con un componente visione artificiale, ad esempio la classificazione delle immagini o il rilevamento di oggetti, richiedono in genere migliaia di immagini e etichette corrispondenti.

Azure Machine Learning offre una posizione centrale per creare, gestire e monitorare i progetti di etichettatura. I progetti di etichettatura consentono di coordinare i dati, le etichette e i membri del team, per una gestione più efficiente delle attività di etichettatura. Le attività attualmente supportate sono la classificazione delle immagini, multi-etichetta o multi-classe, e l'identificazione di oggetti tramite i riquadri di selezione.

Creare un [progetto di assegnazione di etichette dei dati](how-to-create-labeling-projects.md)e restituire un set di dati da usare negli esperimenti di machine learning.

<a name="drift"></a>

## <a name="data-drift"></a>Drift dei dati

Nel contesto di Machine Learning, la deriva dei dati è la modifica dei dati di input del modello che comporta un peggioramento delle prestazioni del modello. Si tratta di uno dei motivi principali per cui l'accuratezza del modello diminuisce nel tempo, di conseguenza il monitoraggio della deriva dei dati consente di rilevare problemi di prestazioni del modello.

Vedere l'articolo [creare un monitor del set](how-to-monitor-datasets.md) di dati per altre informazioni su come rilevare e inviare avvisi ai dati in un set di dati.

## <a name="next-steps"></a>Passaggi successivi 

+ Creare un set di dati in Azure Machine Learning Studio o con Python SDK [seguendo questa procedura.](how-to-create-register-datasets.md)
+ Provare gli esempi di training del set di dati con i [notebook di esempio](https://aka.ms/dataset-tutorial).
+ Per esempi di deriva dei dati, vedere questa [esercitazione sulla deviazione dei dati](https://aka.ms/datadrift-notebook).
