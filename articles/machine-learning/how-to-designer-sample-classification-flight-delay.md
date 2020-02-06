---
title: 'Progettazione: Esempio di previsione dei ritardi dei voli'
titleSuffix: Azure Machine Learning
description: Creare un classificatore e usare codice R personalizzato per prevedere i ritardi dei voli con la finestra di progettazione di Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.date: 12/25/2019
ms.openlocfilehash: d459350572d68cc5dcbbfd56933483404b94f918
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/02/2020
ms.locfileid: "76963277"
---
# <a name="build-a-classifier--use-r-to-predict-flight-delays-with-azure-machine-learning-designer"></a>Creare un classificatore e usare R per prevedere i ritardi dei voli con la finestra di progettazione di Azure Machine Learning

**Finestra di progettazione (anteprima) - Esempio 6**

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Questa pipeline usa i dati dei voli e i dati meteorologici cronologici per prevedere se un volo passeggeri pianificato subirà un ritardo di oltre 15 minuti. Questo problema può essere affrontato come un problema di classificazione che prevede due classi: in ritardo o puntuale.

Ecco il grafo della pipeline finale per questo esempio:

[![Grafo della pipeline](media/how-to-designer-sample-classification-flight-delay/pipeline-graph.png)](media/how-to-designer-sample-classification-flight-delay/pipeline-graph.png#lightbox)

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [aml-ui-prereq](../../includes/aml-ui-prereq.md)]

4. Fare clic su Sample 6 per aprirlo.

## <a name="get-the-data"></a>Ottenere i dati

Questo esempio usa il set di dati **Flight Delays Data**. Fa parte della raccolta dati TranStats del Dipartimento dei trasporti degli Stati Uniti. Il set di dati contiene informazioni sui ritardi dei voli da aprile a ottobre 2013. Il set di dati è stato pre-elaborato come segue:

* È stato applicato un filtro per includere i 70 aeroporti più trafficati negli Stati Uniti continentali.
* I voli cancellati sono stati rietichettati in modo da indicare un ritardo superiore a 15 minuti.
* I voli deviati sono stati esclusi.
* Sono state selezionate 14 colonne.

Per integrare i dati relativi ai voli, viene usato il set di dati **Weather Dataset**. I dati meteorologici contengono osservazioni meteorologiche su base oraria dall'agenzia NOAA e rappresentano le osservazioni fornite dalle stazioni meteo aeroportuali nello stesso periodo di tempo del set di dati dei voli. Il set di dati è stato pre-elaborato come segue:

* Gli ID delle stazioni meteo sono stati mappati agli ID degli aeroporti corrispondenti.
* Le stazioni meteo non associate ai 70 aeroporti più trafficati sono state rimosse.
* La colonna Date è stata suddivisa in colonne separate: Year, Month e Day.
* Sono state selezionate 26 colonne.

## <a name="pre-process-the-data"></a>Pre-elaborare i dati

Prima di poter analizzare un set di dati è in genere necessario pre-elaborarlo.

![data-process](./media/how-to-designer-sample-classification-flight-delay/data-process.png)

### <a name="flight-data"></a>Dati dei voli

Le colonne **Carrier**, **OriginAirportID** e **DestAirportID** vengono salvate come numeri interi. Tuttavia, poiché si tratta di attributi categorici, è necessario usare il modulo **Edit Metadata** (Modifica metadati) per convertirli nel formato categorico.

![edit-metadata](./media/how-to-designer-sample-classification-flight-delay/edit-metadata.png)

Usare quindi l'opzione **Select Columns** (Seleziona colonne) nel modulo Dataset (Set di dati) per escludere dal set di dati le colonne che possono causare data leakage: **DepDelay**, **DepDel15**, **ArrDelay**, **Canceled**, **Year**. 

Per unire i record di volo con i record meteo su base oraria, usare l'ora di partenza pianificata come una delle chiavi di join. Per eseguire il join, la colonna CSRDepTime deve essere arrotondata per difetto all'ora più vicina, operazione eseguita nel modulo **Execute R Script** (Esegui script R). 

### <a name="weather-data"></a>Dati meteo

Le colonne con un rapporto elevato di valori mancanti vengono escluse usando il modulo **Project Columns** (Colonne di progetto). Queste colonne includono tutte le colonne con valori stringa: **ValueForWindCharacter**, **WetBulbFarenheit**, **WetBulbCelsius**, **PressureTendency**, **PressureChange**, **SeaLevelPressure** e **StationPressure**.

Il modulo **Clean Missing Data** (Pulisci dati mancanti) viene quindi applicato alle colonne rimanenti per rimuovere le righe con dati mancanti.

Gli orari delle osservazioni meteo vengono arrotondati per eccesso all'ora completa più vicina. Gli orari dei voli pianificati e gli orari delle osservazioni meteo vengono arrotondati in direzioni opposte per garantire che il modello usi solo i dati meteo prima dell'ora del volo. 

Poiché i dati meteo vengono indicati nell'ora locale, le differenze di fuso orario vengono conteggiate sottraendo le colonne del fuso orario dall'ora di partenza pianificata e dall'ora di osservazione meteo. Queste operazioni vengono eseguite usando il modulo **Execute R Script** (Esegui script R).

### <a name="joining-datasets"></a>Unione dei set di dati

I record dei voli vengono aggiunti ai dati meteorologici dell'aeroporto di origine del volo (**OriginAirportID**) usando il modulo **Join Data** (Unisci dati).

 ![unione dei dati dei voli e meteo in base all'aeroporto di origine](./media/how-to-designer-sample-classification-flight-delay/join-origin.png)


I record dei voli vengono aggiunti ai dati meteorologici in base all'aeroporto di destinazione del volo (**DestAirportID**).

 ![Unione dei dati dei voli e meteo in base all'aeroporto di destinazione](./media/how-to-designer-sample-classification-flight-delay/join-destination.png)

### <a name="preparing-training-and-test-samples"></a>Preparazione degli esempi di training e di test

Il modulo **Split Data** (Dividi dati) suddivide i dati nei record da aprile a settembre per il training e i record di ottobre per i test.

 ![Dividere i dati di training e di test](./media/how-to-designer-sample-classification-flight-delay/split.png)

Le colonne relative all'anno, al mese e al fuso orario vengono rimosse dal set di dati di training tramite il modulo Select Columns (Seleziona colonne).

## <a name="define-features"></a>Definire le caratteristiche

In Machine Learning le caratteristiche sono singole proprietà misurabili di un elemento a cui si è interessati. Per trovare un set di caratteristiche pertinenti, è necessario sperimentare e conoscere il dominio. Alcune caratteristiche sono infatti migliori di altre per le stime. Inoltre, alcune caratteristiche possono avere una forte correlazione con altre e non aggiungere quindi nuove informazioni nel modello. Tali caratteristiche possono essere rimosse.

Per creare un modello, è possibile usare tutte le caratteristiche disponibili oppure selezionare un subset di caratteristiche.

## <a name="choose-and-apply-a-learning-algorithm"></a>Scegliere e applicare un algoritmo di apprendimento

Creare un modello usando il modulo **Two-Class Logistic Regression** (Regressione logistica a due classi) ed eseguire il training del modello sul set di dati di training. 

Il risultato del modulo **Train Model** (Esegui training del modello) è un modello di classificazione con training che può essere usato per assegnare un punteggio a nuovi campioni ai fini delle previsioni. Usare il set di test per generare punteggi dai modelli con training. Usare quindi il modulo **Evaluate Model** (Valuta modello) per analizzare e confrontare la qualità dei modelli.
Dopo aver eseguito la pipeline, per visualizzare l'output del modulo **Score Model** (Assegna un punteggio al modello), fare clic sulla porta di output e selezionare **Visualize** (Visualizza). L'output include le etichette con punteggio e le probabilità per le etichette.

Infine, per verificare la qualità dei risultati, aggiungere il modulo **Evaluate Model** (Valuta modello) nella finestra di disegno della pipeline e connettere la porta di input sinistra all'output del modulo Score Model (Assegna un punteggio al modello). Dopo aver eseguito la pipeline, per visualizzare l'output del modulo **Evaluate Model** (Valuta modello), fare clic sulla porta di output e selezionare **Visualize** (Visualizza).

## <a name="evaluate"></a>Valutazione
Il modello di regressione logistica presenta un'area sotto la curva di 0,631 nel set di test.

 ![evaluate](media/how-to-designer-sample-classification-flight-delay/sample6-evaluate-1225.png)

## <a name="next-steps"></a>Passaggi successivi

Esplorare gli altri esempi disponibili per la finestra di progettazione:

- [Esempio 1 - Regressione: Prevedere il prezzo di un'automobile](how-to-designer-sample-regression-automobile-price-basic.md)
- [Esempio 2 - Regressione: Confrontare gli algoritmi per la previsione del prezzo delle automobili](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [Esempio 3 - Classificazione con selezione delle caratteristiche: Previsione del reddito](how-to-designer-sample-classification-predict-income.md)
- [Esempio 4 - Classificazione: Prevedere il rischio di credito (sensibile al costo)](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [Esempio 5 - Classificazione: Prevedere la varianza](how-to-designer-sample-classification-churn.md)
- [Esempio 7 - Classificazione: Wikipedia SP 500 Dataset](how-to-designer-sample-text-classification.md)