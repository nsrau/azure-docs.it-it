---
title: Usare le metriche per monitorare l&quot;hub IoT di Azure | Documentazione Microsoft
description: "Procedura: Usare le metriche dell&quot;hub IoT di Azure per valutare e monitorare l&quot;integrità complessiva degli hub IoT."
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
ms.date: 11/16/2016
ms.author: nberdy
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: e8cac4af4b971320429cc4c76b8d806e314e1143


---
# <a name="iot-hub-metrics"></a>Metriche di Hub IoT
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

| Metrica | Descrizione della metrica | Uso previsto per la metrica |
| --- | --- | --- |
| d2c.telemetry.ingress.allProtocol | Numero di messaggi inviati in tutti i dispositivi | Panoramica dei dati sugli invii di messaggi |
| d2c.telemetry.ingress.success | Numero di tutti i messaggi validi nell'hub | Panoramica dei messaggi validi in ingresso nell'hub |
| d2c.telemetry.egress.success | Il numero di tutte le operazioni di scrittura riuscite in un endpoint | Panoramica del fan-out dei messaggi in base alle route di un utente |
| d2c.telemetry.egress.invalid | Il numero dei messaggi non recapitati per incompatibilità con l'endpoint | Panoramica del numero di errori che si verificano durante la scrittura nel set di endpoint dell'utente. Valori elevati possono indicare endpoint non correttamente configurati. |
| d2c.telemetry.egress.dropped | Il numero di messaggi eliminati per mancata integrità di un endpoint | Panoramica del numero di messaggi eliminati a causa della configurazione corrente dell'hub IoT |
| d2c.telemetry.egress.fallback | Il numero di messaggi corrispondenti alla route di fallback | Per gli utenti che inviano pipe di tutti i messaggi a endpoint diversi da quello predefinito, questa metrica indica i gap nella configurazione del routing |
| d2c.telemetry.egress.orphaned | Il numero di messaggi non corrispondenti ad alcuna route inclusa la route di fallback | Panoramica del numero di messaggi orfani a causa della configurazione corrente dell'hub IoT |
| d2c.endpoints.latency.eventHubs | La latenza media tra l'ingresso del messaggio nell'hub IoT e l'ingresso del messaggio in un endpoint di Hub eventi, in millisecondi | La distribuzione consente agli utenti di identificare una configurazione di endpoint insufficienti |
| d2c.endpoints.latency.serviceBusQueues | La latenza media tra l'ingresso del messaggio nell'hub IoT e l'ingresso del messaggio in un endpoint della coda del bus di servizio, in millisecondi | La distribuzione consente agli utenti di identificare una configurazione di endpoint insufficienti |
| d2c.endpoints.latency.serviceBusTopic | La latenza media tra l'ingresso del messaggio nell'hub IoT e l'ingresso del messaggio in un endpoint dell'argomento del bus di servizio, in millisecondi | La distribuzione consente agli utenti di identificare una configurazione di endpoint insufficienti |
| d2c.endpoints.latency.builtIn.events | La latenza media tra l'ingresso del messaggio nell'hub IoT e l'ingresso del messaggio nell'endpoint predefinito (messaggi/eventi), in millisecondi | La distribuzione consente agli utenti di identificare una configurazione di endpoint insufficienti |
| c2d.commands.egress.complete.success | Numero di tutti i messaggi di comandi completati dal dispositivo ricevente per tutti i dispositivi |Insieme alle metriche relative ad abbandono e rifiuto fornisce una panoramica della percentuale complessiva di comandi da cloud a dispositivo riusciti |
| c2d.commands.egress.abandon.success | Numero di tutti i messaggi abbandonati dal dispositivo ricevente per tutti i dispositivi |Evidenzia i potenziali problemi se i messaggi vengono abbandonati più spesso del previsto |
| c2d.commands.egress.reject.success | Numero di tutti i messaggi rifiutati dal dispositivo ricevente per tutti i dispositivi |Evidenzia i potenziali problemi se i messaggi vengono rifiutati più spesso del previsto |
| devices.totalDevices | Conteggio del numero di dispositivi registrati nell'hub IoT |Numero di dispositivi registrati nell'hub |
| devices.connectedDevices.allProtocol | Conteggio del numero medio, minimo e massimo di dispositivi connessi simultaneamente |Panoramica del numero di dispositivi connessi all'hub |

## <a name="next-steps"></a>Passaggi successivi
Al termine di questa panoramica delle metriche dell'hub IoT è possibile usare i collegamenti seguenti per maggiori informazioni sulla gestione dell'hub IoT di Azure:

* [Monitoraggio delle operazioni][lnk-monitor]

Per altre informazioni sulle funzionalità dell'hub IoT, vedere:

* [Guida per gli sviluppatori dell'hub IoT][lnk-devguide]
* [Simulazione di un dispositivo con IoT Gateway SDK][lnk-gateway]

<!-- Links and images -->
[1]: media/iot-hub-metrics/enable-metrics-1.png
[2]: media/iot-hub-metrics/enable-metrics-2.png

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-operations-monitoring]: iot-hub-operations-monitoring.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-dr]: iot-hub-ha-dr.md

[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md



<!--HONumber=Jan17_HO4-->


