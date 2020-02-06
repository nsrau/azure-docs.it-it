---
title: 'Progettazione: Esempio di previsione dei prezzi delle automobili (avanzato)'
titleSuffix: Azure Machine Learning
description: Creare e confrontare più modelli di regressione di Machine Learning per prevedere il prezzo di un'automobile in base a caratteristiche tecniche con la finestra di progettazione di Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.date: 12/25/2019
ms.openlocfilehash: 189b36c8d9a7c1d1e247bb2cb34795dc4e3595ce
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/02/2020
ms.locfileid: "76963343"
---
# <a name="train--compare-multiple-regression-models-to-predict-car-prices-with-azure-machine-learning-designer"></a>Eseguire il training e confrontare più modelli di regressione per prevedere i prezzi delle automobili con la finestra di progettazione di Azure Machine Learning

**Finestra di progettazione (anteprima) - Esempio 2**

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Informazioni su come creare una pipeline di Machine Learning senza scrivere una sola riga di codice usando la finestra di progettazione (anteprima). Questo esempio consente di eseguire il training e confrontare più modelli di regressione per prevedere il prezzo di un'automobile in base alle caratteristiche tecniche. Verranno forniti i fondamenti logici per le scelte effettuate in questa pipeline, in modo da poter risolvere i problemi di Machine Learning.

Se si è alle prime armi con Machine Learning, vedere la versione [base](how-to-designer-sample-regression-automobile-price-basic.md) di questa pipeline.

Ecco il grafo completato per questa pipeline:

[![Grafo della pipeline](./media/how-to-designer-sample-regression-automobile-price-compare-algorithms/graph.png)](./media/how-to-designer-sample-regression-automobile-price-compare-algorithms/graph.png#lightbox)

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [aml-ui-prereq](../../includes/aml-ui-prereq.md)]

4. Fare clic su Sample 2 per aprirlo. 

## <a name="pipeline-summary"></a>Riepilogo della pipeline

Per compilare la pipeline di Machine Learning, seguire questa procedura:

1. Ottenere i dati.
1. Pre-elaborare i dati.
1. Eseguire il training del modello.
1. Testare, valutare e confrontare i modelli.

## <a name="get-the-data"></a>Ottenere i dati

Questo esempio usa il set di dati **Automobile price data (Raw)** , che fa parte di UCI Machine Learning Repository. Il set di dati contiene 26 colonne che includono informazioni sulle automobili, ad esempio marca, modello, prezzo, caratteristiche del veicolo (come il numero di cilindri), consumo e un punteggio di rischio assicurativo.

## <a name="pre-process-the-data"></a>Pre-elaborare i dati

Le attività di preparazione dei dati principali includono la pulizia, l'integrazione, la trasformazione, la riduzione e la discretizzazione o quantizzazione dei dati. Nella finestra di progettazione sono disponibili i moduli per eseguire queste operazioni e altre attività di pre-elaborazione dei dati nel gruppo **Data Transformation** (Trasformazione dati) nel pannello sinistro.

Usare il modulo **Select Columns in Dataset** (Seleziona colonne nel set di dati) per escludere le perdite normalizzate con molti valori mancanti. Usare quindi **Clean Missing Data** (Pulisci dati mancanti) per rimuovere le righe con valori mancanti. Questo consente di creare un set di dati di training pulito.

![Pre-elaborazione dei dati](./media/how-to-designer-sample-regression-automobile-price-compare-algorithms/data-processing.png)

## <a name="train-the-model"></a>Eseguire il training del modello

I problemi di Machine Learning possono variare. Le attività comuni di Machine Learning includono sistemi di classificazione, clustering, regressione e raccomandazione, ognuno dei quali può richiedere un algoritmo diverso. La scelta dell'algoritmo dipende spesso dai requisiti del caso d'uso. Dopo aver selezionato un algoritmo, è necessario ottimizzarne i parametri per eseguire il training di un modello più accurato. È quindi necessario valutare tutti i modelli in base a metriche come accuratezza, intelligibilità ed efficienza.

Poiché lo scopo di questa pipeline è prevedere i prezzi delle automobili e la colonna label (prezzo) contiene numeri reali, un modello di regressione è una scelta ottimale. Dal momento che il numero di caratteristiche è relativamente ridotto (inferiore a 100) e tali caratteristiche non sono di tipo sparse, è probabile che il limite della decisione sia non lineare.

Per confrontare le prestazioni di algoritmi diversi, vengono usati due algoritmi non lineari, **Boosted Decision Tree Regression** (Regressione albero delle decisioni con boosting) e **Decision Forest Regression** (Regressione foresta delle decisioni), per creare i modelli. Entrambi gli algoritmi contengono parametri che è possibile modificare, ma in questo esempio vengono usati i valori predefiniti per questa pipeline.

Usare il modulo **Split data** (Dividi dati) per dividere in modo casuale i dati di input in modo che il set di dati di training contenga il 70% dei dati originali e il set di dati di test contenga il 30% dei dati originali.

## <a name="test-evaluate-and-compare-the-models"></a>Testare, valutare e confrontare i modelli

Verranno usati due diversi set di dati scelti in modo casuale per eseguire il training e il test del modello, come descritto nella sezione precedente. Dividere il set di dati e usare set di dati diversi per eseguire il training e il test del modello in modo da rendere la valutazione del modello più obiettiva.

Una volta eseguito il training del modello, usare i moduli **Score Model** (Assegna un punteggio al modello) e **Evaluate Model** (Valuta modello) per generare risultati previsti e valutare i modelli. **Score Model** (Assegna un punteggio al modello) genera previsioni per il set di dati di test usando il modello con training. Passare quindi i punteggi al modulo **Evaluate Model** (Valuta modello) per generare le metriche di valutazione.



Ecco i risultati:

![Confrontare i risultati](./media/how-to-designer-sample-regression-automobile-price-compare-algorithms/result.png)

Questi risultati mostrano che il modello creato con l'algoritmo **Boosted Decision Tree Regression** (Regressione albero delle decisioni con boosting) presenta un errore quadratico medio inferiore rispetto al modello basato sull'algoritmo **Decision Forest Regression** (Regressione foresta delle decisioni).



## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Passaggi successivi

Esplorare gli altri esempi disponibili per la finestra di progettazione:

- [Esempio 1 - Regressione: Prevedere il prezzo di un'automobile](how-to-designer-sample-regression-automobile-price-basic.md)
- [Esempio 3 - Classificazione con selezione delle caratteristiche: Previsione del reddito](how-to-designer-sample-classification-predict-income.md)
- [Esempio 4 - Classificazione: Prevedere il rischio di credito (sensibile al costo)](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [Esempio 5 - Classificazione: Prevedere la varianza](how-to-designer-sample-classification-churn.md)
- [Esempio 6 - Classificazione: Prevedere i ritardi dei voli](how-to-designer-sample-classification-flight-delay.md)
- [Esempio 7 - Classificazione: Wikipedia SP 500 Dataset](how-to-designer-sample-text-classification.md)
