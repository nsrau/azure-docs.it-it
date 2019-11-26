---
title: "Finestra di progettazione: stimare l'esempio prezzi auto (avanzate)"
titleSuffix: Azure Machine Learning
description: Compilare & confrontare più modelli di regressione ML per stimare il prezzo di un'automobile in base a funzionalità tecniche con Azure Machine Learning Designer.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: peterlu
ms.date: 11/04/2019
ms.openlocfilehash: 60baf2229b6c704f951e6cc54949109d5e403bc0
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74225014"
---
# <a name="train--compare-multiple-regression-models-to-predict-car-prices-with-azure-machine-learning-designer"></a>Eseguire il Training & confrontare più modelli di regressione per stimare i prezzi delle automobili con Azure Machine Learning Designer

**Esempio di finestra di progettazione (anteprima) 2**

[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-enterprise-sku.md)]

Informazioni su come creare una pipeline di Machine Learning senza scrivere una singola riga di codice usando la finestra di progettazione (anteprima). Questo esempio consente di eseguire il training e confrontare più modelli di regressione per stimare il prezzo di un'automobile in base alle funzionalità tecniche. Forniremo la logica per le scelte effettuate in questa pipeline, in modo da poter affrontare i problemi di apprendimento automatico.

Se si sta iniziando a usare Machine Learning, vedere la [versione di base](how-to-designer-sample-regression-automobile-price-basic.md) di questa pipeline.

Ecco il grafico completato per questa pipeline:

[Grafico ![della pipeline](media/how-to-designer-sample-regression-predict-automobile-price-compare-algorithms/graph.png)](media/how-to-designer-sample-regression-predict-automobile-price-compare-algorithms/graph.png#lightbox)

## <a name="prerequisites"></a>prerequisiti

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Fare clic su Sample 2 per aprirlo. 

## <a name="pipeline-summary"></a>Riepilogo della pipeline

Per compilare la pipeline di Machine Learning, seguire questa procedura:

1. Ottenere i dati.
1. Pre-elaborare i dati.
1. Eseguire il training del modello.
1. Testare, valutare e confrontare i modelli.

## <a name="get-the-data"></a>Ottenere i dati

Questo esempio usa il set di **dati automobile price data (RAW)** , che fa parte del Repository UCI Machine Learning. Questo set di dati contiene 26 colonne contenenti informazioni sulle automobili, incluse le funzionalità marca, modello, prezzo, veicolo (ad esempio il numero di cilindri), MPG e un punteggio di rischio assicurativo.

## <a name="pre-process-the-data"></a>Pre-elaborare i dati

Le attività di preparazione dei dati principali includono la pulizia, l'integrazione, la trasformazione, la riduzione e la discretizzazione o la quantizzazione dei dati. Nella finestra di progettazione è possibile trovare moduli per eseguire queste operazioni e altre attività di pre-elaborazione dei dati nel gruppo **trasformazione dati** nel riquadro sinistro.

Usare il modulo **Select Columns in DataSet** per escludere le perdite normalizzate con molti valori mancanti. Si usano quindi **Clean Missing data** per rimuovere le righe con valori mancanti. Questo consente di creare un set di dati di training puliti.

![Pre-elaborazione dei dati](media/how-to-designer-sample-regression-predict-automobile-price-compare-algorithms/data-processing.png)

## <a name="train-the-model"></a>Eseguire il training del modello

I problemi di Machine Learning variano. Le attività comuni di Machine Learning includono sistemi di classificazione, clustering, regressione e raccomandazione, ciascuno dei quali potrebbe richiedere un algoritmo diverso. La scelta dell'algoritmo dipende spesso dai requisiti del caso d'uso. Dopo aver selezionato un algoritmo, è necessario ottimizzarne i parametri per eseguire il training di un modello più accurato. È quindi necessario valutare tutti i modelli in base alle metriche come accuratezza, intelligibilità ed efficienza.

Poiché l'obiettivo di questa pipeline è stimare i prezzi delle automobili e perché la colonna Label (price) contiene numeri reali, un modello di regressione è una scelta ottimale. Poiché il numero di funzionalità è relativamente ridotto (inferiore a 100) e queste funzionalità non sono di tipo sparse, è probabile che il limite della decisione sia non lineare.

Per confrontare le prestazioni di algoritmi diversi, vengono usati due algoritmi non lineari, la **regressione dell'albero delle decisioni con boosting** e la **regressione della foresta delle decisioni**, per la compilazione dei modelli. Entrambi gli algoritmi hanno parametri che è possibile modificare, ma in questo esempio vengono usati i valori predefiniti per questa pipeline.

Usare il modulo **Split data** per dividere in modo casuale i dati di input in modo che il set di dati di training contenga il 70% dei dati originali e il set di dati di testing contenga il 30% dei dati originali.

## <a name="test-evaluate-and-compare-the-models"></a>Testare, valutare e confrontare i modelli

Usare due diversi set di dati scelti in modo casuale per eseguire il training e il test del modello, come descritto nella sezione precedente. Suddividere il set di dati e utilizzare set di dati diversi per eseguire il training e il test del modello in modo da rendere la valutazione del modello più obiettivo.

Dopo aver eseguito il training del modello, utilizzare i moduli **Score Model** e **Evaluate Model** per generare risultati stimati e valutare i modelli. **Score Model** genera stime per il set di dati di test usando il modello sottoposto a training. Passare quindi i punteggi per **valutare il modello** per generare le metriche di valutazione.



Ecco i risultati:

![Confrontare i risultati](media/how-to-designer-sample-regression-predict-automobile-price-compare-algorithms/result.png)

Questi risultati mostrano che il modello compilato con la **regressione dell'albero delle decisioni con boosting** ha un errore quadratico medio più basso rispetto al modello basato sulla **regressione della foresta delle decisioni**.

Entrambi gli algoritmi hanno un errore inferiore nel set di dati di training rispetto al set di dati di test non visibile.

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Passaggi successivi

Esplorare gli altri esempi disponibili per la finestra di progettazione:

- [Esempio 1: regressione: stimare il prezzo di un'automobile](how-to-designer-sample-regression-automobile-price-basic.md)
- [Esempio 3: classificazione con selezione delle caratteristiche: stima del reddito](how-to-designer-sample-classification-predict-income.md)
- [Esempio 4-Classificazione: stimare il rischio di credito (costo sensibile)](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [Esempio 5-classificazione: varianza di stima](how-to-designer-sample-classification-churn.md)
- [Esempio 6-Classificazione: stima dei ritardi dei voli](how-to-designer-sample-classification-flight-delay.md)
- [Esempio 7-classificazione di testo: set di dati di Wikipedia SP 500](how-to-designer-sample-text-classification.md)
