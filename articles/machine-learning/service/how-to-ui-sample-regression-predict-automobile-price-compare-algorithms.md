---
title: 'Esempio di interfaccia visiva #3: regressione al prezzo e confrontare gli algoritmi'
titleSuffix: Azure Machine Learning
description: Questo articolo illustra come creare un esperimento di apprendimento automatico complesso senza scrivere una sola riga di codice usando l'interfaccia visiva. Informazioni su come eseguire il training e il confronto di più modelli di regressione per stimare il prezzo di un'automobile in base alle funzionalità tecniche
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/10/2019
ms.openlocfilehash: c40d76b87ca7437e25c567176b0309f08f3ca9f2
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/19/2019
ms.locfileid: "71131571"
---
# <a name="sample-2---regression-predict-price-and-compare-algorithms"></a>Esempio 2: regressione: Stimare il prezzo e confrontare gli algoritmi

Informazioni su come creare un esperimento di apprendimento automatico complesso senza scrivere una singola riga di codice usando l'interfaccia visiva. Questo esempio consente di eseguire il training e confrontare più modelli di regressione per stimare il prezzo di un'automobile in base alle funzionalità tecniche. Forniremo la logica per le scelte effettuate in questo esperimento, in modo da poter affrontare i problemi di apprendimento automatico.

Se si sta iniziando a usare Machine Learning, vedere la [versione di base](how-to-ui-sample-regression-predict-automobile-price-basic.md) di questo esperimento.

Ecco il grafo completato per questo esperimento:

[![Grafico dell'esperimento](media/how-to-ui-sample-regression-predict-automobile-price-compare-algorithms/graph.png)](media/how-to-ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Selezionare il pulsante **Apri** per l'esperimento di esempio 2:

    ![Aprire l'esperimento](media/how-to-ui-sample-regression-predict-automobile-price-compare-algorithms/open-sample2.png)

## <a name="experiment-summary"></a>Riepilogo esperimento

Seguire questa procedura per creare l'esperimento di Machine Learning:

1. Ottenere i dati.
1. Pre-elaborare i dati.
1. Eseguire il training del modello.
1. Testare, valutare e confrontare i modelli.

## <a name="get-the-data"></a>Ottenere i dati

Questo esempio usa il set di **dati automobile price data (RAW)** , che fa parte del Repository UCI Machine Learning. Questo set di dati contiene 26 colonne contenenti informazioni sulle automobili, incluse le funzionalità marca, modello, prezzo, veicolo (ad esempio il numero di cilindri), MPG e un punteggio di rischio assicurativo.

## <a name="pre-process-the-data"></a>Pre-elaborare i dati

Le attività di preparazione dei dati principali includono la pulizia, l'integrazione, la trasformazione, la riduzione e la discretizzazione o la quantizzazione dei dati. In interfaccia visiva è possibile trovare moduli per eseguire queste operazioni e altre attività di pre-elaborazione dei dati nel gruppo di **trasformazione dati** nel pannello di sinistra.

Usare il modulo **Select Columns in DataSet** per escludere le perdite normalizzate con molti valori mancanti. Si usano quindi **Clean Missing data** per rimuovere le righe con valori mancanti. Questo consente di creare un set di dati di training puliti.

![Pre-elaborazione dei dati](media/how-to-ui-sample-regression-predict-automobile-price-compare-algorithms/data-processing.png)

## <a name="train-the-model"></a>Eseguire il training del modello

I problemi di Machine Learning variano. Le attività comuni di Machine Learning includono sistemi di classificazione, clustering, regressione e raccomandazione, ciascuno dei quali potrebbe richiedere un algoritmo diverso. La scelta dell'algoritmo dipende spesso dai requisiti del caso d'uso. Dopo aver selezionato un algoritmo, è necessario ottimizzarne i parametri per eseguire il training di un modello più accurato. È quindi necessario valutare tutti i modelli in base alle metriche come accuratezza, intelligibilità ed efficienza.

Dato che l'obiettivo di questo esperimento è stimare i prezzi delle automobili e perché la colonna Label (price) contiene numeri reali, un modello di regressione è una scelta ottimale. Poiché il numero di funzionalità è relativamente ridotto (inferiore a 100) e queste funzionalità non sono di tipo sparse, è probabile che il limite della decisione sia non lineare.

Dato che l'obiettivo di questo esperimento è stimare i prezzi delle automobili e perché la colonna Label (price) contiene numeri reali, un modello di regressione è una scelta ottimale. Poiché il numero di funzionalità è relativamente ridotto (inferiore a 100) e queste funzionalità non sono di tipo sparse, è probabile che il limite della decisione sia non lineare.

Per confrontare le prestazioni di algoritmi diversi, vengono usati due algoritmi non lineari, la **regressione dell'albero delle decisioni con boosting** e la **regressione della foresta delle decisioni**, per la compilazione dei modelli. Entrambi gli algoritmi hanno parametri che è possibile modificare, ma in questo esempio vengono usati i valori predefiniti per questo esperimento.

Usare il modulo **Split data** per dividere in modo casuale i dati di input in modo che il set di dati di training contenga il 70% dei dati originali e il set di dati di testing contenga il 30% dei dati originali.

## <a name="test-evaluate-and-compare-the-models"></a>Testare, valutare e confrontare i modelli

Usare due diversi set di dati scelti in modo casuale per eseguire il training e il test del modello, come descritto nella sezione precedente. Suddividere il set di dati e utilizzare set di dati diversi per eseguire il training e il test del modello in modo da rendere la valutazione del modello più obiettivo.

Dopo aver eseguito il training del modello, utilizzare i moduli **Score Model** e **Evaluate Model** per generare risultati stimati e valutare i modelli. **Score Model** genera stime per il set di dati di test usando il modello sottoposto a training. Passare quindi i punteggi per **valutare il modello** per generare le metriche di valutazione.

In questo esperimento vengono usate due istanze di **Evaluate Model** per confrontare due coppie di modelli.

Confrontare innanzitutto due algoritmi nel set di dati di training.
In secondo luogo, confrontare due algoritmi nel set di dati di testing.

Ecco i risultati:

![Confrontare i risultati](media/how-to-ui-sample-regression-predict-automobile-price-compare-algorithms/result.png)

Questi risultati mostrano che il modello compilato con la **regressione dell'albero delle decisioni con boosting** ha un errore quadratico medio più basso rispetto al modello basato sulla **regressione della foresta delle decisioni**.

Entrambi gli algoritmi hanno un errore inferiore nel set di dati di training rispetto al set di dati di test non visibile.

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Passaggi successivi

Esplorare gli altri esempi disponibili per l'interfaccia visiva:

- [Esempio 1: regressione: Stimare il prezzo di un'automobile](how-to-ui-sample-regression-predict-automobile-price-basic.md)
- [Esempio 3-classificazione: Stima del rischio di credito](how-to-ui-sample-classification-predict-credit-risk-basic.md)
- [Esempio 4-Classificazione: Stimare il rischio di credito (costo sensibile)](how-to-ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [Esempio 5-classificazione: Varianza stima](how-to-ui-sample-classification-predict-churn.md)
- [Esempio 6-Classificazione: Stimare i ritardi dei voli](how-to-ui-sample-classification-predict-flight-delay.md)
