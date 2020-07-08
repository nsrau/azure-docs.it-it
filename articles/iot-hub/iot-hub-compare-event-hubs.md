---
title: Confrontare l'Hub IoT di Azure e gli Hub eventi di Azure | Documentazione Microsoft
description: Un confronto tra i servizi di Azure Hub IoT e Hub eventi evidenzia le differenze funzionali e i casi d'uso. Il confronto include protocolli supportati, gestione dei dispositivi, monitoraggio e caricamenti di file.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: kgremban
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 7aa59f8181ad60b3d43846a3f4f1f471a050b238
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "81733432"
---
# <a name="connecting-iot-devices-to-azure-iot-hub-and-event-hubs"></a>Connessione di dispositivi IoT di Azure: hub IoT e hub eventi

Azure offre servizi sviluppati in modo specifico per tipi diversi di connettività e comunicazione che consentono di connettere i dati alla potenza del cloud. L'hub IoT di Azure e Hub eventi di Azure sono entrambi servizi cloud che supportano l'inserimento di grandi quantità di dati e sono in grado di elaborare o archiviare tali dati per generare informazioni aziendali dettagliate. I due servizi sono simili in quanto entrambi supportano l'inserimento di dati con bassa latenza e affidabilità elevata, ma sono progettati per scopi diversi. L'hub Internet delle cose è stato sviluppato per soddisfare i requisiti univoci della connessione dei dispositivi Internet al cloud di Azure, mentre gli hub eventi sono stati progettati per Big Data flusso. Microsoft consiglia di usare l'hub Azure Internet per connettere i dispositivi Internet delle cose ad Azure

Hub Azure è il gateway cloud che connette i dispositivi Internet per raccogliere dati e ottenere informazioni aziendali e automazione. L'hub IoT include anche funzionalità che arricchiscono la relazione tra i dispositivi e i sistemi back-end. Le funzionalità di comunicazione bidirezionale comportano che, mentre si ricevono dati dai dispositivi, è anche possibile inviare comandi e criteri ai dispositivi. Ad esempio, usare la messaggistica da cloud a dispositivo per aggiornare le proprietà o richiamare le azioni di gestione dei dispositivi. La comunicazione da cloud a dispositivo consente anche di inviare Intelligence per il cloud ai dispositivi perimetrali con Azure IoT Edge. L'identità univoca a livello di dispositivo fornita dall'hub IoT consente di proteggere meglio la soluzione IoT da potenziali attacchi. 

[Hub eventi di Azure](../event-hubs/event-hubs-what-is-event-hubs.md) è il servizio di Azure per lo streaming di Big Data. È progettato per scenari basati sul flusso di dati a una velocità effettiva elevata in cui i clienti possono inviare miliardi di richieste al giorno. Hub eventi usa un modello consumer partizionato per scalare orizzontalmente il flusso ed è integrato nei servizi Big Data e analisi di Azure, inclusi Databricks, Analisi di flusso, ADLS e HDInsight. Con funzionalità quali l'acquisizione di hub eventi e l'aumento automatico, questo servizio è progettato per supportare applicazioni e soluzioni per Big Data. Inoltre, l'hub Internet usa hub eventi per il percorso del flusso di dati di telemetria, quindi la soluzione Internet delle cose si avvantaggia anche della grande potenza degli hub eventi.

Per riepilogare, entrambe le soluzioni sono progettate per l'inserimento di dati su vasta scala. Solo l'hub Internet delle cose fornisce le funzionalità specifiche per le cose che sono progettate per massimizzare il valore aziendale di connessione dei dispositivi Internet al cloud di Azure.  In una fase iniziale, iniziare con Hub IoT per supportare gli scenari di inserimento dati garantisce di avere accesso immediato alle funzionalità IoT complete laddove le esigenze aziendali e tecniche lo richiedano.

La tabella seguente mette a confronto i due livelli dell'hub IoT con Hub eventi per facilitare la valutazione di questi servizi in termini di funzionalità IoT. Per altre informazioni sui livelli Standard e Basic dell'hub IoT, vedere [Come scegliere il livello corretto dell'hub IoT](iot-hub-scaling.md).

| Funzionalità IoT | Hub IoT (livello Standard) | Hub IoT (livello Basic) | Hub eventi |
| --- | --- | --- | --- |
| Messaggistica da dispositivo a cloud | ![Controllo][checkmark] | ![Controllo][checkmark] | ![Controllo][checkmark] |
| Protocolli: HTTPS, AMQP, AMQP su WebSocket | ![Controllo][checkmark] | ![Controllo][checkmark] | ![Controllo][checkmark] |
| Protocolli: MQTT, MQTT su WebSocket | ![Controllo][checkmark] | ![Controllo][checkmark] |  |
| Identità per dispositivo | ![Controllo][checkmark] | ![Controllo][checkmark] |  |
| Caricamento di file dai dispositivi | ![Controllo][checkmark] | ![Controllo][checkmark] |  |
| Servizio di provisioning di dispositivi | ![Controllo][checkmark] | ![Controllo][checkmark] |  |
| Messaggistica da cloud a dispositivo | ![Controllo][checkmark] |  |  |
| Gestione di dispositivi e di dispositivi gemelli | ![Controllo][checkmark] |  |  |
| Flussi del dispositivo (anteprima) | ![Controllo][checkmark] |  |  |
| IoT Edge | ![Controllo][checkmark] |  |  |

Anche se l'unico caso d'uso è l'inserimento dei dati da dispositivo a cloud, è consigliabile usare l'hub IoT poiché fornisce un servizio progettato per la connettività dei dispositivi IoT. 

### <a name="next-steps"></a>Passaggi successivi

Per informazioni più dettagliate sulle funzionalità dell'hub IoT, vedere [Guida per gli sviluppatori dell'hub IoT](iot-hub-devguide.md).

<!-- This one reference link is used over and over. --robinsh -->
[checkmark]: ./media/iot-hub-compare-event-hubs/ic195031.png
