---
title: Interfaccia visiva grafica
titleSuffix: Azure Machine Learning
description: Informazioni su termini, concetti e flussi di lavoro che costituiscono l'interfaccia visiva (anteprima) per Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 05/15/2019
ms.openlocfilehash: 65daf1468d27825d9904a14e42e43796d3985321
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2019
ms.locfileid: "70996515"
---
# <a name="what-is-the-visual-interface-for-azure-machine-learning"></a>Che cos'è l'interfaccia visiva per Azure Machine Learning? 

L'interfaccia visiva (anteprima) per Azure Machine Learning consente di preparare i dati, eseguire il training, testare, distribuire, gestire e tenere traccia dei modelli di Machine Learning senza scrivere codice.

Non è necessaria alcuna programmazione. i [set di impostazioni](#dataset) e i [moduli](#module) vengono collegati visivamente per costruire il modello.

L'interfaccia visiva usa l' [area di lavoro](concept-workspace.md) Azure Machine Learning per:

+ Gli artefatti di scrittura dell' [esperimento](#experiment) vengono eseguiti nell'area di lavoro.
+ Accedere ai [set di impostazioni](#dataset).
+ Usare le [risorse di calcolo](#compute) nell'area di lavoro per eseguire l'esperimento. 
+ Registrare i [modelli](concept-azure-machine-learning-architecture.md#models).
+ [Distribuire](#deployment) i modelli come servizi Web nelle risorse di calcolo nell'area di lavoro.

![Cenni preliminari sull'interfaccia visiva](media/ui-concept-visual-interface/overview.png)

## <a name="workflow"></a>Flusso di lavoro

L'interfaccia visiva offre un'area di disegno interattiva visiva per compilare, testare ed eseguire rapidamente un'iterazione su un modello. 

+ È possibile trascinare i [moduli](#module) nell'area di disegno.
+ Connettere i moduli per formare un [esperimento](#experiment).
+ Eseguire l'esperimento usando la risorsa di calcolo dell'area di lavoro del servizio Machine Learning.
+ Per eseguire l'iterazione sulla progettazione del modello, modificare l'esperimento ed eseguirlo di nuovo.
+ Quando si è pronti, convertire l' **esperimento di training** in un **esperimento predittivo**.
+ [Distribuire](#deployment) l'esperimento predittivo come servizio Web in modo che sia possibile accedere al modello da altri utenti.

## <a name="experiment"></a>Esperimento

Creare un esperimento da zero oppure usare un esperimento di esempio esistente come modello.  Ogni volta che si esegue un esperimento, gli artefatti vengono archiviati nell'area di lavoro.

Un esperimento è costituito da set di impostazioni e moduli analitici, che è possibile connettere insieme per costruire un modello. In particolare, un esperimento valido ha le caratteristiche seguenti:

* I set di impostazioni possono essere connessi solo ai moduli.
* I moduli possono essere connessi a set di impostazioni o ad altri moduli.
* Tutte le porte di input per i moduli devono disporre di una connessione al flusso di dati.
* È necessario impostare tutti i parametri obbligatori per ogni modulo.


Per informazioni su come iniziare a usare l'interfaccia visiva, vedere [esercitazione: Stimare il prezzo di un'automobile con l'interfaccia visiva grafica](ui-tutorial-automobile-price-train-score.md).

## <a name="dataset"></a>Set di dati

Un set di dati è costituito dai dati caricati nell'interfaccia visiva da usare nel processo di modellazione. Sono inclusi numerosi set di impostazioni di esempio che consentono di sperimentare ed è possibile caricare più set di impostazioni in modo che siano necessari.

## <a name="module"></a>Modulo

Un modulo è un algoritmo che è possibile applicare ai dati. L'interfaccia visiva include un numero di moduli che vanno dalle funzioni di ingresso dei dati ai processi di training, valutazione e convalida.

Un modulo può avere un set di parametri che è possibile usare per configurare gli algoritmi interni del modulo. Quando si seleziona un modulo nell'area di disegno, i parametri del modulo vengono visualizzati nel riquadro proprietà a destra dell'area di disegno. È possibile modificare i parametri in questo riquadro per ottimizzare il modello.

![Proprietà del modulo](media/ui-concept-visual-interface/properties.png)

Per informazioni sull'esplorazione della libreria di algoritmi di machine learning disponibili, vedere [Cenni preliminari sui moduli di & algoritmi](../algorithm-module-reference/module-reference.md)

## <a name="compute"></a>Risorse di calcolo

Usare le risorse di calcolo dall'area di lavoro per eseguire l'esperimento o ospitare i modelli distribuiti come servizi Web. Le destinazioni di calcolo supportate sono:


| Destinazione del calcolo | Formazione | Distribuzione |
| ---- |:----:|:----:|
| Ambiente di calcolo di Azure Machine Learning | ✓ | |
| Servizio Azure Kubernetes | | ✓ |

Le destinazioni di calcolo sono collegate all' [area di lavoro](concept-workspace.md)Machine Learning. È possibile gestire le destinazioni di calcolo nell'area di lavoro nell' [portale di Azure](https://portal.azure.com) o nella [pagina di destinazione dell'area di lavoro (anteprima)](https://ml.azure.com).

## <a name="deployment"></a>Distribuzione

Quando il modello di analisi predittiva è pronto, lo si distribuisce come servizio Web direttamente dall'interfaccia visiva.

I servizi Web forniscono un'interfaccia tra un'applicazione e il modello di assegnazione dei punteggi. Un'applicazione esterna può comunicare con il modello di assegnazione dei punteggi in tempo reale. Una chiamata a un servizio Web restituisce i risultati della stima a un'applicazione esterna. Per effettuare una chiamata a un servizio Web, passare una chiave API creata al momento della distribuzione del servizio Web. Il servizio Web è basato su REST, una scelta di architettura diffusa per progetti di programmazione Web.

Per informazioni su come distribuire il modello, vedere [esercitazione: Distribuire un modello di Machine Learning con l'interfaccia](ui-tutorial-automobile-price-deploy.md)visiva.

## <a name="next-steps"></a>Passaggi successivi

* Scopri le nozioni di base sull'analisi predittiva e [l'apprendimento automatico con l'esercitazione: Stimare il prezzo di un'automobile con l'interfaccia visiva grafica](ui-tutorial-automobile-price-train-score.md)
* Usare uno degli esempi e modificare il gruppo in base alle esigenze:
    * [Esempio 1: regressione: Prezzo stimato](ui-sample-regression-predict-automobile-price-basic.md)
    * [Esempio 2: regressione: Stimare il prezzo e confrontare gli algoritmi](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
    * [Esempio 3-classificazione: Stima del rischio di credito](ui-sample-classification-predict-credit-risk-basic.md)
    * [Esempio 4-Classificazione: Stimare il rischio di credito (costo sensibile)](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
    * [Esempio 5-classificazione: Prevedere varianza, appetency e vendita](ui-sample-classification-predict-churn.md)
