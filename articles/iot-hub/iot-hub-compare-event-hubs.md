---
title: Confrontare l'Hub IoT di Azure e gli Hub eventi di Azure | Documentazione Microsoft
description: Un confronto tra i servizi di Azure Hub IoT e Hub eventi evidenzia le differenze funzionali e i casi d'uso. Il confronto include protocolli supportati, gestione dei dispositivi, monitoraggio e caricamenti di file.
author: kgremban
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: kgremban
ms.openlocfilehash: 20bb0cb6982bcbea6b18989099322cfd3389b0b0
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2019
ms.locfileid: "54819638"
---
# <a name="connecting-iot-devices-to-azure-iot-hub-and-event-hubs"></a>Connessione dei dispositivi IoT ad Azure: Hub IoT e Hub eventi

Azure offre servizi sviluppati in modo specifico per tipi diversi di connettività e comunicazione che consentono di connettere i dati alla potenza del cloud. L'hub IoT di Azure e Hub eventi di Azure sono entrambi servizi cloud che supportano l'inserimento di grandi quantità di dati e sono in grado di elaborare o archiviare tali dati per generare informazioni aziendali dettagliate. I due servizi sono simili in quanto entrambi supportano l'inserimento di dati con bassa latenza e affidabilità elevata, ma sono progettati per scopi diversi. L'hub IoT è stato sviluppato in modo specifico per soddisfare i requisiti specifici di connessione su larga scala di dispositivi IoT al cloud di Azure, mentre l'hub eventi è stato progettato per il flusso dei Big Data. Ecco perché Microsoft consiglia di usare l'hub IoT di Azure per la connessione di dispositivi IoT ad Azure

L'hub IoT di Azure è il gateway cloud che connette i dispositivi IoT per raccogliere i dati e generare informazioni aziendali dettagliate e per l'automazione. L'hub IoT include anche funzionalità che arricchiscono la relazione tra i dispositivi e i sistemi back-end. Con le funzionalità di comunicazione bidirezionale è possibile ricevere dati dai dispositivi e al tempo stesso inviare comandi e criteri ai dispositivi per aggiornare le proprietà o richiamare un'azione di gestione dei dispositivi stessi.  Tale connettività da cloud a dispositivo rafforza anche l'importante funzionalità di rilascio di intelligence cloud per i dispositivi perimetrali con Azure IoT Edge. L'identità univoca a livello di dispositivo fornita dall'hub IoT consente di proteggere meglio la soluzione IoT da potenziali attacchi. 

[Hub eventi di Azure](../event-hubs/event-hubs-what-is-event-hubs.md) è il servizio di Azure per lo streaming di Big Data. È progettato per scenari basati sul flusso di dati a una velocità effettiva elevata in cui i clienti possono inviare miliardi di richieste al giorno. Hub eventi usa un modello consumer partizionato per scalare orizzontalmente il flusso ed è integrato nei servizi Big Data e analisi di Azure, inclusi Databricks, Analisi di flusso, ADLS e HDInsight. Con funzionalità quali l'acquisizione di hub eventi e l'aumento automatico, questo servizio è progettato per supportare applicazioni e soluzioni per Big Data. L'IoT Hub sfrutta anche Hub eventi per il proprio percorso del flusso di dati di telemetria, pertanto la soluzione IoT sfrutta anche la potenza notevole di Hub eventi.

Per riepilogare, anche se entrambe le soluzioni sono progettate per l'inserimento di dati su larga scala, solo Hub IoT offre le ricche funzionalità specifiche per IoT progettate per ottimizzare il valore aziendale di connessione dei dispositivi IoT al cloud di Azure.  In una fase iniziale, iniziare con Hub IoT per supportare gli scenari di inserimento dati garantisce di avere accesso immediato alle funzionalità IoT complete laddove le esigenze aziendali e tecniche lo richiedano.

La tabella seguente mette a confronto i due livelli dell'hub IoT con Hub eventi per facilitare la valutazione di questi servizi in termini di funzionalità IoT. Per altre informazioni sui livelli Standard e Basic dell'hub IoT, vedere [Come scegliere il livello corretto dell'hub IoT](iot-hub-scaling.md).

| Funzionalità IoT | Hub IoT (livello Standard) | Hub IoT (livello Basic) | Hub eventi |
| --- | --- | --- | --- |
| Messaggistica da dispositivo a cloud | ![Controllo][checkmark] | ![Controllo][checkmark] | ![Controllo][checkmark] |
| Protocols: HTTPS, AMQP, AMQP su WebSocket | ![Controllo][checkmark] | ![Controllo][checkmark] | ![Controllo][checkmark] |
| Protocols: MQTT, MQTT su WebSocket | ![Controllo][checkmark] | ![Controllo][checkmark] |  |
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
