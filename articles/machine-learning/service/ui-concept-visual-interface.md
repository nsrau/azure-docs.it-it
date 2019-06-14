---
title: Interfaccia visiva grafica
titleSuffix: Azure Machine Learning service
description: Informazioni sui concetti, le condizioni e del flusso di lavoro che costituiscono l'interfaccia visiva (anteprima) per il servizio di Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 05/15/2019
ms.openlocfilehash: be07e0f3438ea93312d4eb440e7e63b8f98e11b8
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67077366"
---
# <a name="what-is-the-visual-interface-for-azure-machine-learning-service"></a>Che cos'è l'interfaccia visiva per il servizio di Azure Machine Learning? 

L'interfaccia visiva (anteprima) per il servizio Azure Machine Learning consente di preparare i dati, eseguire il training, testare, distribuire, gestire e tenere traccia di modelli di machine learning senza scrivere codice.

Non è richiesta alcuna programmazione, quindi ci si connette visivamente [set di dati](#dataset) e [moduli](#module) per costruire il modello.

L'interfaccia visiva Usa il servizio Azure Machine Learning [dell'area di lavoro](concept-workspace.md) per:

+ Scrivere gli elementi della [sperimentare](#experiment) viene eseguita nell'area di lavoro.
+ L'accesso [set di dati](#dataset).
+ Usare la [risorse di calcolo](#compute) nell'area di lavoro per eseguire l'esperimento. 
+ Registrare [modelli](concept-azure-machine-learning-architecture.md#models).
+ [Distribuire](#deployment) modelli come servizi web su risorse nell'area di lavoro di calcolo.

![Panoramica dell'interfaccia visiva](media/ui-concept-visual-interface/overview.png)

## <a name="workflow"></a>Flusso di lavoro

L'interfaccia visiva ti offre un'area di disegno interattiva, visual per compilare, testare e l'iterazione su un modello. 

+ È di trascinamento e rilascio [moduli](#module) nell'area di disegno.
+ Connettere tra loro i moduli per creare un [sperimentare](#experiment).
+ Eseguire l'esperimento usando la risorsa di calcolo dell'area di lavoro del servizio Machine Learning.
+ L'iterazione del progetto di modello per modificare l'esperimento ed eseguirlo nuovamente.
+ Quando si è pronti, convertire le **esperimento di training** a un **esperimento predittivo**.
+ [Distribuire](#deployment) l'esperimento predittivo come un sito web del servizio in modo che il modello sarà accessibile da altri utenti.

## <a name="experiment"></a>Esperimento

Creare un esperimento da zero, oppure usare un esperimento di esempio esistente come modello.  Ogni volta che si esegue un esperimento, gli elementi vengono memorizzati nell'area di lavoro.

Un esperimento costituito da set di dati e moduli di analisi, che sono collegati tra loro per costruire un modello. In particolare, un esperimento valido ha le caratteristiche seguenti:

* I set di dati possono essere collegati solo ai moduli.
* I moduli possono essere collegati al set di dati o altri moduli.
* Tutte le porte di input per i moduli devono avere un collegamento al flusso di dati.
* Tutte le necessarie, è necessario impostare parametri per ogni modulo.

Per un esempio di un semplice esperimento, vedere [Guida introduttiva: Preparare e visualizzare i dati senza scrivere codice in Azure Machine Learning](ui-quickstart-run-experiment.md).

Per una procedura dettagliata più completa di una soluzione analitica predittiva, vedere [esercitazione: Stimare il prezzo di un'automobile con l'interfaccia visiva](ui-tutorial-automobile-price-train-score.md).

## <a name="dataset"></a>Set di dati

Un set di dati sono dati che sono stati caricati per l'interfaccia visiva da usare nel processo di modellazione. Un numero di set di dati di esempio è incluso per poter sperimentare ed è possibile caricare più set di dati necessari in qualsiasi momento.

## <a name="module"></a>Modulo

Un modulo è un algoritmo che è possibile applicare ai dati. L'interfaccia visiva è un numero di moduli, da funzioni di inserimento dei dati di training, assegnazione dei punteggi e processi di convalida.

Un modulo può avere un set di parametri che è possibile usare per configurare gli algoritmi interni del modulo. Quando si seleziona un modulo nell'area di disegno, i parametri del modulo vengono visualizzati nel riquadro delle proprietà a destra dell'area di disegno. È possibile modificare i parametri in questo riquadro per ottimizzare il modello.

![Proprietà del modulo](media/ui-concept-visual-interface/properties.png)

Per altre informazioni di riferimento tramite la libreria di algoritmi di machine learning disponibili, vedere [Panoramica di riferimento di algoritmi e moduli](../algorithm-module-reference/module-reference.md)

## <a name="compute"></a> Le risorse di calcolo

Utilizzo di calcolo delle risorse dall'area di lavoro per eseguire l'esperimento o host di modelli distribuiti come servizi web. Le destinazioni di calcolo supportate sono:


| Destinazione del calcolo | Formazione | Distribuzione |
| ---- |:----:|:----:|
| Ambiente di calcolo di Azure Machine Learning | ✓ | |
| Servizio Azure Kubernetes | | ✓ |

Calcolare le destinazioni sono associate per il Machine Learning [dell'area di lavoro](concept-workspace.md). Gestire le destinazioni di calcolo nell'area di lavoro nel [portale di Azure](https://portal.azure.com).

## <a name="deployment"></a>Distribuzione

Quando il modello di analitica predittiva è pronto, si distribuirlo come servizio web direttamente dall'interfaccia visiva.

I servizi web forniscono un'interfaccia tra un'applicazione e il modello di punteggio. Un'applicazione esterna può comunicare con il modello di punteggio in tempo reale. Una chiamata a un servizio web restituisce i risultati della stima a un'applicazione esterna. Per effettuare una chiamata a un servizio Web, passare una chiave API creata al momento della distribuzione del servizio Web. Il servizio web è basato su REST, la scelta di architettura diffusa per progetti di programmazione web.

Per informazioni su come distribuire il modello, vedere [esercitazione: Distribuire un modello di machine learning con l'interfaccia visiva](ui-tutorial-automobile-price-deploy.md).

## <a name="next-steps"></a>Passaggi successivi

* Nozioni di base analitica predittiva e machine learning con [Guida introduttiva: Preparare e visualizzare i dati senza scrivere codice in Azure Machine Learning](ui-quickstart-run-experiment.md).
* Usare uno degli esempi e modificare a suite le proprie esigenze:
    * [Esempio 1: regressione: Stimare il prezzo](ui-sample-regression-predict-automobile-price-basic.md)
    * [Esempio 2: regressione: Stimare il prezzo e confrontare gli algoritmi](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
    * [Esempio 3: classificazione: Prevedere il rischio di credito](ui-sample-classification-predict-credit-risk-basic.md)
    * [Esempio 4: classificazione: Prevedere il rischio di credito (costo sensibile)](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
    * [Esempio 5 - classificazione: Prevedere la varianza, stime sui desideri e up-selling](ui-sample-classification-predict-churn.md)
