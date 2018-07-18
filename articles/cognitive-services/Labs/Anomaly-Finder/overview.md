---
title: Informazioni su Ricerca di anomalie - Servizi cognitivi Microsoft | Microsoft Docs
description: Usare algoritmi avanzati in Ricerca di anomalie come aiuto per individuare anomalie nei dati di serie temporali e restituire informazioni in Servizi cognitivi Microsoft.
services: cognitive-services
author: tonyxing
ms.service: cognitive-services
ms.technology: anomaly-detection
ms.topic: article
ms.date: 04/19/2018
ms.author: tonyxing
ms.openlocfilehash: 1080bb0ad1d901a8b9a5ace4993d4e0d46924a03
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35375353"
---
# <a name="what-is-anomaly-finder"></a>Informazioni su Ricerca di anomalie

Ricerca di anomalie consente di monitorare i dati nel tempo e di rilevare anomalie mediante funzioni di apprendimento automatico che si adattano a varie casistiche di dati, applicando automaticamente il modello statistico appropriato indipendentemente dal settore, dallo scenario o dal volume di dati. Usando una serie temporale come input, l'API Ricerca di anomalie determina se un punto dati è un'anomalia, il valore previsto e i limiti superiore e inferiore per la visualizzazione. Come servizio predefinito di intelligenza artificiale, Ricerca di anomalie non richiede competenze di apprendimento automatico oltre alla capacità di usare un'API RESTful. Consente quindi di eseguire attività di sviluppo in modo semplice e versatile, in quanto funziona con qualsiasi serie temporale di dati e può anche essere integrato in sistemi di streaming di dati. Ricerca di anomalie comprende un'ampia gamma di casi d'uso, ad esempio strumenti finanziari per la gestione di frodi, furti, cambiamenti dei mercati e potenziali incidenti di business, o il monitoraggio del traffico di dispositivi IoT mantenendo al contempo l'anonimato. Questa soluzione può anche essere monetizzata come parte di un servizio per aiutare gli utenti finali a comprendere cambiamenti nei dati, nelle spese, nel ritorno sugli investimenti o nelle attività degli utenti.
Provare l'API Ricerca di anomalie per acquisire una comprensione più approfondita dei propri dati. 

Quest'API offre varie possibilità:

* Prevedere i valori attesi in base ai dati cronologici nelle serie temporali
* Stabilire se un punto dati è un'anomalia rispetto al modello cronologico
* Generare una banda per visualizzare l'intervallo di valori "normale"

![Anomaly_Finder](./media/anomaly_detection1.png) 

Figura 1: rilevare anomalie nei ricavi delle vendite

![Anomaly_Finder](./media/anomaly_detection2.png)

Figura 2: rilevare cambiamenti del modello delle richieste di servizi

## <a name="requirements"></a>Requisiti

- Quantità minima di dati per le serie temporali in input: minimo di 13 punti dati per serie temporali senza una chiara periodicità, minimo di 4 cicli di punti dati per serie temporali con periodicità nota. 
- Integrità dei dati: i punti dati delle serie temporali sono separati nello stesso intervallo e non vi sono punti mancanti. 

## <a name="identify-anomalies"></a>Individuare le anomalie

L'API Rilevamento anomalie restituisce risultati che indicano se un determinato punto dati è un'anomalia o meno e forniscono le informazioni aggiuntive seguenti:
* Period: periodicità usata dall'API per rilevare i punti anomali.
* WarningText: eventuali informazioni di avviso.
* ExpectedValue: valore previsto dal modello basato sull'apprendimento
* IsAnomaly: risultato che indica se i punti dati sono anomalie o meno
* IsAnomaly_Neg: risultato che indica se i punti dati sono anomalie in direzione negativa (flessioni)
* IsAnomaly_Pos: risultato che indica se i punti dati sono anomalie in direzione positiva (picchi)
* UpperMargin: la somma di ExpectedValue e UpperMargin determina il limite superiore entro il quale il punto dati viene ancora considerato normale.
* LowerMargin: la differenza tra ExpectedValue e LowerMargin determina il limite inferiore entro il quale il punto dati viene ancora considerato normale.

> [!Note]
> UpperMargin e LowerMargin possono essere usati per generare una banda intorno alle serie temporali effettive per visualizzare l'intervallo dei valori normali. 

## <a name="adjusting-lower-and-upper-bounds-in-post-processing-on-the-response"></a>Regolare i limiti inferiore e superiore nella post-elaborazione della risposta

L'API Rilevamento anomalie restituisce un risultato predefinito che indica se un punto dati è un'anomalia; i limiti superiore e inferiore possono essere calcolati in base a ExpectedValue e UpperMargin/LowerMargin. I valori predefiniti sono adeguati alla maggior parte dei casi. Esistono tuttavia scenari che richiedono limiti diversi da quelli predefiniti. La pratica consigliata consiste nell'applicare un coefficiente a UpperMargin o LowerMargin per regolare i limiti dinamici.

### <a name="examples-with-1152-as-coefficiency"></a>Esempi con 1/1,5/2 come coefficiente

![Sensibilità predefinita](./media/sensitivity_1.png)

![Sensibilità 1,5](./media/sensitivity_1.5.png)

![Sensibilità 2](./media/sensitivity_2.png)

Richiesta con dati di esempio

[!INCLUDE [Request](./includes/request.md)]

Risposta JSON di esempio

[!INCLUDE [Response](./includes/response.md)]
