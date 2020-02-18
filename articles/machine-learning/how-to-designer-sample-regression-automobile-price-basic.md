---
title: 'Progettazione: Esempio di previsione dei prezzi delle automobili (base)'
titleSuffix: Azure Machine Learning
description: Creare un modello di regressione di Machine Learning per prevedere il prezzo di un'automobile senza scrivere una sola riga di codice con la finestra di progettazione di Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.date: 02/11/2020
ms.openlocfilehash: 58adbc7607b0b32e79123b701c37f55ce7cc1d2e
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77138120"
---
# <a name="use-regression-to-predict-car-prices-with-azure-machine-learning-designer"></a>Usare la regressione per prevedere i prezzi delle automobili con la finestra di progettazione di Azure Machine Learning

**Finestra di progettazione (anteprima) - Esempio 1**

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Informazioni su come creare un modello di regressione di Machine Learning senza scrivere una sola riga di codice usando la finestra di progettazione (anteprima).

Questa pipeline consente di eseguire il training di un **regressore lineare** per prevedere il prezzo di un'automobile in base a caratteristiche tecniche quali marca, modello, potenza e dimensioni. Poiché si sta provando a rispondere alla domanda "Quanto costa?", si tratta di un problema di regressione. Tuttavia, in questo esempio è possibile applicare gli stessi passaggi fondamentali per risolvere qualsiasi tipo di problema di Machine Learning, indipendentemente dal fatto che si tratti di regressione, classificazione, clustering e così via.

I passaggi fondamentali di un modello di Machine Learning di training sono:

1. Ottenere i dati
1. Pre-elaborare i dati
1. Eseguire il training del modello
1. Valutare il modello

Ecco il grafo finale completato della pipeline. Questo articolo fornisce i fondamenti logici per tutti i moduli, in modo da poter prendere decisioni simili in autonomia.

![Grafo della pipeline](./media/how-to-designer-sample-regression-automobile-price-basic/overall-graph.png)

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [aml-ui-prereq](../../includes/aml-ui-prereq.md)]

4. Fare clic su Sample 1 per aprirlo.


## <a name="get-the-data"></a>Ottenere i dati

Questo esempio usa il set di dati **Automobile price data (Raw)** , che fa parte di UCI Machine Learning Repository. Il set di dati contiene 26 colonne che includono informazioni sulle automobili, ad esempio marca, modello, prezzo, caratteristiche del veicolo (come il numero di cilindri), consumo e un punteggio di rischio assicurativo. Lo scopo di questo esempio è prevedere il prezzo dell'auto.

## <a name="pre-process-the-data"></a>Pre-elaborare i dati

Le attività di preparazione dei dati principali includono la pulizia, l'integrazione, la trasformazione, la riduzione e la discretizzazione o quantizzazione dei dati. Nella finestra di progettazione sono disponibili i moduli per eseguire queste operazioni e altre attività di pre-elaborazione dei dati nel gruppo **Data Transformation** (Trasformazione dati) nel pannello sinistro.

Usare il modulo **Select Columns in Dataset** (Seleziona colonne nel set di dati) per escludere le perdite normalizzate con molti valori mancanti. Usare quindi **Clean Missing Data** (Pulisci dati mancanti) per rimuovere le righe con valori mancanti. Questo consente di creare un set di dati di training pulito.

![Pre-elaborazione dei dati](./media/how-to-designer-sample-regression-automobile-price-basic/data-processing.png)

## <a name="train-the-model"></a>Eseguire il training del modello

I problemi di Machine Learning possono variare. Le attività comuni di Machine Learning includono sistemi di classificazione, clustering, regressione e raccomandazione, ognuno dei quali può richiedere un algoritmo diverso. La scelta dell'algoritmo dipende spesso dai requisiti del caso d'uso. Dopo aver selezionato un algoritmo, è necessario ottimizzarne i parametri per eseguire il training di un modello più accurato. È quindi necessario valutare tutti i modelli in base a metriche come accuratezza, intelligibilità ed efficienza.

Poiché lo scopo di questo esempio è prevedere i prezzi delle automobili e la colonna con etichetta (prezzo) corrisponde a dati continui, un modello di regressione è una scelta ottimale. Per questa pipeline viene usata la **regressione lineare**.

Usare il modulo **Split data** (Dividi dati) per dividere in modo casuale i dati di input in modo che il set di dati di training contenga il 70% dei dati originali e il set di dati di test contenga il 30% dei dati originali.

## <a name="test-evaluate-and-compare"></a>Testare, valutare e confrontare

Dividere il set di dati e usare set di dati diversi per eseguire il training e il test del modello in modo da rendere la valutazione del modello più obiettiva.

Una volta eseguito il training del modello, è possibile usare i moduli **Score Model** (Assegna un punteggio al modello) e **Evaluate Model** (Valuta modello) per generare risultati previsti e valutare i modelli.

**Score Model** (Assegna un punteggio al modello) genera previsioni per il set di dati di test usando il modello con training. Per controllare il risultato, selezionare la porta di output di **Score Model** (Assegna un punteggio al modello) e quindi selezionare **Visualize** (Visualizza).

![Assegnare un punteggio al risultato](./media/how-to-designer-sample-regression-automobile-price-basic/sample1-score-1225.png)

Passare i punteggi al modulo **Evaluate Model** (Valuta modello) per generare le metriche di valutazione. Per controllare il risultato, selezionare la porta di output di **Evaluate Model** (Valuta modello) e quindi selezionare **Visualize** (Visualizza).

![Valutare il risultato](./media/how-to-designer-sample-regression-automobile-price-basic/sample1-evaluate-1225.png)

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Passaggi successivi

Esplorare gli altri esempi disponibili per la finestra di progettazione:

- [Esempio 2 - Regressione: Confrontare gli algoritmi per la previsione del prezzo delle automobili](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [Esempio 3 - Classificazione con selezione delle caratteristiche: Previsione del reddito](how-to-designer-sample-classification-predict-income.md)
- [Esempio 4 - Classificazione: Prevedere il rischio di credito (sensibile al costo)](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [Esempio 5 - Classificazione: Prevedere la varianza](how-to-designer-sample-classification-churn.md)
- [Esempio 6 - Classificazione: Prevedere i ritardi dei voli](how-to-designer-sample-classification-flight-delay.md)
- [Esempio 7 - Classificazione del testo: Set di dati SP 500 di Wikipedia](how-to-designer-sample-text-classification.md)