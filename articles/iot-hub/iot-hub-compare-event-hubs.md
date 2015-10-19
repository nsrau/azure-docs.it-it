<properties
 pageTitle="Confrontare Azure Hub IoT e gli Hub di eventi | Microsoft Azure"
 description="Un confronto tra Hub IoT e Hub eventi"
 services="iot-hub"
 documentationCenter=".net"
 authors="fsautomata"
 manager="kevinmil"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="tbd"
 ms.date="10/02/2015"
 ms.author="elioda"/>

# Confronto di Hub IoT e Hub eventi

Uno degli usi principali dell'hub IoT di Azure è la raccolta di dati di telemetria dai dispositivi. Per questo motivo l'hub IoT viene spesso confrontato con [Hub eventi][], un servizio per l'elaborazione di eventi che fornisce l'ingresso di eventi e dati di telemetria nel cloud su vasta scala, con bassa latenza e affidabilità elevata.

I servizi presentano tuttavia molte differenze, illustrate in dettaglio nelle sezioni seguenti.

| Area | Hub IoT | Hub eventi |
| ---- | ------- | ---------- |
| Modelli di comunicazione | Ingresso di eventi da dispositivo a cloud e messaggistica da cloud a dispositivo. | Solo ingresso di eventi, considerato di solito per scenari da dispositivo a cloud. |
| Sicurezza | Identità per dispositivo e controllo di accesso revocabile. Vedere la [Guida per gli sviluppatori dell'hub IoT - Sicurezza]. | [Criteri di accesso condivisi][Event Hub - security] a livello di Hub eventi, con supporto limitato per la revoca tramite [criteri dell'entità di pubblicazione][Event Hub publisher policies]. Nel contesto delle soluzioni IoT, è spesso necessario implementare una soluzione personalizzata per supportare le credenziali per dispositivo e le misure antispoofing. |
| Scale | L'hub IoT è ottimizzato per supportare milioni di dispositivi connessi simultaneamente. | Hub eventi può supportare un numero più limitato di connessioni simultanee: al massimo 5.000 connessioni AMQP, in base al valore di [Quote del bus di servizio][]. D'altra parte, Hub eventi consente di specificare la partizione per ogni messaggio inviato. |
| SDK del dispositivo | L'hub IoT fornisce [SDK del dispositivo][Azure IoT Hub SDKs] per un'ampia gamma di piattaforme e linguaggi | Hub eventi è supportato in .NET, C e fornisce interfacce di invio AMQP e HTTP. |

In breve, anche se il solo caso di utilizzo è l'ingresso dei dati di telemetria da dispositivo a cloud, l'hub IoT fornisce un servizio progettato espressamente per la connettività dei dispositivi IoT e continuerà a espandere le proposte di valore per questi scenari con funzionalità specifiche per IoT. Hub eventi è progettato per l'ingresso di eventi su vasta scala, sia nel contesto di scenari all'interno e tra data center.

Non è insolito usare l'hub IoT e Hub eventi insieme, lasciando che il primo gestisca la comunicazione da dispositivo a cloud e il secondo la fase successiva di ingresso degli eventi nei motori di elaborazione in tempo reale.

## Passaggi successivi

Per altre informazioni sull'hub IoT di Azure, vedere questi collegamenti:

- [Introduzione agli hub IoT (esercitazione)][lnk-get-started]
- [Che cos'è l’hub IoT Azure?][]

[Hub eventi]: ../event-hubs/event-hubs-what-is-event-hubs.md
[Guida per gli sviluppatori dell'hub IoT - Sicurezza]: iot-hub-devguide.md#security
[Event Hub - security]: ../event-hubs/event-hubs-authentication-and-security-model-overview.md
[Event Hub publisher policies]: ../event-hubs/event-hubs-overview.md#common-publisher-tasks
[Quote del bus di servizio]: ../service-bus/service-bus-quotas.md
[Azure IoT Hub SDKs]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[Che cos'è l’hub IoT Azure?]: iot-hub-what-is-iot-hub.md

<!---HONumber=Oct15_HO2-->