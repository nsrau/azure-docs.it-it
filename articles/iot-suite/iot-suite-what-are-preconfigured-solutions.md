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
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="02/19/2016"
 ms.author="dobett"/>

# Informazioni sulle soluzioni preconfigurate di Azure IoT Suite

Le soluzioni preconfigurate di Azure IoT Suite sono implementazioni dei modelli di soluzione IoT comuni che è possibile distribuire in Microsoft Azure con la sottoscrizione di Azure. È possibile usare le soluzioni preconfigurate:

- Come punto di partenza per le proprie soluzioni IoT.
- Per informazioni sui modelli comuni nello sviluppo e nella progettazione della soluzione IoT.

Ogni soluzione preconfigurata implementa uno scenario IoT comune ed è un'implementazione end-to-end completa, usando dispositivi simulati per generare dati di telemetria.

Oltre a distribuire ed eseguire le soluzioni in Azure, è possibile scaricare il codice sorgente completo e quindi personalizzare ed estendere la soluzione per soddisfare i requisiti IoT specifici.

> [AZURE.NOTE] L'articolo [Introduzione alle soluzioni preconfigurate IoT][lnk-preconf-get-started] descrive come distribuire ed eseguire una delle soluzioni.

La tabella seguente mostra quali funzionalità IoT specifiche sono disponibili per le soluzioni:

| Soluzione | Inserimento di dati | Identità del dispositivo | Comando e controllo | Regole e azioni | Analisi predittiva |
|------------------------|-----|-----|-----|-----|-----|
| [Monitoraggio remoto][lnk-remote-monitoring] | Sì | Sì | Sì | Sì | - |
| [Manutenzione predittiva][lnk-predictive-maintenance] | Sì | Sì | Sì | Sì | Sì |

## Panoramica della soluzione preconfigurata per il monitoraggio remoto

In questo articolo viene descritta la soluzione preconfigurata per il monitoraggio remoto perché è la più semplice delle soluzioni e illustra gli elementi di progettazione comuni condivisi dalle altre soluzioni.

Il diagramma seguente illustra gli elementi chiave della soluzione di monitoraggio remoto. Le sezioni seguenti forniscono altre informazioni su questi elementi.

![Architettura della soluzione preconfigurata per il monitoraggio remoto][img-remote-monitoring-arch]

## Dispositivi

Quando si distribuisce la soluzione preconfigurata per il monitoraggio remoto, nella soluzione viene eseguito il pre-provisioning di quattro dispositivi simulati che simulano un dispositivo di raffreddamento. I dispositivi simulati hanno un modello di umidità e temperatura predefinito che genera i dati di telemetria.

Quando un dispositivo si connette per la prima volta all'hub IoT nella soluzione preconfigurata per il monitoraggio remoto, il messaggio di informazioni sul dispositivo inviato all'hub IoT enumera l'elenco di comandi a cui il dispositivo può rispondere. I comandi disponibili nella soluzione preconfigurata per il monitoraggio remoto sono:

- *Ping dispositivo*. Il dispositivo risponde a questo comando con un acknowledgement. Questo comando è utile per verificare che il dispositivo sia ancora attivo e in ascolto.
- *Avvia telemetria*. Indica al dispositivo di iniziare a inviare i dati di telemetria.
- *Arresta telemetria*. Indica al dispositivo di smettere di inviare i dati di telemetria.
- *Modifica la temperatura del punto dati impostato*. Controlla i valori dei dati di telemetria sulla temperatura simulati inviati dal dispositivo. Questo comando è utile per i test.
- *Telemetria diagnostica*. Controlla se il dispositivo deve inviare la temperatura esterna come dato di telemetria.
- *Modifica stato del dispositivo*. Imposta la proprietà dei metadati sullo stato del dispositivo segnalata dal dispositivo. Questo comando è utile per i test.

È possibile aggiungere alla soluzione altri dispositivi simulati che generano gli stessi dati di telemetria e rispondono agli stessi comandi.

## Hub IoT

Un hub IoT riceve i dati di telemetria da più dispositivi in un singolo endpoint. Un hub IoT gestisce anche gli endpoint specifici del dispositivo in cui ogni dispositivo può recuperare i comandi a esso destinati.

L'hub IoT espone i dati di telemetria ricevuti tramite l'endpoint di un gruppo di consumer.

L'istanza dell'hub IoT in questa soluzione preconfigurata corrisponde al *gateway cloud* nell'[architettura di una soluzione IoT][lnk-what-is-azure-iot] tipica.

## Analisi di flusso di Azure

La soluzione preconfigurata usa tre processi di [Analisi di flusso di Azure][lnk-asa] per filtrare i flussi di dati di telemetria provenienti dai dispositivi di raffreddamento:


- *Processo Informazioni sul dispositivo*: invia messaggi specifici di registrazione del dispositivo al registro dei dispositivi della soluzione, un database DocumentDB.
- *Processo Telemetria*: invia tutti i dati di telemetria non elaborati all'archivio BLOB di Azure per l'archiviazione offline sicura e calcola le aggregazioni dei dati di telemetria visualizzate nel dashboard della soluzione.
- *Processo Regole*: filtra il flusso dei dati di telemetria in base ai valori che superano qualsiasi soglia delle regole. Quando viene attivata una regola, la visualizzazione dashboard del portale della soluzione visualizza questo evento come una nuova riga nella tabella della cronologia di avvisi e attiva un'azione in base alle impostazioni definite nelle visualizzazioni relative alle regole e alle azioni nel portale della soluzione.

In questa soluzione preconfigurata i processi di Analisi di flusso di Azure fanno parte del *back-end della soluzione IoT* nell'[architettura di una soluzione IoT][lnk-what-is-azure-iot] tipica.

## Processore di eventi

Un'istanza di [EventPocessorHost][lnk-event-processor] in esecuzione in un [processo Web][lnk-web-job] elabora l'output dei processi Informazioni sul dispositivo e Regole.

In questa soluzione preconfigurata il processore di eventi fa parte del *back-end della soluzione IoT* nell'[architettura di una soluzione IoT][lnk-what-is-azure-iot] tipica.

## Portale della soluzione

![Dashboard della soluzione][img-dashboard]

Il portale della soluzione è un'interfaccia utente basata sul Web che viene distribuita nel cloud come parte della soluzione preconfigurata. Consente di:

- Visualizzare la cronologia di avvisi e i dati di telemetria in un dashboard.
- Eseguire il provisioning di nuovi dispositivi.
- Gestire e monitorare i dispositivi.
- Inviare comandi a dispositivi specifici.
- Gestire regole e azioni.

> [AZURE.NOTE] La soluzione preconfigurata mantiene anche il [registro delle identità dei dispositivi][lnk-identity-registry] dell'hub IoT sincronizzato con il registro dei dispositivi della soluzione, per database DocumentDB, in cui sono archiviati i metadati avanzati del dispositivo.

In questa soluzione preconfigurata il portale della soluzione fa parte del *back-end della soluzione IoT* ed è incluso nella *connettività aziendale e di elaborazione* nell'[architettura di una soluzione IoT][lnk-what-is-azure-iot] tipica.

## Passaggi successivi

Esplorare queste risorse per altre informazioni sulle soluzioni preconfigurate IoT:

- [Iniziare a usare le soluzioni preconfigurate IoT][lnk-preconf-get-started]
- [Panoramica della soluzione preconfigurata di manutenzione predittiva][lnk-predictive-maintenance]

[img-remote-monitoring-arch]: ./media/iot-suite-what-are-preconfigured-solutions/remote-monitoring-arch1.png
[img-dashboard]: ./media/iot-suite-what-are-preconfigured-solutions/dashboard.png
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

<!---HONumber=AcomDC_0224_2016-->