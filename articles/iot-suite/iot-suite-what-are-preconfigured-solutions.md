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
ms.date: 08/09/2016
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 597043b17993ebddc9cf730ddce849e1d6ff3bc9


---
# <a name="what-are-the-azure-iot-suite-preconfigured-solutions"></a>Informazioni sulle soluzioni preconfigurate di Azure IoT Suite
Le soluzioni preconfigurate di Azure IoT Suite sono implementazioni dei modelli di soluzione IoT comuni che è possibile distribuire in Azure usando la propria sottoscrizione. È possibile usare le soluzioni preconfigurate:

* Come punto di partenza per le proprie soluzioni IoT.
* Per informazioni sui modelli comuni nello sviluppo e nella progettazione della soluzione IoT.

Ogni soluzione preconfigurata è un'implementazione end-to-end completa che usa dispositivi simulati per generare dati di telemetria.

Oltre a distribuire ed eseguire le soluzioni in Azure, è possibile scaricare il codice sorgente completo e quindi personalizzare ed estendere la soluzione per soddisfare i requisiti IoT specifici.

> [!NOTE]
> Per distribuire una delle soluzioni preconfigurate,visitare il sito Web [Microsoft Azure IoT Suite][lnk-azureiotsuite]. L'articolo [Esercitazione: Introduzione alle soluzioni preconfigurate][lnk-getstarted-preconfigured] fornisce altre informazioni su come distribuire ed eseguire una delle soluzioni.
> 
> 

La tabella seguente mostra quali funzionalità IoT specifiche sono disponibili per le soluzioni:

| Soluzione | Inserimento di dati | Identità del dispositivo | Comando e controllo | Regole e azioni | Analisi predittiva |
| --- | --- | --- | --- | --- | --- |
| [Monitoraggio remoto][lnk-getstarted-preconfigured] |Sì |Sì |Sì |Sì |- |
| [Manutenzione predittiva][lnk-predictive-maintenance] |sì |Sì |Sì |Sì |Sì |

* *Inserimento di dati*: inserimento di dati su vasta scala nel cloud.
* *Identità del dispositivo*: gestione delle identità univoche di ogni dispositivo connesso.
* *Comando e controllo*: invio di messaggi a un dispositivo dal cloud per far eseguire determinate azioni al dispositivo.
* *Regole e azioni*: il back-end della soluzione usa regole per agire su specifici dati da dispositivo a cloud.
* *Analisi predittiva*: il back-end della soluzione analizza i dati da dispositivo a cloud per stimare quando eseguire determinate azioni. Ad esempio, usa l'analisi di dati di telemetria per il motore di un aereo per determinare eseguire la manutenzione.

## <a name="remote-monitoring-preconfigured-solution-overview"></a>Panoramica della soluzione preconfigurata per il monitoraggio remoto
In questo articolo viene descritta la soluzione preconfigurata per il monitoraggio remoto perché illustra molti degli elementi di progettazione comuni condivisi dalle altre soluzioni.

Il diagramma seguente illustra gli elementi chiave della soluzione di monitoraggio remoto. Le sezioni seguenti forniscono altre informazioni su questi elementi.

![Architettura della soluzione preconfigurata per il monitoraggio remoto][img-remote-monitoring-arch]

## <a name="devices"></a>Dispositivi
Quando si distribuisce la soluzione preconfigurata per il monitoraggio remoto, nella soluzione viene eseguito il pre-provisioning di quattro dispositivi simulati che simulano un dispositivo di raffreddamento. I dispositivi simulati hanno un modello di umidità e temperatura predefinito che genera i dati di telemetria. Questi dispositivi simulati sono inclusi per illustrare il flusso end-to-end di dati nella soluzione e per fornire una fonte pratica di dati di telemetria e una destinazione per i comandi per gli sviluppatori back-end che usano la soluzione come punto di partenza per un'implementazione personalizzata.

Quando un dispositivo si connette per la prima volta all'hub IoT nella soluzione preconfigurata per il monitoraggio remoto, il messaggio di informazioni sul dispositivo inviato all'hub IoT enumera l'elenco di comandi a cui il dispositivo può rispondere. I comandi disponibili nella soluzione preconfigurata per il monitoraggio remoto sono: 

* *Ping dispositivo*: il dispositivo risponde a questo comando con un acknowledgement. Questo comando è utile per verificare che il dispositivo sia ancora attivo e in ascolto.
* *Avvia telemetria*: indica al dispositivo di iniziare a inviare i dati di telemetria.
* *Arresta telemetria*: indica al dispositivo di arrestare l'invio dei dati di telemetria.
* *Modifica la temperatura del punto dati impostato*: controlla i valori dei dati di telemetria sulla temperatura simulati inviati dal dispositivo. Questo comando è utile per testare la logica back-end.
* *Telemetria diagnostica*: controlla se il dispositivo deve inviare la temperatura esterna come dato di telemetria.
* *Modifica stato del dispositivo*: imposta la proprietà dei metadati sullo stato del dispositivo segnalata dal dispositivo. Questo comando è utile per testare la logica back-end.

È possibile aggiungere alla soluzione altri dispositivi simulati che generano gli stessi dati di telemetria e rispondono agli stessi comandi. 

## <a name="iot-hub"></a>Hub IoT
In questa soluzione preconfigurata l'istanza dell'hub IoT corrisponde al *gateway cloud* nell'[architettura di una soluzione IoT][lnk-what-is-azure-iot] tipica.

Un hub IoT riceve i dati di telemetria dai dispositivi in un singolo endpoint. Un hub IoT gestisce anche gli endpoint specifici del dispositivo in cui ogni dispositivo può recuperare i comandi a esso destinati.

L'hub IoT rende disponibili i dati di telemetria ricevuti attraverso l'endpoint di lettura dei dati di telemetria sul lato servizio.

## <a name="azure-stream-analytics"></a>Azure Stream Analytics
La soluzione preconfigurata usa tre processi di [Analisi di flusso di Azure][lnk-asa] per filtrare il flusso di dati di telemetria proveniente dai dispositivi:

* *Processo Informazioni sul dispositivo* : invia i dati a un hub eventi che indirizza messaggi specifici sulla registrazione del dispositivo, inviati quando un dispositivo si connette o in risposta a un comando **Modifica stato del dispositivo** , al registro del dispositivo della soluzione (un database DocumentDB). 
* *Processo Telemetria* : invia tutti i dati di telemetria non elaborati all'archivio BLOB di Azure per l'archiviazione offline sicura e calcola le aggregazioni dei dati di telemetria visualizzate nel dashboard della soluzione.
* *Processo Regole* : filtra il flusso dei dati di telemetria in base ai valori che superano qualsiasi soglia delle regole e invia i dati a un hub eventi. Quando viene attivata una regola, la visualizzazione dashboard del portale della soluzione visualizza questo evento come una nuova riga nella tabella della cronologia di avvisi e attiva un'azione in base alle impostazioni definite nelle visualizzazioni relative alle regole e alle azioni nel portale della soluzione.

In questa soluzione preconfigurata i processi di Analisi di flusso di Azure fanno parte del **back-end della soluzione IoT** nell'[architettura di una soluzione IoT ][lnk-what-is-azure-iot] tipica.

## <a name="event-processor"></a>Processore di eventi
In questa soluzione preconfigurata il processore di eventi fa parte del **back-end della soluzione IoT** nell'[architettura di una soluzione IoT ][lnk-what-is-azure-iot] tipica.

I processi **Informazioni sul dispositivo** e **Regole** di Analisi di flusso di Azure inviano l'output agli hub eventi in modo che siano recapitati ad altri servizi back-end. La soluzione usa un'istanza di [EventPocessorHost][lnk-event-processor], in esecuzione in un [processo Web][lnk-web-job], per leggere i messaggi provenienti da questi hub eventi. **EventProcessorHost** usa i dati di **Informazioni sul dispositivo** per aggiornare i dati del dispositivo nel database DocumentDB e usa **Regole** per richiamare l'app per la logica e aggiornare gli avvisi visualizzati nel portale della soluzione.

## <a name="device-identity-registry-and-documentdb"></a>Registro delle identità dei dispositivi e DocumentDB
Ogni hub IoT include un [registro delle identità dei dispositivi][lnk-identity-registry] che archivia le chiavi dei dispositivi. L'hub IoT usa queste informazioni per autenticare i dispositivi: un dispositivo deve essere registrato e deve avere una chiave valida per potersi connettere all'hub.

Questa soluzione contiene informazioni aggiuntive sui dispositivi, ad esempio lo stato, i comandi supportati e altri metadati. La soluzione usa un database DocumentDB per archiviare i dati del dispositivo specifici per la soluzione e il portale della soluzione recupera i dati da questo database DocumentDB per la visualizzazione e la modifica.

La soluzione deve anche mantenere sincronizzate le informazioni del registro delle identità dei dispositivi con il contenuto del database DocumentDB. **EventProcessorHost** usa i dati del processo di analisi di flusso **Informazioni sul dispositivo** per gestire la sincronizzazione.

## <a name="solution-portal"></a>Portale della soluzione
![Dashboard della soluzione][img-dashboard]

Il portale della soluzione è un'interfaccia utente basata sul Web che viene distribuita nel cloud come parte della soluzione preconfigurata. Consente di:

* Visualizzare la cronologia di avvisi e i dati di telemetria in un dashboard.
* Eseguire il provisioning di nuovi dispositivi.
* Gestire e monitorare i dispositivi.
* Inviare comandi a dispositivi specifici.
* Gestire regole e azioni.

In questa soluzione preconfigurata il portale della soluzione fa parte del **back-end della soluzione IoT** ed è incluso nella **connettività aziendale e di elaborazione** nell'[architettura della soluzione IoT ][lnk-what-is-azure-iot] tipica.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle architetture delle soluzioni IoT, vedere [Microsoft Azure IoT services: Reference Architecture][lnk-refarch] (Servizi di Microsoft Azure IoT: architettura di riferimento).

Dopo aver acquisito le informazioni di base sulle soluzioni preconfigurate, è possibile iniziare distribuendo la soluzione per il *monitoraggio remoto*: [Esercitazione: Introduzione alle soluzioni preconfigurate][lnk-getstarted-preconfigured].

[img-remote-monitoring-arch]: ./media/iot-suite-what-are-preconfigured-solutions/remote-monitoring-arch1.png
[img-dashboard]: ./media/iot-suite-what-are-preconfigured-solutions/dashboard.png
[lnk-what-is-azure-iot]: iot-suite-what-is-azure-iot.md
[lnk-asa]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-event-processor]: ../event-hubs/event-hubs-programming-guide.md#event-processor-host
[lnk-web-job]: ../app-service-web/web-sites-create-web-jobs.md
[lnk-identity-registry]: ../iot-hub/iot-hub-devguide-identity-registry.md
[lnk-predictive-maintenance]: iot-suite-predictive-overview.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-refarch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf
[lnk-getstarted-preconfigured]: iot-suite-getstarted-preconfigured-solutions.md



<!--HONumber=Nov16_HO2-->


