---
title: Opzioni di inserimento dati
titleSuffix: Azure Machine Learning
description: Informazioni sulle opzioni di inserimento dei dati per il training dei modelli di apprendimento automatico.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
author: nibaccam
ms.author: nibaccam
ms.date: 02/26/2020
ms.openlocfilehash: 6b1c671d2079c7d8ab59e9afe981ccef3f58ef27
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79086881"
---
# <a name="data-ingestion-in-azure-machine-learning"></a>Inserimento di dati in Azure Machine LearningData in Azure Machine Learning

In questo articolo vengono appresi i pro e i contro delle opzioni di inserimento dati seguenti disponibili con Azure Machine Learning.In this article, you learn the pros and ss of the following data ingestion options available with Azure Machine Learning. 

1. [Pipeline di Azure Data FactoryAzure Data Factory](#use-azure-data-factory) pipelines
2. [Azure Machine Learning Python SDK](#use-the-python-sdk)

L'inserimento dei dati è il processo in cui i dati non strutturati vengono estratti da una o più origini e quindi preparati per il training dei modelli di apprendimento automatico. È anche dispendioso in termini di tempo, soprattutto se eseguito manualmente e se si dispone di grandi quantità di dati da più origini. L'automazione di questo sforzo consente di liberare risorse e garantire che i modelli utilizzino i dati più recenti e applicabili.

Azure Data Factory (ADF) è creato in modo specifico per estrarre, caricare e trasformare i dati, tuttavia Python SDK consente di sviluppare una soluzione di codice personalizzata per le attività di inserimento dati di base. Se nessuno dei due è quello che ti serve, puoi anche usare ADF e Python SDK insieme per creare un flusso di lavoro di inserimento dati complessivo che soddisfi le tue esigenze. 

## <a name="use-azure-data-factory"></a>Usare Azure Data Factory

[Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction) offre supporto nativo per il monitoraggio delle origini dati e i trigger per le pipeline di inserimento dati.  

La tabella seguente riepiloga i pro e i contro per l'uso di Azure Data Factory per i flussi di lavoro per l'inserimento dei dati.

|Vantaggi|Svantaggi
---|---
Creato appositamente per estrarre, caricare e trasformare i dati.|Attualmente offre un set limitato di attività della pipeline di Azure Data Factory 
Consente di creare flussi di lavoro basati sui dati per l'orchestrazione dello spostamento dei dati e delle trasformazioni su larga scala.|Costoso da costruire e mantenere. Per altre informazioni, vedere La [pagina dei prezzi](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) di Azure Data Factory.See Azure Data Factory's pricing page for more information.
Integrato con vari strumenti di Azure come Azure Databricks e Funzioni di [AzureIntegrated](https://docs.microsoft.com/azure/data-factory/control-flow-azure-function-activity) with various Azure tools like [Azure Databricks](https://docs.microsoft.com/azure/data-factory/transform-data-using-databricks-notebook) and Azure Functions | Non esegue script in modo nativo, ma si basa su un calcolo separato per l'esecuzione di script 
Supporta in modo nativo l'inserimento di dati attivati dall'origine dati| 
La preparazione dei dati e i processi di training del modello sono separati.|
Funzionalità di derivazione dei dati incorporata per i flussi di dati di Azure Data FactoryEmbedded data lineage capability for Azure Data Factory dataflows|
Fornisce [un'interfaccia utente](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal) con un'esperienza di codice basso per gli approcci non di script |

Questi passaggi e il diagramma seguente illustrano il flusso di lavoro per l'inserimento dei dati di Azure Data Factory.These steps and the following diagram illustrate Azure Data Factory's data ingestion workflow.

1. Estrarre i dati dalle relative origini
1. Trasformare e salvare i dati in un contenitore BLOB di output, che funge da archivio dati per Azure Machine LearningTransform and save the data to an output blob container, which serves as data storage for Azure Machine Learning
1. Con i dati preparati archiviati, la pipeline di Azure Data Factory richiama una pipeline di Machine Learning di training che riceve i dati preparati per il training del modello


    ![Inserimento dati ADF](media/concept-data-ingestion/data-ingest-option-one.svg)
    
Informazioni su come creare una pipeline di inserimento dati per Machine Learning con [Azure Data Factory.](how-to-data-ingest-adf.md)

## <a name="use-the-python-sdk"></a>Usare Python SDK 

Con [Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml)è possibile incorporare attività di inserimento dati in un passaggio della pipeline di Azure Machine Learning.With the Python SDK , you can incorporat data iningon tasks into an [Azure Machine Learning pipeline](how-to-create-your-first-pipeline.md) step.

Nella tabella seguente vengono riepilogati i pro e con per l'utilizzo dell'SDK e di un passaggio delle pipeline di Configurazione guidata impostazioni di configurazione dei dati per le attività di inserimento dei dati.

Vantaggi| Svantaggi
---|---
Configurare i propri script Python | Non supporta in modo nativo l'attivazione della modifica dell'origine dati. Richiede app per la logica o implementazioni di funzioni di AzureRequires Logic App or Azure Function implementations
Preparazione dei dati come parte di ogni esecuzione del training del modelloData preparation as part of every model training execution|Richiede competenze di sviluppo per creare uno script di inserimento dati
Supporta gli script di preparazione dei dati in varie destinazioni di calcolo, tra cui il [calcolo](concept-compute-target.md#azure-machine-learning-compute-managed) di Azure Machine LearningSupports data preparation scripts on various compute targets, including Azure Machine Learning compute |Non fornisce un'interfaccia utente per la creazione del meccanismo di inserimento

Nel diagramma seguente la pipeline di Azure Machine Learning è costituita da due passaggi: inserimento di dati e training del modello. Il passaggio di inserimento dati include attività che possono essere eseguite utilizzando le librerie Python e Python SDK, ad esempio l'estrazione di dati da origini locali/web e trasformazioni di dati di base, ad esempio l'imputazione dei valori mancanti. La fase di training usa quindi i dati preparati come input per lo script di training per eseguire il training del modello di apprendimento automatico. 

![Pipeline di Azure: inserimento dati SDKAzure pipeline : SDK data ingestion](media/concept-data-ingestion/data-ingest-option-two.png)

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come creare una pipeline di inserimento dati per Machine Learning con [Azure Data Factory](how-to-data-ingest-adf.md)
* Informazioni su come automatizzare e gestire i cicli di vita di sviluppo delle pipeline di inserimento dati con le pipeline di [Azure.](how-to-cicd-data-ingestion.md)
