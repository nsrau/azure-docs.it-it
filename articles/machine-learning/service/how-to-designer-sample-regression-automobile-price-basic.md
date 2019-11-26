---
title: 'Finestra di progettazione: stima prezzi auto (base)'
titleSuffix: Azure Machine Learning
description: Creare un modello di regressione ML per stimare il prezzo di un'automobile senza scrivere una singola riga di codice con Azure Machine Learning Designer.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: peterlu
ms.date: 11/04/2019
ms.openlocfilehash: 4dfcc76c31452d93d30cd9f2e4c649924b68c203
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74213858"
---
# <a name="use-regression-to-predict-car-prices-with-azure-machine-learning-designer"></a>Usare la regressione per stimare i prezzi delle automobili con Azure Machine Learning Designer

**Finestra di progettazione (anteprima) campione 1**

[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-enterprise-sku.md)]

Informazioni su come creare un modello di regressione di Machine Learning senza scrivere una sola riga di codice usando la finestra di progettazione (anteprima).

Questa pipeline addestra un **regressore della foresta delle decisioni** per stimare il prezzo di un'automobile in base a funzionalità tecniche quali marca, modello, potenza e dimensioni. Poiché si sta provando a rispondere alla domanda "Qual è?" si tratta di un problema di regressione. Tuttavia, in questo esempio è possibile applicare gli stessi passaggi fondamentali per risolvere qualsiasi tipo di problema di Machine Learning, indipendentemente dal fatto che si tratti di regressione, classificazione, clustering e così via.

I passaggi fondamentali di un modello di apprendimento automatico del training sono:

1. Ottenere i dati
1. Pre-elaborare i dati
1. Eseguire il training del modello
1. Valutare il modello

Ecco il grafico finale completato della pipeline. Questo articolo fornisce la spiegazione razionale per tutti i moduli, in modo da poter prendere decisioni simili.

![Grafico della pipeline](media/how-to-designer-sample-regression-predict-automobile-price-basic/overall-graph.png)

## <a name="prerequisites"></a>prerequisiti

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Fare clic sul campione 1 per aprirlo.


## <a name="get-the-data"></a>Ottenere i dati

Questo esempio usa il set di **dati automobile price data (RAW)** , che fa parte del Repository UCI Machine Learning. Il set di dati contiene 26 colonne contenenti informazioni sulle automobili, incluse le funzionalità marca, modello, prezzo, veicolo (ad esempio il numero di cilindri), MPG e un punteggio di rischio assicurativo. Lo scopo di questo esempio è stimare il prezzo dell'auto.

## <a name="pre-process-the-data"></a>Pre-elaborare i dati

Le attività di preparazione dei dati principali includono la pulizia, l'integrazione, la trasformazione, la riduzione e la discretizzazione o la quantizzazione dei dati. Nella finestra di progettazione è possibile trovare moduli per eseguire queste operazioni e altre attività di pre-elaborazione dei dati nel gruppo **trasformazione dati** nel riquadro sinistro.

Usare il modulo **Select Columns in DataSet** per escludere le perdite normalizzate con molti valori mancanti. Usare quindi **Clean Missing data** per rimuovere le righe con valori mancanti. Questo consente di creare un set di dati di training puliti.

![Pre-elaborazione dei dati](./media/how-to-designer-sample-regression-predict-automobile-price-basic/data-processing.png)

## <a name="train-the-model"></a>Eseguire il training del modello

I problemi di Machine Learning variano. Le attività comuni di Machine Learning includono sistemi di classificazione, clustering, regressione e raccomandazione, ciascuno dei quali potrebbe richiedere un algoritmo diverso. La scelta dell'algoritmo dipende spesso dai requisiti del caso d'uso. Dopo aver selezionato un algoritmo, è necessario ottimizzarne i parametri per eseguire il training di un modello più accurato. È quindi necessario valutare tutti i modelli in base alle metriche come accuratezza, intelligibilità ed efficienza.

Poiché lo scopo di questo esempio è stimare i prezzi delle automobili e perché la colonna Label (price) contiene numeri reali, un modello di regressione è una scelta ottimale. Poiché il numero di funzionalità è relativamente ridotto (inferiore a 100) e queste funzionalità non sono di tipo sparse, è probabile che il limite della decisione sia non lineare. Quindi usiamo la **regressione della foresta delle decisioni** per questa pipeline.

Usare il modulo **Split data** per dividere in modo casuale i dati di input in modo che il set di dati di training contenga il 70% dei dati originali e il set di dati di testing contenga il 30% dei dati originali.

## <a name="test-evaluate-and-compare"></a>Test, valutazione e confronto

Suddividere il set di dati e utilizzare set di dati diversi per eseguire il training e il test del modello in modo da rendere la valutazione del modello più obiettivo.

Dopo aver eseguito il training del modello, è possibile utilizzare i moduli **Score Model** e **Evaluate Model** per generare risultati stimati e valutare i modelli.

**Score Model** genera stime per il set di dati di test usando il modello sottoposto a training. Per controllare il risultato, selezionare la porta di output di **Score Model** e quindi selezionare **Visualize (Visualizza**).

![Risultato del Punteggio](./media/how-to-designer-sample-regression-predict-automobile-price-basic/score-result.png)

Passare i punteggi al modulo **Evaluate Model** per generare le metriche di valutazione. Per controllare il risultato, selezionare la porta di output del **modello Evaluate** e quindi selezionare **Visualize (Visualizza**).

![Risultato della valutazione](./media/how-to-designer-sample-regression-predict-automobile-price-basic/evaluate-result.png)

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Passaggi successivi

Esplorare gli altri esempi disponibili per la finestra di progettazione:

- [Esempio 2: regressione: confrontare gli algoritmi per la stima del prezzo dell'automobile](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [Esempio 3: classificazione con selezione delle caratteristiche: stima del reddito](how-to-designer-sample-classification-predict-income.md)
- [Esempio 4-Classificazione: stimare il rischio di credito (costo sensibile)](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [Esempio 5-classificazione: varianza di stima](how-to-designer-sample-classification-churn.md)
- [Esempio 6-Classificazione: stima dei ritardi dei voli](how-to-designer-sample-classification-flight-delay.md)
- [Esempio 7-classificazione di testo: set di dati di Wikipedia SP 500](how-to-designer-sample-text-classification.md)