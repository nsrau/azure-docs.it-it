---
title: 'Classificazione: Prevedere il rischio di credito'
titleSuffix: Azure Machine Learning service
description: Questo esperimento di esempio di interfaccia visiva di seguito viene illustrato come eseguire la classificazione binaria per prevedere il rischio di credito basata sulle informazioni fornite in un'applicazione di carta di credito.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: article
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/02/2019
ms.openlocfilehash: 3d4ec3c71aaed6bddb012fb17ee5bb96da00cd76
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028531"
---
# <a name="sample-3---classification-predict-credit-risk"></a>Esempio 3: classificazione: Prevedere il rischio di credito

Questo esperimento di esempio di interfaccia visiva di seguito viene illustrato come eseguire la classificazione binaria per prevedere il rischio di credito basata sulle informazioni fornite in un'applicazione di carta di credito. Mostra come è possibile eseguire la classificazione di base, incluse le operazioni di elaborazione dei dati, dividere il set di dati in training set e test, il training del modello, assegnare un punteggio al set di dati di test e valutare le stime.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Selezionare il **aperto** pulsante per l'esperimento di esempio 3:

    ![Aprire l'esperimento](media/ui-sample-classification-predict-credit-risk-basic/open-sample3.png)

## <a name="related-sample"></a>Esempio correlato

[Esempio 4: classificazione: (Costo sensibili) previsione del rischio di credito](ui-sample-classification-predict-credit-risk-cost-sensitive.md) fornisce un esperimento avanzato che lo stesso problema di questo esperimento è stato risolto. Viene illustrato come eseguire _costo sensibile_ classificazione tramite un' **Execute Python Script** modulo e confrontare le prestazioni dei due algoritmi di classificazione binaria. Se si desidera per ulteriori informazioni su come creare esperimenti di classificazione, fare riferimento a esso.

## <a name="data"></a>Dati

Viene usato il set di dati German Credit Card dal repository UC Irvine.
Il set di dati contiene 1000 campioni con 1 etichetta e 20 funzionalità. Ogni esempio rappresenta una persona. Le funzionalità includono funzionalità numeriche e di categoria. Vedere le [sito Web UCI](https://archive.ics.uci.edu/ml/datasets/Statlog+%28German+Credit+Data%29) per il significato delle funzionalità categoriche. L'ultima colonna è l'etichetta, che indica il rischio di credito e dispone di due soli valori possibili: rischio di credito elevato = 2 e a basso rischio di credito = 1.

## <a name="experiment-summary"></a>Riepilogo di esperimento


È seguire questi passaggi per creare l'esperimento:

1. Trascinare il modulo di German Credit Card UCI Data set di dati nell'area di disegno dell'esperimento.
1. Aggiungere un **Edit Metadata** modulo in modo che possiamo aggiungere nomi significativi per ogni colonna.
1. Aggiungere un **Split Data** modulo per creare set di training e test. Impostare la frazione di righe del primo set di dati di output su 0,7. Questa impostazione specifica che il 70% dei dati sarà output alla porta di sinistra del modulo e le restanti verso la porta di destra. Usiamo il set di dati a sinistra per il training e quello giusto per il test.
1. Aggiungere un **Two-Class Boosted Decision Tree** modulo per inizializzare un classificatore dell'albero delle decisioni con Boosting.
1. Aggiungere un **Train Model** modulo. Connettere la funzione di classificazione nel passaggio precedente per la porta di input sinistra del **Train Model**. Aggiungere il set di training (porta di output sinistra di **Split Data**) alla porta di input destra del **Train Model**. Il **Train Model** eseguirà il training del classificatore.
1. Aggiungere un **Score Model** modulo e connettere il **Train Model** modulo ad esso. Aggiungere quindi il set di test (porta di destra del **Split Data**) per il **Score Model**. Il **Score Model** apporterà le stime. È possibile selezionare la porta di output per visualizzare le stime e probabilità la classe positiva.
1. Aggiungere un **Evaluate Model** modulo e connettere il set di dati con punteggio per la porta di input a sinistra. Per visualizzare i risultati della valutazione, selezionare la porta di output del **Evaluate Model** modulo e selezionare **Visualize**.
    
Ecco il grafico completo esperimento:

![Grafico dell'esperimento](media/ui-sample-classification-predict-credit-risk-basic/overall-graph.png)


## <a name="results"></a>Risultati

![Valutare i risultati](media/ui-sample-classification-predict-credit-risk-basic/evaluate-result.png)

Nei risultati della valutazione, si noterà che l'area sottesa alla curva del modello è 0.776. La precisione è 0.621 nella soglia di 0,5, il richiamo è 0,456 e il punteggio F1 è 0.526.

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Passaggi successivi

Esplorare gli altri esempi disponibili per l'interfaccia visiva:

- [Esempio 1: regressione: Stimare il prezzo di un'automobile](ui-sample-regression-predict-automobile-price-basic.md)
- [Esempio 2: regressione: Confrontare gli algoritmi per la stima di prezzo di un'automobile](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [Esempio 4: classificazione: Prevedere il rischio di credito (costo sensibile)](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [Esempio 5 - classificazione: Prevedere la varianza](ui-sample-classification-predict-churn.md)