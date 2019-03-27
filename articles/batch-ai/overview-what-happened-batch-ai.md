---
title: Che cosa accadrà ad Azure Batch per intelligenza artificiale? | Microsoft Docs
description: Informazioni su ciò che accadrà ad Azure Batch per intelligenza artificiale e all'opzione di calcolo del servizio Azure Machine Learning.
ms.service: batch-ai
services: batch-ai
ms.topic: overview
ms.author: jmartens
author: j-martens
ms.date: 2/28/2019
ms.openlocfilehash: edd6a7e5f385d766d51631d77f5889233af2469a
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57194503"
---
# <a name="whats-happening-to-azure-batch-ai"></a>Che cosa accadrà ad Azure Batch per intelligenza artificiale?

**Il servizio Azure Batch per intelligenza artificiale verrà ritirato nel mese di marzo.** Le funzionalità di Batch per intelligenza artificiale per il training e l'assegnazione dei punteggi su larga scala sono ora disponibili nel [servizio Azure Machine Learning](../machine-learning/service/overview-what-is-azure-ml.md), diventato disponibile a livello generale il 4 dicembre 2018.

Oltre a molte altre funzionalità di apprendimento automatico, il servizio Azure Machine Learning include una destinazione di calcolo gestita e basata sul cloud per il training, la distribuzione e l'assegnazione dei punteggi ai modelli di Machine Learning. Questa destinazione di calcolo viene chiamata [ambiente di calcolo di Azure Machine Learning](../machine-learning/service/how-to-set-up-training-targets.md#amlcompute). [Avviare la migrazione e usarlo subito](#migrate). È possibile interagire con il servizio Azure Machine Learning tramite gli [SDK di Python](../machine-learning/service/quickstart-create-workspace-with-python.md), l'interfaccia della riga di comando e il [portale di Azure](../machine-learning/service/quickstart-get-started.md).

## <a name="support-timeline"></a>Sequenza temporale del supporto

Attualmente è possibile usare le sottoscrizioni esistenti di Azure Batch per intelligenza artificiale come prima. Tuttavia, non sono possibili **nuove sottoscrizioni** e non verranno effettuati nuovi investimenti.

A partire dal &nbsp;31&nbsp; marzo 2019, le sottoscrizioni inutilizzate di Batch per intelligenza artificiale non funzioneranno più.

## <a name="compare-to-azure-machine-learning"></a>Confronto con Azure Machine Learning
Il servizio Azure Machine Learning è un servizio cloud che è possibile usare per eseguire il training di modelli di Machine Learning, distribuirli, automatizzarli e gestirli, il tutto su vasta scala, grazie all'ampia portata del cloud. Per una descrizione completa, vedere il [servizio Azure Machine Learning in questa panoramica](../machine-learning/service/overview-what-is-azure-ml.md).
 

Un tipico ciclo di vita di sviluppo del modello prevede la preparazione dei dati, il training e la sperimentazione e una fase di distribuzione. Questo ciclo end-to-end può essere orchestrato usando le pipeline di Machine Learning.

![Diagramma di flusso](./media/overview-what-happened-batch-ai/lifecycle.png)


[Altre informazioni sul funzionamento del servizio e sui relativi concetti principali](../machine-learning/service/concept-azure-machine-learning-architecture.md). Molti dei concetti nel flusso di lavoro del training del modello sono simili ai concetti esistenti in Batch per intelligenza artificiale. 

In particolare, di seguito è riportata una mappatura di tali concetti:
 
|Servizio Batch per intelligenza artificiale|  Servizio Azure Machine Learning|
|:--:|:---:|
|Area di lavoro|Area di lavoro|
|HDInsight|   Ambiente di calcolo di tipo `AmlCompute`|
|File server|Archivi dati|
|Sperimentazioni|Sperimentazioni|
|Processi|Esecuzioni (consente esecuzioni annidate)|
 
Di seguito è riportata un'altra visualizzazione della stessa tabella che consente di rappresentare in altro modo gli stessi concetti:
 
### <a name="batch-ai-hierarchy"></a>Gerarchia di Batch per intelligenza artificiale
![Diagramma di flusso](./media/overview-what-happened-batch-ai/batchai-heirarchy.png) 
 
### <a name="azure-machine-learning-service-hierarchy"></a>Gerarchia del servizio Azure Machine Learning
![Diagramma di flusso](./media/overview-what-happened-batch-ai/azure-machine-learning-service-heirarchy.png) 

## <a name="platform-capabilities"></a>Funzionalità delle piattaforme
Il servizio Azure Machine Learning offre un'ampia gamma di nuove funzionalità, tra cui uno stack end-to-end di training->distribuzione che è possibile usare per lo sviluppo dell'intelligenza artificiale senza dover gestire le risorse di Azure. Questa tabella mette a confronto il supporto delle funzionalità per il training tra i due servizi.

|Funzionalità|Servizio Batch per intelligenza artificiale|Servizio Azure Machine Learning|
|-------|:-------:|:-------:|
|Scelta delle dimensioni delle macchine virtuali |CPU/GPU    |CPU/GPU. Supporta anche FPGA per l'inferenza|
|Cluster idoneo per l'intelligenza artificiale (driver, docker e così via)|  Sì |Sì|
|Preparazione nodo| Sì|    No |
|Scelta delle famiglie di sistemi operativi   |Parziale    |No |
|Macchine virtuali dedicate e con priorità bassa  |Sì    |Sì|
|Ridimensionamento automatico   |Sì    |Sì (per impostazione predefinita)|
|Tempo di attesa per il ridimensionamento automatico  |No  |Sì|
|SSH    |Sì|   Sì|
|Montaggio a livello di cluster |Sì (condivisioni file, BLOB, NFS, personalizzato)   |Sì (montaggio o download dell'archivio dati)|
|Training distribuito|  Sì |Sì|
|Modalità di esecuzione processi|    Macchina virtuale o contenitore|    Contenitore|
|Immagine del contenitore personalizzata|    Sì |Sì|
|Qualsiasi Toolkit    |Sì    |Sì (esecuzione di uno script Python)|
|Preparazione processi|    Sì |Non ancora|
|Montaggio a livello di processo |Sì (condivisioni file, BLOB, NFS, personalizzato)   |Sì (condivisioni file, BLOB)|
|Monitoraggio processi     |Tramite GetJob|    Tramite cronologia di esecuzione (informazioni più dettagliate, runtime personalizzato per il push di altre metriche)|
|Recupero di log di processo e di file/modelli |   Tramite ListFiles e API di archiviazione  |Tramite il servizio Artifact|
 |Supporto di Tensorboard   |No |    Sì|
|Quote a livello di famiglia di macchine virtuali |Sì    |Sì (con la capacità precedente ereditata)|
 
Oltre alla tabella precedente, nel servizio Azure Machine Learning esistono funzionalità che tradizionalmente non erano supportate in Batch per intelligenza artificiale.

|Funzionalità|Servizio Batch per intelligenza artificiale|Servizio Azure Machine Learning|
|-------|:-------:|:-------:|
|Preparazione dell'ambiente    |No  |Sì (preparazione ambiente Conda e caricamento in Registro Azure Container)|
|Ottimizzazione degli iperparametri  |No |    Sì|
|Gestione di modelli   |No  |Sì|
|Operazionalizzazione/Distribuzione| No   |Tramite servizio Azure Kubernetes e Istanze di Azure Container|
|Preparazione dei dati   |No  |Sì|
|Destinazioni di calcolo    |Macchine virtuali di Azure  |Locale, Batch per intelligenza artificiale (come AmlCompute), DataBricks, HDInsight|
|Funzionalità automatizzate di Machine Learning |No |    Sì|
|Pipeline  |No  |Sì|
|Punteggio batch  |Sì    |Sì|
|Supporto portale/interfaccia della riga di comando|    Sì |Sì|


## <a name="programming-interfaces"></a>Interfacce di programmazione

Questa tabella presenta le varie interfacce di programmazione disponibili per ogni servizio.
    
|Funzionalità|Servizio Batch per intelligenza artificiale|Servizio Azure Machine Learning|
|-------|:-------:|:-------:|
|SDK    |Java, C#, Python, Nodejs   |Python (sia basato sulla configurazione di esecuzione che sull'oggetto Estimator per i framework comuni)|
|CLI    |Sì    |Non ancora|
|Portale di Azure   |Sì    |Sì (tranne l'invio di processi)|
|API REST   |Sì    |Sì ma distribuita tra i microservizi|


L'aggiornamento dal servizio Batch per intelligenza artificiale in anteprima al servizio Azure Machine Learning nella versione di disponibilità generale offre un'esperienza migliore attraverso concetti più facili da usare come gli oggetti Estimator e gli archivi dati. Garantisce anche contratti di servizio per Azure con disponibilità generale e assistenza clienti.

Il servizio Azure Machine Learning introduce anche nuove funzionalità come l'apprendimento automatico automatizzato, l'ottimizzazione degli iperparametri e le pipeline di Machine Learning, che sono utili nella maggior parte dei carichi di lavoro di intelligenza artificiale su larga scala. La possibilità di distribuire un modello con training senza ricorrere a un servizio separato consente di completare il ciclo di data science dalla preparazione dei dati (con l'SDK di preparazione dei dati) fino all'operazionalizzazione e al monitoraggio dei modelli.

<a name="migrate"></a>
## <a name="migrate"></a>Migrazione

Per informazioni su come eseguire la migrazione e sul mapping tra il codice usato nel servizio Batch e il codice del servizio Azure Machine Learning, vedere [Eseguire la migrazione da Azure Batch per intelligenza artificiale al servizio Azure Machine Learning](how-to-migrate.md).

## <a name="get-support"></a>Supporto

Azure Batch per intelligenza artificiale verrà ritirato il 31 marzo e non è possibile effettuare la registrazione di nuove sottoscrizioni nel servizio, a meno che non venga inserito nell'elenco elementi consentiti generando un'eccezione tramite il supporto.  Contattare Microsoft in [Anteprima del training di Azure Batch per intelligenza artificiale](mailto:AzureBatchAITrainingPreview@service.microsoft.com) per qualsiasi domanda o per commenti e suggerimenti durante la migrazione al servizio Azure Machine Learning.

Il servizio Azure Machine Learning è ora disponibile a livello generale. Ciò significa che viene fornito con un contratto di servizio e vari piani di supporto tra cui scegliere.

I prezzi per l'uso dell'infrastruttura di Azure sia tramite il servizio Batch per intelligenza artificiale che tramite il servizio Azure Machine Learning non dovrebbero variare, poiché in entrambi i casi viene addebitato solo il prezzo per le risorse di calcolo sottostanti. Per altre informazioni, vedere il [calcolatore prezzi](https://azure.microsoft.com/pricing/details/machine-learning-service/).

Vedere la disponibilità a livello di area tra i due servizi nel [portale di Azure](https://azure.microsoft.com/global-infrastructure/services/?products=batch-ai,machine-learning-service&regions=all).


## <a name="next-steps"></a>Passaggi successivi

+ Leggere le informazioni su [come eseguire la migrazione](how-to-migrate.md) e sul mapping tra il codice usato nel servizio Batch e il codice del servizio Azure Machine Learning.

+ Leggere la [panoramica del servizio Azure Machine Learning](../machine-learning/service/overview-what-is-azure-ml.md).

+ [Configurare una destinazione di calcolo per il training del modello](../machine-learning/service/how-to-set-up-training-targets.md) con il servizio Azure Machine Learning.

+ Rivedere la [roadmap per Azure](https://azure.microsoft.com/updates/) per informazioni su altri aggiornamenti del servizio di Azure.
