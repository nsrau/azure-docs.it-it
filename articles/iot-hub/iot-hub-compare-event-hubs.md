<properties
 pageTitle="Confrontare Azure Hub IoT e gli Hub di eventi di Azure | Microsoft Azure"
 description="Un confronto tra i servizi Azure IoT Hub e gli Hub di eventi di Azure evidenzia delle differenze funzionali e dei casi di utilizzo."
 services="iot-hub"
 documentationCenter=""
 authors="fsautomata"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="06/06/2016"
 ms.author="elioda"/>

# Confronto tra l'hub IoT e Hub eventi

Uno degli usi principali dell'hub IoT di Azure è la raccolta di dati di telemetria dai dispositivi. Per questo motivo, l'hub IoT viene spesso paragonato all'[Hub eventi di Azure][]. Come l'hub IoT, Hub eventi è un servizio di elaborazione eventi che consente l'ingresso di eventi e telemetria nel cloud su vasta scala, con bassa latenza e affidabilità elevata.

I servizi presentano tuttavia molte differenze, illustrate in dettaglio nella tabella seguente.

| Area | Hub IoT | Hub eventi |
| ---- | ------- | ---------- |
| Modelli di comunicazione | Abilita la messaggistica da dispositivo a cloud e da cloud a dispositivo. | Consente solo l'ingresso di eventi, considerato di solito per scenari da dispositivo a cloud. |
| Supporto dei protocolli del dispositivo | Supporta AMQP, AMQP su WebSocket, MQTT e HTTP/1. L'hub IoT funziona anche con il [Gateway del protocollo Azure IoT][lnk-azure-protocol-gateway], un'implementazione di gateway del protocollo personalizzabile per supportare i protocolli personalizzati. | Supporta AMQP, AMQP su WebSocket e HTTP/1. |
| Sicurezza | Garantisce l’identità per dispositivo e il controllo di accesso revocabile. Vedere la [sezione protezione della Guida per gli sviluppatori di hub IoT]. | Garantisce [criteri di accesso condivisi][Event Hub - security] a livello di Hub eventi, con supporto limitato per la revoca tramite [criteri dell'entità di pubblicazione][Event Hub publisher policies]. Per le soluzioni IoT è spesso necessario implementare una soluzione personalizzata per supportare le credenziali per dispositivo e le misure antispoofing. |
| Monitoraggio delle operazioni | Consente alle soluzioni IoT di sottoscrivere un'ampia gamma di eventi di gestione delle identità dei dispositivi e di connettività, ad esempio errori di autenticazione di singoli dispositivi, limitazione ed eccezioni con un formato non valido. Questi eventi consentono di identificare rapidamente i problemi di connettività a livello di singolo dispositivo. | Espone solo le metriche aggregate. |
| Scalabilità | È ottimizzato per supportare milioni di dispositivi connessi simultaneamente. | Può supportare un numero più limitato di connessioni simultanee: fino a 5000 connessioni AMQP, in base a quanto descritto in [Quote del bus di servizio Azure][]. D'altra parte, Hub eventi consente di specificare la partizione per ogni messaggio inviato. |
| SDK del dispositivo | Fornisce l’[SDK del dispositivo][Azure IoT Hub SDKs] per un'ampia gamma di piattaforme e linguaggi. | È supportato in .NET, C e fornisce interfacce di invio AMQP e HTTP. |
| Caricamento di file | Consente alle soluzioni IoT di caricare file dai dispositivi al cloud. Include un endpoint di notifica di file per l'integrazione del flusso di lavoro e una categoria di monitoraggio delle operazioni per il supporto del debug. | Utilizza un modello claim check per richiedere manualmente i file ai dispositivi e fornire dispositivi con chiave di archiviazione per la transazione. |

In breve, anche se il solo caso di utilizzo è l'ingresso dei dati di telemetria da dispositivo a cloud, l'hub IoT fornisce un servizio progettato espressamente per la connettività dei dispositivi IoT. Esso continuerà a espandere le proposte di valore per questi scenari con funzionalità specifiche per IoT. Hub eventi è progettato per l'ingresso di eventi su vasta scala, sia nel contesto di scenari all'interno e tra i data center.

Non è insolito usare sia l'hub IoT che Hub eventi nella stessa soluzione, lasciando che il primo gestisca la comunicazione da dispositivo a cloud e che il secondo gestisca la fase successiva di ingresso degli eventi nei motori di elaborazione in tempo reale.

## Passaggi successivi

Per altre informazioni sull'hub IoT di Azure, vedere questi collegamenti:

- [Introduzione all'hub IoT di Azure (esercitazione)][lnk-get-started]
- [Che cos'è l’hub IoT Azure?][]

[Hub eventi di Azure]: ../event-hubs/event-hubs-what-is-event-hubs.md
[sezione protezione della Guida per gli sviluppatori di hub IoT]: iot-hub-devguide.md#security
[Event Hub - security]: ../event-hubs/event-hubs-authentication-and-security-model-overview.md
[Event Hub publisher policies]: ../event-hubs/event-hubs-overview.md#common-publisher-tasks
[Quote del bus di servizio Azure]: ../service-bus/service-bus-quotas.md
[Azure IoT Hub SDKs]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[Che cos'è l’hub IoT Azure?]: iot-hub-what-is-iot-hub.md
[lnk-azure-protocol-gateway]: iot-hub-protocol-gateway.md

<!---HONumber=AcomDC_0615_2016-->