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
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733432"
---
# <a name="connecting-iot-devices-to-azure-iot-hub-and-event-hubs"></a>Connessione di dispositivi IoT di Azure: hub IoT e hub eventi

Azure offre servizi sviluppati in modo specifico per tipi diversi di connettività e comunicazione che consentono di connettere i dati alla potenza del cloud. L'hub IoT di Azure e Hub eventi di Azure sono entrambi servizi cloud che supportano l'inserimento di grandi quantità di dati e sono in grado di elaborare o archiviare tali dati per generare informazioni aziendali dettagliate. I due servizi sono simili in quanto entrambi supportano l'inserimento di dati con bassa latenza e affidabilità elevata, ma sono progettati per scopi diversi. L'hub IoT è stato sviluppato per soddisfare i requisiti univoci di connessione dei dispositivi IoT al cloud di Azure, mentre Hub eventi è stato progettato per lo streaming di Big Data.IoT Hub was developed to address the unique requirements of connecting IoT devices to the Azure cloud while Event Hubs was designed for big data streaming. Microsoft recommends using Azure IoT Hub to connect IoT devices to Azure

Hub IoT di Azure è il gateway cloud che connette i dispositivi IoT per raccogliere dati e ottenere informazioni aziendali e automazione. L'hub IoT include anche funzionalità che arricchiscono la relazione tra i dispositivi e i sistemi back-end. Le funzionalità di comunicazione bidirezionale implicano che, mentre si ricevono dati dai dispositivi, è anche possibile inviare comandi e criteri ai dispositivi. Ad esempio, usare la messaggistica da cloud a dispositivo per aggiornare le proprietà o richiamare azioni di gestione dei dispositivi. La comunicazione tra cloud e dispositivo consente inoltre di inviare cloud intelligence ai dispositivi perimetrali con Azure IoT Edge.Cloud-to-device communication also enables you to send cloud intelligence to your edge devices with Azure IoT Edge. L'identità univoca a livello di dispositivo fornita dall'hub IoT consente di proteggere meglio la soluzione IoT da potenziali attacchi. 

[Hub eventi di Azure](../event-hubs/event-hubs-what-is-event-hubs.md) è il servizio di Azure per lo streaming di Big Data. È progettato per scenari basati sul flusso di dati a una velocità effettiva elevata in cui i clienti possono inviare miliardi di richieste al giorno. Hub eventi usa un modello consumer partizionato per scalare orizzontalmente il flusso ed è integrato nei servizi Big Data e analisi di Azure, inclusi Databricks, Analisi di flusso, ADLS e HDInsight. Con funzionalità quali l'acquisizione di hub eventi e l'aumento automatico, questo servizio è progettato per supportare applicazioni e soluzioni per Big Data. Inoltre, l'hub IoT usa gli hub eventi per il percorso del flusso di telemetria, pertanto la soluzione IoT trae vantaggio anche dall'enorme potenza degli hub eventi.

Per riassumere, entrambe le soluzioni sono progettate per l'inserimento di dati su larga scala. Solo l'hub IoT offre le funzionalità specifiche dell'IoT progettate per ottimizzare il valore aziendale della connessione dei dispositivi IoT al cloud di Azure.Only IoT Hub provides the rich IoT-specific capabilities that are designed for you to maximize the business value of connecting your IoT devices to the Azure cloud.  In una fase iniziale, iniziare con Hub IoT per supportare gli scenari di inserimento dati garantisce di avere accesso immediato alle funzionalità IoT complete laddove le esigenze aziendali e tecniche lo richiedano.

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
