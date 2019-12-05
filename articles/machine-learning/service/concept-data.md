---
title: Dati in Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Informazioni su come Azure Machine Learning interagisce con i dati e su come vengono usati negli esperimenti di machine learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
author: nibaccam
ms.author: nibaccam
ms.date: 12/09/2019
ms.openlocfilehash: 4ea0f7092862ef910e9f44e81cc4f5f40849bc70
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74822955"
---
# <a name="data-access-in-azure-machine-learning"></a>Accesso ai dati in Azure Machine Learning

Questo articolo illustra le soluzioni di gestione e integrazione dei dati di Azure Machine Learning per le attività di machine learning. Questo articolo presuppone che sia già stato creato un [account di archiviazione di Azure](https://docs.microsoft.comazure/storage/common/storage-quickstart-create-account?tabs=azure-portal) e un servizio di archiviazione di [Azure](https://docs.microsoft.com/azure/storage/common/storage-introduction).

Quando si è pronti per usare i dati nella risorsa di archiviazione, è consigliabile

1. Creare un archivio dati Azure Machine Learning.
2. Dall'archivio dati creare un set di dati Azure Machine Learning. 
3. Usare il set di dati nell'esperimento di machine learning da uno dei due 
    1. Montaggio della destinazione di calcolo dell'esperimento per il training del modello

        **OR** 

    1. Il suo utilizzo diretto in soluzioni Azure Machine Learning come l'esecuzione di esperimenti automatizzati di Machine Learning, le pipeline di machine learning e la [finestra di progettazione di Azure Machine Learning](concept-designer.md).
4. Creare monitoraggi del set di dati per i set di dati di input e output del modello per rilevare la tendenza ai dati. 
5. Se viene rilevata la deriva dei dati, aggiornare il set di dati e ripetere il training del modello di conseguenza.

Il diagramma seguente fornisce una dimostrazione visiva di questo flusso di lavoro di accesso ai dati consigliato.

![Data-Concept-diagramma](media/concept-data/data-concept-diagram.svg)

## <a name="access-data-in-storage"></a>Accedere ai dati nell'archiviazione

Per accedere ai dati nell'account di archiviazione, Azure Machine Learning offre archivi dati e set di dati. Gli archivi dati forniscono un livello di astrazione sul servizio di archiviazione. Questo facilita la sicurezza e la facilità di accesso alla risorsa di archiviazione, poiché le informazioni di connessione vengono mantenute nell'archivio dati e non vengono esposte negli script. I set di dati puntano al file o ai file specifici nell'archivio sottostante che si vuole usare per l'esperimento di machine learning. Insieme, i datastore e i set di dati offrono un flusso di lavoro di distribuzione di dati protetto, scalabile e riproducibile per le attività di machine learning.

### <a name="datastores"></a>Archivi dati

Un archivio dati Azure Machine Learning è un'astrazione di archiviazione sui servizi di archiviazione di Azure. [Registrare e creare un archivio](how-to-access-data.md) dati per connettersi facilmente all'account di archiviazione di Azure e accedere ai dati nei servizi di archiviazione di Azure sottostanti.

Servizi di archiviazione di Azure supportati che possono essere registrati come archivi dati:
+ Contenitore BLOB di Azure
+ Condivisione file di Azure
+ Azure Data Lake
+ Azure Data Lake Gen2
+ database SQL di Azure
+ Database di Azure per PostgreSQL
+ File system di Databricks
+ Database di Azure per MySQL

### <a name="datasets"></a>Set di dati

[Creare un set](how-to-create-register-datasets.md) di dati Azure Machine Learning per interagire con i dati negli archivi dati e comprimere i dati in un oggetto utilizzabile per le attività di machine learning. Registrare il set di dati nell'area di lavoro per condividerlo e riutilizzarlo in diversi esperimenti senza complessi di inserimento di dati.

I set di dati possono essere creati da file locali, URL pubblici, set di dati [aperti di Azure](#open)o file specifici negli archivi dati. Per creare un set di dati da un dataframe Pandas in memoria, scrivere i dati in un file locale, ad esempio un volume CSV, e creare il set di dati da tale file. I set di dati non sono copie dei dati, ma sono riferimenti che puntano ai dati del servizio di archiviazione, pertanto non è stato generato alcun costo aggiuntivo per l'archiviazione. 

Il diagramma seguente mostra che, se non si dispone di un servizio di archiviazione di Azure, è possibile creare un set di dati direttamente da file locali, URL pubblici o un set di dati aperto di Azure. In questo modo si connette il set di dati all'archivio dati predefinito creato automaticamente con l' [area di lavoro Azure Machine Learning](concept-workspace.md)dell'esperimento.

![Data-Concept-diagramma](media/concept-data/dataset-workflow.svg)

Le funzionalità di set di impostazioni aggiuntive sono disponibili nella documentazione seguente:

+ Derivazione del set [di dati Version e Track](how-to-version-track-datasets.md) .
+ Consente di [monitorare il set](how-to-monitor-datasets.md) di dati per semplificare il rilevamento della tendenza.
+  Per la documentazione sui due tipi di set di impostazioni, vedere gli argomenti seguenti:
    + [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) rappresenta i dati in formato tabulare analizzando il file o l'elenco di file fornito. Che consente di materializzare i dati in un frame di dati Pandas o Spark per ulteriori operazioni di manipolazione e pulizia. Per un elenco completo dei file da cui è possibile creare TabularDatasets, vedere la [classe TabularDatasetFactory](https://aka.ms/tabulardataset-api-reference).

    + [Filedataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py) fa riferimento a uno o più file negli archivi dati o negli URL pubblici. Con questo metodo è possibile scaricare o montare i file di cui si sceglie la destinazione di calcolo come oggetto filedataset.

## <a name="work-with-your-data"></a>Usare i dati

Con i set di elementi, è possibile eseguire numerose attività di machine learning grazie a una perfetta integrazione con Azure Machine Learning funzionalità. 

+ Eseguire il [training di modelli di Machine Learning](how-to-train-with-datasets.md).
+ Utilizzare set di impostazioni in 
     + [esperimenti di Machine Learning automatici](how-to-create-portal-experiments.md)
     + [finestra di progettazione](tutorial-designer-automobile-price-train-score.md#import-data) 
+ Accedere ai set di impostazioni per l'assegnazione dei punteggi con l'inferenza batch nelle [pipeline di Machine Learning](how-to-create-your-first-pipeline.md).
+ Creare un [progetto di assegnazione di etichette dati](#label).
+ Configurare un monitoraggio del set di [dati](#drift) per il rilevamento della deriva.

<a name="open"></a>

## <a name="azure-open-datasets"></a>Set di dati aperti di Azure

[Azure Open Datasets](how-to-create-register-datasets.md#create-datasets-with-azure-open-datasets) include set di dati pubblici curati che è possibile usare per aggiungere caratteristiche specifiche dello scenario alle soluzioni di Machine Learning e realizzare modelli più accurati. I set di impostazioni aperti si trovano nel cloud in Microsoft Azure e sono integrati nel Azure Machine Learning. È anche possibile accedere ai set di dati tramite API e usarli in altri prodotti, come Power BI e Azure Data Factory.

I set di dati aperti di Azure includono i dati di dominio pubblico per il meteo, il censimento, le festività, la sicurezza pubblica e la posizione che consentono di eseguire il training di modelli di machine learning e di arricchire le soluzioni È anche possibile condividere i propri set di dati pubblici in Azure Open Datasets.

<a name="label"></a>

## <a name="data-labeling"></a>Assegnazione di etichette ai dati

L'etichettatura di grandi quantità di dati nei progetti di Machine Learning è spesso stata considerata un'attività molto complessa. Quelli con un componente visione artificiale, ad esempio la classificazione delle immagini o il rilevamento di oggetti, richiedono in genere migliaia di immagini e etichette corrispondenti.

Azure Machine Learning offre una posizione centrale per creare, gestire e monitorare i progetti di etichettatura. I progetti di etichettatura consentono di coordinare i dati, le etichette e i membri del team, per una gestione più efficiente delle attività di etichettatura. Le attività attualmente supportate sono la classificazione delle immagini, multi-etichetta o multi-classe, e l'identificazione di oggetti tramite i riquadri di selezione.

+ Creare un [progetto di assegnazione di etichette dei dati](how-to-create-labeling-projects.md)e restituire un set di dati da usare negli esperimenti di machine learning.

<a name="drift"></a>

## <a name="data-drift"></a>Drift dei dati

Nel contesto di Machine Learning, la deriva dei dati è la modifica dei dati di input del modello che comporta un peggioramento delle prestazioni del modello. Si tratta di uno dei motivi principali per cui l'accuratezza del modello diminuisce nel tempo, di conseguenza il monitoraggio della deriva dei dati consente di rilevare problemi di prestazioni del modello.
Vedere l'articolo [creare un monitor del set](how-to-monitor-datasets.md) di dati per altre informazioni su come rilevare e inviare avvisi ai dati in un set di dati.

## <a name="next-steps"></a>Passaggi successivi 

+ Creare un set di dati in Azure Machine Learning Studio o con Python SDK, [seguire questa procedura.](how-to-create-register-datasets.md)
+ Provare gli esempi di training del set di dati con i [notebook di esempio](https://aka.ms/dataset-tutorial).
+ Per esempi di deriva dei dati, vedere questa [esercitazione sulla deviazione dei dati](https://aka.ms/datadrift-notebook).