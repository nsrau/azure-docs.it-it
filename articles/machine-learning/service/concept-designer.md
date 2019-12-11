---
title: Sviluppare modelli ML con progettazione
titleSuffix: Azure Machine Learning
description: Informazioni su termini, concetti e flussi di lavoro che costituiscono la finestra di progettazione per Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: peterlu
author: peterclu
ms.date: 11/12/2019
ms.openlocfilehash: efc9d7fb31dc9f63780c3f94238bbbfb17756089
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74973629"
---
# <a name="what-is-azure-machine-learning-designer-preview"></a>Che cos'è Azure Machine Learning Designer (anteprima)? 
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-enterprise-sku.md)]

Azure Machine Learning Designer consente di connettere visivamente [set di impostazioni](#datasets) e [moduli](#module) in un'area di disegno interattiva per creare modelli di apprendimento automatico. Per informazioni su come iniziare a usare la finestra di progettazione, vedere [esercitazione: stimare il prezzo di un'automobile con la finestra di progettazione](tutorial-designer-automobile-price-train-score.md)

![Esempio di finestra di progettazione di Azure Machine Learning](./media/concept-ml-pipelines/designer-drag-and-drop.gif)

La finestra di progettazione USA l' [area di lavoro](concept-workspace.md) Azure Machine Learning per organizzare le risorse condivise, ad esempio:

+ [Pipeline](#pipeline)
+ [Set di dati](#datasets)
+ [Risorse di calcolo](#compute)
+ [Modelli registrati](concept-azure-machine-learning-architecture.md#models)
+ [Pipeline pubblicate](#publish)
+ [Endpoint in tempo reale](#deploy)

## <a name="model-training-and-deployment"></a>Training e distribuzione di modelli

La finestra di progettazione offre un'area di disegno visiva per compilare, testare e distribuire modelli di machine learning. Con la finestra di progettazione è possibile:

+ Trascinare e rilasciare i [set di impostazioni](#datasets) e i [moduli](#module) nell'area di disegno.
+ Connettere i moduli per creare una [bozza della pipeline](#pipeline-draft).
+ Inviare un' [esecuzione di pipeline](#pipeline-run) usando le risorse di calcolo nell'area di lavoro Azure Machine Learning.
+ Convertire le **pipeline di training** in **pipeline di inferenza**.
+ [Pubblicare](#publish) le pipeline in un endpoint della **pipeline** REST per inviare le nuove esecuzioni di pipeline con diversi set di impostazioni e parametri.
    + Pubblicare una **pipeline di training** per riutilizzare una singola pipeline per eseguire il training di più modelli durante la modifica di parametri e set di impostazioni.
    + Pubblicare una **pipeline di inferenza batch** per eseguire stime sui nuovi dati usando un modello precedentemente sottoposto a training.
+ [Distribuire](#deploy) una **pipeline di inferenza in tempo reale** a un endpoint in tempo reale per eseguire stime sui nuovi dati in tempo reale.

![Diagramma di flusso di lavoro per training, inferenza batch e inferenza in tempo reale nella finestra di progettazione](media/concept-designer/designer-workflow-diagram.png)

## <a name="pipeline"></a>Pipeline

Una [pipeline](concept-azure-machine-learning-architecture.md#ml-pipelines) è costituita da set di impostazioni e moduli analitici, che è possibile connettere insieme. Le pipeline hanno molti usi: è possibile creare una pipeline che esegue il training di un singolo modello o uno che esegue il training di più modelli. È possibile creare una pipeline che esegue stime in tempo reale o in batch oppure creare una pipeline che pulisca solo i dati. Le pipeline consentono di riutilizzare il lavoro e organizzare i progetti.

### <a name="pipeline-draft"></a>Bozza della pipeline

Quando si modifica una pipeline nella finestra di progettazione, lo stato di avanzamento viene salvato come **bozza della pipeline**. È possibile modificare una bozza di pipeline in qualsiasi momento aggiungendo o rimuovendo moduli, configurando le destinazioni di calcolo, creando parametri e così via.

Una pipeline valida presenta queste caratteristiche:

* I set di impostazioni possono connettersi solo ai moduli.
* I moduli possono connettersi solo a set di impostazioni o ad altri moduli.
* Tutte le porte di input per i moduli devono disporre di una connessione al flusso di dati.
* È necessario impostare tutti i parametri obbligatori per ogni modulo.

Quando si è pronti per eseguire la bozza della pipeline, si invia un'esecuzione della pipeline.

### <a name="pipeline-run"></a>Esecuzione della pipeline

Ogni volta che si esegue una pipeline, la configurazione della pipeline e i relativi risultati vengono archiviati nell'area di lavoro come **esecuzione della pipeline**. È possibile tornare a qualsiasi esecuzione di pipeline per esaminarla a scopo di controllo o risoluzione dei problemi. **Clonare** un'esecuzione di pipeline per creare una nuova bozza di pipeline da modificare.

Le esecuzioni di pipeline sono raggruppate in [esperimenti](concept-azure-machine-learning-architecture.md#experiments) per organizzare la cronologia di esecuzione. È possibile impostare l'esperimento per ogni esecuzione della pipeline. 

## <a name="datasets"></a>Set di dati

Un set di dati di Machine Learning semplifica l'accesso e l'uso dei dati. Nella finestra di progettazione sono inclusi alcuni set di impostazioni di esempio che consentono di sperimentare. È possibile [registrare](./how-to-create-register-datasets.md) più set di impostazioni in modo che siano necessari.

## <a name="module"></a>Modulo

Un modulo è un algoritmo che è possibile applicare ai dati. La finestra di progettazione ha una serie di moduli che variano dalle funzioni di ingresso dei dati ai processi di training, assegnazione di punteggi e convalida.

Un modulo può avere un set di parametri che è possibile usare per configurare gli algoritmi interni del modulo. Quando si seleziona un modulo nell'area di disegno, i parametri del modulo vengono visualizzati nel riquadro proprietà a destra dell'area di disegno. È possibile modificare i parametri in questo riquadro per ottimizzare il modello. È possibile impostare le risorse di calcolo per i singoli moduli nella finestra di progettazione. 

![Proprietà del modulo](media/concept-designer/properties.png)

Per informazioni sull'esplorazione della libreria di algoritmi di machine learning disponibili, vedere [Cenni preliminari sui moduli di & algoritmi](../algorithm-module-reference/module-reference.md)

## <a name="compute"></a>Risorse di calcolo

Usare le risorse di calcolo dall'area di lavoro per eseguire la pipeline e ospitare i modelli distribuiti come endpoint in tempo reale o endpoint della pipeline (per inferenza batch). Le destinazioni di calcolo supportate sono:

| Destinazione del calcolo | Formazione | Distribuzione |
| ---- |:----:|:----:|
| Ambiente di calcolo di Azure Machine Learning | ✓ | |
| Servizio Azure Kubernetes | | ✓ |

Le destinazioni di calcolo sono collegate all' [area di lavoro Azure Machine Learning](concept-workspace.md). È possibile gestire le destinazioni di calcolo nell'area di lavoro in [Azure Machine Learning Studio (classico)](https://ml.azure.com).

## <a name="deploy"></a>Distribuire

Per eseguire l'inferenza in tempo reale, è necessario distribuire una pipeline come **endpoint in tempo reale**. L'endpoint in tempo reale crea un'interfaccia tra un'applicazione esterna e il modello di assegnazione dei punteggi. Una chiamata a un endpoint in tempo reale restituisce i risultati della stima all'applicazione in tempo reale. Per effettuare una chiamata a un endpoint in tempo reale, passare la chiave API creata al momento della distribuzione dell'endpoint. L'endpoint è basato su REST, una scelta di architettura diffusa per progetti di programmazione Web.

Gli endpoint in tempo reale devono essere distribuiti in un cluster del servizio Azure Kubernetes.

Per informazioni su come distribuire il modello, vedere [esercitazione: distribuire un modello di Machine Learning con la finestra di progettazione](tutorial-designer-automobile-price-deploy.md).

## <a name="publish"></a>Pubblica

È anche possibile pubblicare una pipeline in un **endpoint della pipeline**. Analogamente a un endpoint in tempo reale, un endpoint della pipeline consente di inviare le nuove esecuzioni di pipeline da applicazioni esterne usando le chiamate REST. Tuttavia, non è possibile inviare o ricevere dati in tempo reale tramite un endpoint della pipeline.

Le pipeline pubblicate sono flessibili e possono essere usate per eseguire il training o il training dei modelli, [eseguire l'inferenza di batch](how-to-run-batch-predictions-designer.md), elaborare nuovi dati e molto altro ancora. È possibile pubblicare più pipeline in un singolo endpoint della pipeline e specificare quale versione della pipeline eseguire.

Una pipeline pubblicata viene eseguita nelle risorse di calcolo definite nella bozza della pipeline per ogni modulo.

La finestra di progettazione crea lo stesso oggetto [PublishedPipeline](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.publishedpipeline?view=azure-ml-py) dell'SDK.


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

