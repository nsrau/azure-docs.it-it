---
title: Usare le metriche per monitorare l'hub IoT di Azure | Documentazione Microsoft
description: "Procedura: Usare le metriche dell'hub IoT di Azure per valutare e monitorare l'integrità complessiva degli hub IoT."
author: nberdy
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/25/2017
ms.author: nberdy
ms.openlocfilehash: 34a561c5bb523b914f4b88dc8c6edc2bd00627d0
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/01/2018
ms.locfileid: "50741930"
---
# <a name="understand-iot-hub-metrics"></a>Comprendere le metriche dell'hub IoT

Le metriche dell'hub IoT offrono dati migliori sullo stato delle risorse di Azure IoT nella sottoscrizione di Azure. La metrica dell'hub IoT consente di valutare l'integrità complessiva del servizio e dei dispositivi connessi. Le statistiche per l'utente sono importanti perché consentono di vedere le attività in corso nell'hub IoT e di individuare la causa radice dei problemi senza dover contattare il supporto di Azure.

Le metriche sono abilitate per impostazione predefinita. È possibile visualizzare le metriche dell'hub IoT nel portale di Azure.

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

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|d2c<br>.telemetry<br>.ingress.<br>allProtocol|Tentativi di invio di messaggi di telemetria|Conteggio|Totale|Il numero di messaggi di telemetria da dispositivo a cloud che si è cercato di inviare all'hub IoT|Nessuna dimensione|
|d2c<br>.telemetry<br>.ingress<br>.success|Messaggi di telemetria inviati|Conteggio|Totale|Il numero di messaggi di telemetria da dispositivo a cloud inviati all'hub IoT|Nessuna dimensione|
|c2d<br>.commands<br>.egress<br>.complete<br>.success|Comandi completati|Conteggio|Totale|Numero di comandi da cloud a dispositivo completati dal dispositivo|Nessuna dimensione|
|c2d<br>.commands<br>.egress<br>.abandon<br>.success|Comandi abbandonati|Conteggio|Totale|Numero di comandi da cloud a dispositivo abbandonati dal dispositivo|Nessuna dimensione|
|c2d<br>.commands<br>.egress<br>.reject<br>.success|Comandi rifiutati|Conteggio|Totale|Numero di comandi da cloud a dispositivo rifiutati dal dispositivo|Nessuna dimensione|
|devices<br>.totalDevices|Dispositivi totali (deprecati)|Conteggio|Totale|Il numero di dispositivi registrati nell'hub IoT|Nessuna dimensione|
|devices<br>.connectedDevices<br>.allProtocol|Dispositivi connessi (deprecati) |Conteggio|Totale|Il numero di dispositivi connessi all'hub IoT|Nessuna dimensione|
|d2c<br>.telemetry<br>.egress<br>.success|Routing: messaggi di telemetria recapitati|Conteggio|Totale|Numero di volte in cui i messaggi sono stati recapitati correttamente a tutti gli endpoint tramite routing dell'hub IoT. Se un messaggio viene indirizzato a più endpoint, questo valore aumenta di uno per ogni recapito con esito positivo. Se un messaggio viene recapitato più volte allo stesso endpoint, questo valore aumenta di uno per ogni recapito con esito positivo.|Nessuna dimensione|
|d2c<br>.telemetry<br>.egress<br>.dropped|Routing: messaggi di telemetria eliminati |Conteggio|Totale|Numero di volte in cui i messaggi sono stati eliminati dal routing dell'hub IoT a causa degli endpoint inattivi. Questo valore non include messaggi recapitati alla route di fallback perché i messaggi eliminati non vengono recapitati in tale punto.|Nessuna dimensione|
|d2c<br>.telemetry<br>.egress<br>.orphaned|Routing: messaggi di telemetria orfani |Conteggio|Totale|Numero di volte in cui i messaggi sono stati resi orfani dal routing dell'hub IoT perché non corrispondono ad alcuna regola di routing (inclusa la regola di fallback). |Nessuna dimensione|
|d2c<br>.telemetry<br>.egress<br>.invalid|Routing: messaggi di telemetria incompatibili|Conteggio|Totale|Numero di volte in cui il routing dell'hub IoT non è riuscito a recapitare i messaggi a causa di un'incompatibilità con l'endpoint. Questo valore non include i nuovi tentativi.|Nessuna dimensione|
|d2c<br>.telemetry<br>.egress<br>.fallback|Routing: messaggi recapitati al fallback|Conteggio|Totale|Numero di volte in cui il routing dell'hub IoT ha recapitato i messaggi all'endpoint associato con la route di fallback.|Nessuna dimensione|
|d2c<br>.endpoints<br>.egress<br>.eventHubs|Routing: messaggi inviati all'hub eventi|Conteggio|Totale|Numero di volte in cui il routing dell'hub IoT ha recapitato i messaggi agli endpoint dell'hub eventi.|Nessuna dimensione|
|d2c<br>.endpoints<br>.latency<br>.eventHubs|Routing: latenza dei messaggi per l'hub eventi|Millisecondi|Media|Latenza media (in millisecondi) tra l'ingresso del messaggio nell'hub IoT e l'ingresso del messaggio in un endpoint dell'hub eventi.|Nessuna dimensione|
|d2c<br>.endpoints<br>.egress<br>.serviceBusQueues|Routing: messaggi recapitati alla coda del bus di servizio|Conteggio|Totale|Numero di volte in cui il routing dell'hub IoT ha recapitato i messaggi agli endpoint della coda del bus di servizio.|Nessuna dimensione|
|d2c<br>.endpoints<br>.latency<br>.serviceBusQueues|Routing: latenza dei messaggi per la coda del bus di servizio|Millisecondi|Media|Latenza media (in millisecondi) tra l'ingresso dei messaggi all'hub IoT e l'ingresso dei messaggi di telemetria in un endpoint della coda del bus di servizio.|Nessuna dimensione|
|d2c<br>.endpoints<br>.egress<br>.serviceBusTopics|Routing: messaggi recapitati all'argomento del bus di servizio|Conteggio|Totale|Numero di volte in cui il routing dell'hub IoT ha recapitato i messaggi agli endpoint dell'argomento del bus di servizio.|Nessuna dimensione|
|d2c<br>.endpoints<br>.latency<br>.serviceBusTopics|Routing: latenza dei messaggi per l'argomento del bus di servizio|Millisecondi|Media|Latenza media (in millisecondi) tra l'ingresso dei messaggi all'hub IoT e l'ingresso dei messaggi di telemetria in un endpoint dell'argomento del bus di servizio.|Nessuna dimensione|
|d2c<br>.endpoints<br>.egress<br>.builtIn<br>.events|Routing: messaggi recapitati a messaggi/eventi|Conteggio|Totale|Numero di volte in cui il routing dell'hub IoT ha recapitato i messaggi all'endpoint predefinito (messaggi/eventi).|Nessuna dimensione|
|d2c<br>.endpoints<br>.latency<br>.builtIn.events|Routing: latenza dei messaggi per messaggi/eventi|Millisecondi|Media|Latenza media (in millisecondi) tra l'ingresso del messaggio nell'hub IoT e l'ingresso del messaggio nell'endpoint predefinito (messaggi/eventi).|Nessuna dimensione|
|d2c<br>.endpoints<br>.egress<br>.storage|Routing: messaggi recapitati all'archiviazione|Conteggio|Totale|Numero di volte in cui il routing dell'hub IoT ha recapitato i messaggi agli endpoint di archiviazione.|Nessuna dimensione|
|d2c<br>.endpoints<br>.latency<br>.storage|Routing: latenza dei messaggi per l'archiviazione|Millisecondi|Media|Latenza media (in millisecondi) tra l'ingresso dei messaggi all'hub IoT e l'ingresso dei messaggi di telemetria in un endpoint di archiviazione.|Nessuna dimensione|
|d2c<br>.endpoints<br>.egress<br>.storage<br>.bytes|Routing: dati recapitati all'archiviazione|Byte|Totale|Quantità di dati (byte) che il routing dell'hub IoT ha recapitato agli endpoint di archiviazione.|Nessuna dimensione|
|d2c<br>.endpoints<br>.egress<br>.storage<br>.blobs|Routing: BLOB recapitati all'archiviazione|Conteggio|Totale|Numero di volte in cui routing dell'hub IoT ha recapitato BLOB agli endpoint di archiviazione.|Nessuna dimensione|
|d2c<br>.twin<br>.read<br>.success|Letture dei dispositivi gemelli completate dai dispositivi|Conteggio|Totale|Numero di tutte le letture dei dispositivi gemelli avviate dal dispositivo completate.|Nessuna dimensione|
|d2c<br>.twin<br>.read<br>.failure|Letture dei dispositivi gemelli non riuscite per i dispositivi|Conteggio|Totale|Numero di tutte le letture dei dispositivi gemelli avviate dal dispositivo non riuscite.|Nessuna dimensione|
|d2c<br>.twin<br>.read<br>.size|Dimensioni delle risposte di letture dei dispositivi gemelli dai dispositivi|Byte|Media|Numero medio, minimo e massimo di letture dei dispositivi gemelli avviate dal dispositivo completate.|Nessuna dimensione|
|d2c<br>.twin<br>.update<br>.success|Aggiornamenti dei dispositivi gemelli completati dai dispositivi|Conteggio|Totale|Numero di tutti gli aggiornamenti dei dispositivi gemelli avviati dal dispositivo completati.|Nessuna dimensione|
|d2c<br>.twin<br>.update<br>.failure|Aggiornamenti dei dispositivi gemelli non riusciti per i dispositivi|Conteggio|Totale|Numero di tutti gli aggiornamenti dei dispositivi gemelli avviati dal dispositivo non riusciti.|Nessuna dimensione|
|d2c<br>.twin<br>.update<br>.size|Dimensioni degli aggiornamenti dei dispositivi gemelli dai dispositivi|Byte|Media|Dimensioni medie, minime e massime degli aggiornamenti dei dispositivi gemelli avviati dal dispositivo completati.|Nessuna dimensione|
|c2d<br>.methods<br>.success|Chiamate a metodi diretti riuscite|Conteggio|Totale|Numero di tutte le chiamate a metodi diretti riuscite.|Nessuna dimensione|
|c2d<br>.methods<br>.failure|Chiamate a metodi diretti non riuscite|Conteggio|Totale|Numero di tutte le chiamate a metodi diretti non riuscite.|Nessuna dimensione|
|c2d<br>.methods<br>.requestSize|Dimensioni delle richieste di chiamate a metodi diretti|Byte|Media|Dimensioni medie, minime e massime delle richieste di chiamate a metodi diretti riuscite.|Nessuna dimensione|
|c2d<br>.methods<br>.responseSize|Dimensioni delle risposte a chiamate a metodi diretti|Byte|Media|Dimensioni medie, minime e massime delle risposte a chiamate a metodi diretti riuscite.|Nessuna dimensione|
|c2d<br>.twin<br>.read<br>.success|Letture dei dispositivi gemelli completate dal back-end|Conteggio|Totale|Numero di tutte le letture dei dispositivi gemelli avviate dal back-end completate.|Nessuna dimensione|
|c2d<br>.twin<br>.read<br>.failure|Letture dei dispositivi gemelli non riuscite per il back-end|Conteggio|Totale|Numero di tutte le letture dei dispositivi gemelli avviate dal back-end non riuscite.|Nessuna dimensione|
|c2d<br>.twin<br>.read<br>.size|Dimensioni delle risposte di letture dei dispositivi gemelli dal back-end|Byte|Media|Numero medio, minimo e massimo di letture dei dispositivi gemelli avviate dal back-end completate.|Nessuna dimensione|
|c2d<br>.twin<br>.update<br>.success|Aggiornamenti dei dispositivi gemelli completati dal back-end|Conteggio|Totale|Numero di tutti gli aggiornamenti dei dispositivi gemelli avviati dal back-end completati.|Nessuna dimensione|
|c2d<br>.twin<br>.update<br>.failure|Aggiornamenti dei dispositivi gemelli non riusciti per il back-end|Conteggio|Totale|Numero di tutti gli aggiornamenti dei dispositivi gemelli avviati dal back-end non riusciti.|Nessuna dimensione|
|c2d<br>.twin<br>.update<br>.size|Dimensioni degli aggiornamenti dei dispositivi gemelli dal back-end|Byte|Media|Dimensioni medie, minime e massime degli aggiornamenti dei dispositivi gemelli avviati dal back-end completati.|Nessuna dimensione|
|twinQueries<br>.success|Query dei dispositivi gemelli completate|Conteggio|Totale|Numero di tutte le query dei dispositivi gemelli completate.|Nessuna dimensione|
|twinQueries<br>.failure|Query dei dispositivi gemelli non riuscite|Conteggio|Totale|Numero di tutte le query dei dispositivi gemelli non riuscite.|Nessuna dimensione|
|twinQueries<br>.resultSize|Dimensioni dei risultati delle query dei dispositivi gemelli|Byte|Media|Dimensioni medie, minime e massime dei risultati delle query dei dispositivi gemelli.|Nessuna dimensione|
|jobs<br>.createTwinUpdateJob<br>.success|Creazioni di processi di aggiornamento dei dispositivi gemelli completate|Conteggio|Totale|Numero di tutte le creazioni di processi di aggiornamento dei dispositivi gemelli completate.|Nessuna dimensione|
|jobs<br>.createTwinUpdateJob<br>.failure|Creazioni di processi di aggiornamento dei dispositivi gemelli non riuscite|Conteggio|Totale|Numero di tutte le creazioni di processi di aggiornamento dei dispositivi gemelli non riuscite.|Nessuna dimensione|
|jobs<br>.createDirectMethodJob<br>.success|Creazioni di processi di chiamata al metodo completate|Conteggio|Totale|Numero di tutte le creazioni di processi di chiamata a metodi diretti completate.|Nessuna dimensione|
|jobs<br>.createDirectMethodJob<br>.failure|Creazioni di processi di chiamata al metodo non riuscite|Conteggio|Totale|Numero di tutte le creazioni di processi di chiamata a metodi diretti non riuscite.|Nessuna dimensione|
|jobs<br>.listJobs<br>.success|Chiamate per elencare i processi riuscite|Conteggio|Totale|Numero di tutte le chiamate per elencare i processi riuscite.|Nessuna dimensione|
|jobs<br>.listJobs<br>.failure|Chiamate per elencare i processi non riuscite|Conteggio|Totale|Numero di tutte le chiamate per elencare i processi non riuscite.|Nessuna dimensione|
|jobs<br>.cancelJob<br>.success|Annullamenti di processi riusciti|Conteggio|Totale|Numero di tutte le chiamate per annullare i processi riuscite.|Nessuna dimensione|
|jobs<br>.cancelJob<br>.failure|Annullamenti di processi non riusciti|Conteggio|Totale|Numero di tutte le chiamate per annullare i processi non riuscite.|Nessuna dimensione|
|jobs<br>.queryJobs<br>.success|Query sui processi riuscite|Conteggio|Totale|Numero di tutte le chiamate per eseguire query sui processi riuscite.|Nessuna dimensione|
|jobs<br>.queryJobs<br>.failure|Query sui processi non riuscite|Conteggio|Totale|Numero di tutte le chiamate per eseguire query sui processi non riuscite.|Nessuna dimensione|
|jobs<br>.completed|Processi completati|Conteggio|Totale|Numero di tutti i processi completati.|Nessuna dimensione|
|jobs<br>.failed|Processi non riusciti|Conteggio|Totale|Numero di tutti i processi non riusciti.|Nessuna dimensione|
|d2c<br>.telemetry<br>.ingress<br>.sendThrottle|Number of throttling errors (Numero di errori di limitazione)|Conteggio|Totale|Numero di errori di limitazione dovuti alle limitazioni della velocità effettiva del dispositivo|Nessuna dimensione|
|dailyMessage<br>QuotaUsed|Total number of messages used (Numero totale di messaggi usati)|Conteggio|Media|Numero totale di messaggi usati nella data odierna. Si tratta di un valore cumulativo che viene reimpostato su zero alle 00:00 UTC ogni giorno.|Nessuna dimensione|
|deviceDataUsage|Uso totale dati dispositivi (deprecato)|Byte|Totale|Byte trasferiti a e da qualsiasi dispositivo connesso all'hub IoT|Nessuna dimensione|
|deviceDataUsageV2|Uso totale dei dati del dispositivo (anteprima)|Byte|Totale|Byte trasferiti a e da qualsiasi dispositivo connesso all'hub IoT|Nessuna dimensione|
|totalDeviceCount|Dispositivi totali (anteprima)|Conteggio|Media|Il numero di dispositivi registrati nell'hub IoT|Nessuna dimensione|
|connected<br>DeviceCount|Dispositivi connessi (anteprima)|Conteggio|Media|Il numero di dispositivi connessi all'hub IoT|Nessuna dimensione|
|configurazioni|Metriche di configurazione|Conteggio|Totale|Metriche per le operazioni di configurazione|Nessuna dimensione|

## <a name="next-steps"></a>Passaggi successivi

Al termine di questa panoramica delle metriche dell'hub IoT è possibile usare i collegamenti seguenti per maggiori informazioni sulla gestione dell'hub IoT di Azure:

* [Monitoraggio delle operazioni](iot-hub-operations-monitoring.md)

Per altre informazioni sulle funzionalità dell'hub IoT, vedere:

* [Guida per sviluppatori dell'hub IoT](iot-hub-devguide.md)

* [Distribuzione dell'intelligenza artificiale in dispositivi perimetrali con Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
