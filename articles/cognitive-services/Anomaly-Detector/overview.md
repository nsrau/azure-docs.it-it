---
title: Cos'è l'API Rilevamento anomalie?
titleSuffix: Azure Cognitive Services
description: Usare gli algoritmi avanzati dell'API Rilevamento anomalie per identificare le anomalie nei dati di serie temporali.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: overview
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 80e0984deff83726fd96a462a1ae8a4375db9d2e
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67721599"
---
# <a name="what-is-the-anomaly-detector-api"></a>Cos'è l'API Rilevamento anomalie?

L'API Rilevamento anomalie permette di monitorare e rilevare le anomalie nei dati di serie temporali con l'apprendimento automatico. L'API Rilevamento anomalie si adatta identificando e applicando automaticamente i modelli di mapping più appropriati ai dati, indipendentemente dal settore, dallo scenario o dal volume. Usando i dati di serie temporali, l'API determina i limiti per il rilevamento di anomalie, i valori previsti e quali punti dati sono anomalie.

![Rilevare cambiamenti del modello delle richieste di servizi](./media/anomaly_detection2.png)

L'uso del Rilevamento anomalie non richiede alcuna esperienza precedente in apprendimento automatico e l'API RESTful consente di integrare facilmente il servizio nelle applicazioni e nei processi.

## <a name="features"></a>Funzionalità

Con il Rilevamento anomalie è possibile rilevare automaticamente le anomalie in tutti i dati di serie temporali, o appena si verificano in tempo reale. 

|Funzionalità  |DESCRIZIONE  |
|---------|---------|
|Rilevare le anomalie appena si verificano in tempo reale. | Rilevare le anomalie nei dati di streaming usando i punti dati visualizzati in precedenza per determinare se la versione più recente è un'anomalia. Questa operazione genera un modello usando i punti dati inviati dall'utente e determina se il punto di destinazione è un'anomalia. Chiamando l'API con ogni nuovo punto dati generato, è possibile monitorare i dati durante la creazione. |
|Rilevare le anomalie nel set di dati come batch. | Usare le serie temporali per rilevare eventuali anomalie che potrebbero esistere in tutti i dati. Questa operazione genera un modello usando tutti i dati di serie temporali, in cui ogni punto è analizzato con lo stesso modello.         |
| Ottenere informazioni aggiuntive sui dati. | Ottenere informazioni dettagliate utili su eventuali anomalie osservate, inclusi i valori previsti, i limiti e le posizioni delle anomalie. |
| Regolare i limiti di rilevamento anomalie. | L'API Rilevamento anomalie crea automaticamente limiti per il rilevamento anomalie. Modificare questi limiti in modo da aumentare o diminuire la sensibilità dell'API alle anomalie di dati e adeguare i dati. |

## <a name="demo"></a>Demo

Per iniziare rapidamente a usare l'API Rilevamento anomalie, provare una [demo online](https://notebooks.azure.com/AzureAnomalyDetection/projects/anomalydetector) che può essere eseguita nel browser. Questa demo viene eseguita in un notebook di Jupyter ospitato sul Web e mostra come inviare una richiesta API e visualizzare il risultato.

Per eseguire la demo, completare i passaggi seguenti:

1. Ottenere una chiave di sottoscrizione valida dell'API Rilevamento anomalie e un endpoint API. La sezione seguente contiene le istruzioni per l'iscrizione. 
2. Accedere e fare clic su Clone in alto a destra.
3. Fare clic su **Run on free compute** (Esegui in ambiente di calcolo gratuito)
4. Selezionare uno dei notebook per questo esempio.
5. Aggiungere una chiave di sottoscrizione valida dell'API Rilevamento anomalie alla variabile `subscription_key`. Modificare la variabile `endpoint` in modo nel proprio endpoint. Ad esempio: `https://westus2.api.cognitive.microsoft.com`
1. Nella barra dei menu superiore fare clic su **Cell** (Cella) quindi su **Run All** (Esegui tutto).

## <a name="workflow"></a>Flusso di lavoro

L'API Rilevamento anomalie è un servizio Web RESTful, facile da chiamare da qualsiasi linguaggio di programmazione che può effettuare richieste HTTP e analizzare codice JSON.

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

Dopo aver eseguito l'iscrizione:

1. Convertire i dati di serie temporali in un formato JSON valido. Usare le [procedure consigliate](concepts/anomaly-detection-best-practices.md) durante la preparazione dei dati per ottenere i risultati migliori.
1. Inviare una richiesta all'API Rilevamento anomalie con i dati.
1. Elaborare la risposta dell'API tramite l'analisi del messaggio JSON restituito.

## <a name="next-steps"></a>Passaggi successivi

* [Guida introduttiva: Rilevare le anomalie nei dati delle serie temporali tramite l'API REST Rilevamento anomalie](quickstarts/detect-data-anomalies-csharp.md)
* [Demo online](https://notebooks.azure.com/AzureAnomalyDetection/projects/anomalydetector) dell'API Rilevamento anomalie
* [Informazioni di riferimento sulle API del servizio REST](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect) dell'API Rilevamento anomalie