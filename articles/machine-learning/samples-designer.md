---
title: Pipeline della finestra di progettazione di esempio
titleSuffix: Azure Machine Learning
description: Usare gli esempi nella finestra di progettazione di Azure Machine Learning per avviare rapidamente le pipeline di Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 03/10/2020
ms.openlocfilehash: 82476b9cc8d92c815df602496ed3dcb33014a4fd
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2020
ms.locfileid: "79037289"
---
# <a name="designer-sample-pipelines"></a>Pipeline di esempio della finestra di progettazione

Usare gli esempi incorporati nella finestra di progettazione di Azure Machine Learning per iniziare rapidamente a creare le proprie pipeline di Machine Learning. Il [repository GitHub](https://github.com/Azure/MachineLearningDesigner) della finestra di progettazione di Azure Machine Learning contiene la documentazione dettagliata per comprendere alcuni scenari comuni di Machine Learning.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://aka.ms/AMLFree).
* Un'area di lavoro di Azure Machine Learning con Enterprise SKU.


## <a name="how-to-use-sample-pipelines"></a>Come usare le pipeline di esempio

La finestra di progettazione salva una copia delle pipeline di esempio nell'area di lavoro dello studio. È possibile modificare la pipeline per adattarla alle proprie esigenze e salvarla come pipeline personalizzata. È possibile usarla come punto di partenza per avviare rapidamente i progetti.

1. Accedere a <a href="https://ml.azure.com?tabs=jre" target="_blank">ml.azure.com</a> e selezionare l'area di lavoro che si vuole usare.

1. Selezionare **Designer** (Finestra di progettazione).

1. Selezionare una pipeline di esempio nella sezione **Nuova pipeline**.

    Selezionare **Mostra più esempi** per un elenco completo di esempi.

## <a name="regression-samples"></a>Esempi di regressione

Altre informazioni sugli esempi di regressione incorporati.

| Titolo di esempio | Descrizione | 
| --- | --- |
| [Esempio 1: Regressione - Previsione dei prezzi delle automobili (base)](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-regression-automobile-price-basic.md) | Stimare i prezzi delle automobili usando la regressione lineare. |
| [Esempio 2: Regressione - Previsione dei prezzi delle automobili (avanzata)](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-regression-automobile-price-compare-algorithms.md) | Stimare i prezzi delle automobili usando la foresta delle decisioni e i regressori degli alberi delle decisioni con boosting. Confrontare i modelli per trovare l'algoritmo migliore.

## <a name="classification-samples"></a>Esempi di classificazione

Altre informazioni sugli esempi di classificazione incorporati. È possibile ottenere altre informazioni sugli esempi senza collegamenti alla documentazione aprendo gli esempi e visualizzando i commenti del modulo.

| Titolo di esempio | Descrizione | 
| --- | --- |
| [Esempio 3: Classificazione binaria con selezione delle funzionalità - Stima del reddito](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-predict-income.md) | Stimare il reddito come alto o basso, usando un albero delle decisioni con boosting a due classi. Usare la correlazione di Pearson per selezionare le funzionalità.
| [Esempio 4: Classificazione binaria con script Python personalizzato - Stima del rischio di credito](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-credit-risk-cost-sensitive.md) | Classificare le applicazioni di credito come ad alto o basso rischio. Usare il modulo di esecuzione dello script Python per ponderare i dati.
| [Esempio 5: Classificazione binaria - Stima delle relazioni con i clienti](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-churn.md) | Stimare l'abbandono dei clienti usando alberi delle decisioni con boosting a due classi. Usare SMOTE per campionare i dati distorti.
| [Esempio 7: Classificazione del testo - Set di dati Wikipedia SP 500](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-text-classification.md) | Classificare i tipi di aziende da articoli Wikipedia con regressione logistica multiclasse. |
| Esempio 12: Classificazione multiclasse - Riconoscimento delle lettere | Creare un insieme di classificatori binari per classificare le lettere scritte. |

## <a name="recommender-samples"></a>Esempi di raccomandazione

Altre informazioni sugli esempi di raccomandazione incorporati. È possibile ottenere altre informazioni sugli esempi senza collegamenti alla documentazione aprendo gli esempi e visualizzando i commenti del modulo.

| Titolo di esempio | Descrizione | 
| --- | --- |
| Esempio 10: Raccomandazione - Tweet sulla classificazione dei film | Creare un motore di raccomandazione film da titoli di film e classificazioni. |

## <a name="utility-samples"></a>Esempi di utilità

Altre informazioni sugli esempi che illustrano le utilità e le funzionalità di Machine Learning. È possibile ottenere altre informazioni sugli esempi senza collegamenti alla documentazione aprendo gli esempi e visualizzando i commenti del modulo.

| Titolo di esempio | Descrizione | 
| --- | --- |
| [Esempio 6: Usare uno script R personalizzato - Stima dei ritardi dei voli](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-flight-delay.md) |
| Esempio 8: Convalida incrociata per la classificazione binaria - Stima del reddito per adulti | Usare la convalida incrociata per creare un classificatore binario per il reddito degli adulti.
| Esempio 9: Permutation Feature Importance | Usare l'importanza della caratteristica di permutazione per calcolare i punteggi di importanza per il set di dati di test. 
| Esempio 11: Ottimizzare i parametri per la classificazione binaria - Stima del reddito per adulti | Usare gli iperparametri del modello di ottimizzazione per trovare iperparametri ottimali per creare un classificatore binario. |

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come creare e distribuire modelli di Machine Learning con l'[Esercitazione: Stimare il prezzo di un'automobile con la finestra di progettazione](tutorial-designer-automobile-price-train-score.md)
