---
title: 'Esempio di interfaccia visiva #3: Classificazione per la stima del rischio di credito'
titleSuffix: Azure Machine Learning
description: Informazioni su come creare un classificatore di Machine Learning senza scrivere una singola riga di codice usando l'interfaccia visiva.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/10/2019
ms.openlocfilehash: 26e3f5f237cdd148d12f0a413eb97ed52cb84b49
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2019
ms.locfileid: "70997047"
---
# <a name="sample-3---classification-predict-credit-risk"></a>Esempio 3-classificazione: Prevedere il rischio di credito

Informazioni su come creare un classificatore di Machine Learning senza scrivere una singola riga di codice usando l'interfaccia visiva. Questo esempio consente di eseguire il training di un **albero delle decisioni con boosting a due classi** per stimare il rischio di credito (alto o basso) in base alle informazioni sulle applicazioni di credito, ad esempio la cronologia dei crediti, l'età e il numero di

Poiché si sta provando a rispondere alla domanda "quale?" si tratta di un problema di classificazione. Tuttavia, è possibile applicare lo stesso processo fondamentale per risolvere qualsiasi tipo di problema di Machine Learning, indipendentemente dal fatto che si tratti di regressione, classificazione, clustering e così via.

Ecco il grafo completato per questo esperimento:

![Grafico dell'esperimento](media/ui-sample-classification-predict-credit-risk-basic/overall-graph.png)

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Selezionare il pulsante **Apri** per l'esperimento Sample 3:

    ![Aprire l'esperimento](media/ui-sample-classification-predict-credit-risk-basic/open-sample3.png)

## <a name="related-sample"></a>Esempio correlato

[Esempio 4-Classificazione: La stima del rischio di credito (sensibile](ui-sample-classification-predict-credit-risk-cost-sensitive.md) ai costi) fornisce un esperimento avanzato che risolve lo stesso problema di questo esperimento. Viene illustrato come eseguire una classificazione _sensibile ai costi_ usando un modulo **Execute Python script** e confrontare le prestazioni di due algoritmi di classificazione binaria. Per ulteriori informazioni su come compilare gli esperimenti di classificazione, vedere.

## <a name="data"></a>Data

Il set di dati della carta di credito tedesco viene usato dal repository UC Irvine.
Il set di dati contiene 1.000 esempi con 20 funzionalità e 1 etichetta. Ogni esempio rappresenta una persona. Le funzionalità includono le funzionalità numeriche e categoriche. Per il significato delle funzionalità categoriche, vedere il [sito Web UCI](https://archive.ics.uci.edu/ml/datasets/Statlog+%28German+Credit+Data%29) . L'ultima colonna è l'etichetta, che indica il rischio di credito e dispone solo di due valori possibili: High Credit Risk = 2 e low Credit Risk = 1.

## <a name="experiment-summary"></a>Riepilogo esperimento

Per creare l'esperimento, attenersi alla procedura seguente:

1. Trascinare il modulo del set di dati di dati UCI della carta di credito tedesco nell'area di disegno dell'esperimento.
1. Aggiungere un modulo **Modifica metadati** in modo che sia possibile aggiungere nomi significativi per ogni colonna.
1. Aggiungere un modulo **Split data** per creare i set di training e di test. Impostare la frazione di righe nel primo set di dati di output su 0,7. Questa impostazione specifica che il 70% dei dati verrà restituito alla porta sinistra del modulo e il resto sulla porta destra. Il set di dati a sinistra viene usato per il training e quello giusto per i test.
1. Aggiungere un modulo a **albero delle decisioni con boosting a due classi** per inizializzare un classificatore di albero delle decisioni con boosting.
1. Aggiungere un modulo **Train Model** . Connettere il classificatore dal passaggio precedente alla porta di input sinistra del modello di **Training**. Aggiungere il set di training (la porta di output sinistra dei **dati di suddivisione**) alla porta di input destra del **modello Train**. Il training **Model** eseguirà il training del classificatore.
1. Aggiungere un modulo **Score Model** e connettervi il modulo **Train Model** . Aggiungere quindi il set di test (la porta destra dei **dati di suddivisione**) al **modello di Punteggio**. Il **modello di Punteggio** effettuerà le stime. È possibile selezionare la relativa porta di output per visualizzare le stime e le probabilità della classe positiva.
1. Aggiungere un modulo **Evaluate Model** e connettere il set di dati con punteggio alla relativa porta di input sinistra. Per visualizzare i risultati della valutazione, selezionare la porta di output del modulo **Evaluate Model** e selezionare **Visualize (Visualizza**).

Ecco il grafico completo dell'esperimento:

![Grafico dell'esperimento](media/ui-sample-classification-predict-credit-risk-basic/overall-graph.png)

## <a name="results"></a>Risultati

![Valutare i risultati](media/ui-sample-classification-predict-credit-risk-basic/evaluate-result.png)

Nei risultati della valutazione è possibile notare che l'AUC del modello è 0,776. Alla soglia 0,5, la precisione è 0,621, il richiamo è 0,456 e il Punteggio F1 è 0,526.

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Passaggi successivi

Esplorare gli altri esempi disponibili per l'interfaccia visiva:

- [Esempio 1: regressione: Stimare il prezzo di un'automobile](ui-sample-regression-predict-automobile-price-basic.md)
- [Esempio 2: regressione: Confrontare gli algoritmi per la stima del prezzo dell'automobile](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [Esempio 4-Classificazione: Stimare il rischio di credito (costo sensibile)](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [Esempio 5-classificazione: Varianza stima](ui-sample-classification-predict-churn.md)
- [Esempio 6-Classificazione: Stimare i ritardi dei voli](ui-sample-classification-predict-flight-delay.md)
