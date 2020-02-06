---
title: 'Progettazione: Esempio di classificazione e previsione del reddito'
titleSuffix: Azure Machine Learning
description: Seguire questo esempio per creare un classificatore senza codice per prevedere il reddito con la finestra di progettazione di Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.date: 12/25/2019
ms.openlocfilehash: 560339fb04e3bbbe42c4370655e74e8536a7c015
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/02/2020
ms.locfileid: "76963369"
---
# <a name="build-a-classifier--use-feature-selection-to-predict-income-with-azure-machine-learning-designer"></a>Creare un classificatore e usare la selezione delle caratteristiche per prevedere il reddito con la finestra di progettazione di Azure Machine Learning

**Finestra di progettazione (anteprima) - Esempio 3**

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Informazioni su come creare un classificatore di Machine Learning senza scrivere una sola riga di codice usando la finestra di progettazione (anteprima). Questo esempio esegue il training di un **albero delle decisioni con boosting a due classi** per prevedere il reddito della popolazione adulta (>=50.000 o <=50.000).

Poiché si prova a rispondere alla domanda "Quale?", si tratta di un problema di classificazione. Tuttavia, è possibile applicare lo stesso processo fondamentale per risolvere qualsiasi tipo di problema di Machine Learning, indipendentemente dal fatto che si tratti di regressione, classificazione, clustering e così via.

Ecco il grafo della pipeline finale per questo esempio:

![Grafo della pipeline](./media/how-to-designer-sample-classification-predict-income/overall-graph.png)

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [aml-ui-prereq](../../includes/aml-ui-prereq.md)]

4. Fare clic su Sample 3 per aprirlo.



## <a name="data"></a>Data

Il set di dati contiene 14 caratteristiche e una colonna di etichetta. Sono disponibili più tipi di caratteristiche, numeriche e categoriche. Il diagramma seguente mostra un estratto del set di dati: ![data](media/how-to-designer-sample-classification-predict-income/sample3-dataset-1225.png)



## <a name="pipeline-summary"></a>Riepilogo della pipeline

Per creare la pipeline, seguire questa procedura:

1. Trascinare il modulo Adult Census Income Binary DataSet nell'area di disegno della pipeline.
1. Aggiungere un modulo **Split Data** (Dividi dati) per creare i set di training e di test. Impostare la frazione di righe nel primo set di dati di output su 0,7. Questa impostazione specifica che l'output del 70% dei dati verrà effettuato sulla porta sinistra del modulo e il resto sulla porta destra. Il set di dati a sinistra verrà usato per il training e quello a destra per il test.
1. Aggiungere il modulo **Filter Based Feature Selection** (Selezione caratteristiche basata su filtro) per selezionare 5 caratteristiche per PearsonCorrelation. 
1. Aggiungere un modulo **Two-Class Boosted Decision Tree** (Albero delle decisioni con boosting a due classi) per inizializzare un classificatore di albero delle decisioni con boosting.
1. Aggiungere un modulo **Train Model** (Esegui training modello). Connettere il classificatore dal passaggio precedente alla porta di input sinistra del modulo **Train Model** (Esegui training modello). Connettere il set di dati filtrato dal modulo Filter Based Feature Selection (Selezione caratteristiche basata su filtro) come set di dati di training.  Il modulo **Train Model** (Esegui training modello) eseguirà il training del classificatore.
1. Aggiungere il modulo Select Columns Transformation and Apply Transformation (Seleziona trasformazione colonne e applica trasformazione) per applicare la stessa trasformazione (selezione caratteristiche basata su filtro) al set di dati di test.
![apply-transformation](./media/how-to-designer-sample-classification-predict-income/transformation.png)
1. Aggiungere il modulo **Score Model** (Assegna un punteggio al modello) e connettere il modulo **Train Model** (Esegui training modello). Aggiungere quindi il set di test, ovvero l'output del modulo Apply Transformation (Applica trasformazione) che applica la selezione delle caratteristiche anche al set di test, al modulo **Score Model** (Assegna un punteggio al modello). Il modulo **Score Model** (Assegna un punteggio al modello) genererà le previsioni. È possibile selezionare la relativa porta di output per visualizzare le previsioni e le probabilità della classe positiva.


    Questa pipeline contiene due moduli per l'assegnazione dei punteggi e in quello di destra è stata esclusa la colonna label prima di eseguire la previsione, al fine di procedere alla preparazione per la distribuzione di un endpoint in tempo reale, perché l'input del servizio Web prevede solo caratteristiche e non etichette. 

1. Aggiungere un modulo **Evaluate Model** (Valuta modello) e connettere il set di dati con punteggio alla relativa porta di input sinistra. Per visualizzare i risultati della valutazione, selezionare la porta di output del modulo **Evaluate Model** (Valuta modello) e quindi **Visualize** (Visualizza).

## <a name="results"></a>Risultati

![Valutare i risultati](media/how-to-designer-sample-classification-predict-income/sample3-evaluate-1225.png)

Nei risultati della valutazione è possibile osservare le curve, ad esempio ROC e Precision-Recall, e la matrice di confusione. 

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Passaggi successivi

Esplorare gli altri esempi disponibili per la finestra di progettazione:

- [Esempio 1 - Regressione: Prevedere il prezzo di un'automobile](how-to-designer-sample-regression-automobile-price-basic.md)
- [Esempio 2 - Regressione: Confrontare gli algoritmi per la previsione del prezzo delle automobili](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [Esempio 4 - Classificazione: Prevedere il rischio di credito (sensibile al costo)](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [Esempio 5 - Classificazione: Prevedere la varianza](how-to-designer-sample-classification-churn.md)
- [Esempio 6 - Classificazione: Prevedere i ritardi dei voli](how-to-designer-sample-classification-flight-delay.md)
- [Esempio 7 - Classificazione: Wikipedia SP 500 Dataset](how-to-designer-sample-text-classification.md)
