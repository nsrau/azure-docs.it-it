---
title: 'Finestra di progettazione: esempio di classificazione, stima del reddito'
titleSuffix: Azure Machine Learning
description: Seguire questo esempio per creare un classificatore senza codice per stimare il reddito con Azure Machine Learning Designer.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: peterlu
ms.date: 11/04/2019
ms.openlocfilehash: adc7712a4f41daea9ed691e6df52290e98e8d81f
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74214083"
---
# <a name="build-a-classifier--use-feature-selection-to-predict-income-with-azure-machine-learning-designer"></a>Compilare un classificatore & usare la selezione delle funzioni per stimare il reddito con Azure Machine Learning Designer

**Finestra di progettazione (anteprima) sample 3**

[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-enterprise-sku.md)]

Informazioni su come creare un classificatore di Machine Learning senza scrivere una singola riga di codice usando la finestra di progettazione (anteprima). Questo esempio consente di eseguire il training di un **albero delle decisioni con boosting a due classi** per stimare il reddito del censimento adulto (> = 50.000 o < = 50.000).

Poiché la domanda risponde a "quale?" si tratta di un problema di classificazione. Tuttavia, è possibile applicare lo stesso processo fondamentale per risolvere qualsiasi tipo di problema di Machine Learning, indipendentemente dal fatto che si tratti di regressione, classificazione, clustering e così via.

Ecco il grafico della pipeline finale per questo esempio:

![Grafico della pipeline](media/how-to-designer-sample-classification-predict-income/overall-graph.png)

## <a name="prerequisites"></a>prerequisiti

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Fare clic sul campione 3 per aprirlo.



## <a name="data"></a>Dati

Il set di dati contiene 14 funzionalità e una colonna etichetta. Sono disponibili più tipi di funzionalità, inclusi numerici e categorici. Il diagramma seguente mostra un estratto del set di dati: ![dati](media/how-to-designer-sample-classification-predict-income/data.png)



## <a name="pipeline-summary"></a>Riepilogo della pipeline

Per creare la pipeline, attenersi alla procedura seguente:

1. Trascinare il modulo DataSet Adult Census income Binary DataSet nel Canvas della pipeline.
1. Aggiungere un modulo **Split data** per creare i set di training e di test. Impostare la frazione di righe nel primo set di dati di output su 0,7. Questa impostazione specifica che il 70% dei dati verrà restituito alla porta sinistra del modulo e il resto sulla porta destra. Il set di dati a sinistra viene usato per il training e quello giusto per i test.
1. Aggiungere il modulo di selezione delle caratteristiche **basato su filtro** per selezionare 5 funzionalità per PearsonCorreclation. 
1. Aggiungere un modulo a **albero delle decisioni con boosting a due classi** per inizializzare un classificatore di albero delle decisioni con boosting.
1. Aggiungere un modulo **Train Model** . Connettere il classificatore dal passaggio precedente alla porta di input sinistra del modello di **Training**. Connettere il set di dati filtrato dal modulo di selezione delle caratteristiche basato su filtro come set di dati di training  Il training **Model** eseguirà il training del classificatore.
1. Aggiungere la trasformazione Seleziona colonne e applicare il modulo di trasformazione per applicare la stessa trasformazione (selezione funzionalità basata su filtro) al set di dati di test.
![Apply-Transformation](media/how-to-designer-sample-classification-predict-income/transformation.png)
1. Aggiungere il modulo **Score Model** e connettervi il modulo **Train Model** . Aggiungere quindi il set di test (l'output del modulo Apply Transformation che applica la selezione delle caratteristiche anche al set di test) al **modello di Punteggio**. Il **modello di Punteggio** effettuerà le stime. È possibile selezionare la relativa porta di output per visualizzare le stime e le probabilità della classe positiva.


    Questa pipeline ha due moduli score, uno a destra ha esclusa la colonna Label prima di eseguire la stima. Questa operazione è preparata per la distribuzione di un endpoint in tempo reale, perché l'input del servizio Web prevede che non vengano etichettate solo le funzionalità. 

1. Aggiungere un modulo **Evaluate Model** e connettere il set di dati con punteggio alla relativa porta di input sinistra. Per visualizzare i risultati della valutazione, selezionare la porta di output del modulo **Evaluate Model** e selezionare **Visualize (Visualizza**).

## <a name="results"></a>Risultati

![Valutare i risultati](media/how-to-designer-sample-classification-predict-income/evaluate-result.png)

Nei risultati della valutazione è possibile osservare che le curve, ad esempio ROC, Precision-Recall e confusione, sono metriche. 

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Passaggi successivi

Esplorare gli altri esempi disponibili per la finestra di progettazione:

- [Esempio 1: regressione: stimare il prezzo di un'automobile](how-to-designer-sample-regression-automobile-price-basic.md)
- [Esempio 2: regressione: confrontare gli algoritmi per la stima del prezzo dell'automobile](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [Esempio 4-Classificazione: stimare il rischio di credito (costo sensibile)](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [Esempio 5-classificazione: varianza di stima](how-to-designer-sample-classification-churn.md)
- [Esempio 6-Classificazione: stima dei ritardi dei voli](how-to-designer-sample-classification-flight-delay.md)
- [Esempio 7-classificazione di testo: set di dati di Wikipedia SP 500](how-to-designer-sample-text-classification.md)
