<properties
 pageTitle="Descrizione delle soluzioni preconfigurate di Azure IoT | Microsoft Azure"
 description="Descrizione delle soluzioni preconfigurate di Azure IoT e della relativa architettura con collegamenti a risorse aggiuntive."
 services=""
 documentationCenter=".net"
 authors="aguilaaj"
 manager="kevinmil"
 editor=""/>

<tags
 ms.service="na"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="tbd"
 ms.date="09/29/2015"
 ms.author="araguila"/>

# Che cosa sono le soluzioni preconfigurate di Azure IoT?

È possibile distribuire soluzioni preconfigurate che implementano scenari comuni di Internet delle cose (IoT) in Microsoft Azure con la sottoscrizione di Azure. È possibile usare le soluzioni preconfigurate:

- Come punto di partenza per le proprie soluzioni IoT.
- Per informazioni sui modelli più comuni nello sviluppo e nella progettazione della soluzione IoT.

Ogni soluzione preconfigurata implementa uno scenario IoT comune ed è un'implementazione end-to-end completa.

Oltre a distribuire ed eseguire le soluzioni preconfigurate in Azure, è possibile scaricare il codice sorgente completo per personalizzare ed estendere la soluzione e soddisfare così i requisiti specifici.

Le soluzioni preconfigurate disponibili sono:

- Monitoraggio remoto
- Manutenzione predittiva

La tabella seguente mostra quali funzionalità IoT specifiche sono disponibili per queste soluzioni preconfigurate:

| Soluzione | Inserimento di dati | Identità del dispositivo | Comando e controllo | Regole e azioni | Analisi predittiva |
|------------------------|----------------|-----------------|---------------------|-------------------|----------------------|
| Monitoraggio remoto | Sì | Sì | Sì | Sì | - | | Manutenzione predittiva | Sì | Sì | Sì | Sì | Sì |

## Panoramica dell'esempio Monitoraggio remoto

Monitoraggio remoto è la più semplice delle soluzioni preconfigurate con funzionalità complete. Questa sezione descrive alcune delle funzionalità chiave della soluzione preconfigurata per il monitoraggio remoto dopo un'introduzione al set completo di soluzioni preconfigurate.

Il diagramma seguente illustra le funzionalità chiave della soluzione e le sezioni seguenti. Le sezioni successive forniscono altre informazioni sugli elementi mostrati in questa diagramma.

![Architettura della soluzione preconfigurata per il monitoraggio remoto][img-remote-monitoring-arch]

### Dispositivo

Il dispositivo di cui è stato preventivamente eseguito il provisioning nella soluzione preconfigurata per il monitoraggio remoto è una simulazione software di un dispositivo di raffreddamento che invia dati di telemetria su temperatura e umidità. Il dispositivo può anche rispondere a un set di comandi inviati dal portale della soluzione con l'hub IoT. I comandi già implementati nel simulatore sono: Ping Device, Start Telemetry, Stop Telemetry, Change Set Point Temp, Diagnostic Telemetry e Change Device State.

I dispositivi di raffreddamento in questa soluzione preconfigurata corrispondono ai **dispositivi e alle origini dati** dell'architettura di una tipica soluzione IoT.

### Hub IoT

Un hub IoT riceve i dati di telemetria dai dispositivi di raffreddamento in un solo endpoint e gestisce gli endpoint specifici del dispositivo dove i dispositivi possono recuperare i comandi, ad esempio PingDevice.

L'hub IoT espone i dati di telemetria ricevuti tramite l'endpoint di un gruppo di consumer.

L'istanza dell'hub IoT in questa soluzione preconfigurata corrisponde all'**applicazione back-end IoT** nell'architettura di una tipica soluzione IoT.

### Analisi di flusso di Azure

La soluzione preconfigurata usa i processi di [Analisi di flusso di Azure][] per filtrare i flussi di eventi provenienti dai dispositivi di raffreddamento. Un processo invia tutti i dati di telemetria ai BLOB di archiviazione di Azure per l'archiviazione offline sicura. Il secondo processo filtra nel flusso di eventi i messaggi di risposta ai comandi e i messaggi di aggiornamento dello stato del dispositivo e invia questi messaggi specifici a un endpoint di Hub eventi di Azure. Il terzo processo filtra gli avvisi attivati e li visualizza nella tabella della cronologia avvisi nella visualizzazione dashboard del portale della soluzione.


### Processore di eventi

Un'istanza del processore di eventi, in esecuzione in un processo Web, elabora la risposta al comando e i dati sullo stato del dispositivo e archivia queste informazioni in un database Azure DocumentDB.

### Portale della soluzione

Il portale della soluzione è un'interfaccia utente basata sul Web che consente di:

- Visualizzare la cronologia di avvisi e telemetria nel dashboard.
- Eseguire il provisioning di nuovi dispositivi.
- Gestire e monitorare i dispositivi.
- Inviare comandi a dispositivi specifici.
- Gestire regole e azioni.

> [AZURE.NOTE]La visualizzazione dispositivi del portale della soluzione mantiene anche il registro di identità dei dispositivi dell'hub IoT sincronizzato con l'archivio di informazioni più dettagliate sullo stato dei dispositivi nel database DocumentDB.

## Passaggi successivi

Esplorare queste risorse per altre informazioni sulle soluzioni preconfigurate IoT:

- [Panoramica delle soluzioni preconfigurate di Azure IoT](iot-suite-overview.md)
- [Iniziare a usare le soluzioni preconfigurate IoT](iot-suite-getstarted-preconfigured-solutions.md)

[img-remote-monitoring-arch]: ./media/iot-suite-what-are-preconfigured-solutions/remote-monitoring-arch1.png
[Analisi di flusso di Azure]: https://azure.microsoft.com/services/stream-analytics/

<!---HONumber=Oct15_HO2-->