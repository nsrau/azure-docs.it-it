---
title: Monitorare gli argomenti e le sottoscrizioni di eventi-IoT Edge di griglia di eventi di Azure | Microsoft Docs
description: Monitorare gli argomenti e le sottoscrizioni di eventi
author: banisadr
ms.author: babanisa
ms.reviewer: spelluru
ms.date: 01/09/2020
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: ce7c92f121fb458d528d63d0af0aad025b377386
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2020
ms.locfileid: "77086668"
---
# <a name="monitor-topics-and-event-subscriptions"></a>Monitorare gli argomenti e le sottoscrizioni di eventi

Griglia di eventi su Edge espone una serie di metriche per gli argomenti e le sottoscrizioni di eventi nel [formato di esposizione Prometeo](https://prometheus.io/docs/instrumenting/exposition_formats/). Questo articolo descrive le metriche disponibili e le modalità di abilitazione.

## <a name="enable-metrics"></a>Abilitazione di metriche

Configurare il modulo per emettere le metriche impostando la variabile di ambiente `metrics__reporterType` su `prometheus` nelle opzioni di creazione del contenitore:

 ```json
        {
          "Env": [
            "metrics__reporterType=prometheus"
          ],
          "HostConfig": {
            "PortBindings": {
              "4438/tcp": [
                {
                    "HostPort": "4438"
                }
              ]
            }
          }
        }
 ```    

Le metriche saranno disponibili in `5888/metrics` del modulo per http e `4438/metrics` per HTTPS. Ad esempio, `http://<modulename>:5888/metrics?api-version=2019-01-01-preview` per http. A questo punto, un modulo metrica può eseguire il polling dell'endpoint per raccogliere le metriche come in questa [architettura di esempio](https://github.com/veyalla/ehm).

## <a name="available-metrics"></a>Metriche disponibili

Gli argomenti e le sottoscrizioni di eventi emettono metriche per fornire informazioni dettagliate sulle prestazioni del modulo e del recapito degli eventi.

### <a name="topic-metrics"></a>Metriche dell'argomento

| Metrica | Descrizione |
| ------ | ----------- |
| EventsReceived | Numero di eventi pubblicati nell'argomento
| UnmatchedEvents | Numero di eventi pubblicati nell'argomento che non corrispondono a una sottoscrizione di eventi e che vengono eliminati
| SuccessRequests | Numero di richieste di pubblicazione in ingresso ricevute dall'argomento
| SystemErrorRequests | Numero di richieste di pubblicazione in ingresso non riuscite a causa di un errore interno del sistema
| UserErrorRequests | Il numero di richieste di pubblicazione in ingresso non è riuscito a causa di un errore dell'utente, ad esempio JSON non valido
| SuccessRequestLatencyMs | Latenza di risposta richiesta di pubblicazione in millisecondi


### <a name="event-subscription-metrics"></a>Metriche della sottoscrizione di eventi

| Metrica | Descrizione |
| ------ | ----------- |
| deliverySuccessCounts | Numero di eventi recapitati correttamente all'endpoint configurato
| deliveryFailureCounts | Numero di eventi che non sono stati recapitati all'endpoint configurato
| deliverySuccessLatencyMs | Latenza degli eventi recapitati in millisecondi
| deliveryFailureLatencyMs | Latenza degli errori di recapito degli eventi in millisecondi
| systemDelayForFirstAttemptMs | Ritardo di sistema degli eventi prima del primo tentativo di recapito in millisecondi
| deliveryAttemptsCount | Numero di tentativi di recapito eventi-esito positivo e negativo
| expiredCounts | Numero di eventi scaduti e non recapitati all'endpoint configurato