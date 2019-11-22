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
ms.date: 09/05/2019
ms.author: aahi
ms.openlocfilehash: 706bd9c141b191632acee1c04745d64baaf50ee5
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74012064"
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

Per comprendere il funzionamento di Rilevamento anomalie, vedere questa [dimostrazione interattiva](https://aka.ms/adDemo).
Per eseguire la demo, è necessario creare una risorsa di Rilevamento anomalie e ottenere l'endpoint e la chiave API.

## <a name="notebook"></a>Blocco appunti

Per informazioni su come chiamare l'API Rilevamento anomalie, provare questo [notebook di Azure](https://aka.ms/adNotebook). Questo notebook Jupyter ospitato sul Web illustra come inviare una richiesta API e visualizzare il risultato.

Per eseguire il notebook, seguire questa procedura:

1. Ottenere una chiave di sottoscrizione valida dell'API Rilevamento anomalie e un endpoint API. La sezione seguente contiene le istruzioni per l'iscrizione.
1. Accedere e fare clic su Clone in alto a destra.
1. Prima di completare l'operazione di clonazione, deselezionare l'opzione "Public" (Pubblico) nella finestra di dialogo. In caso contrario, il notebook, incluse le chiavi di sottoscrizione, sarà pubblico.
1. Fare clic su **Run on free compute** (Esegui in ambiente di calcolo gratuito)
1. Selezionare uno dei notebook.
1. Aggiungere una chiave di sottoscrizione valida dell'API Rilevamento anomalie alla variabile `subscription_key`. 
1. Modificare la variabile `endpoint` in modo nel proprio endpoint. Ad esempio: `https://westus2.api.cognitive.microsoft.com/anomalydetector/v1.0/timeseries/last/detect`
1. Nella barra dei menu superiore fare clic su **Cell** (Cella) quindi su **Run All** (Esegui tutto).

## <a name="workflow"></a>Flusso di lavoro

L'API Rilevamento anomalie è un servizio Web RESTful, facile da chiamare da qualsiasi linguaggio di programmazione che può effettuare richieste HTTP e analizzare codice JSON.

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

Dopo aver eseguito l'iscrizione:

1. Convertire i dati di serie temporali in un formato JSON valido. Usare le [procedure consigliate](concepts/anomaly-detection-best-practices.md) durante la preparazione dei dati per ottenere i risultati migliori.
1. Inviare una richiesta all'API Rilevamento anomalie con i dati.
1. Elaborare la risposta dell'API tramite l'analisi del messaggio JSON restituito.

## <a name="algorithms"></a>Algoritmi

* Fare riferimento ai blog tecnici [Introduzione all'API Rilevamento anomalie di Azure](https://techcommunity.microsoft.com/t5/AI-Customer-Engineering-Team/Introducing-Azure-Anomaly-Detector-API/ba-p/490162) e [Overview of SR-CNN algorithm in Azure Anomaly Detector](https://techcommunity.microsoft.com/t5/AI-Customer-Engineering-Team/Overview-of-SR-CNN-algorithm-in-Azure-Anomaly-Detector/ba-p/982798) (Panoramica dell'algoritmo SR-CNN in Rilevamento anomalie di Azure) sugli algoritmi in dettaglio.
* Per informazioni sugli algoritmi SR-CNN all'avanguardia sviluppati da Microsoft, vedere il documento accettato da KDD 2019 relativo al [servizio Microsoft per il rilevamento delle anomalie nelle serie temporali](https://arxiv.org/abs/1906.03821).

> [!VIDEO https://www.youtube.com/embed/ERTaAnwCarM]

## <a name="join-the-anomaly-detector-community"></a>Partecipare alla community di Rilevamento anomalie

* Partecipare al [gruppo Anomaly Detector Advisors su Microsoft Teams](https://aka.ms/AdAdvisorsJoin)
* Vedere il [contenuto generato dall'utente](user-generated-content.md) selezionato

## <a name="next-steps"></a>Passaggi successivi

* [Guida introduttiva: Rilevare le anomalie nei dati delle serie temporali tramite l'API REST Rilevamento anomalie](quickstarts/detect-data-anomalies-csharp.md)
* [Demo online](https://notebooks.azure.com/AzureAnomalyDetection/projects/anomalydetector) dell'API Rilevamento anomalie
* [Informazioni di riferimento sulle API del servizio REST](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect) dell'API Rilevamento anomalie
