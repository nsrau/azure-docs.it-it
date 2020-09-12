---
title: Compilare modelli ML con la finestra di progettazione (anteprima)
titleSuffix: Azure Machine Learning
description: Informazioni su termini, concetti e flussi di lavoro che costituiscono la finestra di progettazione per Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: peterlu
author: peterclu
ms.date: 06/28/2020
ms.custom: designer
ms.openlocfilehash: 788eebaf28a768acbdb5b1eb778f196fe05dc3e3
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2020
ms.locfileid: "89651220"
---
# <a name="what-is-azure-machine-learning-designer-preview"></a>Che cos'è la finestra di progettazione di Azure Machine Learning (anteprima)? 
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

La finestra di progettazione di Azure Machine Learning consente di connettere in modo visivo [set di dati](#datasets) e [moduli](#module) in un pannello Canvas interattivo per creare modelli di Machine Learning. Per informazioni su come iniziare a usare la finestra di progettazione, vedere [Esercitazione: Stimare il prezzo di un'automobile con la finestra di progettazione](tutorial-designer-automobile-price-train-score.md)

![Esempio di finestra di progettazione di Azure Machine Learning](./media/concept-designer/designer-drag-and-drop.gif)

La finestra di progettazione usa l'[area di lavoro](concept-workspace.md) di Azure Machine Learning per organizzare le risorse condivise, ad esempio:

+ [Pipeline](#pipeline)
+ [Set di dati](#datasets)
+ [Risorse di calcolo](#compute)
+ [Modelli registrati](concept-azure-machine-learning-architecture.md#models)
+ [Pipeline pubblicate](#publish)
+ [Endpoint in tempo reale](#deploy)

## <a name="model-training-and-deployment"></a>Training e distribuzione dei modelli

La finestra di progettazione offre un pannello Canvas visivo per compilare, testare e distribuire modelli di Machine Learning. Con la finestra di progettazione è possibile:

+ Trascinare i [set di dati](#datasets) e i [moduli](#module) selezionati nel pannello Canvas.
+ Connettere i moduli per creare una [bozza della pipeline](#pipeline-draft).
+ Inviare un'[esecuzione della pipeline](#pipeline-run) usando le risorse di calcolo nell'area di lavoro Azure Machine Learning.
+ Convertire le **pipeline di training** in **pipeline di inferenza**.
+ [Pubblicare](#publish) le pipeline in un endpoint della **pipeline** REST per inviare una nuova pipeline che viene eseguita con parametri e set di impostazioni diversi.
    + Pubblicare una **pipeline di training** per riusare una singola pipeline per il training di più modelli durante la modifica di parametri e set di dati.
    + Pubblicare una **pipeline di inferenza batch** per eseguire stime sui nuovi dati usando un modello precedentemente sottoposto a training.
+ [Distribuire](#deploy) una **pipeline di inferenza in tempo reale** a un endpoint in tempo reale per eseguire stime sui nuovi dati in tempo reale.

![Diagramma di flusso di lavoro per training, inferenza batch e inferenza in tempo reale nella finestra di progettazione](./media/concept-designer/designer-workflow-diagram.png)

## <a name="pipeline"></a>Pipeline

Una [pipeline](concept-azure-machine-learning-architecture.md#ml-pipelines) è costituita da set di impostazioni e moduli analitici che è possibile connettere. Le pipeline hanno molti usi: è possibile creare una pipeline che esegue il training di un singolo modello o una pipeline che esegue il training di più modelli. È possibile creare una pipeline che esegue stime in tempo reale o in batch oppure creare una pipeline che pulisca solo i dati. Le pipeline consentono di riusare il lavoro e organizzare i progetti.

### <a name="pipeline-draft"></a>Bozza di pipeline

Quando si modifica una pipeline nella finestra di progettazione, lo stato viene salvato come **bozza di pipeline**. È possibile modificare una bozza di pipeline in qualsiasi momento aggiungendo o rimuovendo moduli, configurando le destinazioni di calcolo, creando parametri e così via.

Di seguito sono riportate le caratteristiche di una pipeline valida:

* I set di dati possono essere collegati solo ai moduli.
* I moduli possono essere collegati solo a set di dati o ad altri moduli.
* Tutte le porte di input per i moduli devono avere un collegamento al flusso di dati.
* Tutti i parametri necessari per ogni modulo devono essere impostati.

Quando si è pronti per eseguire la bozza di pipeline, si invia un'esecuzione della pipeline.

### <a name="pipeline-run"></a>Esecuzione della pipeline

Ogni volta che si esegue una pipeline, la configurazione della pipeline e i relativi risultati vengono archiviati nell'area di lavoro come **esecuzione della pipeline**. È possibile tornare a un'esecuzione della pipeline per esaminarla ai fini della risoluzione dei problemi o del controllo. **Clonare** un'esecuzione della pipeline per creare una nuova bozza di pipeline da modificare.

Le esecuzioni delle pipeline vengono raggruppate in [esperimenti](concept-azure-machine-learning-architecture.md#experiments) per organizzare la cronologia di esecuzione. È possibile impostare l'esperimento per ogni esecuzione delle pipeline. 

## <a name="datasets"></a>Set di dati

Un set di dati di Machine Learning semplifica l'accesso e l'uso dei dati. Nella finestra di progettazione sono inclusi diversi set di impostazioni di esempio che consentono di sperimentare. È possibile [registrare](how-to-create-register-datasets.md) più set di dati in base alle necessità.

## <a name="module"></a>Modulo

Un modulo è un algoritmo che è possibile applicare ai dati. La finestra di progettazione include diversi moduli che variano dalle funzioni di ingresso dei dati ai processi di training, valutazione e convalida.

Un modulo può avere un set di parametri che è possibile usare per configurare gli algoritmi interni del modulo. Quando si seleziona un modulo nel pannello Canvas, i parametri del modulo sono visualizzati nel riquadro Proprietà a destra del pannello Canvas. È possibile modificare i parametri in questo riquadro per ottimizzare il modello. È possibile impostare le risorse di calcolo per i singoli moduli nella finestra di progettazione. 

:::image type="content" source="./media/concept-designer/properties.png"alt-text="Proprietà del modulo":::


Per informazioni su come spostarsi nella libreria di algoritmi di Machine Learning disponibili, vedere la [panoramica di riferimento agli algoritmi e ai moduli](algorithm-module-reference/module-reference.md). Per informazioni sulla scelta di un algoritmo, vedere la [scheda di riferimento rapido sugli algoritmi di Azure Machine Learning](algorithm-cheat-sheet.md).

## <a name="compute-resources"></a><a name="compute"></a> Risorse di calcolo

Usare le risorse di calcolo dall'area di lavoro per eseguire la pipeline e ospitare i modelli distribuiti come endpoint in tempo reale o endpoint della pipeline (per l'inferenza batch). Le destinazioni di calcolo supportate sono:

| Destinazione del calcolo | Formazione | Distribuzione |
| ---- |:----:|:----:|
| Ambiente di calcolo di Azure Machine Learning | ✓ | |
| Istanza di calcolo di Azure Machine Learning | ✓ | |
| Servizio Azure Kubernetes | | ✓ |

Le destinazioni di calcolo sono collegate all'[area di lavoro di Azure Machine Learning](concept-workspace.md). È possibile gestire le destinazioni di calcolo nell'area di lavoro in [Azure Machine Learning Studio](https://ml.azure.com).

## <a name="deploy"></a>Distribuire

Per eseguire l'inferenza in tempo reale, è necessario distribuire una pipeline come **endpoint in tempo reale**. L'endpoint in tempo reale crea un'interfaccia tra un'applicazione esterna e il modello di punteggio. Una chiamata a un endpoint in tempo reale restituisce i risultati della stima all'applicazione in tempo reale. Per effettuare una chiamata a un endpoint in tempo reale, passare una chiave API creata al momento della distribuzione dell'endpoint. Un endpoint è basato su REST, un'opzione di architettura diffusa per progetti di programmazione Web.

Gli endpoint in tempo reale devono essere distribuiti in un cluster del servizio Azure Kubernetes.

Per informazioni su come distribuire il modello, vedere [Esercitazione: Distribuire un modello di Machine Learning con la finestra di progettazione](tutorial-designer-automobile-price-deploy.md).

## <a name="publish"></a>Pubblica

È anche possibile pubblicare una pipeline in un **endpoint della pipeline**. Analogamente a un endpoint in tempo reale, un endpoint della pipeline consente di inviare le nuove esecuzioni delle pipeline da applicazioni esterne usando le chiamate REST. Non è tuttavia possibile inviare o ricevere dati in tempo reale tramite un endpoint della pipeline.

Le pipeline pubblicate sono flessibili e possono essere usate per eseguire o ripetere il training dei modelli, [eseguire l'inferenza batch](how-to-run-batch-predictions-designer.md), elaborare nuovi dati e molto altro ancora. È possibile pubblicare più pipeline in un singolo endpoint della pipeline e specificare quale versione della pipeline eseguire.

Una pipeline pubblicata viene eseguita nelle risorse di calcolo definite nella bozza di pipeline per ogni modulo.

La finestra di progettazione crea lo stesso oggetto [PublishedPipeline](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.publishedpipeline?view=azure-ml-py&preserve-view=true) dell'SDK.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sulle nozioni di base sull'analisi predittiva e Machine Learning con l'[Esercitazione: Stimare il prezzo di un'automobile con la finestra di progettazione](tutorial-designer-automobile-price-train-score.md)
* Informazioni su come modificare gli [esempi della finestra di progettazione](samples-designer.md) esistenti per adattarli alle proprie esigenze.
