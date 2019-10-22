---
title: 'Esempio di interfaccia visiva #3: classificazione per la stima del rischio di credito'
titleSuffix: Azure Machine Learning
description: Informazioni su come creare un classificatore di Machine Learning senza scrivere una singola riga di codice usando l'interfaccia visiva.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 09/23/2019
ms.openlocfilehash: 4649303b8ee643130b8e254f01bfffbe8ad9eb2b
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693493"
---
# <a name="sample-3---classification-predict-credit-risk"></a>Esempio 3-classificazione: stima del rischio di credito

Informazioni su come creare un classificatore di Machine Learning senza scrivere una singola riga di codice usando l'interfaccia visiva. Questo esempio consente di eseguire la pipeline di un **albero delle decisioni con boosting a due classi** per stimare il rischio di credito (alto o basso) in base alle informazioni sulle applicazioni di credito, ad esempio la cronologia dei crediti, l'età e il numero di

Poiché la domanda risponde a "quale?" si tratta di un problema di classificazione. Tuttavia, è possibile applicare lo stesso processo fondamentale per risolvere qualsiasi tipo di problema di Machine Learning, indipendentemente dal fatto che si tratti di regressione, classificazione, clustering e così via.

Ecco il grafico della pipeline finale per questo esempio:

![Grafico della pipeline](media/how-to-ui-sample-classification-predict-credit-risk-basic/overall-graph.png)

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Selezionare il pulsante **Apri** per la pipeline di esempio 3:

    ![Aprire la pipeline](media/how-to-ui-sample-classification-predict-credit-risk-basic/open-sample3.png)

## <a name="related-sample"></a>Esempio correlato

[Esempio 4-Classificazione: la stima del rischio di credito (sensibile ai costi)](how-to-ui-sample-classification-predict-credit-risk-cost-sensitive.md) fornisce una pipeline avanzata che risolve lo stesso problema di questo esempio. Viene illustrato come eseguire una classificazione *sensibile ai costi* usando un modulo **Execute Python script** e confrontare le prestazioni di due algoritmi di classificazione binaria. Per altre informazioni su come creare pipeline di classificazione, fare riferimento ad esso.


## <a name="data"></a>Dati

Nell'esempio viene usato il set di dati della carta di credito tedesca del repository UC Irvine. Contiene 1.000 campioni con 20 funzionalità e 1 etichetta. Ogni esempio rappresenta una persona. Le funzionalità includono le funzionalità numeriche e categoriche. Per il significato delle funzionalità categoriche, vedere il [sito Web UCI](https://archive.ics.uci.edu/ml/datasets/Statlog+%28German+Credit+Data%29) . L'ultima colonna è l'etichetta, che indica il rischio di credito e dispone solo di due valori possibili: High Credit Risk = 2 e low Credit Risk = 1.

## <a name="pipeline-summary"></a>Riepilogo della pipeline

Per creare la pipeline, attenersi alla procedura seguente:

1. Trascinare il modulo del set di dati di dati UCI della carta di credito tedesco nell'area di disegno della pipeline.
1. Aggiungere un modulo **Modifica metadati** in modo che sia possibile aggiungere nomi significativi per ogni colonna.
1. Aggiungere un modulo **Split data** per creare i set di training e di test. Impostare la frazione di righe nel primo set di dati di output su 0,7. Questa impostazione specifica che il 70% dei dati verrà restituito alla porta sinistra del modulo e il resto sulla porta destra. Il set di dati a sinistra viene usato per il training e quello giusto per i test.
1. Aggiungere un modulo a **albero delle decisioni con boosting a due classi** per inizializzare un classificatore di albero delle decisioni con boosting.
1. Aggiungere un modulo **Train Model** . Connettere il classificatore dal passaggio precedente alla porta di input sinistra del modello di **Training**. Aggiungere il set di training (la porta di output sinistra dei **dati di suddivisione**) alla porta di input destra del **modello Train**. Il training **Model** eseguirà il training del classificatore.
1. Aggiungere un modulo **Score Model** e connettervi il modulo **Train Model** . Aggiungere quindi il set di test (la porta destra dei **dati di suddivisione**) al **modello di Punteggio**. Il **modello di Punteggio** effettuerà le stime. È possibile selezionare la relativa porta di output per visualizzare le stime e le probabilità della classe positiva.
1. Aggiungere un modulo **Evaluate Model** e connettere il set di dati con punteggio alla relativa porta di input sinistra. Per visualizzare i risultati della valutazione, selezionare la porta di output del modulo **Evaluate Model** e selezionare **Visualize (Visualizza**).

## <a name="results"></a>Risultati

![Valutare i risultati](media/how-to-ui-sample-classification-predict-credit-risk-basic/evaluate-result.png)

Nei risultati della valutazione è possibile notare che l'AUC del modello è 0,776. Alla soglia 0,5, la precisione è 0,621, il richiamo è 0,456 e il Punteggio F1 è 0,526.

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Passaggi successivi

Esplorare gli altri esempi disponibili per l'interfaccia visiva:

- [Esempio 1: regressione: stimare il prezzo di un'automobile](how-to-ui-sample-regression-predict-automobile-price-basic.md)
- [Esempio 2: regressione: confrontare gli algoritmi per la stima del prezzo dell'automobile](how-to-ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [Esempio 4-Classificazione: stimare il rischio di credito (costo sensibile)](how-to-ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [Esempio 5-classificazione: varianza di stima](how-to-ui-sample-classification-predict-churn.md)
- [Esempio 6-Classificazione: stima dei ritardi dei voli](how-to-ui-sample-classification-predict-flight-delay.md)
- [Esempio 7-classificazione di testo: revisioni della documentazione](how-to-ui-sample-text-classification.md)