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
ms.openlocfilehash: 7a589ba80b61ea5ef9ea1c941e9a0218a1653c99
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/22/2019
ms.locfileid: "56669739"
---
# <a name="connecting-iot-devices-to-azure-iot-hub-and-event-hubs"></a>Connessione dei dispositivi IoT ad Azure: Hub IoT e Hub eventi

Azure offre servizi sviluppati in modo specifico per tipi diversi di connettività e comunicazione che consentono di connettere i dati alla potenza del cloud. L'hub IoT di Azure e Hub eventi di Azure sono entrambi servizi cloud che supportano l'inserimento di grandi quantità di dati e sono in grado di elaborare o archiviare tali dati per generare informazioni aziendali dettagliate. I due servizi sono simili in quanto entrambi supportano l'inserimento di dati con bassa latenza e affidabilità elevata, ma sono progettati per scopi diversi. L'IoT Hub è stato sviluppato per soddisfare i requisiti univoci di connessione dei dispositivi IoT nel cloud di Azure hub eventi è stato progettato per lo streaming dei big Data. Microsoft consiglia di usare l'IoT Hub di Azure per connettere dispositivi IoT in Azure

IoT Hub di Azure è il gateway cloud che consente di connettere dispositivi IoT per raccogliere dati e ottenere approfondimenti professionali e automazione. L'hub IoT include anche funzionalità che arricchiscono la relazione tra i dispositivi e i sistemi back-end. Le funzionalità di comunicazione bidirezionale significa che anche se si ricevono dati dai dispositivi è anche possibile inviare comandi e i criteri di risposta ai dispositivi. Ad esempio, usare la messaggistica da cloud a dispositivo per aggiornare le proprietà o richiamare le operazioni di gestione. Comunicazioni da cloud a dispositivo consente inoltre di inviare l'intelligence per il cloud ai dispositivi perimetrali con Azure IoT Edge. L'identità univoca a livello di dispositivo fornita dall'hub IoT consente di proteggere meglio la soluzione IoT da potenziali attacchi. 

[Hub eventi di Azure](../event-hubs/event-hubs-what-is-event-hubs.md) è il servizio di Azure per lo streaming di Big Data. È progettato per scenari basati sul flusso di dati a una velocità effettiva elevata in cui i clienti possono inviare miliardi di richieste al giorno. Hub eventi usa un modello consumer partizionato per scalare orizzontalmente il flusso ed è integrato nei servizi Big Data e analisi di Azure, inclusi Databricks, Analisi di flusso, ADLS e HDInsight. Con funzionalità quali l'acquisizione di hub eventi e l'aumento automatico, questo servizio è progettato per supportare applicazioni e soluzioni per Big Data. Inoltre, l'IoT Hub Usa hub eventi per il percorso del flusso di dati di telemetria, in modo che la soluzione IoT anche vantaggi dall'enorme potenza di hub eventi.

Per riepilogare, entrambe le soluzioni sono progettate per l'inserimento di dati su larga scala. Solo l'IoT Hub fornisce le specifiche per IoT funzionalità progettate per ottimizzare il valore di business di connettere i dispositivi IoT nel cloud di Azure.  In una fase iniziale, iniziare con Hub IoT per supportare gli scenari di inserimento dati garantisce di avere accesso immediato alle funzionalità IoT complete laddove le esigenze aziendali e tecniche lo richiedano.

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
