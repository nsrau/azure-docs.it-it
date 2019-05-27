---
title: Cos'è l'API Rilevamento anomalie? | Microsoft Docs
titleSuffix: Azure Cognitive Services
description: Usare algoritmi avanzati dell'API di rilevamento delle anomalie per identificare le anomalie nei dati delle serie temporali.
services: cognitive-services
author: aahill
ms.service: cognitive-services
ms.subservice: anomaly-detection
ms.topic: article
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 48dffb8853d3def590e434c1683ecf038e2a194e
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2019
ms.locfileid: "64415812"
---
# <a name="what-is-the-anomaly-detector-api"></a>Cos'è l'API Rilevamento anomalie?

L'API rilevatore di anomalie consente di monitorare e rilevare le anomalie nei dati delle serie temporali con machine learning. L'API rilevatore di anomalie si adatta automaticamente che identifica e applicando i modelli di mapping più appropriati ai dati, indipendentemente dal settore, scenario o volume di dati. Usa dati delle serie temporali, l'API determina i limiti per il rilevamento di anomalie, i valori previsti, e quali punti dati sono le anomalie.

![Rilevare cambiamenti del modello delle richieste di servizi](./media/anomaly_detection2.png)

Usando il rilevatore di anomalie non richiede alcuna esperienza precedente in machine learning e l'API RESTful che consente di integrare facilmente il servizio nelle applicazioni e processi.

## <a name="features"></a>Funzionalità

Con il rilevatore di anomalie, è possibile rilevare automaticamente le anomalie in tutta la serie temporale, o appena si verificano in tempo reale. 

|Funzionalità  |Descrizione  |
|---------|---------|
|Rilevare le anomalie appena si verificano in tempo reale. | Rilevare le anomalie nei dati di streaming usando i punti dati visualizzati in precedenza per determinare se la versione più recente è un'anomalia. Questa operazione genera un modello usando i punti dati, inviare e determina se il punto di destinazione è un'anomalia. Chiamando l'API con ogni nuovo punto dati che è generare, è possibile monitorare i dati durante la creazione. |
|Rilevare le anomalie in tutto il set di dati come batch. | Usare le serie temporali per rilevare eventuali anomalie che potrebbero esistere in tutto i dati. Questa operazione genera un modello usando i dati delle serie intera temporali, con ogni punto di analisi con lo stesso modello.         |
| Ottenere informazioni aggiuntive sui dati. | Ottenere informazioni dettagliate utili su eventuali anomalie osservati, inclusi i valori previsti, i limiti di anomalie e le posizioni e i dati. |
| Regolare i limiti di rilevamento delle anomalie. | L'API rilevatore di anomalie crea automaticamente limiti per il rilevamento anomalie. Modificare questi limiti per aumentare o diminuire la sensibilità dell'API per le anomalie di dati e soddisfare tutti i dati. |

## <a name="demo"></a>Demo

Per iniziare rapidamente a usare l'API rilevatore di anomalie, provare a un [demo online](https://notebooks.azure.com/AzureAnomalyDetection/projects/anomalydetector) che possono essere eseguite nel browser. In questa demo viene eseguito in un notebook di Jupyter ospitato sul web e illustra come inviare una richiesta di API e visualizzare il risultato.

Per eseguire la demo, completare i passaggi seguenti:

1. Ottenere una chiave di sottoscrizione valida di API rilevatore di anomalie e un endpoint API. La sezione seguente contiene le istruzioni per l'iscrizione. 
2. Accedere e fare clic su Clone, in alto a destra.
3. Fare clic su **eseguire sul calcolo disponibile**
4. Selezionare uno dei notebook per questo esempio.
5. Aggiungere la chiave di sottoscrizione valida di API rilevatore di anomalie per il `subscription_key` variabile. Modifica il `endpoint` variabile all'endpoint. Ad esempio: `https://westus2.api.cognitive.microsoft.com`
1. Nella barra dei menu in alto fare clic su **cella**, quindi **Esegui tutto**.

## <a name="workflow"></a>Flusso di lavoro

L'API rilevatore di anomalie è un servizio web RESTful, rendendo più semplice chiamare da qualsiasi linguaggio di programmazione che può effettuare richieste HTTP e analizzare JSON.

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

Dopo aver eseguito l'iscrizione:

1. Richiedere i dati delle serie temporali e convertirlo in un formato JSON valido. Uso [procedure consigliate](concepts/anomaly-detection-best-practices.md) durante la preparazione dei dati per ottenere risultati ottimali.
1. Inviare una richiesta per l'API rilevatore di anomalie con i dati.
1. Elaborare la risposta dell'API tramite l'analisi del messaggio JSON restituito.

## <a name="next-steps"></a>Passaggi successivi

* [Avvio rapido: Rilevare le anomalie nei dati delle serie temporali utilizzando l'API REST di rilevatore di anomalie](quickstarts/detect-data-anomalies-csharp.md)
* L'API rilevatore di anomalie [demo online](https://notebooks.azure.com/AzureAnomalyDetection/projects/anomalydetector)
* Il rilevatore di anomalie [riferimento all'API REST](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect)