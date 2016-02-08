<properties
 pageTitle="Soluzioni preconfigurate di Azure IoT | Microsoft Azure"
 description="Descrizione delle soluzioni preconfigurate di Azure IoT e della relativa architettura con collegamenti a risorse aggiuntive."
 services=""
 suite="iot-suite"
 documentationCenter=""
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-suite"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="11/30/2015"
 ms.author="dobett"/>

# Informazioni sulle soluzioni preconfigurate di Azure IoT Suite

Le soluzioni preconfigurate di Azure IoT Suite sono implementazioni dei modelli di soluzione IoT comuni che è possibile distribuire in Microsoft Azure con la sottoscrizione di Azure. È possibile usare le soluzioni preconfigurate:

- Come punto di partenza per le proprie soluzioni IoT.
- Per informazioni sui modelli comuni nello sviluppo e nella progettazione della soluzione IoT.

Ogni soluzione preconfigurata implementa uno scenario IoT comune ed è un'implementazione end-to-end completa, usando dispositivi simulati per generare dati di telemetria.

Oltre a distribuire ed eseguire le soluzioni preconfigurate in Azure, è possibile scaricare il codice sorgente completo e quindi personalizzare ed estendere la soluzione per soddisfare i requisiti IoT specifici.

Le soluzioni preconfigurate disponibili sono:

- [Monitoraggio remoto][lnk-remote-monitoring]
- [Manutenzione predittiva][lnk-predictive-maintenance]

La tabella seguente mostra quali funzionalità IoT specifiche sono disponibili per queste soluzioni preconfigurate:

| Soluzione | Inserimento di dati | Identità del dispositivo | Comando e controllo | Regole e azioni | Analisi predittiva |
|------------------------|-----|-----|-----|-----|-----|
| Monitoraggio remoto | Sì | Sì | Sì | Sì | - |
| Manutenzione predittiva | Sì | Sì | Sì | Sì | Sì |

## Panoramica della soluzione preconfigurata per il monitoraggio remoto

Questa sezione descrive alcuni degli elementi principali della soluzione preconfigurata di monitoraggio remoto. Il monitoraggio remoto è la più semplice delle soluzioni preconfigurate e illustra gli elementi di progettazione comuni che condividono le altre soluzioni preconfigurate.

Il diagramma seguente illustra gli elementi chiave della soluzione di monitoraggio remoto. Le sezioni seguenti forniscono altre informazioni su questi elementi.

![Architettura della soluzione preconfigurata per il monitoraggio remoto][img-remote-monitoring-arch]

## Dispositivi

Quando si distribuisce la soluzione preconfigurata di monitoraggio remoto, la distribuzione include istanze di un simulatore di dispositivi software che simula un dispositivo fisico. I dispositivi simulati inviano dati di telemetria su temperatura e umidità per un endpoint hub IoT. Dispositivi simulati rispondono anche ai seguenti comandi inviati dal portale di soluzioni tramite l'hub IoT:

- Ping dispositivo
- Avvia telemetria
- Arresta telemetria
- Modifica la temperatura del punto dati impostato
- Telemetria diagnostica
- Modifica stato del dispositivo

## Hub IoT

Un hub IoT riceve i dati di telemetria da più dispositivi in un singolo endpoint. Un hub IoT gestisce anche gli endpoint specifici del dispositivo in cui ogni dispositivo può recuperare i comandi, ad esempio il **Ping dispositivo**, a esso destinati.

L'hub IoT espone i dati di telemetria ricevuti tramite l'endpoint di un gruppo di consumer.

L'istanza dell'hub IoT in questa soluzione preconfigurata corrisponde al *gateway cloud* nell'[architettura di una soluzione IoT][lnk-what-is-azure-iot] tipica.

## Analisi di flusso di Azure

La soluzione preconfigurata usa tre processi di [Analisi di flusso di Azure][lnk-asa] per filtrare i flussi di dati di telemetria provenienti dai dispositivi di raffreddamento:

- Il processo n. 1 invia tutti i dati di telemetria all'archivio BLOB di Azure per l'archiviazione offline sicura
- Il processo n. 2 filtra il flusso di dati di telemetria per identificare i messaggi di risposta ai comandi e i messaggi di aggiornamento di stato del dispositivo dai dispositivi e invia questi messaggi specifici a un endpoint dell'Hub eventi di Azure.
- Il processo n. 3 filtra il flusso di dati di telemetria per i valori che generano avvisi. Quando un valore attiva un allarme, la soluzione visualizza la notifica nella tabella della cronologia di avvisi nella visualizzazione dashboard del portale della soluzione.

In questa soluzione preconfigurata, i processi di Analisi di flusso di Azure fanno parte del *backend della soluzione IoT* nell'[architettura di una soluzione IoT][lnk-what-is-azure-iot] tipica.

## Processore di eventi

Un'istanza di [EventPocessorHost][lnk-event-processor] in esecuzione in un [processo Web][lnk-web-job] elabora i messaggi di risposta al comando e sullo stato del dispositivo identificati dal processo di Analisi di flusso di Azure n. 2 e quindi archivia queste informazioni in un database [Azure DocumentDB][lnk-document-db].

In questa soluzione preconfigurata il processore di eventi fa parte del *backend della soluzione IoT* nell'[architettura di una soluzione IoT][lnk-what-is-azure-iot] tipica.

## Portale della soluzione

Il portale della soluzione è un'interfaccia utente basata sul Web che viene distribuita nel cloud come parte della soluzione preconfigurata. Consente di:

- Visualizzare la cronologia di avvisi e i dati di telemetria in un dashboard.
- Eseguire il provisioning di nuovi dispositivi.
- Gestire e monitorare i dispositivi.
- Inviare comandi a dispositivi specifici.
- Gestire regole e azioni.

> [AZURE.NOTE] Il portale della soluzione mantiene anche il [registro delle identità dei dispositivi][lnk-identity-registry] dell'hub IoT sincronizzato con l'archivio delle informazioni più dettagliate sullo stato dei dispositivi nel database DocumentDB della soluzione.

In questa soluzione preconfigurata il portale della soluzione fa parte del *back-end della soluzione IoT* ed è incluso nella *connettività commerciale e di elaborazione* nell'[architettura di una soluzione IoT][lnk-what-is-azure-iot] tipica.

## Passaggi successivi

Esplorare queste risorse per altre informazioni sulle soluzioni preconfigurate IoT:

- [Panoramica delle soluzioni preconfigurate di Azure IoT][lnk-suite-overview]
- [Iniziare a usare le soluzioni preconfigurate IoT][lnk-preconf-get-started]

[img-remote-monitoring-arch]: ./media/iot-suite-what-are-preconfigured-solutions/remote-monitoring-arch1.png
[lnk-remote-monitoring]: iot-suite-remote-monitoring-sample-walkthrough.md
[lnk-what-is-azure-iot]: iot-suite-what-is-azure-iot.md
[lnk-asa]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-event-processor]: event-hubs-programming-guide.md#event-processor-host
[lnk-web-job]: web-sites-create-web-jobs.md
[lnk-document-db]: https://azure.microsoft.com/documentation/services/documentdb/
[lnk-identity-registry]: iot-hub-devguide.md#device-identity-registry
[lnk-suite-overview]: iot-suite-overview.md
[lnk-preconf-get-started]: iot-suite-getstarted-preconfigured-solutions.md
[lnk-predictive-maintenance]: iot-suite-predictive-overview.md

<!---HONumber=AcomDC_0128_2016-->