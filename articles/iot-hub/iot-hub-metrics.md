---
title: Usare le metriche per monitorare l'hub IoT di Azure | Documentazione Microsoft
description: "Procedura: Usare le metriche dell'hub IoT di Azure per valutare e monitorare l'integrità complessiva degli hub IoT."
services: iot-hub
documentationcenter: 
author: nberdy
manager: timlt
editor: 
ms.assetid: a47108fd-f994-4105-b21d-5b8f697b699c
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/25/2017
ms.author: nberdy
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: cafe5009b8e96b147b5bbed1957024f6d96feb58
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2017
---
# <a name="understand-iot-hub-metrics"></a>Comprendere le metriche dell'hub IoT
Le metriche dell'hub IoT offrono dati migliori sullo stato delle risorse di Azure IoT nella sottoscrizione di Azure. La metrica dell'hub IoT consente di valutare l'integrità complessiva del servizio e dei dispositivi connessi. Le statistiche per l'utente sono importanti perché consentono di vedere le attività in corso nell'hub IoT e di individuare la causa radice dei problemi senza dover contattare il supporto di Azure.

Le metriche sono abilitate per impostazione predefinita. È possibile visualizzare le metriche dell'hub IoT nel portale di Azure.

## <a name="how-to-view-iot-hub-metrics"></a>Come visualizzare le metriche dell'hub IoT
1. Creare un hub IoT. Le istruzioni sulla creazione di un hub IoT sono disponibili nella [Guida introduttiva][lnk-get-started].
2. Aprire il pannello dell'hub IoT. In questa pagina fare clic su **Metrica**.
   
    ![][1]
3. Nel pannello Metrica è possibile visualizzare le metriche per l'hub IoT e creare visualizzazioni personalizzate delle metriche. È possibile scegliere di inviare i dati della metrica a un endpoint di Hub eventi o a un account di archiviazione di Azure facendo clic su **Impostazioni di diagnostica**.
   
    ![][2]

## <a name="iot-hub-metrics-and-how-to-use-them"></a>Metriche dell'hub IoT e modalità d'uso
L'hub IoT offre diverse metriche per fornire una panoramica dell'integrità dell'hub e il numero totale di dispositivi connessi. È possibile combinare le informazioni da più metriche per disegnare un quadro generale dello stato dell'hub IoT. La tabella seguente descrive le metriche che ogni hub IoT rileva e la correlazione di ognuna con lo stato generale dell'hub IoT.

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|
|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|Tentativi di invio di messaggi di telemetria|Conteggio|Totale|Il numero di messaggi di telemetria da dispositivo a cloud che si è cercato di inviare all'hub IoT|
|d2c.telemetry.ingress.success|Messaggi di telemetria inviati|Conteggio|Totale|Il numero di messaggi di telemetria da dispositivo a cloud inviati all'hub IoT|
|c2d.commands.egress.complete.success|Comandi completati|Conteggio|Totale|Numero di comandi da cloud a dispositivo completati dal dispositivo|
|c2d.commands.egress.abandon.success|Comandi abbandonati|Conteggio|Totale|Numero di comandi da cloud a dispositivo abbandonati dal dispositivo|
|c2d.commands.egress.reject.success|Comandi rifiutati|Conteggio|Totale|Numero di comandi da cloud a dispositivo rifiutati dal dispositivo|
|devices.totalDevices|Totale dispositivi|Conteggio|Totale|Il numero di dispositivi registrati nell'hub IoT|
|devices.connectedDevices.allProtocol|Dispositivi connessi|Conteggio|Totale|Il numero di dispositivi connessi all'hub IoT|
|d2c.telemetry.egress.success|Messaggi telemetria recapitati|Conteggio|Totale|Numero di volte in cui i messaggi sono stati scritti negli endpoint (totale)|
|d2c.telemetry.egress.dropped|Messaggi rimossi|Conteggio|Totale|Numero di messaggi rimossi perché non corrispondenti alle route e la route di fallback è stata disabilitata|
|d2c.telemetry.egress.orphaned|Messaggi orfani|Conteggio|Totale|Il numero di messaggi non corrispondenti ad alcuna route inclusa la route di fallback|
|d2c.telemetry.egress.invalid|Messaggi non validi|Conteggio|Totale|Il numero dei messaggi non recapitati per incompatibilità con l'endpoint|
|d2c.telemetry.egress.fallback|Messaggi corrispondenti alla condizione di fallback|Conteggio|Totale|Numero di messaggi scritti nell'endpoint di fallback|
|d2c.endpoints.egress.eventHubs|Messaggi recapitati agli endpoint dell'hub eventi|Conteggio|Totale|Numero di volte in cui i messaggi sono stati scritti negli endpoint dell'hub eventi|
|d2c.endpoints.latency.eventHubs|Latenza messaggi per gli endpoint di Hub eventi|Millisecondi|Media|La latenza media tra l'ingresso del messaggio nell'hub IoT e l'ingresso del messaggio in un endpoint di Hub eventi, in millisecondi|
|d2c.endpoints.egress.serviceBusQueues|Messaggi recapitati agli endpoint della coda del bus di servizio|Conteggio|Totale|Numero di volte in cui i messaggi sono stati scritti negli endpoint della coda del bus di servizio|
|d2c.endpoints.latency.serviceBusQueues|Latenza messaggi per gli endpoint della coda del bus di servizio|Millisecondi|Media|La latenza media tra l'ingresso del messaggio nell'hub IoT e l'ingresso del messaggio in un endpoint della coda del bus di servizio, in millisecondi|
|d2c.endpoints.egress.serviceBusTopics|Messaggi recapitati agli endpoint dell'argomento del bus di servizio|Conteggio|Totale|Numero di volte in cui i messaggi sono stati scritti negli endpoint dell'argomento del bus di servizio|
|d2c.endpoints.latency.serviceBusTopics|Latenza messaggi per gli endpoint dell'argomento del bus di servizio|Millisecondi|Media|La latenza media tra l'ingresso del messaggio nell'hub IoT e l'ingresso del messaggio in un endpoint dell'argomento del bus di servizio, in millisecondi|
|d2c.endpoints.egress.builtIn.events|Messaggi recapitati all'endpoint predefinito (messaggi/eventi)|Conteggio|Totale|Numero di volte in cui i messaggi sono stati scritti nell'endpoint predefinito (messaggi/eventi)|
|d2c.endpoints.latency.builtIn.events|Latenza dei messaggi per l'endpoint predefinito (messaggi/eventi)|Millisecondi|Media|La latenza media tra l'ingresso del messaggio nell'hub IoT e l'ingresso del messaggio nell'endpoint predefinito (messaggi/eventi), in millisecondi |
|d2c.twin.read.success|Letture dei dispositivi gemelli completate dai dispositivi|Conteggio|Totale|Numero di tutte le letture dei dispositivi gemelli avviate dal dispositivo completate.|
|d2c.twin.read.failure|Letture dei dispositivi gemelli non riuscite per i dispositivi|Conteggio|Totale|Numero di tutte le letture dei dispositivi gemelli avviate dal dispositivo non riuscite.|
|d2c.twin.read.size|Dimensioni delle risposte di letture dei dispositivi gemelli dai dispositivi|Byte|Media|Numero medio, minimo e massimo di letture dei dispositivi gemelli avviate dal dispositivo completate.|
|d2c.twin.update.success|Aggiornamenti dei dispositivi gemelli completati dai dispositivi|Conteggio|Totale|Numero di tutti gli aggiornamenti dei dispositivi gemelli avviati dal dispositivo completati.|
|d2c.twin.update.failure|Aggiornamenti dei dispositivi gemelli non riusciti per i dispositivi|Conteggio|Totale|Numero di tutti gli aggiornamenti dei dispositivi gemelli avviati dal dispositivo non riusciti.|
|d2c.twin.update.size|Dimensioni degli aggiornamenti dei dispositivi gemelli dai dispositivi|Byte|Media|Dimensioni medie, minime e massime degli aggiornamenti dei dispositivi gemelli avviati dal dispositivo completati.|
|c2d.methods.success|Chiamate a metodi diretti riuscite|Conteggio|Totale|Numero di tutte le chiamate a metodi diretti riuscite.|
|c2d.methods.failure|Chiamate a metodi diretti non riuscite|Conteggio|Totale|Numero di tutte le chiamate a metodi diretti non riuscite.|
|c2d.methods.requestSize|Dimensioni delle richieste di chiamate a metodi diretti|Byte|Media|Dimensioni medie, minime e massime delle richieste di chiamate a metodi diretti riuscite.|
|c2d.methods.responseSize|Dimensioni delle risposte a chiamate a metodi diretti|Byte|Media|Dimensioni medie, minime e massime delle risposte a chiamate a metodi diretti riuscite.|
|c2d.twin.read.success|Letture dei dispositivi gemelli completate dal back-end|Conteggio|Totale|Numero di tutte le letture dei dispositivi gemelli avviate dal back-end completate.|
|c2d.twin.read.failure|Letture dei dispositivi gemelli non riuscite per il back-end|Conteggio|Totale|Numero di tutte le letture dei dispositivi gemelli avviate dal back-end non riuscite.|
|c2d.twin.read.size|Dimensioni delle risposte di letture dei dispositivi gemelli dal back-end|Byte|Media|Numero medio, minimo e massimo di letture dei dispositivi gemelli avviate dal back-end completate.|
|c2d.twin.update.success|Aggiornamenti dei dispositivi gemelli completati dal back-end|Conteggio|Totale|Numero di tutti gli aggiornamenti dei dispositivi gemelli avviati dal back-end completati.|
|c2d.twin.update.failure|Aggiornamenti dei dispositivi gemelli non riusciti per il back-end|Conteggio|Totale|Numero di tutti gli aggiornamenti dei dispositivi gemelli avviati dal back-end non riusciti.|
|c2d.twin.update.size|Dimensioni degli aggiornamenti dei dispositivi gemelli dal back-end|Byte|Media|Dimensioni medie, minime e massime degli aggiornamenti dei dispositivi gemelli avviati dal back-end completati.|
|twinQueries.success|Query dei dispositivi gemelli completate|Conteggio|Totale|Numero di tutte le query dei dispositivi gemelli completate.|
|twinQueries.failure|Query dei dispositivi gemelli non riuscite|Conteggio|Totale|Numero di tutte le query dei dispositivi gemelli non riuscite.|
|twinQueries.resultSize|Dimensioni dei risultati delle query dei dispositivi gemelli|Byte|Media|Dimensioni medie, minime e massime dei risultati delle query dei dispositivi gemelli.|
|jobs.createTwinUpdateJob.success|Creazioni di processi di aggiornamento dei dispositivi gemelli completate|Conteggio|Totale|Numero di tutte le creazioni di processi di aggiornamento dei dispositivi gemelli completate.|
|jobs.createTwinUpdateJob.failure|Creazioni di processi di aggiornamento dei dispositivi gemelli non riuscite|Conteggio|Totale|Numero di tutte le creazioni di processi di aggiornamento dei dispositivi gemelli non riuscite.|
|jobs.createDirectMethodJob.success|Creazioni di processi di chiamata al metodo completate|Conteggio|Totale|Numero di tutte le creazioni di processi di chiamata a metodi diretti completate.|
|jobs.createDirectMethodJob.failure|Creazioni di processi di chiamata al metodo non riuscite|Conteggio|Totale|Numero di tutte le creazioni di processi di chiamata a metodi diretti non riuscite.|
|jobs.listJobs.success|Chiamate per elencare i processi riuscite|Conteggio|Totale|Numero di tutte le chiamate per elencare i processi riuscite.|
|jobs.listJobs.failure|Chiamate per elencare i processi non riuscite|Conteggio|Totale|Numero di tutte le chiamate per elencare i processi non riuscite.|
|jobs.cancelJob.success|Annullamenti di processi riusciti|Conteggio|Totale|Numero di tutte le chiamate per annullare i processi riuscite.|
|jobs.cancelJob.failure|Annullamenti di processi non riusciti|Conteggio|Totale|Numero di tutte le chiamate per annullare i processi non riuscite.|
|jobs.queryJobs.success|Query sui processi riuscite|Conteggio|Totale|Numero di tutte le chiamate per eseguire query sui processi riuscite.|
|jobs.queryJobs.failure|Query sui processi non riuscite|Conteggio|Totale|Numero di tutte le chiamate per eseguire query sui processi non riuscite.|
|jobs.completed|Processi completati|Conteggio|Totale|Numero di tutti i processi completati.|
|jobs.failed|Processi non riusciti|Conteggio|Totale|Numero di tutti i processi non riusciti.|

## <a name="next-steps"></a>Passaggi successivi
Al termine di questa panoramica delle metriche dell'hub IoT è possibile usare i collegamenti seguenti per maggiori informazioni sulla gestione dell'hub IoT di Azure:

* [Monitoraggio delle operazioni][lnk-monitor]

Per altre informazioni sulle funzionalità dell'hub IoT, vedere:

* [Guida per gli sviluppatori dell'hub IoT][lnk-devguide]
* [Distribuzione dell'intelligenza artificiale in dispositivi perimetrali con Azure IoT Edge][lnk-iotedge]

<!-- Links and images -->
[1]: media/iot-hub-metrics/enable-metrics-1.png
[2]: media/iot-hub-metrics/enable-metrics-2.png

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-operations-monitoring]: iot-hub-operations-monitoring.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-dr]: iot-hub-ha-dr.md

[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
