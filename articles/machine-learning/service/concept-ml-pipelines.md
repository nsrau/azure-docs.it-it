---
title: Creare pipeline di machine Learning - servizio Azure Machine Learning
description: In questo articolo viene illustrata la pipeline di machine learning che è possibile compilare con SDK di Azure Machine Learning per Python e i vantaggi dell'uso di pipeline. Le pipeline di Machine learning (ML) vengono utilizzate dai data scientist per creare, ottimizzare e gestire i flussi di lavoro di apprendimento automatico.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: sanpil
author: sanpil
ms.date: 09/24/2018
ms.openlocfilehash: e1181d07ed51b1edf5da68d759d871703bd1540f
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2018
ms.locfileid: "47162781"
---
# <a name="pipelines-and-azure-machine-learning"></a>Pipeline e Azure Machine Learning

In questo articolo viene illustrata la pipeline di machine learning che è possibile compilare con SDK di Azure Machine Learning per Python e i vantaggi dell'uso di pipeline.

## <a name="what-are-machine-learning-pipelines"></a>Cosa sono le pipeline di apprendimento automatico?

Le pipeline di Machine learning (ML) vengono utilizzate dai data scientist per creare, ottimizzare e gestire i flussi di lavoro di apprendimento automatico. Una tipica pipeline include una sequenza di passaggi che trattano le aree seguenti:

+ Preparazione dei dati, ad esempio normalizzazioni e trasformazioni
+ Training dei modelli, ad esempio regolazione e convalida dei parametri hyper
+ Distribuzione e valutazione del modello  

Il diagramma seguente mostra un esempio di pipeline:

[ ![Pipeline di apprendimento automatico nel servizio Azure Machine Learning](./media/concept-ml-pipelines/pipelines.png) ] (. / media/concept-ml-pipelines/machine-learning-pipelines-big.png#lightbox)

## <a name="why-build-pipelines-with-azure-machine-learning"></a>Perché creare delle pipeline con Azure Machine Learning?

[SDK di Azure Machine Learning per Python](#the-python-sdk-for-pipelines) può essere utilizzato per creare pipeline di Machine Learning o anche per inviare e rilevare le esecuzioni della pipeline singole.

Con le pipeline, è possibile ottimizzare il flusso di lavoro con maggiore semplicità, velocità, portabilità e riutilizzo. Quando si compilano le pipeline con Azure Machine Learning, è possibile concentrarsi su ciò che si conosce meglio &mdash; l'apprendimento automatico &mdash; invece dell'infrastruttura distribuita.

Usare i passaggi distinti rende possibile eseguire nuovamente solo i passaggi che è necessario regolare e testare il flusso di lavoro. Un passaggio è un'unità di calcolo nella pipeline. Come illustrato nel diagramma precedente, l'attività di preparazione dei dati può comportare molti passaggi inclusi, ma non limitati alla normalizzazione, trasformazione, convalida e definizione delle funzioni.

Una volta che la pipeline è stata progettata, vi è spesso una maggiore ottimizzazione di tutto il ciclo di training della pipeline. Quando viene nuovamente eseguita una pipeline, l'esecuzione va ai passaggi che devono essere nuovamente eseguiti, come uno script di training aggiornato, e ignora gli elementi che non sono stati modificati. Lo stesso paradigma si applica agli script invariati usati per l'esecuzione del passaggio. 

Con Azure Machine Learning, è possibile utilizzare vari Toolkit e Framework come Microsoft Cognitive Toolkit o TensorFlow per ogni passaggio nella pipeline. Azure coordina tra le varie [destinazioni di calcolo](concept-azure-machine-learning-architecture.md) utilizzate in modo che i dati intermedi possano essere condivisi semplicemente con le destinazioni di calcolo a valle. 

## <a name="key-advantages"></a>Vantaggi principali

I vantaggi principali per la creazione di pipeline per i flussi di lavoro di apprendimento automatico è:

|Vantaggi principali|DESCRIZIONE|
|:-------:|-----------|
|**Esecuzione&nbsp;automatica**|Pianificare alcuni passaggi per eseguire in parallelo o in sequenza in modo affidabile e automatico. Dal momento che la preparazione e la modellazione dei dati può durare giorni o settimane, è ora possibile concentrarsi su altre attività mentre è in esecuzione la pipeline. |
|**Risorse di calcolo miste e diverse**|Usare più pipeline che sono coordinate in modo affidabile tra le varie risorse di archiviazione e servizi di calcolo scalabili ed eterogenei. Passaggi di singole pipeline possono essere eseguiti su diverse destinazioni di calcolo, come HDInsight, GPU Data, Science VM e Databricks, per rendere efficiente l'utilizzo delle opzioni di calcolo disponibili.|
|**Riutilizzo**|Le pipeline possono essere impostate come modello per scenari specifici, come di ripetizione del training e assegnazione di punteggio batch.  Possono essere attivati da sistemi esterni attraverso semplici chiamate REST.|
|**Rilevamento e controllo delle versioni**|Anziché rilevare manualmente i dati e i percorsi del risultato durante l'iterazione, usare le pipeline SDK per assegnare esplicitamente un nome e modificare le origini dei dati, gli input e gli output, oltre a gestire gli script e i dati separatamente per una maggiore produttività.|

## <a name="the-python-sdk-for-pipelines"></a>Python SDK per le pipeline

Usare Python per creare le pipeline di Machine Learning. SDK di Azure Machine Learning offre costrutti imperativi per la sequenziazione e l'abbinamento dei passaggi nelle pipeline quando non è presente alcuna dipendenza dei dati. È possibile interagire con esso nel notebook Jupyter o in un altro IDE preferito. 

Usando le dipendenze dei dati dichiarativi, è possibile ottimizzare le attività. SDK include un framework di moduli precompilati per attività comuni, come il trasferimento dei dati, la creazione di destinazione di calcolo e la pubblicazione del modello. Il framework può essere esteso per modellare le convenzioni implementando passaggi personalizzati che sono riutilizzabili tra le pipeline.

Le pipeline possono essere salvate come modelli e possono essere distribuite in un endpoint REST in modo che sia possibile pianificare i processi di assegnazione dei punteggi o ripetizione del training.

Consultare i [documenti di riferimento Python SDK per le pipeline](http://aka.ms/aml-sdk) e il notebook nella sezione successiva per informazioni su come crearne una personalizzata.

## <a name="example-notebooks"></a>Notebook di esempio
 
Il notebook seguente illustra le pipeline con Azure Machine Learning: `pipeline/pipeline-batch-scoring.ipynb`.
 
Ottenere questi notebook:
 
[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]
