---
title: Monitorare gli argomenti e le sottoscrizioni di eventi - Azure Event Grid IoT Edge Documenti Microsoft
description: Monitorare argomenti e sottoscrizioni di eventiMonitor topics and event subscriptions
author: banisadr
ms.author: babanisa
ms.reviewer: spelluru
ms.date: 01/09/2020
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: ce7c92f121fb458d528d63d0af0aad025b377386
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77086668"
---
# <a name="monitor-topics-and-event-subscriptions"></a>Monitorare argomenti e sottoscrizioni di eventiMonitor topics and event subscriptions

Griglia di eventi sul bordo espone una serie di metriche per gli argomenti e le sottoscrizioni di eventi nel [formato di esposizione Prometheus](https://prometheus.io/docs/instrumenting/exposition_formats/). Questo articolo descrive le metriche disponibili e come abilitarle.

## <a name="enable-metrics"></a>Abilitazione di metriche

Configurare il modulo per `metrics__reporterType` generare `prometheus` metriche impostando la variabile di ambiente su nel contenitore per la creazione di opzioni:Configure the module to emit metrics by setting the environment variable to in the container create options:

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

Le metriche `5888/metrics` saranno disponibili all'indirizzo del modulo per http e `4438/metrics` https. Ad esempio, `http://<modulename>:5888/metrics?api-version=2019-01-01-preview` per http. A questo punto, un modulo di metriche può eseguire il polling dell'endpoint per raccogliere le metriche come in questa architettura di [esempio.](https://github.com/veyalla/ehm)

## <a name="available-metrics"></a>Metriche disponibili

Sia gli argomenti che le sottoscrizioni di eventi generano metriche per fornire informazioni dettagliate sul recapito degli eventi e sulle prestazioni dei moduli.

### <a name="topic-metrics"></a>Metriche degli argomenti

| Metrica | Descrizione |
| ------ | ----------- |
| EventiEventiricevuti | Numero di eventi pubblicati sull'argomento
| UnmatchedEvents | Numero di eventi pubblicati nell'argomento che non corrispondono a una sottoscrizione di eventi e vengono eliminati
| Richieste di successoSuccessRequests | Numero di richieste di pubblicazione in ingresso ricevute dall'argomento
| Richieste di sistema | Numero di richieste di pubblicazione in ingresso non riuscito a causa di un errore di sistema interno
| UserErrorRequests (Richieste utente) | Numero nelle richieste di pubblicazione in ingresso non riuscito a causa di un errore dell'utente, ad esempio JSON in formato non validoNumber on inbound publish requests failed due to user error such as malformed JSON
| SuccessRequestLatencyMs | Latenza di risposta richiesta di pubblicazione in millisecondi


### <a name="event-subscription-metrics"></a>Metriche della sottoscrizione di eventi

| Metrica | Descrizione |
| ------ | ----------- |
| DeliverySuccessCounts | Numero di eventi recapitati correttamente all'endpoint configurato
| DeliveryFailureCounts | Numero di eventi che non è stato recapitato all'endpoint configurato
| DeliverySuccessLatencyMs | Latenza degli eventi recapitati correttamente in millisecondi
| DeliveryFailureLatencyMs | Latenza degli errori di recapito degli eventi in millisecondi
| SystemDelayForFirstAttemptMs (SystemDelayForFirstAttemptMs) | Ritardo del sistema degli eventi prima del primo tentativo di recapito in millisecondi
| DeliveryAttemptsCount (Conteggio dei tentativi di recapito | Numero di tentativi di recapito degli eventi - esito positivo e negativo
| ScadutiConteggi | Numero di eventi scaduti e non recapitati all'endpoint configurato