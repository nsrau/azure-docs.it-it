---
title: 'Regressione: Stimare il prezzo'
titleSuffix: Azure Machine Learning service
description: Questo esperimento di esempio di interfaccia visiva di seguito viene illustrato come compilare un modello di regressione per prevedere il prezzo di un'automobile. Il processo include corsi di formazione, test e la valutazione del modello su set di dati di Automobile price data (Raw).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: article
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/02/2019
ms.openlocfilehash: fa9b9179cda767d69d08dcd357a03123bde901cb
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028891"
---
# <a name="sample-1---regression-predict-price"></a>Esempio 1: regressione: Stimare il prezzo

Questo esperimento di esempio di interfaccia visiva di seguito viene illustrato come compilare un modello di regressione per prevedere il prezzo di un'automobile. Il processo include set di training, testing e valutare il modello usando il **dati sui prezzi di Automobile (Raw)** set di dati.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Selezionare il **aperto** pulsante per l'esperimento di esempio 1:

    ![Aprire l'esperimento](media/ui-sample-regression-predict-automobile-price-basic/open-sample1.png)

## <a name="related-sample"></a>Esempio correlato

[Esempio 2: regressione: Stima del prezzo dell'automobile (confrontare algoritmi)](ui-sample-regression-predict-automobile-price-compare-algorithms.md) fornisce un esperimento di esempio più complesso che lo stesso problema di questo esperimento è stato risolto con due modelli di regressione differenti. Viene illustrato come confrontare rapidamente gli algoritmi diversi. Eseguire l'estrazione se si sta cercando un esempio più avanzato.

## <a name="experiment-summary"></a>Riepilogo di esperimento

Utilizziamo questa procedura per creare l'esperimento:

1. Ottenere i dati.
1. Pre-elaborare i dati.
1. Il training del modello.
1. Test, valutare e confrontare i modelli.

Ecco l'intero grafico dell'esperimento:

![Grafico dell'esperimento](media/ui-sample-regression-predict-automobile-price-basic/overall-graph.png)

## <a name="get-the-data"></a>Ottenere i dati

In questo esperimento, viene usato il **dati sui prezzi di Automobile (Raw)** set di dati dal Repository UCI Machine Learning. Il set di dati contiene 26 colonne contenenti informazioni sulle automobili, tra cui marca, modello, prezzo, caratteristiche del veicolo (ad esempio, il numero di cilindri), MPG e un punteggio di rischio assicurativo. L'obiettivo di questo esperimento è stimare il prezzo dell'automobile.

## <a name="pre-process-the-data"></a>Pre-elaborare i dati

L'attività di preparazione dei dati principali includono pulizia dei dati, integrazione, trasformazione, la riduzione e discretizzazione o la quantizzazione. Dell'interfaccia visiva grafica, è possibile trovare i moduli per eseguire queste operazioni e altri dati di attività di pre-elaborazione il **Data Transformation** gruppo nel riquadro sinistro.

Usiamo il **Select Columns in Dataset** modulo Escludi normalized-losses con numerosi valori mancanti. Quindi, utilizziamo **Clean Missing Data** per rimuovere le righe contenenti valori mancanti. Ciò consente di creare un set di dati di training pulito.

![Pre-elaborazione dei dati](./media/ui-sample-regression-predict-automobile-price-basic/data-processing.png)

## <a name="train-the-model"></a>Eseguire il training del modello
Problemi di Machine learning variano. Attività comuni di machine learning include la classificazione, clustering, la regressione e sistemi di raccomandazione, ognuno dei quali potrebbe richiedere un algoritmo diverso. La scelta dell'algoritmo spesso dipende dai requisiti del caso di utilizzo. Quando si seleziona un algoritmo, è necessario ottimizzare i parametri per il training di un modello più accurato. È quindi necessario valutare tutti i modelli basati sulla metrica, ad esempio precisione, la comprensione e l'efficienza.

Poiché l'obiettivo di questo esperimento consiste nello stimare i prezzi delle automobili e perché la colonna di etichetta (prezzo) contiene i numeri reali, un modello di regressione è una buona scelta. Si considera che il numero di funzionalità è relativamente piccolo (minore di 100) e queste funzionalità non sono sparse, il limite di decisione è probabilmente non lineare. Quindi utilizziamo **Decision Forest Regression** per questo esperimento.

Usiamo il **Split Data** modulo per dividere in modo casuale i dati di input in modo che il set di dati di training contiene il 70% dei dati originali e i set di dati di test contiene 30% dei dati originali.

## <a name="test-evaluate-and-compare"></a>Test, valutare e confrontare

 Si suddivide il set di dati e usare diversi set di dati per il training e test del modello per semplificare la valutazione del modello di obiettivo di altre.

Dopo il training del modello, viene usato il **Score Model** e **Evaluate Model** moduli per generare i risultati stimati e valutare i modelli.

**Score Model** genera stime per il set di dati di test usando il modello con training. Per controllare il risultato, selezionare la porta di output del **Score Model** e quindi selezionare **Visualize**.

![Risultato di assegnazione](./media/ui-sample-regression-predict-automobile-price-basic/score-result.png)

Viene quindi passato i punteggi per la **Evaluate Model** modulo per generare metriche di valutazione. Per controllare il risultato, selezionare la porta di output del **Evaluate Model** e quindi selezionare **Visualize**.

![Valutare i risultati](./media/ui-sample-regression-predict-automobile-price-basic/evaluate-result.png)

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Passaggi successivi

Esplorare gli altri esempi disponibili per l'interfaccia visiva:

- [Esempio 2: regressione: Confrontare gli algoritmi per la stima di prezzo di un'automobile](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [Esempio 3: classificazione: Prevedere il rischio di credito](ui-sample-classification-predict-credit-risk-basic.md)
- [Esempio 4: classificazione: Prevedere il rischio di credito (costo sensibile)](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [Esempio 5 - classificazione: Prevedere la varianza](ui-sample-classification-predict-churn.md)
