---
title: Usare le metriche per monitorare l'hub IoT di Azure | Documentazione Microsoft
description: "Procedura: Usare le metriche dell'hub IoT di Azure per valutare e monitorare l'integrità complessiva degli hub IoT."
author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: jlian
ms.openlocfilehash: ec8a00460b4a750339f929eb6879ac6eb63cac8e
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78396474"
---
# <a name="understand-iot-hub-metrics"></a>Comprendere le metriche dell'hub IoT

Le metriche dell'hub IoT offrono dati migliori sullo stato delle risorse di Azure IoT nella sottoscrizione di Azure. La metrica dell'hub IoT consente di valutare l'integrità complessiva del servizio e dei dispositivi connessi. Le statistiche per l'utente sono importanti perché consentono di vedere le attività in corso nell'hub IoT e di individuare la causa radice dei problemi senza dover contattare il supporto di Azure.

Le metriche sono abilitate per impostazione predefinita. È possibile visualizzare le metriche dell'hub IoT nel portale di Azure.

> [!NOTE]
> È possibile usare le metriche dell'hub Internet per visualizzare le informazioni sui dispositivi Plug and Play connessi all'hub Internet. I dispositivi Plug and Play sono parte integrante della [plug and Play di anteprima pubblica](../iot-pnp/overview-iot-plug-and-play.md).

## <a name="how-to-view-iot-hub-metrics"></a>Come visualizzare le metriche dell'hub IoT

1. Creare un hub IoT. È possibile trovare istruzioni su come creare un hub IoT nella guida sull'[invio di dati di telemetria da un dispositivo a un hub IoT](quickstart-send-telemetry-dotnet.md).

2. Aprire il pannello dell'hub IoT. In questa pagina fare clic su **Metrica**.
   
    ![Screenshot che mostra la posizione dell'opzione Metrica nella pagina delle risorse dell'hub IoT](./media/iot-hub-metrics/enable-metrics-1.png)

3. Nel pannello Metrica è possibile visualizzare le metriche per l'hub IoT e creare visualizzazioni personalizzate delle metriche. 
   
    ![Screenshot che mostra la pagina Metrica per l'hub IoT](./media/iot-hub-metrics/enable-metrics-2.png)
    
4. È possibile scegliere di inviare i dati della metrica a un endpoint di Hub eventi o a un account di archiviazione di Azure facendo clic su **Impostazioni di diagnostica** e quindi su **Add diagnostic setting** (Aggiungi impostazione di diagnostica).

   ![Screenshot che mostra la posizione del pulsante Impostazioni di diagnostica](./media/iot-hub-metrics/enable-metrics-3.png)

## <a name="iot-hub-metrics-and-how-to-use-them"></a>Metriche dell'hub IoT e modalità d'uso

L'hub IoT offre diverse metriche per fornire una panoramica dell'integrità dell'hub e il numero totale di dispositivi connessi. È possibile combinare le informazioni da più metriche per disegnare un quadro generale dello stato dell'hub IoT. La tabella seguente descrive le metriche che ogni hub IoT rileva e la correlazione di ognuna con lo stato generale dell'hub IoT.

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|Tentativi di invio di messaggi di telemetria|Conteggio|Totale|Il numero di messaggi di telemetria da dispositivo a cloud che si è cercato di inviare all'hub IoT|nessuno|
|d2c.telemetry.ingress.success|Messaggi di telemetria inviati|Conteggio|Totale|Il numero di messaggi di telemetria da dispositivo a cloud inviati all'hub IoT|nessuno|
|c2d.commands.egress.complete.success|Recapito messaggi C2D completati|Conteggio|Totale|Numero di recapiti di messaggi da cloud a dispositivo completati dal dispositivo|nessuno|
|c2d.commands.egress.abandon.success|Messaggi C2D abbandonati|Conteggio|Totale|Numero di messaggi da cloud a dispositivo abbandonati dal dispositivo|nessuno|
|c2d.commands.egress.reject.success|Messaggi C2D rifiutati|Conteggio|Totale|Numero di messaggi da cloud a dispositivo rifiutati dal dispositivo|nessuno|
|C2DMessagesExpired|Messaggi C2D scaduti (anteprima)|Conteggio|Totale|Numero di messaggi da cloud a dispositivo scaduti|nessuno|
|devices.totalDevices|Dispositivi totali (deprecati)|Conteggio|Totale|Il numero di dispositivi registrati nell'hub IoT|nessuno|
|devices.connectedDevices.allProtocol|Dispositivi connessi (deprecati) |Conteggio|Totale|Il numero di dispositivi connessi all'hub IoT|nessuno|
|d2c.telemetry.egress.success|Routing: messaggi di telemetria recapitati|Conteggio|Totale|Numero di volte in cui i messaggi sono stati recapitati correttamente a tutti gli endpoint tramite routing dell'hub IoT. Se un messaggio viene indirizzato a più endpoint, questo valore aumenta di uno per ogni recapito con esito positivo. Se un messaggio viene recapitato più volte allo stesso endpoint, questo valore aumenta di uno per ogni recapito con esito positivo.|nessuno|
|d2c.telemetry.egress.dropped|Routing: messaggi di telemetria eliminati |Conteggio|Totale|Numero di volte in cui i messaggi sono stati eliminati dal routing dell'hub IoT a causa degli endpoint inattivi. Questo valore non include messaggi recapitati alla route di fallback perché i messaggi eliminati non vengono recapitati in tale punto.|nessuno|
|d2c.telemetry.egress.orphaned|Routing: messaggi di telemetria orfani |Conteggio|Totale|Numero di volte in cui i messaggi sono stati resi orfani dal routing dell'hub IoT perché non corrispondono ad alcuna regola di routing (inclusa la regola di fallback). |nessuno|
|d2c.telemetry.egress.invalid|Routing: messaggi di telemetria incompatibili|Conteggio|Totale|Numero di volte in cui il routing dell'hub IoT non è riuscito a recapitare i messaggi a causa di un'incompatibilità con l'endpoint. Questo valore non include i nuovi tentativi.|nessuno|
|d2c.telemetry.egress.fallback|Routing: messaggi recapitati al fallback|Conteggio|Totale|Numero di volte in cui il routing dell'hub IoT ha recapitato i messaggi all'endpoint associato con la route di fallback.|nessuno|
|d2c.endpoints.egress.eventHubs|Routing: messaggi inviati all'hub eventi|Conteggio|Totale|Numero di volte in cui il routing dell'hub IoT ha recapitato i messaggi agli endpoint dell'hub eventi.|nessuno|
|d2c.endpoints.latency.eventHubs|Routing: latenza dei messaggi per l'hub eventi|Millisecondi|Media|Latenza media (in millisecondi) tra l'ingresso del messaggio nell'hub IoT e l'ingresso del messaggio in un endpoint dell'hub eventi.|nessuno|
|d2c.endpoints.egress.serviceBusQueues|Routing: messaggi recapitati alla coda del bus di servizio|Conteggio|Totale|Numero di volte in cui il routing dell'hub IoT ha recapitato i messaggi agli endpoint della coda del bus di servizio.|nessuno|
|d2c.endpoints.latency.serviceBusQueues|Routing: latenza dei messaggi per la coda del bus di servizio|Millisecondi|Media|Latenza media (in millisecondi) tra l'ingresso dei messaggi all'hub IoT e l'ingresso dei messaggi di telemetria in un endpoint della coda del bus di servizio.|nessuno|
|d2c.endpoints.egress.serviceBusTopics|Routing: messaggi recapitati all'argomento del bus di servizio|Conteggio|Totale|Numero di volte in cui il routing dell'hub IoT ha recapitato i messaggi agli endpoint dell'argomento del bus di servizio.|nessuno|
|d2c.endpoints.latency.serviceBusTopics|Routing: latenza dei messaggi per l'argomento del bus di servizio|Millisecondi|Media|Latenza media (in millisecondi) tra l'ingresso dei messaggi all'hub IoT e l'ingresso dei messaggi di telemetria in un endpoint dell'argomento del bus di servizio.|nessuno|
|d2c.endpoints.egress.builtIn.events|Routing: messaggi recapitati a messaggi/eventi|Conteggio|Totale|Numero di volte in cui il routing dell'hub IoT ha recapitato i messaggi all'endpoint predefinito (messaggi/eventi).|nessuno|
|d2c.endpoints.latency.builtIn.events|Routing: latenza dei messaggi per messaggi/eventi|Millisecondi|Media|Latenza media (in millisecondi) tra l'ingresso del messaggio nell'hub IoT e l'ingresso del messaggio nell'endpoint predefinito (messaggi/eventi).|nessuno|
|d2c.endpoints.egress.storage|Routing: messaggi recapitati all'archiviazione|Conteggio|Totale|Numero di volte in cui il routing dell'hub IoT ha recapitato i messaggi agli endpoint di archiviazione.|nessuno|
|d2c.endpoints.latency.storage|Routing: latenza dei messaggi per l'archiviazione|Millisecondi|Media|Latenza media (in millisecondi) tra l'ingresso dei messaggi all'hub IoT e l'ingresso dei messaggi di telemetria in un endpoint di archiviazione.|nessuno|
|d2c.endpoints.egress.storage.bytes|Routing: dati recapitati all'archiviazione|Byte|Totale|Quantità di dati (byte) che il routing dell'hub IoT ha recapitato agli endpoint di archiviazione.|nessuno|
|d2c.endpoints.egress.storage.blobs|Routing: BLOB recapitati all'archiviazione|Conteggio|Totale|Numero di volte in cui routing dell'hub IoT ha recapitato BLOB agli endpoint di archiviazione.|nessuno|
|EventGridDeliveries|Recapiti di griglia di eventi (anteprima)|Conteggio|Totale|Il numero di eventi dell'hub Internet pubblico pubblicati in griglia di eventi. Utilizzare la dimensione risultato per il numero di richieste riuscite e non riuscite. EventType Dimension indica il tipo di evento (https://aka.ms/ioteventgrid).|ResourceId<br/>Risultato<br/>EventType|
|EventGridLatency|Latenza griglia di eventi (anteprima)|Millisecondi|Media|Latenza media (in millisecondi) dal momento in cui è stato generato l'evento dell'hub Internet quando l'evento è stato pubblicato in griglia di eventi. Questo numero è una media tra tutti i tipi di evento. Utilizzare la dimensione EventType per visualizzare la latenza di un tipo specifico di evento.|ResourceId<br/>EventType|
|d2c.twin.read.success|Letture dei dispositivi gemelli completate dai dispositivi|Conteggio|Totale|Numero di tutte le letture dei dispositivi gemelli avviate dal dispositivo completate.|nessuno|
|d2c.twin.read.failure|Letture dei dispositivi gemelli non riuscite per i dispositivi|Conteggio|Totale|Numero di tutte le letture dei dispositivi gemelli avviate dal dispositivo non riuscite.|nessuno|
|d2c.twin.read.size|Dimensioni delle risposte di letture dei dispositivi gemelli dai dispositivi|Byte|Media|Numero medio, minimo e massimo di letture dei dispositivi gemelli avviate dal dispositivo completate.|nessuno|
|d2c.twin.update.success|Aggiornamenti dei dispositivi gemelli completati dai dispositivi|Conteggio|Totale|Numero di tutti gli aggiornamenti dei dispositivi gemelli avviati dal dispositivo completati.|nessuno|
|d2c.twin.update.failure|Aggiornamenti dei dispositivi gemelli non riusciti per i dispositivi|Conteggio|Totale|Numero di tutti gli aggiornamenti dei dispositivi gemelli avviati dal dispositivo non riusciti.|nessuno|
|d2c.twin.update.size|Dimensioni degli aggiornamenti dei dispositivi gemelli dai dispositivi|Byte|Media|Dimensioni medie, minime e massime degli aggiornamenti dei dispositivi gemelli avviati dal dispositivo completati.|nessuno|
|c2d.methods.success|Chiamate a metodi diretti riuscite|Conteggio|Totale|Numero di tutte le chiamate a metodi diretti riuscite.|nessuno|
|c2d.methods.failure|Chiamate a metodi diretti non riuscite|Conteggio|Totale|Numero di tutte le chiamate a metodi diretti non riuscite.|nessuno|
|c2d.methods.requestSize|Dimensioni delle richieste di chiamate a metodi diretti|Byte|Media|Dimensioni medie, minime e massime delle richieste di chiamate a metodi diretti riuscite.|nessuno|
|c2d.methods.responseSize|Dimensioni delle risposte a chiamate a metodi diretti|Byte|Media|Dimensioni medie, minime e massime delle risposte a chiamate a metodi diretti riuscite.|nessuno|
|c2d.twin.read.success|Letture dei dispositivi gemelli completate dal back-end|Conteggio|Totale|Numero di tutte le letture dei dispositivi gemelli avviate dal back-end completate.|nessuno|
|c2d.twin.read.failure|Letture dei dispositivi gemelli non riuscite per il back-end|Conteggio|Totale|Numero di tutte le letture dei dispositivi gemelli avviate dal back-end non riuscite.|nessuno|
|c2d.twin.read.size|Dimensioni delle risposte di letture dei dispositivi gemelli dal back-end|Byte|Media|Numero medio, minimo e massimo di letture dei dispositivi gemelli avviate dal back-end completate.|nessuno|
|c2d.twin.update.success|Aggiornamenti dei dispositivi gemelli completati dal back-end|Conteggio|Totale|Numero di tutti gli aggiornamenti dei dispositivi gemelli avviati dal back-end completati.|nessuno|
|c2d.twin.update.failure|Aggiornamenti dei dispositivi gemelli non riusciti per il back-end|Conteggio|Totale|Numero di tutti gli aggiornamenti dei dispositivi gemelli avviati dal back-end non riusciti.|nessuno|
|c2d.twin.update.size|Dimensioni degli aggiornamenti dei dispositivi gemelli dal back-end|Byte|Media|Dimensioni medie, minime e massime degli aggiornamenti dei dispositivi gemelli avviati dal back-end completati.|nessuno|
|twinQueries.success|Query dei dispositivi gemelli completate|Conteggio|Totale|Numero di tutte le query dei dispositivi gemelli completate.|nessuno|
|twinQueries.failure|Query dei dispositivi gemelli non riuscite|Conteggio|Totale|Numero di tutte le query dei dispositivi gemelli non riuscite.|nessuno|
|twinQueries.resultSize|Dimensioni dei risultati delle query dei dispositivi gemelli|Byte|Media|Dimensioni medie, minime e massime dei risultati delle query dei dispositivi gemelli.|nessuno|
|jobs.createTwinUpdateJob.success|Creazioni di processi di aggiornamento dei dispositivi gemelli completate|Conteggio|Totale|Numero di tutte le creazioni di processi di aggiornamento dei dispositivi gemelli completate.|nessuno|
|jobs.createTwinUpdateJob.failure|Creazioni di processi di aggiornamento dei dispositivi gemelli non riuscite|Conteggio|Totale|Numero di tutte le creazioni di processi di aggiornamento dei dispositivi gemelli non riuscite.|nessuno|
|jobs.createDirectMethodJob.success|Creazioni di processi di chiamata al metodo completate|Conteggio|Totale|Numero di tutte le creazioni di processi di chiamata a metodi diretti completate.|nessuno|
|jobs.createDirectMethodJob.failure|Creazioni di processi di chiamata al metodo non riuscite|Conteggio|Totale|Numero di tutte le creazioni di processi di chiamata a metodi diretti non riuscite.|nessuno|
|jobs.listJobs.success|Chiamate per elencare i processi riuscite|Conteggio|Totale|Numero di tutte le chiamate per elencare i processi riuscite.|nessuno|
|jobs.listJobs.failure|Chiamate per elencare i processi non riuscite|Conteggio|Totale|Numero di tutte le chiamate per elencare i processi non riuscite.|nessuno|
|jobs.cancelJob.success|Annullamenti di processi riusciti|Conteggio|Totale|Numero di tutte le chiamate per annullare i processi riuscite.|nessuno|
|jobs.cancelJob.failure|Annullamenti di processi non riusciti|Conteggio|Totale|Numero di tutte le chiamate per annullare i processi non riuscite.|nessuno|
|jobs.queryJobs.success|Query sui processi riuscite|Conteggio|Totale|Numero di tutte le chiamate per eseguire query sui processi riuscite.|nessuno|
|jobs.queryJobs.failure|Query sui processi non riuscite|Conteggio|Totale|Numero di tutte le chiamate per eseguire query sui processi non riuscite.|nessuno|
|jobs.completed|Processi completati|Conteggio|Totale|Numero di tutti i processi completati.|nessuno|
|jobs.failed|Processi non riusciti|Conteggio|Totale|Numero di tutti i processi non riusciti.|nessuno|
|d2c.telemetry.ingress.sendThrottle|Number of throttling errors (Numero di errori di limitazione)|Conteggio|Totale|Numero di errori di limitazione dovuti alle limitazioni della velocità effettiva del dispositivo|nessuno|
|dailyMessageQuotaUsed|Total number of messages used (Numero totale di messaggi usati)|Conteggio|Media|Numero totale di messaggi usati nella data odierna. Si tratta di un valore cumulativo che viene reimpostato su zero alle 00:00 UTC ogni giorno.|nessuno|
|deviceDataUsage|Utilizzo totale dei dati del dispositivo|Byte|Totale|Byte trasferiti a e da qualsiasi dispositivo connesso all'hub IoT|nessuno|
|deviceDataUsageV2|Uso totale dei dati del dispositivo (anteprima)|Byte|Totale|Byte trasferiti a e da qualsiasi dispositivo connesso all'hub IoT|nessuno|
|totalDeviceCount|Dispositivi totali (anteprima)|Conteggio|Media|Il numero di dispositivi registrati nell'hub IoT|nessuno|
|connectedDeviceCount|Dispositivi connessi (anteprima)|Conteggio|Media|Il numero di dispositivi connessi all'hub IoT|nessuno|
|configurazioni|Metriche di configurazione|Conteggio|Totale|Metriche per le operazioni di configurazione|nessuno|

## <a name="next-steps"></a>Passaggi successivi

Al termine di questa panoramica delle metriche dell'hub IoT è possibile usare i collegamenti seguenti per maggiori informazioni sulla gestione dell'hub IoT di Azure:

* [Configurare i log di diagnostica](iot-hub-monitor-resource-health.md)

Per altre informazioni sulle funzionalità dell'hub IoT, vedere:

* [Guida per sviluppatori dell'hub IoT](iot-hub-devguide.md)

* [Distribuzione dell'intelligenza artificiale in dispositivi perimetrali con Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
