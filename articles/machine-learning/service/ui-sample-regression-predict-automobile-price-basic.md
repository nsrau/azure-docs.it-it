---
title: 'Esempio di interfaccia visiva #1: regressione al prezzo stimato'
titleSuffix: Azure Machine Learning service
description: Scopri come creare un modello di apprendimento automatico per stimare il prezzo di un'automobile senza scrivere una sola riga di codice.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/10/2019
ms.openlocfilehash: 0c86955e0b10111bf9b6db0d884b73867a4467c5
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68990415"
---
# <a name="sample-1---regression-predict-price"></a>Esempio 1: regressione: Prevedere il prezzo

Informazioni su come creare un modello di regressione di Machine Learning senza scrivere una sola riga di codice usando l'interfaccia visiva.

Questo esperimento consente di eseguire il training di un regressore della **foresta delle decisioni** per stimare il prezzo di un'automobile in base a funzionalità tecniche quali marca, modello, potenza e dimensioni. Poiché stiamo provando a rispondere alla domanda "Qual è?" si tratta di un problema di regressione. Tuttavia, è possibile applicare gli stessi passaggi fondamentali in questo esperimento per risolvere qualsiasi tipo di problema di Machine Learning, sia che si tratti di regressione, classificazione, clustering e così via.

I passaggi fondamentali di un modello di apprendimento automatico del training sono:

1. Ottenere i dati
1. Pre-elaborare i dati
1. Eseguire il training del modello
1. Valutare il modello

Di seguito è riportato il grafico finale e completato dell'esperimento su cui si lavorerà. Verranno fornite le logiche per tutti i moduli, in modo da poter prendere decisioni simili.

![Grafico dell'esperimento](media/ui-sample-regression-predict-automobile-price-basic/overall-graph.png)

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Selezionare il pulsante **Apri** per l'esperimento di esempio 1:

    ![Aprire l'esperimento](media/ui-sample-regression-predict-automobile-price-basic/open-sample1.png)

## <a name="get-the-data"></a>Ottenere i dati

In questo esperimento viene usato il set di **dati automobile price data (RAW)** , che si trova nel Repository UCI Machine Learning. Il set di dati contiene 26 colonne contenenti informazioni sulle automobili, incluse le funzionalità marca, modello, prezzo, veicolo (ad esempio il numero di cilindri), MPG e un punteggio di rischio assicurativo. L'obiettivo di questo esperimento è stimare il prezzo dell'auto.

## <a name="pre-process-the-data"></a>Pre-elaborare i dati

Le attività di preparazione dei dati principali includono la pulizia, l'integrazione, la trasformazione, la riduzione e la discretizzazione o la quantizzazione dei dati. Nell'interfaccia visiva è possibile trovare moduli per eseguire queste operazioni e altre attività di pre-elaborazione dei dati nel gruppo di **trasformazione dati** nel pannello di sinistra.

Viene usato il modulo **Select Columns in DataSet** per escludere le perdite normalizzate con molti valori mancanti. Si usano quindi **Clean Missing data** per rimuovere le righe con valori mancanti. Questo consente di creare un set di dati di training puliti.

![Pre-elaborazione dei dati](./media/ui-sample-regression-predict-automobile-price-basic/data-processing.png)

## <a name="train-the-model"></a>Eseguire il training del modello

I problemi di Machine Learning variano. Le attività comuni di Machine Learning includono sistemi di classificazione, clustering, regressione e raccomandazione, ciascuno dei quali potrebbe richiedere un algoritmo diverso. La scelta dell'algoritmo dipende spesso dai requisiti del caso d'uso. Dopo aver selezionato un algoritmo, è necessario ottimizzarne i parametri per eseguire il training di un modello più accurato. È quindi necessario valutare tutti i modelli in base alle metriche come accuratezza, intelligibilità ed efficienza.

Dato che l'obiettivo di questo esperimento è stimare i prezzi delle automobili e perché la colonna Label (price) contiene numeri reali, un modello di regressione è una scelta ottimale. Poiché il numero di funzionalità è relativamente ridotto (inferiore a 100) e queste funzionalità non sono di tipo sparse, è probabile che il limite della decisione sia non lineare. Quindi usiamo la **regressione della foresta delle decisioni** per questo esperimento.

Viene usato il modulo **Split data** per dividere in modo casuale i dati di input in modo che il set di dati di training contenga il 70% dei dati originali e il set di dati di testing contenga il 30% dei dati originali.

## <a name="test-evaluate-and-compare"></a>Test, valutazione e confronto

 Il set di dati viene suddiviso e vengono utilizzati set di dati diversi per eseguire il training e il test del modello, in modo da rendere più obiettivo la valutazione del modello.

Dopo aver eseguito il training del modello, si utilizzano i moduli **Score Model** e **Evaluate Model** per generare risultati stimati e valutare i modelli.

**Score Model** genera stime per il set di dati di test usando il modello sottoposto a training. Per controllare il risultato, selezionare la porta di output di **Score Model** e quindi selezionare Visualize ( **Visualizza**).

![Risultato del Punteggio](./media/ui-sample-regression-predict-automobile-price-basic/score-result.png)

Vengono quindi passati i punteggi al modulo **Evaluate Model** per generare le metriche di valutazione. Per controllare il risultato, selezionare la porta di output del **modello Evaluate** e quindi selezionare **Visualize (Visualizza**).

![Risultato della valutazione](./media/ui-sample-regression-predict-automobile-price-basic/evaluate-result.png)

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Passaggi successivi

Esplorare gli altri esempi disponibili per l'interfaccia visiva:

- [Esempio 2: regressione: Confrontare gli algoritmi per la stima del prezzo dell'automobile](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [Esempio 3-classificazione: Stima del rischio di credito](ui-sample-classification-predict-credit-risk-basic.md)
- [Esempio 4-Classificazione: Stimare il rischio di credito (costo sensibile)](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [Esempio 5-classificazione: Varianza stima](ui-sample-classification-predict-churn.md)
- [Esempio 6-Classificazione: Stimare i ritardi dei voli](ui-sample-classification-predict-flight-delay.md)
