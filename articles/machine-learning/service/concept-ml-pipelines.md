---
title: 'Azure Pipelines: ottimizzazione dei flussi di lavoro di machine learning'
titleSuffix: Azure Machine Learning service
description: In questo articolo viene illustrata la pipeline di machine learning che è possibile compilare con SDK di Azure Machine Learning per Python e i vantaggi dell'uso di pipeline. Le pipeline di Machine learning (ML) vengono utilizzate dai data scientist per creare, ottimizzare e gestire i flussi di lavoro di apprendimento automatico.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: sanpil
author: sanpil
ms.date: 11/07/2018
ms.custom: seodec18
ms.openlocfilehash: a8ead1fedc8c21152b1ef095dbaebe605776b6e3
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/30/2019
ms.locfileid: "55243869"
---
# <a name="build-machine-learning-pipelines-with-the-azure-machine-learning-service"></a>Creare pipeline di apprendimento automatico con il servizio Azure Machine Learning

In questo articolo viene illustrata la pipeline di apprendimento automatico che è possibile compilare con SDK di Azure Machine Learning per Python e i vantaggi dell'uso delle pipeline.

## <a name="what-are-machine-learning-pipelines"></a>Cosa sono le pipeline di apprendimento automatico?

Tramite le pipeline di machine learning (ML), i data scientist, i data engineer e i professionisti IT possono collaborare alle fasi di:
+ Preparazione dei dati, ad esempio normalizzazioni e trasformazioni
+ Training dei modelli
+ Valutazione del modello.
+ Distribuzione 

Il diagramma seguente mostra un esempio di pipeline:

![Pipeline di apprendimento automatico nel servizio Azure Machine Learning](./media/concept-ml-pipelines/pipelines.png)

## <a name="why-build-pipelines-with-azure-machine-learning"></a>Perché creare delle pipeline con Azure Machine Learning?

È possibile usare l'[SDK di Azure Machine Learning per Python](#the-python-sdk-for-pipelines) per creare pipeline di Machine Learning (ML) o anche per inviare e rilevare le esecuzioni delle singole pipeline.

Con le pipeline, è possibile ottimizzare il flusso di lavoro con maggiore semplicità, velocità, portabilità e riutilizzo. Quando si compilano le pipeline con Azure Machine Learning è possibile concentrarsi sulle proprie competenze, l'apprendimento automatico invece sull'infrastruttura.

Usare passaggi distinti rende possibile eseguire nuovamente solo i passaggi necessari durante la regolazione e il test del flusso di lavoro. Un passaggio è un'unità di calcolo nella pipeline. Come illustrato nel diagramma precedente, l'attività di preparazione dei dati può implicare numerosi passaggi. Questi includono tra l'altro la normalizzazione, trasformazione, convalida e definizione delle funzionalità. Le origini dati e i dati intermedi vengono riutilizzati attraverso la pipeline, risparmiando tempo e risorse di elaborazione. 

Dopo che la pipeline è stata progettata, vi è spesso un'ulteriore ottimizzazione di tutto il ciclo di training della pipeline. Quando viene nuovamente eseguita una pipeline, l'esecuzione va ai passaggi che devono essere nuovamente eseguiti, come uno script di training aggiornato, e ignora gli elementi che non sono stati modificati. Lo stesso paradigma si applica agli script invariati usati per l'esecuzione del passaggio. 

Con Azure Machine Learning, è possibile usare vari toolkit e framework come Microsoft Cognitive Toolkit o TensorFlow per ogni passaggio nella pipeline. Azure coordina le varie [destinazioni di calcolo](concept-azure-machine-learning-architecture.md) usate in modo che i dati intermedi possano essere condivisi semplicemente con le destinazioni di calcolo downstream. 

È possibile [tenere traccia delle metriche per gli esperimenti della pipeline](https://docs.microsoft.com/azure/machine-learning/service/how-to-track-experiments) direttamente dal portale di Azure. 

## <a name="key-advantages"></a>Vantaggi principali

I vantaggi principali della creazione di pipeline per i flussi di lavoro di apprendimento automatico sono:

|Vantaggi principali|DESCRIZIONE|
|:-------:|-----------|
|**Esecuzione&nbsp;automatica**|Pianificare alcuni passaggi per eseguire in parallelo o in sequenza in modo affidabile e automatico. Dal momento che la preparazione e la modellazione dei dati può durare giorni o settimane, è ora possibile concentrarsi su altre attività mentre è in esecuzione la pipeline. |
|**Risorse di calcolo miste e diverse**|Usare più pipeline che sono coordinate in modo affidabile tra le varie risorse di archiviazione e servizi di calcolo scalabili ed eterogenei. Passaggi di singole pipeline possono essere eseguiti su diverse destinazioni di calcolo, come HDInsight, GPU Data, Science VM e Databricks. Ciò rende efficiente l'uso delle opzioni di calcolo disponibili.|
|**Riutilizzo**|Le pipeline possono essere impostate come modello per scenari specifici, come la ripetizione del training e assegnazione di punteggio batch. Attivarle da sistemi esterni attraverso semplici chiamate REST.|
|**Rilevamento e controllo delle versioni**|Invece di tenere traccia manualmente dei dati e dei percorsi dei risultati durante l'iterazione, usare l'SDK delle pipeline per denominare esplicitamente e controllare la versione di origini dati, input e output. È anche possibile gestire gli script e i dati separatamente per una maggiore produttività.|

## <a name="the-python-sdk-for-pipelines"></a>Python SDK per le pipeline

Usare Python per creare le pipeline di Machine Learning. SDK di Azure Machine Learning offre costrutti imperativi per la sequenziazione e l'abbinamento dei passaggi nelle pipeline quando non è presente alcuna dipendenza dei dati. È possibile interagire con esso nel notebook Jupyter o in un altro ambiente di sviluppo integrato preferito. 

Usando le dipendenze dei dati dichiarativi, è possibile ottimizzare le attività. L'SDK include un framework di moduli precompilati per attività comuni, come il trasferimento dei dati e la pubblicazione dei modelli. È possibile estendere il framework per modellare le convenzioni implementando passaggi personalizzati che sono riutilizzabili tra le pipeline. Anche le destinazioni di calcolo e le risorse di archiviazione possono essere gestite direttamente dall'SDK.

Le pipeline possono essere salvate come modelli e possono essere distribuite in un endpoint REST in modo che sia possibile pianificare i processi di assegnazione dei punteggi o ripetizione del training.

Consultare i [documenti di riferimento Python SDK per le pipeline](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) e il notebook nella sezione successiva per informazioni su come crearne una personalizzata.

## <a name="example-notebooks"></a>Notebook di esempio
 
I notebook seguenti illustrano le pipeline con Azure Machine Learning: [how-to-use-azureml/machine-learning-pipelines](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines).
 
[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [creare la prima pipeline](how-to-create-your-first-pipeline.md).
