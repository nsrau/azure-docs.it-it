---
title: Soluzioni preconfigurate di Azure IoT | Documentazione Microsoft
description: Descrizione delle soluzioni preconfigurate di Azure IoT e della relativa architettura con collegamenti a risorse aggiuntive.
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 59009f37-9ba0-4e17-a189-7ea354a858a2
ms.service: iot-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2017
ms.author: dobett
ms.openlocfilehash: e0e79cb3b4c71c5d424f3b46af72fcb8b2f63ead
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="what-are-the-azure-iot-suite-preconfigured-solutions"></a>Informazioni sulle soluzioni preconfigurate di Azure IoT Suite

Le soluzioni preconfigurate di Azure IoT Suite sono implementazioni dei modelli di soluzione IoT comuni che è possibile distribuire in Azure usando la propria sottoscrizione. È possibile usare le soluzioni preconfigurate:

* Come punto di partenza per le proprie soluzioni IoT.
* Per informazioni sui modelli comuni nello sviluppo e nella progettazione della soluzione IoT.

Ogni soluzione preconfigurata è un'implementazione end-to-end completa che usa dispositivi simulati per generare dati di telemetria.

È possibile scaricare il codice sorgente completo per personalizzare ed estendere la soluzione per poter soddisfare requisiti IoT specifici.

> [!NOTE]
> Per distribuire una delle soluzioni preconfigurate, visitare [Microsoft Azure IoT Suite][lnk-azureiotsuite]. L'articolo [Esercitazione: Introduzione alle soluzioni preconfigurate][lnk-getstarted-preconfigured] fornisce altre informazioni su come distribuire ed eseguire una delle soluzioni.

La tabella seguente mostra quali funzionalità IoT specifiche sono disponibili per le soluzioni:

| Soluzione | Inserimento di dati | Identità del dispositivo | Gestione dei dispositivi | Comando e controllo | Regole e azioni | Analisi predittiva |
| --- | --- | --- | --- | --- | --- | --- |
| [Monitoraggio remoto][lnk-getstarted-preconfigured] |Sì |Sì |Sì |Sì |Sì |- |
| [Manutenzione predittiva][lnk-predictive-maintenance] |Sì |Sì |- |Sì |Sì |Sì |
| [Connected factory][lnk-getstarted-factory] |Sì |Sì |Sì |Sì |Sì |- |

* *Inserimento di dati*: inserimento di dati su vasta scala nel cloud.
* *Identità del dispositivo*: gestione delle identità univoche del dispositivo e controllo dell'accesso del dispositivo alla soluzione.
* *Gestione dei dispositivi*: gestione dei metadati del dispositivo ed esecuzione delle operazioni quali il riavvio del dispositivo e gli aggiornamenti del firmware.
* *Comando e controllo*: per far eseguire un'azione al dispositivo, inviare messaggi a un dispositivo dal cloud.
* *Regole e azioni*: per agire su specifici dati da dispositivo a cloud, il back-end della soluzione usa le regole.
* *Analisi predittiva*: il back-end della soluzione analizza i dati da dispositivo a cloud per determinare quando eseguire specifiche azioni. Ad esempio, usa l'analisi di dati di telemetria per il motore di un aereo per determinare eseguire la manutenzione.

## <a name="remote-monitoring-preconfigured-solution-overview"></a>Panoramica della soluzione preconfigurata per il monitoraggio remoto

In questo articolo viene descritta la soluzione preconfigurata per il monitoraggio remoto perché illustra molti degli elementi di progettazione comuni condivisi dalle altre soluzioni.

Il diagramma seguente illustra gli elementi chiave della soluzione di monitoraggio remoto. Le sezioni seguenti offrono altre informazioni su questi elementi.

![Architettura della soluzione preconfigurata per il monitoraggio remoto][img-remote-monitoring-arch]

## <a name="devices"></a>Dispositivi

Quando si distribuisce la soluzione preconfigurata per il monitoraggio remoto, nella soluzione viene eseguito il pre-provisioning di quattro dispositivi simulati che simulano un dispositivo di raffreddamento. I dispositivi simulati hanno un modello di umidità e temperatura predefinito che genera i dati di telemetria. Vengono inclusi questi dispositivi simulati per:

- Illustrare il flusso dei dati end-to-end nella soluzione.
- Offrire un'utile origine di dati di telemetria.
- Offrire una destinazione per i metodi o i comandi se si ricopre il ruolo di sviluppatore di back-end usando la soluzione come punto di partenza per un'implementazione personalizzata.

I dispositivi simulati nella soluzione possono rispondere alle comunicazioni da cloud a dispositivo seguenti:

- *Metodi ([metodi diretti][lnk-direct-methods])*: un metodo di comunicazione bidirezionale in cui un dispositivo connesso deve rispondere immediatamente.
- *Comandi (messaggi da cloud a dispositivo)*: un metodo di comunicazione unidirezionale in cui un dispositivo recupera il comando da una coda durevole.

Per un confronto tra questi diversi approcci, vedere [Indicazioni sulle comunicazioni da cloud a dispositivo][lnk-c2d-guidance].

Quando un dispositivo si connette per la prima volta all'hub IoT nella soluzione preconfigurata, invia un messaggio di informazioni sul dispositivo all'hub. Questo messaggio enumera i metodi a cui il dispositivo può rispondere. Nella soluzione preconfigurata di monitoraggio remoto, i dispositivi simulati supportano questi metodi:

* *Avvio dell'aggiornamento del firmware*: questo metodo avvia un'attività asincrona sul dispositivo per eseguire un aggiornamento del firmware. L'attività asincrona usa le proprietà segnalate per distribuire gli aggiornamenti di stato al dashboard della soluzione.
* *Riavvio*: questo metodo consente il riavvio del dispositivo simulato.
* *FactoryReset*: questo metodo attiva un ripristino alle impostazioni predefinite del dispositivo simulato.

Quando un dispositivo si connette per la prima volta all'hub IoT nella soluzione preconfigurata, invia un messaggio di informazioni sul dispositivo all'hub. Questo messaggio enumera i comandi a cui il dispositivo può rispondere. Nella soluzione preconfigurata di monitoraggio remoto, i dispositivi simulati supportano questi comandi:

* *Ping dispositivo*: il dispositivo risponde a questo comando con un acknowledgement. Questo comando è utile per verificare che il dispositivo sia ancora attivo e in ascolto.
* *Avvia telemetria*: indica al dispositivo di iniziare a inviare i dati di telemetria.
* *Arresta telemetria*: indica al dispositivo di arrestare l'invio dei dati di telemetria.
* *Modifica la temperatura del punto dati impostato*: controlla i valori dei dati di telemetria sulla temperatura simulati inviati dal dispositivo. Questo comando è utile per testare la logica di back-end.
* *Telemetria diagnostica*: controlla se il dispositivo deve inviare la temperatura esterna come dato di telemetria.
* *Change Device State* (Modifica stato del dispositivo): imposta la proprietà dei metadati sullo stato segnalata dal dispositivo. Questo comando è utile per testare la logica di back-end.

È possibile aggiungere alla soluzione altri dispositivi simulati che generano gli stessi dati di telemetria e rispondono agli stessi metodi e comandi.

Oltre a rispondere ai comandi e ai metodi, la soluzione usa [dispositivi gemelli][lnk-device-twin]. I dispositivi usano i dispositivi gemelli per segnalare i valori delle proprietà nella soluzione di back-end. Il dashboard della soluzione usa i dispositivi gemelli per impostare i nuovi valori di proprietà desiderati sui dispositivi. Ad esempio, durante il processo di aggiornamento del firmware il dispositivo simulato segnala lo stato dell'aggiornamento usando le proprietà segnalate.

## <a name="iot-hub"></a>Hub IoT

L'istanza dell'hub IoT in questa soluzione preconfigurata corrisponde al *gateway cloud* nell'[architettura di una soluzione IoT][lnk-what-is-azure-iot] tipica.

Un hub IoT riceve i dati di telemetria dai dispositivi in un singolo endpoint. Un hub IoT gestisce anche gli endpoint specifici del dispositivo in cui ogni dispositivo può recuperare i comandi a esso destinati.

L'hub IoT rende disponibili i dati di telemetria ricevuti attraverso l'endpoint di lettura dei dati di telemetria sul lato servizio.

La funzionalità di gestione del dispositivo dell'hub IoT consente di gestire le proprietà del dispositivo dal portale della soluzione e di pianificare i processi che eseguono operazioni come:

- Riavvio dei dispositivi
- Modifica degli stati del dispositivo
- Aggiornamenti del firmware

## <a name="azure-stream-analytics"></a>Analisi di flusso di Azure

La soluzione preconfigurata usa tre processi di [Analisi di flusso di Azure][lnk-asa] per filtrare i flussi di dati di telemetria provenienti dai dispositivi:

* *Processo DeviceInfo*: invia dati a un hub eventi che indirizza i messaggi specifici di registrazione del dispositivo al Registro di sistema del dispositivo della soluzione. Questo Registro di sistema del dispositivo è un database di Azure Cosmos DB. Questi messaggi vengono inviati quando un dispositivo si connette per la prima volta o in risposta a un comando **Change device state** (Modifica lo stato del dispositivo).
* *Processo Telemetria* : invia tutti i dati di telemetria non elaborati all'archivio BLOB di Azure per l'archiviazione offline sicura e calcola le aggregazioni dei dati di telemetria visualizzate nel dashboard della soluzione.
* *Processo Regole* : filtra il flusso dei dati di telemetria in base ai valori che superano qualsiasi soglia delle regole e invia i dati a un hub eventi. Quando viene attivata una regola, la vista del dashboard del portale della soluzione mostra questo evento come una nuova riga nella tabella della cronologia di allarme. Queste regole possono inoltre attivare un'azione in base alle impostazioni definite nelle viste **Regole** e **Azioni** nel portale della soluzione.

In questa soluzione preconfigurata i processi di Analisi di flusso di Azure fanno parte del **back-end della soluzione IoT** nell'[architettura di una soluzione IoT][lnk-what-is-azure-iot] tipica.

## <a name="event-processor"></a>Processore di eventi

In questa soluzione preconfigurata il processore di eventi fa parte del **back-end della soluzione IoT** nell'[architettura di una soluzione IoT][lnk-what-is-azure-iot] tipica.

I processi **DeviceInfo** e **Rules** di Analisi di flusso di Azure inviano l'output agli hub eventi in modo che siano recapitati ad altri servizi back-end. La soluzione usa un'istanza di [EventPocessorHost][lnk-event-processor] in esecuzione in un [processo Web][lnk-web-job] per leggere i messaggi da questi hub eventi. **EventProcessorHost** usa:
- I dati di **DeviceInfo** per aggiornare i dati del dispositivo nel database Cosmos DB.
- I dati delle **regole** per richiamare l'app per la logica e aggiornare gli avvisi visualizzati nel portale della soluzioni.

## <a name="device-identity-registry-device-twin-and-cosmos-db"></a>Registro delle identità dei dispositivi, dispositivo gemello e Cosmos DB

Ogni hub IoT include un [registro delle identità dei dispositivi][lnk-identity-registry] che archivia le chiavi del dispositivo. L'hub IoT usa queste informazioni per autenticare i dispositivi: un dispositivo deve essere registrato e deve avere una chiave valida per potersi connettere all'hub.

Un [dispositivo gemello][lnk-device-twin] è un documento JSON gestito dall'hub IoT. Un gemello dispositivo di un dispositivo contiene:

- Proprietà segnalate inviate dal dispositivo all'hub. È possibile visualizzare queste proprietà nel portale della soluzione.
- Le proprietà che si desidera inviare al dispositivo desiderate. È possibile impostare queste proprietà nel portale della soluzione.
- I tag che esistono solo nel dispositivo gemello e non sul dispositivo. È possibile utilizzare questi tag per filtrare gli elenchi di dispositivi nel portale della soluzione.

Questa soluzione usa i dispositivi gemelli per gestire i metadati del dispositivo. La soluzione usa anche un database Cosmos DB per archiviare i dati aggiuntivi del dispositivo specifici per la soluzione, ad esempio i comandi supportati da ogni dispositivo e la cronologia dei comandi.

La soluzione deve anche mantenere sincronizzate le informazioni del registro delle identità dei dispositivi con il contenuto del database Cosmos DB. **EventProcessorHost** usa i dati del processo di analisi di flusso **Informazioni sul dispositivo** per gestire la sincronizzazione.

## <a name="solution-portal"></a>Portale della soluzione

![portale della soluzione][img-dashboard]

Il portale della soluzione è un'interfaccia utente basata sul Web che viene distribuita nel cloud come parte della soluzione preconfigurata. Consente di:

* Visualizzare la cronologia di avvisi e i dati di telemetria in un dashboard.
* Eseguire il provisioning di nuovi dispositivi.
* Gestire e monitorare i dispositivi.
* Inviare comandi a dispositivi specifici.
* Richiamare i metodi su dispositivi specifici.
* Gestire regole e azioni.
* Pianificare i processi da eseguire in uno o più dispositivi.

In questa soluzione preconfigurata il portale della soluzione fa parte del **back-end della soluzione IoT** ed è incluso nella **connettività aziendale e di elaborazione** nell'[architettura della soluzione IoT][lnk-what-is-azure-iot] tipica.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle architetture delle soluzioni IoT, vedere il documento relativo all'[architettura di riferimento dei servizi IoT di Microsoft Azure][lnk-refarch].

Dopo aver acquisito informazioni sulle soluzioni preconfigurate è possibile iniziare distribuendo la soluzione preconfigurata per il *monitoraggio remoto*: [Introduzione alle soluzioni preconfigurate][lnk-getstarted-preconfigured].

[img-remote-monitoring-arch]: ./media/iot-suite-what-are-preconfigured-solutions/remote-monitoring-arch1.png
[img-dashboard]: ./media/iot-suite-what-are-preconfigured-solutions/dashboard.png
[lnk-what-is-azure-iot]: iot-suite-what-is-azure-iot.md
[lnk-asa]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-event-processor]: ../event-hubs/event-hubs-programming-guide.md#event-processor-host
[lnk-web-job]: ../app-service/web-sites-create-web-jobs.md
[lnk-identity-registry]: ../iot-hub/iot-hub-devguide-identity-registry.md
[lnk-predictive-maintenance]: iot-suite-predictive-overview.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-refarch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf
[lnk-getstarted-preconfigured]: iot-suite-getstarted-preconfigured-solutions.md
[lnk-c2d-guidance]: ../iot-hub/iot-hub-devguide-c2d-guidance.md
[lnk-device-twin]: ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-direct-methods]: ../iot-hub/iot-hub-devguide-direct-methods.md
[lnk-getstarted-factory]: iot-suite-connected-factory-overview.md
