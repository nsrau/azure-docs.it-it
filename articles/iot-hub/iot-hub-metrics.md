<properties
 pageTitle="Metrica di diagnostica dell'hub IoT"
 description="Panoramica della metrica dell'hub IoT di Azure che consente agli utenti di valutare l'integrità complessiva delle proprie risorse"
 services="iot-hub"
 documentationCenter=""
 authors="nberdy"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="08/11/2016"
 ms.author="nberdy"/>

# Introduzione alla metrica di diagnostica

La metrica di diagnostica fornisce dati migliori sullo stato delle risorse di Azure nella sottoscrizione. La metrica consente di valutare l'integrità complessiva del servizio e dei dispositivi ad esso connessi. Le statistiche per l'utente sono importanti perché consentono di vedere le attività in corso nell'hub IoT e di individuare la causa radice dei problemi senza dover contattare il supporto di Azure.

È possibile abilitare la metrica di diagnostica dal portale di Azure.

## Come abilitare la metrica di diagnostica

1. Creare un hub IoT. È possibile trovare istruzioni su come creare un hub IoT in [Introduzione all'hub IoT di Azure][lnk-get-started].

2. Aprire il pannello dell'hub IoT. Da qui, fare clic su **Diagnostica**.

    ![][1]

3. Configurare la diagnostica impostando lo stato su **Sì** e selezionando un account di archiviazione per archiviare i dati di diagnostica. Selezionare **Metriche**, quindi premere **Salva**. Si noti che l'account di archiviazione deve essere creato in anticipo e che il costo di archiviazione viene addebitato separatamente. È anche possibile inviare dati di diagnostica a un endpoint dell'hub eventi.

    ![][2]

4. Dopo avere impostato la diagnostica, tornare al pannello **Panoramica** dell'hub IoT. Le informazioni relative alla metrica vengono popolate nella sezione **Monitoraggio** del pannello. Facendo clic sul grafico, viene aperto il riquadro Metrica dove è possibile visualizzare un riepilogo delle informazioni corrispondenti per l'hub IoT e modificare la selezione della metrica visualizzata nel grafico. È anche possibile configurare gli avvisi in base ai valori della metrica.

    ![][3]

## Metrica e modalità d'uso

L'hub IoT fornisce metriche diverse per fornire una panoramica dell'integrità dell'hub e il numero totale di dispositivi ad esso connessi. È possibile combinare le informazioni da più metriche per disegnare un quadro generale dello stato dell'hub IoT. La tabella seguente descrive le metriche che ogni hub IoT rileva e la correlazione di ognuna con lo stato generale dell'hub IoT.

| Metrica | Descrizione della metrica | Uso previsto per la metrica |
| ---- | ---- | ---- |
| d2c.telemetry.ingress.allProtocol | Numero di messaggi inviati in tutti i dispositivi | Panoramica dei dati sugli invii di messaggi |
| d2c.telemetry.ingress.success | Numero di tutti i messaggi validi nell'hub | Panoramica dei messaggi validi in ingresso nell'hub |
| c2d.commands.egress.complete.success | Numero di tutti i messaggi di comandi completati dal dispositivo ricevente per tutti i dispositivi | Insieme alle metriche relative ad abbandono e rifiuto fornisce una panoramica della percentuale complessiva di comandi C2D riusciti |
| c2d.commands.egress.abandon.success | Numero di tutti i messaggi abbandonati dal dispositivo ricevente per tutti i dispositivi | Evidenzia i potenziali problemi se i messaggi vengono abbandonati più spesso del previsto |
| c2d.commands.egress.reject.success | Numero di tutti i messaggi rifiutati dal dispositivo ricevente per tutti i dispositivi | Evidenzia i potenziali problemi se i messaggi vengono rifiutati più spesso del previsto |
| devices.totalDevices | Numero medio, minimo e massimo di dispositivi registrati nell'hub IoT | Numero di dispositivi registrati nell'hub |
| devices.connectedDevices.allProtocol | Numero medio, minimo e massimo di dispositivi connessi simultaneamente | Panoramica del numero di dispositivi connessi all'hub |

## Passaggi successivi

Al termine di questa panoramica della metrica di diagnostica è possibile usare i collegamenti seguenti per saperne di più sulla gestione dell'hub IoT di Azure:

- [Monitoraggio delle operazioni][lnk-monitor]
- [Gestire l'accesso all'hub IoT][lnk-itpro]

Per esplorare ulteriormente le funzionalità dell'hub IoT, vedere:

- [Progettare una soluzione][lnk-design]
- [Guida per sviluppatori][lnk-devguide]
- [Informazioni sulla gestione dei dispositivi tramite l'interfaccia utente di esempio][lnk-dmui]
- [Simulazione di un dispositivo con Gateway SDK][lnk-gateway]

<!-- Links and images -->
[1]: media/iot-hub-metrics/enable-metrics-1.png
[2]: media/iot-hub-metrics/enable-metrics-2.png
[3]: media/iot-hub-metrics/enable-metrics-3.png

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-operations-monitoring]: iot-hub-operations-monitoring.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-dr]: iot-hub-ha-dr.md

[lnk-monitor]: iot-hub-operations-monitoring.md
[lnk-itpro]: iot-hub-itpro-info.md

[lnk-design]: iot-hub-guidance.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-dmui]: iot-hub-device-management-ui-sample.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md

<!---HONumber=AcomDC_0817_2016-->