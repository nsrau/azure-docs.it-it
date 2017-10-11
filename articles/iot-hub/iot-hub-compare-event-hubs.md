---
title: Confrontare l'Hub IoT di Azure e gli Hub eventi di Azure | Documentazione Microsoft
description: Un confronto tra i servizi di Azure Hub IoT e Hub eventi evidenzia le differenze funzionali e i casi d'uso. Il confronto include protocolli supportati, gestione dei dispositivi, monitoraggio e caricamenti di file.
services: iot-hub
documentationcenter: 
author: fsautomata
manager: timlt
editor: 
ms.assetid: aeddea62-8302-48e2-9aad-c5a0e5f5abe9
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: elioda
ms.openlocfilehash: a37df79a38a35b61cca72918d4d893a4bfc83b7e
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2017
---
# <a name="comparison-of-azure-iot-hub-and-azure-event-hubs"></a>Confronto tra l'hub IoT e Hub eventi di Azure
Uno degli usi principali dell'hub IoT è la raccolta di dati di telemetria dai dispositivi. Per questo motivo, l'hub IoT viene spesso paragonato all'[Hub eventi di Azure][Azure Event Hubs]. Come l'hub IoT, Hub eventi è un servizio di elaborazione eventi che consente l'ingresso di eventi e telemetria nel cloud su vasta scala, con bassa latenza e affidabilità elevata.

I servizi presentano tuttavia molte differenze, illustrate in dettaglio nella tabella seguente:

| Area | Hub IoT | Hub eventi |
| --- | --- | --- |
| Modelli di comunicazione | Abilita le [comunicazioni da dispositivo a cloud][lnk-d2c-guidance] (messaggistica, caricamenti di file e proprietà segnalate) e le [comunicazioni da cloud a dispositivo][lnk-c2d-guidance] (metodi diretti, proprietà desiderate, messaggistica). |Consente solo l'ingresso di eventi, considerato di solito per scenari da dispositivo a cloud. |
| Informazioni sullo stato dei dispositivi | I [dispositivi gemelli][lnk-twins] possono archiviare le informazioni sullo stato dei dispositivi ed eseguire query su tali informazioni. | Nessuna informazioni sullo stato dei dispositivi può essere archiviata. |
| Supporto dei protocolli del dispositivo |Supporta MQTT, MQTT su WebSockets, AMQP, AMQP su WebSockets e HTTP. L'hub IoT funziona anche con il [Gateway del protocollo di IoT di Azure][lnk-azure-protocol-gateway], un'implementazione di gateway del protocollo personalizzabile per supportare i protocolli personalizzati. |Supporta AMQP, AMQP su WebSocket e HTTP. |
| Sicurezza |Garantisce l’identità per dispositivo e il controllo di accesso revocabile. Vedere la [sezione protezione della Guida per gli sviluppatori di hub IoT]. |Garantisce [criteri di accesso condivisi][Event Hubs - security] a livello di Hub eventi, con supporto limitato per la revoca tramite [criteri dell'entità di pubblicazione][Event Hubs publisher policies]. Per le soluzioni IoT è spesso necessario implementare una soluzione personalizzata per supportare le credenziali per dispositivo e le misure antispoofing. |
| Monitoraggio delle operazioni |Consente alle soluzioni IoT di sottoscrivere un'ampia gamma di eventi di gestione delle identità dei dispositivi e di connettività, ad esempio errori di autenticazione di singoli dispositivi, limitazione ed eccezioni con un formato non valido. Questi eventi consentono di identificare rapidamente i problemi di connettività a livello di singolo dispositivo. |Espone solo le metriche aggregate. |
| Scalabilità |È ottimizzato per supportare milioni di dispositivi connessi simultaneamente. |Contatori per le connessioni in base alle [quote di Hub eventi di Azure][Azure Event Hubs quotas]. D'altra parte, Hub eventi consente di specificare la partizione per ogni messaggio inviato. |
| SDK del dispositivo |Fornisce [SDK di dispositivi][Azure IoT SDKs] per una vasta gamma di piattaforme e linguaggi, oltre a indirizzare le API MQTT, AMQP e HTTP. |È supportato su .NET, Java e C, oltre alle interfacce di invio AMQP e HTTP. |
| Caricamento di file |Consente alle soluzioni IoT di caricare file dai dispositivi al cloud. Include un endpoint di notifica di file per l'integrazione del flusso di lavoro e una categoria di monitoraggio delle operazioni per il supporto del debug. | Non supportati. |
| Inviare messaggi a più endpoint | Sono supportati fino a 10 endpoint personalizzati. Le regole determinano il modo in cui i messaggi vengono indirizzati agli endpoint personalizzati. Per altre informazioni, vedere [Inviare e ricevere messaggi con l'hub IoT][lnk-devguide-messaging]. | È necessario scrivere e ospitare codice aggiuntivo per l'invio del messaggio. |

In breve, anche se il solo caso di utilizzo è l'ingresso dei dati di telemetria da dispositivo a cloud, l'hub IoT fornisce un servizio progettato per la connettività dei dispositivi IoT. Esso continua a espandere le proposte di valore per questi scenari con funzionalità specifiche per IoT. Hub eventi è progettato per l'ingresso di eventi su vasta scala, sia nel contesto di scenari all'interno e tra i data center.

Non è insolito usare l'hub IoT e gli hub eventi nella stessa soluzione. L'hub IoT gestisce la comunicazione da dispositivo a cloud e gli hub eventi gestiscono la fase successiva di ingresso degli eventi nei motori di elaborazione in tempo reale.

### <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulla pianificazione della distribuzione dell'hub IoT, vedere [Ridimensionamento, disponibilità elevata e ripristino di emergenza][lnk-scaling].

Per altre informazioni sulle funzionalità dell'hub IoT, vedere:

* [Guida per gli sviluppatori dell'hub IoT][lnk-devguide]
* [Simulazione di un dispositivo con IoT Edge][lnk-iotedge]

[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md

[Azure Event Hubs]: ../event-hubs/event-hubs-what-is-event-hubs.md
[sezione protezione della Guida per gli sviluppatori di hub IoT]: iot-hub-devguide-security.md
[Event Hubs - security]: ../event-hubs/event-hubs-authentication-and-security-model-overview.md
[Event Hubs publisher policies]: ../event-hubs/event-hubs-features.md#event-publishers
[Azure Event Hubs quotas]: ../event-hubs/event-hubs-quotas.md
[Azure IoT SDKs]: https://github.com/Azure/azure-iot-sdks
[lnk-azure-protocol-gateway]: iot-hub-protocol-gateway.md

[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: iot-hub-linux-iot-edge-simulated-device.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
