---
title: Confrontare l'hub IoT e Hub eventi di Azure | Microsoft Docs
description: Un confronto tra i servizi di Azure Hub IoT e Hub eventi evidenzia le differenze funzionali e i casi d'uso. Il confronto include protocolli supportati, gestione dei dispositivi, monitoraggio e caricamenti di file.
services: iot-hub
documentationcenter: ''
author: kgremban
manager: timlt
editor: ''
ms.assetid: aeddea62-8302-48e2-9aad-c5a0e5f5abe9
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/01/2018
ms.author: kgremban
ms.openlocfilehash: 303a2bde0a1e0b25ca6eb145e7b0cd6c91fff351
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2018
---
# <a name="comparison-of-azure-iot-hub-and-azure-event-hubs"></a>Confronto tra l'hub IoT e Hub eventi di Azure

L'hub IoT di Azure e Hub eventi di Azure sono entrambi servizi cloud che supportano l'inserimento di grandi quantità di dati e sono in grado di elaborare o archiviare tali dati per generare informazioni aziendali dettagliate. I due servizi sono simili poiché entrambi supportano l'elaborazione dei dati di eventi e di telemetria con bassa latenza e affidabilità elevata. Tuttavia, solo l'hub IoT è stato sviluppato con le funzionalità specifiche necessarie per supportare scenari di Internet delle cose (IoT, Internet of Things) su larga scala. 

L'hub IoT di Azure è il gateway cloud che connette i dispositivi e raccoglie i dati per generare informazioni aziendali dettagliate e per l'automazione. L'hub IoT facilita il flusso dei dati verso il cloud e la gestione dei dispositivi su larga scala. Un'importante differenza tra l'hub IoT e altri servizi di inserimento dati è rappresentata dalla presenza nell'hub IoT di funzionalità che rafforzano la relazione tra i dispositivi e i sistemi back-end. Con le funzionalità di comunicazione bidirezionale è possibile ricevere dati dai dispositivi e al tempo stesso inviare messaggi di risposta ai dispositivi per aggiornare le proprietà o richiamare un'azione. L'identità a livello di dispositivo consente di proteggere il sistema. L'elaborazione distribuita sposta la logica del servizio cloud sui dispositivi periferici.

[Hub eventi di Azure][Azure Event Hubs] è un servizio per l'inserimento di eventi in grado di elaborare e archiviare grandi quantità di informazioni e dati di telemetria. Hub eventi è stato progettato per l'inserimento di eventi su larga scala, nel contesto di scenari tra più data center o all'interno di uno stesso data center, ma non fornisce le funzionalità IoT avanzate che sono disponibili con l'hub IoT. Per questo motivo non è consigliabile usare Hub eventi per le soluzioni IoT. 

La tabella seguente mette a confronto i due livelli dell'hub IoT con Hub eventi per facilitare la valutazione di questi servizi in termini di funzionalità IoT. Per altre informazioni sui livelli Standard e Basic dell'hub IoT, vedere [Come scegliere il livello corretto dell'hub IoT][lnk-scaling].

| Funzionalità IoT | Hub IoT (livello Standard) | Hub IoT (livello Basic) | Hub eventi |
| --- | --- | --- | --- |
| Messaggistica da dispositivo a cloud | ![Controllo][1] | ![Controllo][1] | ![Controllo][1] |
| Protocolli: HTTPS, AMQP, AMQP su WebSocket | ![Controllo][1] | ![Controllo][1] | ![Controllo][1] |
| Protocolli: MQTT, MQTT su WebSocket | ![Controllo][1] | ![Controllo][1] |  |
| Identità per dispositivo | ![Controllo][1] | ![Controllo][1] |  |
| Caricamento di file dai dispositivi | ![Controllo][1] | ![Controllo][1] |  |
| Servizio di provisioning di dispositivi | ![Controllo][1] | ![Controllo][1] |  |
| Messaggistica da cloud a dispositivo | ![Controllo][1] |  |  |
| Gestione di dispositivi e di dispositivi gemelli | ![Controllo][1] |  |  |
| IoT Edge | ![Controllo][1] |  |  |

Anche se l'unico caso d'uso è l'inserimento dei dati da dispositivo a cloud, è consigliabile usare l'hub IoT poiché fornisce un servizio progettato per la connettività dei dispositivi IoT. 

### <a name="next-steps"></a>Passaggi successivi

Per informazioni più dettagliate sulle funzionalità dell'hub IoT, vedere [Guida per gli sviluppatori dell'hub IoT][lnk-devguide].


[Azure Event Hubs]: ../event-hubs/event-hubs-what-is-event-hubs.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md

<!--Image references-->
[1]: ./media/iot-hub-compare-event-hubs/ic195031.png