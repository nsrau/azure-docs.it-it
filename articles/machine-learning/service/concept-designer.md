---
title: Sviluppare modelli ML con progettazione
titleSuffix: Azure Machine Learning
description: Informazioni su termini, concetti e flussi di lavoro che costituiscono la finestra di progettazione per Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 11/04/2019
ms.openlocfilehash: ee97322e58fe7ab3a1474f55c6294822b8ce90da
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73517865"
---
# <a name="what-is-azure-machine-learning-designer-preview"></a>Che cos'è Azure Machine Learning Designer (anteprima)? 
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-enterprise-sku.md)]

La finestra di progettazione di Azure Machine Learning consente di preparare i dati, eseguire il training, testare, distribuire, gestire e tenere traccia dei modelli di Machine Learning senza scrivere codice.

Non è necessaria alcuna programmazione. i [set di impostazioni](#datasets) e i [moduli](#module) vengono collegati visivamente per costruire il modello.

La finestra di progettazione USA l' [area di lavoro](concept-workspace.md) Azure Machine Learning per:

+ Creare, modificare ed eseguire [pipeline](#pipeline) nell'area di lavoro.
+ Accedere ai [set di impostazioni](#datasets).
+ Usare le [risorse di calcolo](#compute) nell'area di lavoro per eseguire la pipeline. 
+ Registrare i [modelli](concept-azure-machine-learning-architecture.md#models).
+ [Pubblicare](#publish) pipeline come endpoint REST.
+ [Distribuire](#deployment) i modelli come endpoint della pipeline (per inferenza batch) o endpoint in tempo reale sulle risorse di calcolo nell'area di lavoro.

![Panoramica della finestra di progettazione](media/ui-concept-visual-interface/overview.png)

## <a name="workflow"></a>Flusso di lavoro

La finestra di progettazione offre un'area di disegno interattiva visiva per compilare, testare ed eseguire rapidamente un'iterazione su un modello. 

+ È possibile trascinare e rilasciare i [set di impostazioni](#datasets) e i [moduli](#module) nell'area di disegno.
+ Connettere i moduli per formare una [pipeline](#pipeline).
+ Eseguire la pipeline usando la risorsa di calcolo dell'area di lavoro del servizio Machine Learning.
+ Eseguire l'iterazione sulla progettazione del modello modificando la pipeline ed eseguendo di nuovo l'operazione.
+ Quando si è pronti, convertire la **pipeline di training** in una **pipeline di inferenza**.
+ [Pubblicare](#publish) la pipeline come endpoint REST se si vuole inviarla nuovamente senza il codice Python costruito.
+ [Distribuire](#deployment) la pipeline di inferenza come endpoint della pipeline o endpoint in tempo reale in modo che sia possibile accedere al modello da altri.

## <a name="pipeline"></a>Pipeline

Creare una [pipeline](concept-azure-machine-learning-architecture.md#ml-pipelines) ml da zero oppure usare una pipeline di esempio esistente come modello. Ogni volta che si esegue una pipeline, gli elementi vengono archiviati nell'area di lavoro. Le esecuzioni di pipeline sono raggruppate in [esperimenti](concept-azure-machine-learning-architecture.md#experiments).

Una pipeline è costituita da set di impostazioni e moduli analitici, che è possibile connettere insieme per costruire un modello. In particolare, una pipeline valida presenta queste caratteristiche:

* I set di impostazioni possono essere connessi solo ai moduli.
* I moduli possono essere connessi a set di impostazioni o ad altri moduli.
* Tutte le porte di input per i moduli devono disporre di una connessione al flusso di dati.
* È necessario impostare tutti i parametri obbligatori per ogni modulo.


Per informazioni su come iniziare a usare la finestra di progettazione, vedere [esercitazione: stimare il prezzo di un'automobile con la finestra di progettazione](tutorial-designer-automobile-price-train-score.md).

## <a name="datasets"></a>Set di dati

Un set di dati di Machine Learning semplifica l'accesso e l'uso dei dati. Nella finestra di progettazione sono inclusi alcuni set di impostazioni di esempio che consentono di sperimentare. È possibile [registrare](./how-to-create-register-datasets.md) più set di impostazioni in modo che siano necessari.

## <a name="module"></a>Modulo

Un modulo è un algoritmo che è possibile applicare ai dati. La finestra di progettazione ha una serie di moduli che variano dalle funzioni di ingresso dei dati ai processi di training, assegnazione di punteggi e convalida.

Un modulo può avere un set di parametri che è possibile usare per configurare gli algoritmi interni del modulo. Quando si seleziona un modulo nell'area di disegno, i parametri del modulo vengono visualizzati nel riquadro proprietà a destra dell'area di disegno. È possibile modificare i parametri in questo riquadro per ottimizzare il modello.

![Proprietà del modulo](media/ui-concept-visual-interface/properties.png)

Per informazioni sull'esplorazione della libreria di algoritmi di machine learning disponibili, vedere [Cenni preliminari sui moduli di & algoritmi](../algorithm-module-reference/module-reference.md)

## <a name="compute"></a>Risorse di calcolo

Usare le risorse di calcolo dall'area di lavoro per eseguire la pipeline e ospitare i modelli distribuiti come endpoint in tempo reale o endpoint della pipeline (per inferenza batch). Le destinazioni di calcolo supportate sono:

| Destinazione del calcolo | Formazione | Distribuzione |
| ---- |:----:|:----:|
| Ambiente di calcolo di Azure Machine Learning | ✓ | |
| Servizio Azure Kubernetes | | ✓ |

Le destinazioni di calcolo sono collegate all' [area di lavoro](concept-workspace.md)Machine Learning. È possibile gestire le destinazioni di calcolo nell'area di lavoro in [Azure Machine Learning Studio](https://ml.azure.com).

## <a name="publish"></a>Pubblica

Una volta pronta una pipeline, è possibile pubblicarla come endpoint REST. Un [PublishedPipeline](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.publishedpipeline?view=azure-ml-py) può essere inviato senza il codice Python che lo ha costruito.

Inoltre, è possibile usare un PublishedPipeline per inviare nuovamente una pipeline con valori e input di PipelineParameter diversi.

## <a name="deployment"></a>Distribuzione

Quando il modello predittivo è pronto, distribuirlo come endpoint della pipeline o endpoint in tempo reale direttamente dalla finestra di progettazione.

L'endpoint della pipeline è un PublishedPipeline, che è possibile inviare un'esecuzione di pipeline con valori PipelineParameter diversi e input per l'inferenza batch.

L'endpoint in tempo reale fornisce un'interfaccia tra un'applicazione e il modello di assegnazione dei punteggi. Un'applicazione esterna può comunicare con il modello di assegnazione dei punteggi in tempo reale. Una chiamata a un endpoint in tempo reale restituisce i risultati della stima a un'applicazione esterna. Per effettuare una chiamata a un endpoint in tempo reale, passare una chiave API creata al momento della distribuzione dell'endpoint. L'endpoint è basato su REST, una scelta di architettura diffusa per progetti di programmazione Web.

Per informazioni su come distribuire il modello, vedere [esercitazione: distribuire un modello di Machine Learning con la finestra di progettazione](tutorial-designer-automobile-price-deploy.md).

## <a name="moving-from-the-visual-interface-to-the-designer"></a>Passaggio dall'interfaccia visiva alla finestra di progettazione

L'interfaccia visiva (anteprima) è stata aggiornata e ora è Azure Machine Learning Designer (anteprima). La finestra di progettazione è stata riprogettata per l'uso di un back-end basato su pipeline che si integra completamente con le altre funzionalità di Azure Machine Learning. 

In seguito a questi aggiornamenti, alcuni concetti e termini per l'interfaccia visiva sono stati modificati o rinominati. Per le modifiche concettuali più importanti, vedere la tabella seguente. 

| Concetto nella finestra di progettazione | In precedenza nell'interfaccia visiva |
| ---- |:----:|
| Bozza della pipeline | Esperimento |
| Endpoint in tempo reale | Servizio Web |

### <a name="migrating-to-the-designer"></a>Migrazione alla finestra di progettazione

È possibile convertire gli esperimenti di interfaccia visiva e i servizi Web esistenti in pipeline ed endpoint in tempo reale nella finestra di progettazione. Per eseguire la migrazione degli asset dell'interfaccia visiva, attenersi alla procedura seguente:

[!INCLUDE [migrate from the visual interface](../../../includes/aml-vi-designer-migration.md)]


## <a name="next-steps"></a>Passaggi successivi

* Scopri le nozioni di base sull'analisi predittiva e l'apprendimento automatico con [l'esercitazione: stima del prezzo dell'automobile con la finestra di progettazione](tutorial-designer-automobile-price-train-score.md)
* Usare uno degli esempi e modificare il gruppo in base alle esigenze:

- [Esempio 1: regressione: stimare il prezzo di un'automobile](how-to-designer-sample-regression-automobile-price-basic.md)
- [Esempio 2: regressione: confrontare gli algoritmi per la stima del prezzo dell'automobile](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [Esempio 3: classificazione con selezione delle caratteristiche: stima del reddito](how-to-designer-sample-classification-predict-income.md)
- [Esempio 4-Classificazione: stimare il rischio di credito (costo sensibile)](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [Esempio 5-classificazione: varianza di stima](how-to-designer-sample-classification-churn.md)
- [Esempio 6-Classificazione: stima dei ritardi dei voli](how-to-designer-sample-classification-flight-delay.md)
- [Esempio 7-classificazione di testo: set di dati di Wikipedia SP 500](how-to-designer-sample-text-classification.md)

