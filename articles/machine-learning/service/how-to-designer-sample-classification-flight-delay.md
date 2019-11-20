---
title: 'Finestra di progettazione: stima dei ritardi dei voli'
titleSuffix: Azure Machine Learning
description: Compilare un classificatore e usare codice R personalizzato per stimare i ritardi dei voli con Azure Machine Learning Designer.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: peterlu
ms.date: 11/04/2019
ms.openlocfilehash: 06d158fb228ea82e61e785407fc0c59d66c2de15
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74196029"
---
# <a name="build-a-classifier--use-r-to-predict-flight-delays-with-azure-machine-learning-designer"></a>Compilare un classificatore & usare R per stimare i ritardi dei voli con Azure Machine Learning Designer

**Finestra di progettazione (anteprima) Sample 6**

[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-enterprise-sku.md)]

Questa pipeline usa il volo cronologico e i dati meteorologici per prevedere se un volo passeggero pianificato verrà ritardato di oltre 15 minuti. Questo problema può essere affrontato come un problema di classificazione, stimando due classi: ritardata o in tempo.

Ecco il grafico della pipeline finale per questo esempio:

[Grafico ![della pipeline](media/how-to-ui-sample-classification-predict-flight-delay/pipeline-graph.png)](media/how-to-ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="prerequisites"></a>prerequisiti

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Fare clic su Sample 6 per aprirlo.

## <a name="get-the-data"></a>Ottenere i dati

Questo esempio usa il set di **dati dei ritardi dei voli** . Fa parte della raccolta di dati TranStats dal Ministero del trasporto statunitense. Il set di dati contiene informazioni sui ritardi dei voli da aprile a ottobre 2013. Il set di dati è stato pre-elaborato come segue:

* Filtrato per includere gli aeroporti più attivi 70 nella Stati Uniti continentale.
* Sono stati rietichettati i voli annullati in ritardo di oltre 15 minuti.
* Voli deviati esclusi.
* 14 colonne selezionate.

Per integrare i dati relativi al volo, viene usato il set di dati **Weather** . I dati meteorologici contengono osservazioni meteorologiche basate su base oraria dal NOAA e rappresentano osservazioni dalle stazioni meteo aeroportuali, che coprono lo stesso periodo di tempo del set di dati dei voli. È stata pre-elaborata come indicato di seguito:

* È stato eseguito il mapping degli ID stazione meteo agli ID aeroporto corrispondenti.
* Le stazioni meteo non associate a 70 aeroporti più attivi sono state rimosse.
* La colonna della data è stata suddivisa in colonne separate: anno, mese e giorno.
* Selezionato 26 colonne.

## <a name="pre-process-the-data"></a>Pre-elaborare i dati

Un set di dati richiede in genere una pre-elaborazione prima di poter essere analizzato.

![elaborazione dati](media/how-to-ui-sample-classification-predict-flight-delay/data-process.png)

### <a name="flight-data"></a>Dati relativi ai voli

Le colonne **Carrier**, **OriginAirportID**e **DestAirportID** vengono salvate come numeri interi. Tuttavia, si tratta di attributi categorici, usare il modulo **Edit Metadata (Modifica metadati** ) per convertirli in categorico.

![Modifica-metadati](media/how-to-ui-sample-classification-predict-flight-delay/edit-metadata.png)

Usare quindi il modulo **Select Columns** in DataSet per escludere le colonne del set di dati che sono possibili perdite di destinazione: **DepDelay**, **DepDel15**, **ArrDelay**, **annullato**, **year**. 

Per unire i record di volo con i record meteorologici orari, usare l'ora di partenza pianificata come una delle chiavi di join. Per eseguire il join, è necessario arrotondare la colonna CSRDepTime all'ora più vicina, eseguita dal modulo **Execute R script** . 

### <a name="weather-data"></a>Dati meteo

Le colonne con una proporzione elevata di valori mancanti vengono escluse mediante il modulo **Project Columns** . Queste colonne includono tutte le colonne con valori di stringa: **ValueForWindCharacter**, **WetBulbFarenheit**, **WetBulbCelsius**, **PressureTendency**, **PressureChange**, **SeaLevelPressure**e **StationPressure** .

Il modulo **Clean Missing data** viene quindi applicato alle colonne rimanenti per rimuovere le righe con dati mancanti.

I tempi di osservazione Meteo vengono arrotondati per eccesso all'ora completa più vicina. Le ore di volo pianificate e i tempi di osservazione Meteo vengono arrotondati in direzioni opposte per garantire che il modello usi solo il meteo prima del volo. 

Poiché i dati meteo vengono segnalati nell'ora locale, le differenze di fuso orario vengono contabilizzate sottraendo le colonne del fuso orario dall'ora di partenza pianificata e l'ora di osservazione del tempo. Queste operazioni vengono eseguite usando il modulo **Execute R script** .

### <a name="joining-datasets"></a>Unione di set di impostazioni

I record di volo vengono aggiunti ai dati meteorologici all'origine del volo (**OriginAirportID**) usando il modulo **join data** .

 ![partecipa al volo e al meteo per origine](media/how-to-ui-sample-classification-predict-flight-delay/join-origin.png)


I record di volo vengono aggiunti ai dati meteorologici utilizzando la destinazione del volo (**DestAirportID**).

 ![Partecipa al volo e meteo per destinazione](media/how-to-ui-sample-classification-predict-flight-delay/join-destination.png)

### <a name="preparing-training-and-test-samples"></a>Preparazione degli esempi di training e di test

Il modulo **Split data** suddivide i dati nei record da aprile a settembre per il training e i record di ottobre per i test.

 ![Suddividere i dati di training e di test](media/how-to-ui-sample-classification-predict-flight-delay/split.png)

Le colonne anno, mese e fuso orario vengono rimosse dal set di dati di training usando il modulo Select Columns.

## <a name="define-features"></a>Definire le caratteristiche

In Machine Learning le funzionalità sono proprietà misurabili individuali di un elemento a cui si è interessati. Per trovare un set avanzato di funzionalità, è necessario sperimentare e conoscere il dominio. Alcune caratteristiche sono infatti migliori di altre per le stime. Inoltre, alcune funzionalità possono avere una forte correlazione con altre funzionalità e non aggiungeranno nuove informazioni al modello. È possibile rimuovere queste funzionalità.

Per compilare un modello, è possibile utilizzare tutte le funzionalità disponibili oppure selezionare un subset delle funzionalità.

## <a name="choose-and-apply-a-learning-algorithm"></a>Scegliere e applicare un algoritmo di apprendimento

Creare un modello usando il modulo di **regressione logistica a due classi** ed eseguirne il training sul set di dati di training. 

Il risultato del modulo **Train Model** è un modello di classificazione sottoposto a training che può essere usato per assegnare punteggi ai nuovi esempi per eseguire stime. Usare il set di test per generare punteggi dai modelli sottoposti a training. Usare quindi il modulo **Evaluate Model** per analizzare e confrontare la qualità dei modelli.
pipeline dopo l'esecuzione della pipeline, è possibile visualizzare l'output del modulo **Score Model** facendo clic sulla porta di output e selezionando **Visualize (Visualizza**). L'output include le etichette con punteggio e le probabilità per le etichette.

Infine, per testare la qualità dei risultati, aggiungere il modulo **Evaluate Model** nell'area di disegno della pipeline e connettere la porta di input sinistra all'output del modulo Score Model. Eseguire la pipeline e visualizzare l'output del modulo **Evaluate Model** facendo clic sulla porta di output e selezionando **Visualize (Visualizza**).

## <a name="evaluate"></a>Valutazione
Il modello di regressione logistica dispone dell'AUC di 0,631 sul set di test.

 ![evaluate](media/how-to-ui-sample-classification-predict-flight-delay/evaluate.png)

## <a name="next-steps"></a>Passaggi successivi

Esplorare gli altri esempi disponibili per la finestra di progettazione:

- [Esempio 1: regressione: stimare il prezzo di un'automobile](how-to-designer-sample-regression-automobile-price-basic.md)
- [Esempio 2: regressione: confrontare gli algoritmi per la stima del prezzo dell'automobile](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [Esempio 3: classificazione con selezione delle caratteristiche: stima del reddito](how-to-designer-sample-classification-predict-income.md)
- [Esempio 4-Classificazione: stimare il rischio di credito (costo sensibile)](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [Esempio 5-classificazione: varianza di stima](how-to-designer-sample-classification-churn.md)
- [Esempio 7-classificazione di testo: set di dati di Wikipedia SP 500](how-to-designer-sample-text-classification.md)