---
title: Interfaccia visiva grafica
titleSuffix: Azure Machine Learning
description: Informazioni su termini, concetti e flussi di lavoro che costituiscono l'interfaccia visiva (anteprima) per Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.date: 9/23/2019
ms.openlocfilehash: a23f123c6ffadaad4f830e1f4eab3820e6ef56f6
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72692212"
---
# <a name="what-is-the-visual-interface-for-azure-machine-learning"></a>Che cos'è l'interfaccia visiva per Azure Machine Learning? 

L'interfaccia visiva (anteprima) per Azure Machine Learning consente di preparare i dati, eseguire il training, testare, distribuire, gestire e tenere traccia dei modelli di Machine Learning senza scrivere codice.

Non è necessaria alcuna programmazione. i [set di impostazioni](#datasets) e i [moduli](#module) vengono collegati visivamente per costruire il modello.

L'interfaccia visiva usa l' [area di lavoro](concept-workspace.md) Azure Machine Learning per:

+ Creare, modificare ed eseguire [pipeline](#pipeline) nell'area di lavoro.
+ Accedere ai [set di impostazioni](#datasets).
+ Usare le [risorse di calcolo](#compute) nell'area di lavoro per eseguire la pipeline. 
+ Registrare i [modelli](concept-azure-machine-learning-architecture.md#models).
+ [Pubblicare](#publish) pipeline come endpoint REST.
+ [Distribuire](#deployment) i modelli come endpoint della pipeline (per inferenza batch) o endpoint in tempo reale sulle risorse di calcolo nell'area di lavoro.

![Cenni preliminari sull'interfaccia visiva](media/ui-concept-visual-interface/overview.png)

## <a name="workflow"></a>Flusso di lavoro

L'interfaccia visiva offre un'area di disegno interattiva visiva per compilare, testare ed eseguire rapidamente un'iterazione su un modello. 

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


Per informazioni su come iniziare a usare l'interfaccia visiva, vedere [esercitazione: stimare il prezzo di un'automobile con l'interfaccia visiva](ui-tutorial-automobile-price-train-score.md).

## <a name="datasets"></a>Set di dati

Un set di dati di Machine Learning semplifica l'accesso e l'uso dei dati. Nell'interfaccia visiva è incluso un numero di set di impostazioni di esempio che consentono di sperimentare. È possibile [registrare](./how-to-create-register-datasets.md) più set di impostazioni in modo che siano necessari.

## <a name="module"></a>Modulo

Un modulo è un algoritmo che è possibile applicare ai dati. L'interfaccia visiva include un numero di moduli che vanno dalle funzioni di ingresso dei dati ai processi di training, valutazione e convalida.

Un modulo può avere un set di parametri che è possibile usare per configurare gli algoritmi interni del modulo. Quando si seleziona un modulo nell'area di disegno, i parametri del modulo vengono visualizzati nel riquadro proprietà a destra dell'area di disegno. È possibile modificare i parametri in questo riquadro per ottimizzare il modello.

![Proprietà del modulo](media/ui-concept-visual-interface/properties.png)

Per informazioni sull'esplorazione della libreria di algoritmi di machine learning disponibili, vedere [Cenni preliminari sui moduli di & algoritmi](../algorithm-module-reference/module-reference.md)

## <a name="compute"></a>Risorse di calcolo

Usare le risorse di calcolo dall'area di lavoro per eseguire la pipeline e ospitare i modelli distribuiti come endpoint in tempo reale o endpoint della pipeline (per inferenza batch). Le destinazioni di calcolo supportate sono:

| Destinazione del calcolo | Formazione | Distribuzione |
| ---- |:----:|:----:|
| Ambiente di calcolo di Azure Machine Learning | ✓ | |
| Servizio Azure Kubernetes | | ✓ |

Le destinazioni di calcolo sono collegate all' [area di lavoro](concept-workspace.md)Machine Learning. È possibile gestire le destinazioni di calcolo nell'area di lavoro nell' [portale di Azure](https://portal.azure.com) o nella [pagina di destinazione dell'area di lavoro (anteprima)](https://ml.azure.com).

## <a name="publish"></a>Pubblica

Una volta pronta una pipeline, è possibile pubblicarla come endpoint REST. Un [PublishedPipeline](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.publishedpipeline?view=azure-ml-py) può essere inviato senza il codice Python che lo ha costruito.

Inoltre, è possibile usare un PublishedPipeline per inviare nuovamente una pipeline con valori e input di PipelineParameter diversi.

## <a name="deployment"></a>Distribuzione

Quando il modello predittivo è pronto, distribuirlo come endpoint della pipeline o endpoint in tempo reale direttamente dall'interfaccia visiva.

L'endpoint della pipeline è un [PublishedPipeline, che è possibile inviare un'esecuzione di pipeline con valori PipelineParameter diversi e input per l'inferenza batch.

L'endpoint in tempo reale fornisce un'interfaccia tra un'applicazione e il modello di assegnazione dei punteggi. Un'applicazione esterna può comunicare con il modello di assegnazione dei punteggi in tempo reale. Una chiamata a un endpoint in tempo reale restituisce i risultati della stima a un'applicazione esterna. Per effettuare una chiamata a un endpoint in tempo reale, passare una chiave API creata al momento della distribuzione dell'endpoint. L'endpoint è basato su REST, una scelta di architettura diffusa per progetti di programmazione Web.

Per informazioni su come distribuire il modello, vedere [esercitazione: distribuire un modello di Machine Learning con l'interfaccia visiva](ui-tutorial-automobile-price-deploy.md).

## <a name="next-steps"></a>Passaggi successivi

* Scopri le nozioni di base sull'analisi predittiva e Machine Learning con [l'esercitazione: stimare il prezzo dell'automobile con l'interfaccia visiva](ui-tutorial-automobile-price-train-score.md)
* Usare uno degli esempi e modificare il gruppo in base alle esigenze:

    * [Esempio 1: regressione: Prezzo stimato](how-to-ui-sample-regression-predict-automobile-price-basic.md)
    * [Esempio 2-regressione: stimare il prezzo e confrontare gli algoritmi](how-to-ui-sample-regression-predict-automobile-price-compare-algorithms.md)
    * [Esempio 3-classificazione: stima del rischio di credito](how-to-ui-sample-classification-predict-credit-risk-basic.md)
    * [Esempio 4-Classificazione: stimare il rischio di credito (costo sensibile)](how-to-ui-sample-classification-predict-credit-risk-cost-sensitive.md)
    * [Esempio 5-classificazione: stima varianza, appetency e vendita](how-to-ui-sample-classification-predict-churn.md)
    * [Esempio 6-Classificazione: stima dei ritardi dei voli](how-to-ui-sample-classification-predict-flight-delay.md)

