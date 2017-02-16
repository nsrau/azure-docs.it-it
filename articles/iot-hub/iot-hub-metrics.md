---
title: Metriche dell&quot;hub IoT di Azure | Documentazione Microsoft
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
ms.sourcegitcommit: a243e4f64b6cd0bf7b0776e938150a352d424ad1
ms.openlocfilehash: 689e3a07fd9bdd82d8c57cbe714562a780a14714


---
# <a name="iot-hub-metrics"></a>Metriche di Hub IoT
Le metriche dell'hub IoT offrono dati migliori sullo stato delle risorse di Azure IoT nella sottoscrizione di Azure. La metrica dell'hub IoT consente di valutare l'integrità complessiva del servizio e dei dispositivi connessi. Le statistiche per l'utente sono importanti perché consentono di vedere le attività in corso nell'hub IoT e di individuare la causa radice dei problemi senza dover contattare il supporto di Azure.

È possibile abilitare le metriche dell'hub IoT nel portale di Azure.

## <a name="how-to-enable-iot-hub-metrics"></a>Come abilitare le metriche dell'hub IoT
1. Creare un hub IoT. Le istruzioni sulla creazione di un hub IoT sono disponibili nella [Guida introduttiva][lnk-get-started].
2. Aprire il pannello dell'hub IoT. Da qui, fare clic su **Diagnostica**.
   
    ![][1]
3. Configurare la diagnostica impostando lo stato su **Sì** e selezionando un account di Archiviazione di Azure per archiviare i dati di diagnostica. Selezionare **Metriche**, quindi premere **Salva**. Si noti che l'account di Archiviazione di Azure deve essere creato in anticipo e che il costo di archiviazione viene addebitato separatamente. È anche possibile inviare dati di diagnostica a un endpoint dell'hub eventi.
   
    ![][2]
4. Dopo avere impostato la diagnostica, tornare al pannello **Panoramica** dell'hub IoT. Le informazioni relative alla metrica vengono popolate nella sezione **Monitoraggio** del pannello. Facendo clic sul grafico viene aperto il riquadro Metriche dove è possibile visualizzare un riepilogo delle informazioni corrispondenti per l'hub IoT. È possibile modificare la selezione delle metriche visualizzate nel grafico e configurare gli avvisi in base ai valori delle metriche.
   
    ![][3]

## <a name="iot-hub-metrics-and-how-to-use-them"></a>Metriche dell'hub IoT e modalità d'uso
L'hub IoT offre diverse metriche per fornire una panoramica dell'integrità dell'hub e il numero totale di dispositivi connessi. È possibile combinare le informazioni da più metriche per disegnare un quadro generale dello stato dell'hub IoT. La tabella seguente descrive le metriche che ogni hub IoT rileva e la correlazione di ognuna con lo stato generale dell'hub IoT.

| Metrica | Descrizione della metrica | Uso previsto per la metrica |
| --- | --- | --- |
| d2c.telemetry.ingress.allProtocol |Numero di messaggi inviati in tutti i dispositivi |Panoramica dei dati sugli invii di messaggi |
| d2c.telemetry.ingress.success |Numero di tutti i messaggi validi nell'hub IoT |Panoramica dei messaggi validi in ingresso nell'hub IoT |
| c2d.commands.egress.complete.success |Numero di tutti i messaggi di comandi completati dal dispositivo ricevente per tutti i dispositivi |Insieme alle metriche relative ad abbandono e rifiuto offre una panoramica della percentuale complessiva di messaggi da cloud a dispositivo riusciti |
| c2d.commands.egress.abandon.success |Numero di tutti i messaggi abbandonati dal dispositivo ricevente per tutti i dispositivi |Evidenzia i potenziali problemi se i messaggi vengono abbandonati più spesso del previsto |
| c2d.commands.egress.reject.success |Numero di tutti i messaggi rifiutati dal dispositivo ricevente per tutti i dispositivi |Evidenzia i potenziali problemi se i messaggi vengono rifiutati più spesso del previsto |
| devices.totalDevices |Numero medio, minimo e massimo di dispositivi registrati nell'hub IoT |Numero di dispositivi registrati nell'hub IoT |
| devices.connectedDevices.allProtocol |Numero medio, minimo e massimo di dispositivi connessi simultaneamente |Panoramica del numero di dispositivi connessi all'hub IoT |

## <a name="next-steps"></a>Passaggi successivi
Al termine di questa panoramica delle metriche dell'hub IoT è possibile usare i collegamenti seguenti per maggiori informazioni sulla gestione dell'hub IoT di Azure:

* [Monitoraggio delle operazioni][lnk-monitor]

Per altre informazioni sulle funzionalità dell'hub IoT, vedere:

* [Guida per sviluppatori dell'hub IoT][lnk-devguide]
* [Simulazione di un dispositivo con IoT Gateway SDK][lnk-gateway]

<!-- Links and images -->
[1]: media/iot-hub-metrics/enable-metrics-1.png
[2]: media/iot-hub-metrics/enable-metrics-2.png
[3]: media/iot-hub-metrics/enable-metrics-3.png

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-operations-monitoring]: iot-hub-operations-monitoring.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-dr]: iot-hub-ha-dr.md

[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md



<!--HONumber=Dec16_HO1-->


