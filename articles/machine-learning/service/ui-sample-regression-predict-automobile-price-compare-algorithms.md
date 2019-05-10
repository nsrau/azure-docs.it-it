---
title: 'Regressione: Stimare il prezzo e confrontare gli algoritmi'
titleSuffix: Azure Machine Learning service
description: Questo esperimento di esempio di interfaccia visiva di seguito viene illustrato come confrontare le prestazioni dei due modelli di regressione che stima il prezzo di un'automobile. Il processo include corsi di formazione, test e la valutazione del modello su set di dati di Automobile price data (Raw).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: article
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/02/2019
ms.openlocfilehash: 2a4a9e74fa7f56b67f0f4a64f6619db1c5c69a2c
ms.sourcegitcommit: 4891f404c1816ebd247467a12d7789b9a38cee7e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/08/2019
ms.locfileid: "65442139"
---
# <a name="sample-2---regression-predict-price-and-compare-algorithms"></a>Esempio 2: regressione: Stimare il prezzo e confrontare gli algoritmi

Questo esperimento di esempio di interfaccia visiva di seguito viene illustrato come confrontare le prestazioni dei due modelli di regressione che stima il prezzo di un'automobile. Il processo include set di training, testing e valutare il modello usando il **dati sui prezzi di Automobile (Raw)** set di dati.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Selezionare il **aperto** pulsante per l'esperimento di esempio 2:

    ![Aprire l'esperimento](media/ui-sample-regression-predict-automobile-price-compare-algorithms/open-sample2.png)

## <a name="related-sample"></a>Esempio correlato

[Esempio 1: regressione: Stima del prezzo dell'automobile (Basic)](ui-sample-regression-predict-automobile-price-basic.md) fornisce un semplice esperimento che consente di risolvere lo stesso problema di questo esperimento ma che usa un solo modello di regressione. Se si sta cercando un esempio di base della regressione, fare riferimento a esso.

## <a name="experiment-summary"></a>Riepilogo di esperimento

Utilizziamo questa procedura per creare l'esperimento:

1. Ottenere i dati.
1. Pre-elaborare i dati.
1. Il training del modello.
1. Test, valutare e confrontare i modelli.

Ecco l'intero grafico dell'esperimento:

[![Grafico dell'esperimento](media/ui-sample-regression-predict-automobile-price-compare-algorithms/graph.png)](media/ui-sample-regression-predict-automobile-price-compare-algorithms/graph.png#lightbox)


## <a name="get-the-data"></a>Ottenere i dati

In questo esperimento, viene usato il **dati sui prezzi di Automobile (Raw)** set di dati dal Repository UCI Machine Learning. Questo set di dati contiene 26 colonne contenenti informazioni sulle automobili, tra cui marca, modello, prezzo, caratteristiche del veicolo (ad esempio, il numero di cilindri), MPG e un punteggio di rischio assicurativo. L'obiettivo di questo esperimento è stimare il prezzo di un'automobile.

## <a name="pre-process-the-data"></a>Pre-elaborare i dati

L'attività di preparazione dei dati principali includono pulizia dei dati, integrazione, trasformazione, la riduzione e discretizzazione o la quantizzazione. Nell'interfaccia visiva grafica, è possibile trovare i moduli per eseguire queste operazioni e altri dati di attività di pre-elaborazione il **Data Transformation** gruppo nel riquadro sinistro.

In questo esperimento, viene usato il **Select Columns in Dataset** modulo Escludi normalized-losses con numerosi valori mancanti. Quindi, utilizziamo **Clean Missing Data** per rimuovere le righe contenenti valori mancanti. Ciò consente di creare un set di dati di training pulito.

![Pre-elaborazione dei dati](media/ui-sample-regression-predict-automobile-price-compare-algorithms/data-processing.png)

## <a name="train-the-model"></a>Eseguire il training del modello

Problemi di Machine learning variano. Attività comuni di machine learning include la classificazione, clustering, la regressione e sistemi di raccomandazione, ognuno dei quali potrebbe richiedere un algoritmo diverso. La scelta dell'algoritmo spesso dipende dai requisiti del caso di utilizzo. Quando si seleziona un algoritmo, è necessario ottimizzare i parametri per il training di un modello più accurato. È quindi necessario valutare tutti i modelli basati sulla metrica, ad esempio precisione, la comprensione e l'efficienza.

Poiché l'obiettivo di questo esperimento consiste nello stimare i prezzi delle automobili e perché la colonna di etichetta (prezzo) contiene i numeri reali, un modello di regressione è una buona scelta. Si considera che il numero di funzionalità è relativamente piccolo (minore di 100) e queste funzionalità non sono sparse, il limite di decisione è probabilmente non lineare.

Per confrontare le prestazioni di diversi algoritmi, usiamo due algoritmi non lineari **Boosted Decision Tree Regression** e **Decision Forest Regression**, compilare i modelli. Entrambi gli algoritmi contengono parametri che è possibile modificare, ma vengono usati i valori predefiniti per questo esperimento.

Usiamo il **Split Data** modulo per dividere in modo casuale i dati di input in modo che il set di dati di training contiene il 70% dei dati originali e i set di dati di test contiene 30% dei dati originali.

## <a name="test-evaluate-and-compare-the-models"></a>Test, valutare e confrontare i modelli

Usiamo i due diversi set di dati selezionati a caso eseguire il training e quindi testare il modello, come descritto nella sezione precedente. Si suddivide il set di dati e usare diversi set di dati per il training e test del modello per semplificare la valutazione del modello di obiettivo di altre.

Dopo il training del modello, viene usato il **Score Model** e **Evaluate Model** moduli per generare i risultati stimati e valutare i modelli. **Score Model** genera stime per il set di dati di test usando il modello con training. Viene quindi passato per i punteggi **Evaluate Model** per generare metriche di valutazione.

In questo esperimento vengono usate due istanze del **Evaluate Model** per confrontare due coppie di modelli.

Prima di tutto è confrontare due algoritmi sul set di dati di training.
In secondo luogo, si confronta due algoritmi nel set di dati di test.
Ecco i risultati:

![Confrontare i risultati](media/ui-sample-regression-predict-automobile-price-compare-algorithms/result.png)

Questi risultati mostrano che il modello compilato con **Boosted Decision Tree Regression** ha un'inferiore radice errore quadratico medio rispetto al modello basato su **Decision Forest Regression**.

Entrambi gli algoritmi presente un errore più basso nel training set rispetto al set di dati di test non visti.

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Passaggi successivi

Esplorare gli altri esempi disponibili per l'interfaccia visiva:

- [Esempio 1: regressione: Stimare il prezzo di un'automobile](ui-sample-regression-predict-automobile-price-basic.md)
- [Esempio 3: classificazione: Prevedere il rischio di credito](ui-sample-classification-predict-credit-risk-basic.md)
- [Esempio 4: classificazione: Prevedere il rischio di credito (costo sensibile)](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [Esempio 5 - classificazione: Prevedere la varianza](ui-sample-classification-predict-churn.md)
