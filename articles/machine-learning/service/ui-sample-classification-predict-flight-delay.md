---
title: 'Classificazione: Prevedere i ritardi dei voli'
titleSuffix: Azure Machine Learning service
description: Questo articolo illustra come compilare un modello di machine learning per prevedere i ritardi dei voli usando l'interfaccia visiva di trascinamento e rilascio e il codice R personalizzato.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: article
author: xiaoharper
ms.author: zhanxia
ms.reviewer: peterlu
ms.date: 07/02/2019
ms.openlocfilehash: 773e55fe4b5ca5acf27ba1765e5a16075f625187
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67607637"
---
# <a name="sample-6---classification-predict-flight-delays-using-r"></a>Esempio 6: classificazione: Prevedere i ritardi dei voli con R

Questo esperimento Usa cronologici sui voli e i dati meteo per prevedere se un volo passeggeri programmato ritarderà di più di 15 minuti.

Questo problema può essere eseguito come un problema di classificazione, stima due classi, ovvero di ritardo, oppure sull'ora. Per creare un classificatore, questo modello Usa un numero elevato di esempi di dati cronologici sui voli.

Ecco il grafico dell'esperimento finale:

[![Grafico dell'esperimento](media/ui-sample-classification-predict-flight-delay/experiment-graph.png)](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Selezionare il **aperto** pulsante per l'esperimento di esempio 6:

    ![Aprire l'esperimento](media/ui-sample-classification-predict-flight-delay/open-sample6.png)

## <a name="get-the-data"></a>Ottenere i dati

In questo esperimento Usa la **ritardi dei voli** set di dati. Fa parte della raccolta dati TranStats da Stati Uniti. Dipartimento dei trasporti. Il set di dati contiene le informazioni di ritardo dei voli da aprile a ottobre 2013. Prima di caricare i dati per l'interfaccia visiva, che è stato pre-elaborato come segue:

* Filtrato per includere i 70 aeroporti più trafficati negli Stati Uniti continentali.
* Per annullare i voli, rietichettati come posticipato da più di 15 minuti.
* Esclusi i voli deviati.
* 14 colonne selezionate.

Per integrare i dati di volo, il **set di dati meteo** viene usato. I dati meteo contiene osservazioni meteo sono su base oraria forniti da NOAA e rappresenta osservazioni dal stazioni meteo degli aeroporti, che coprono lo stesso periodo di tempo di aprile-ottobre 2013. Prima di caricare l'interfaccia visiva Azure Machine Learning, che è stato pre-elaborato come segue:

* Gli ID delle stazioni meteo sono stati mappati agli ID degli aeroporti corrispondenti.
* Le stazioni meteo non è associate i 70 aeroporti più trafficati sono state rimosse.
* La colonna Date è stata suddivisa in colonne separate: Anno, mese e giorno.
* 26 colonne selezionate.

## <a name="pre-process-the-data"></a>Pre-elaborare i dati

Un set di dati richiede in genere alcuni pre-elaborazione prima che possono essere analizzati.

![dati processo](media/ui-sample-classification-predict-flight-delay/data-process.png)

### <a name="flight-data"></a>Dati sui voli

Le colonne **Carrier**, **OriginAirportID**, e **DestAirportID** vengono salvati come numeri interi. Tuttavia, sono attributi categorici, usare il **Edit Metadata** modulo convertirle in categorico.

![edit-metadata](media/ui-sample-classification-predict-flight-delay/edit-metadata.png)

Quindi usare il **Seleziona colonne** nel modulo di set di dati da escludere dalle colonne di set di dati che sono leakers possibili destinazioni: **DepDelay**, **DepDel15**, **ArrDelay**, **Canceled**, **Year**. 

Per aggiungere i record dei voli con i record del meteo orarie, usare il tempo di decollo pianificati come una delle chiavi di join. Per eseguire l'operazione di join, la colonna CSRDepTime deve essere arrotondata per difetto all'ora più vicina, che avviene nella **Execute R Script** modulo. 

### <a name="weather-data"></a>Dati meteo

Colonne che contengono una porzione importante di valori mancanti vengono esclusi tramite il **Project Columns** modulo. Queste colonne includono tutte le colonne con valori di stringa: **ValueForWindCharacter**, **WetBulbFarenheit**, **WetBulbCelsius**, **PressureTendency**, **PressureChange**, **SeaLevelPressure**, e **StationPressure**.

Il **Clean Missing Data** modulo viene quindi applicato alle colonne rimanenti per rimuovere le righe con dati mancanti.

Tempi di osservazione meteo vengono arrotondati per eccesso per l'intera ora più vicina. Volo pianificato subirà tempi e i tempi di osservazioni meteo sono arrotondati in direzioni opposte per assicurarsi che il modello utilizza solo meteo prima dell'ora sui voli. 

Poiché i dati meteo viene segnalati nell'ora locale, le differenze di fuso orario vengono presi in considerazione sottraendo le colonne di fuso orario tra il momento di decollo pianificati e l'ora di osservazione meteo. Queste operazioni vengono eseguite usando il **Execute R Script** modulo.

### <a name="joining-datasets"></a>Unione di set di dati

I record dei voli vengono uniti con dati meteo di origine del volo (**OriginAirportID**) tramite il **Join dati** modulo.

 ![join dei voli e meteo per origine](media/ui-sample-classification-predict-flight-delay/join-origin.png)


I record dei voli vengono uniti con i dati meteo tramite la destinazione del volo (**DestAirportID**).

 ![Join dei voli e meteo per destinazione](media/ui-sample-classification-predict-flight-delay/join-destination.png)

### <a name="preparing-training-and-test-samples"></a>Preparazione di formazione e gli esempi di Test

Il **Split Data** modulo suddivide i dati in aprile tramite record per il training di settembre e ottobre Registra per test.

 ![Suddividere i set di training e dati di test](media/ui-sample-classification-predict-flight-delay/split.png)

Anno, mese e fuso orario colonne vengono rimosse dal set di dati di training usando il modulo Select Columns.

## <a name="define-features"></a>Definire le caratteristiche

In machine learning, le funzionalità sono singole proprietà misurabili di un elemento che si è interessati. Ricerca di una serie di funzionalità richiede una conoscenza di sperimentazione e il dominio. Alcune caratteristiche sono infatti migliori di altre per le stime. Inoltre, alcune funzionalità potrebbero avere una stretta correlazione con altre funzionalità e verranno aggiunte nuove informazioni al modello. Queste funzionalità possono essere rimossi.

Per compilare un modello, è possibile usare tutte le funzionalità disponibili o selezionare un subset delle funzionalità.

## <a name="choose-and-apply-a-learning-algorithm"></a>Scegliere e applicare un algoritmo di apprendimento

Creare un modello utilizzando il **Two-Class Logistic Regression** modulo ed eseguirne il training sul set di dati di training. 

Il risultato del **Train Model** modulo è un modello di classificazione sottoposto a training che può essere utilizzato per assegnare un punteggio nuovi esempi per eseguire stime. Usare test impostato per generare punteggi da modelli con training. Quindi usare il **Evaluate Model** modulo da analizzare e confrontare la qualità dei modelli.

Dopo aver eseguito l'esperimento, è possibile visualizzare l'output di **Score Model** modulo facendo clic sulla porta di output e selezionando **Visualize**. L'output include le etichette con punteggio e le probabilità per le etichette.

Infine, per testare la qualità dei risultati, aggiungere il **Evaluate Model** modulo all'esperimento di area di disegno e connettere la porta di input sinistra all'output del modulo Score Model. Eseguire l'esperimento e visualizzare l'output del **Evaluate Model** modulo, facendo clic sulla porta di output e selezionando **Visualize**.

## <a name="evaluate"></a>Valutazione
Il modello di regressione logistica è l'area sottesa alla curva di 0.631 sui risultati dei test impostato.

 ![evaluate](media/ui-sample-classification-predict-flight-delay/evaluate.png)

## <a name="next-steps"></a>Passaggi successivi

Esplorare gli altri esempi disponibili per l'interfaccia visiva:

- [Esempio 1: regressione: Stimare il prezzo di un'automobile](ui-sample-regression-predict-automobile-price-basic.md)
- [Esempio 2: regressione: Confrontare gli algoritmi per la stima di prezzo di un'automobile](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [Esempio 3: classificazione: Prevedere il rischio di credito](ui-sample-classification-predict-credit-risk-basic.md)
- [Esempio 4: classificazione: Prevedere il rischio di credito (costo sensibile)](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [Esempio 5 - classificazione: Prevedere la varianza](ui-sample-classification-predict-churn.md)
