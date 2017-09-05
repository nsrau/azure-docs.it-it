---
title: Procedura dettagliata della soluzione preconfigurata per il monitoraggio remoto| Microsoft Docs
description: Descrizione della soluzione preconfigurata per il monitoraggio remoto di Azure IoT e relativa architettura.
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 31fe13af-0482-47be-b4c8-e98e36625855
ms.service: iot-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: dobett
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: c6d76cc741a6d932a506017781e45bc9b8f8c640
ms.contentlocale: it-it
ms.lasthandoff: 05/15/2017

---
# <a name="remote-monitoring-preconfigured-solution-walkthrough"></a>Procedura dettagliata della soluzione preconfigurata per il monitoraggio remoto

La [soluzione preconfigurata][lnk-preconfigured-solutions] per il monitoraggio remoto IoT Suite è un'implementazione di una soluzione di monitoraggio end-to-end per più computer in località remote. La soluzione combina i servizi chiave di Azure per offrire un'implementazione generica dello scenario aziendale. È possibile usare la soluzione come punto di partenza per l'implementazione e [personalizzarla][lnk-customize] in base ai requisiti aziendali specifici.

Questo articolo illustra alcuni degli elementi principali della soluzione di monitoraggio remoto per comprenderne il funzionamento. Queste informazioni consentono di:

* Risolvere i problemi nella soluzione.
* Pianificare come personalizzare la soluzione per soddisfare requisiti specifici. 
* Progettare la propria soluzione IoT che usa i servizi di Azure.

## <a name="logical-architecture"></a>Architettura logica

Il diagramma seguente illustra i componenti logici della soluzione preconfigurata:

![Architettura logica](media/iot-suite-remote-monitoring-sample-walkthrough/remote-monitoring-architecture.png)

## <a name="simulated-devices"></a>Dispositivi simulati

Nella soluzione preconfigurata il dispositivo simulato rappresenta un dispositivo di raffreddamento (ad esempio, un condizionatore d'aria di un edificio o un'unita di trattamento aria di una struttura). Quando si distribuisce la soluzione preconfigurata, viene effettuato automaticamente anche il provisioning di quattro dispositivi simulati eseguiti in un [processo Web di Azure][lnk-webjobs]. I dispositivi simulati semplificano l'esplorazione del comportamento della soluzione senza la necessità di distribuire dispositivi fisici. Per distribuire un dispositivo fisico reale, vedere l'esercitazione [Connettere il dispositivo alla soluzione preconfigurata per il monitoraggio remoto][lnk-connect-rm].

### <a name="device-to-cloud-messages"></a>Messaggi da dispositivo a cloud

Ogni dispositivo simulato può inviare i messaggi seguenti all'hub IoT:

| Message | Descrizione |
| --- | --- |
| Startup |Quando il dispositivo viene avviato, invia al back-end un messaggio di **informazioni sul dispositivo** contenente informazioni su se stesso. I dati includono l'ID dispositivo e un elenco dei comandi e dei metodi supportati dal dispositivo. |
| Presenza |Un dispositivo invia periodicamente un messaggio di **presenza** per segnalare se il dispositivo può rilevare la presenza di un sensore. |
| Telemetria |Un dispositivo invia periodicamente un messaggio di **telemetria** che segnala i valori simulati per la temperatura e l'umidità raccolti dai sensori simulati del dispositivo. |

> [!NOTE]
> La soluzione memorizza l'elenco dei comandi supportati dal dispositivo in un database Cosmos DB e non in un dispositivo gemello.

### <a name="properties-and-device-twins"></a>Proprietà e dispositivi gemelli

Le proprietà del dispositivo riportate di seguito vengono inviate dai dispositivi simulati ai [dispositivi gemelli][lnk-device-twins] nell'hub IoT come *proprietà segnalate*. Il dispositivo invia le proprietà segnalate all'avvio e in risposta a un metodo o un comando di **modifica dello stato del dispositivo**.

| Proprietà | Scopo |
| --- | --- |
| Config.TelemetryInterval | Frequenza (in secondi) di invio dei dati di telemetria dal dispositivo. |
| Config.TemperatureMeanValue | Specifica il valore medio per i dati di telemetria della temperatura simulata. |
| Device.DeviceID |ID fornito o assegnato al momento della creazione di un dispositivo nella soluzione. |
| Device.DeviceState | Stato segnalato dal dispositivo. |
| Device.CreatedTime |Data e ora in cui il dispositivo è stato creato nella soluzione. |
| Device.StartupTime |Ora in cui il dispositivo è stato avviato. |
| Device.LastDesiredPropertyChange |Numero di versione dell'ultima modifica della proprietà desiderata. |
| Device.Location.Latitude |Posizione di latitudine del dispositivo. |
| Device.Location.Longitude |Posizione di longitudine del dispositivo. |
| System.Manufacturer |Produttore del dispositivo |
| System.ModelNumber |Numero di modello del dispositivo. |
| System.SerialNumber |Numero di serie del dispositivo. |
| System.FirmwareVersion |Versione corrente del firmware del dispositivo. |
| System.Platform |Architettura della piattaforma del dispositivo. |
| System.Processor |Processore in esecuzione nel dispositivo. |
| System.InstalledRAM |Quantità di RAM installata sul dispositivo. |

Il simulatore effettua il seeding di queste proprietà nei dispositivi simulati con valori di esempio. Ogni volta che il simulatore inizializza un dispositivo simulato, quest'ultimo segnala i metadati predefiniti nell'hub IoT come proprietà segnale. Le proprietà segnalate possono essere aggiornate solo dal dispositivo. Per modificare una proprietà segnalata, impostare una proprietà desiderata nel portale della soluzione. Il dispositivo è responsabile delle operazioni seguenti:

1. Recuperare periodicamente le proprietà desiderate dall'hub IoT.
2. Aggiornare la configurazione con il valore della proprietà desiderata.
3. Inviare il nuovo valore all'hub come proprietà segnalata.

Nel dashboard della soluzione è possibile usare le *proprietà desiderate* per impostare le proprietà in un dispositivo tramite il [dispositivo gemello][lnk-device-twins]. In genere, il dispositivo legge il valore di una proprietà desiderata dall'hub per aggiornare lo stato interno e segnalare la modifica come proprietà segnalata.

> [!NOTE]
> Il codice del dispositivo simulato usa le proprietà desiderate **Desired.Config.TemperatureMeanValue** e **Desired.Config.TelemetryInterval** soltanto per aggiornare le proprietà segnalate inviate all'hub IoT. Tutte le altre richieste di modifica delle proprietà desiderate vengono ignorate nel dispositivo simulato.

### <a name="methods"></a>Metodi

I dispositivi simulati possono gestire i [metodi diretti][lnk-direct-methods] seguenti richiamati dal dashboard della soluzione tramite l'hub IoT:

| Metodo | Descrizione |
| --- | --- |
| InitiateFirmwareUpdate |Indica al dispositivo di eseguire un aggiornamento del firmware. |
| Reboot |Indica al dispositivo di eseguire il riavvio. |
| FactoryReset |Indica al dispositivo di eseguire un ripristino delle impostazioni predefinite. |

Alcuni metodi fanno uso delle proprietà segnalate per segnalare lo stato. Ad esempio, il metodo **InitiateFirmwareUpdate** simula l'esecuzione asincrona dell'aggiornamento nel dispositivo. Il metodo viene restituito immediatamente nel dispositivo, mentre l'attività asincrona continua a inviare aggiornamenti di stato al dashboard della soluzione tramite le proprietà segnalate.

### <a name="commands"></a>Comandi:

I dispositivi simulati possono gestire i comandi seguenti, sotto forma di messaggi da cloud a dispositivo, inviati dal portale della soluzione tramite l'hub IoT:

| Comando | Descrizione |
| --- | --- |
| PingDevice |Invia un *ping* al dispositivo per verificare che sia attivo. |
| StartTelemetry |Avvia l'invio dei dati di telemetria dal dispositivo. |
| StopTelemetry |Arresta l'invio dei dati di telemetria dal dispositivo. |
| ChangeSetPointTemp |Modifica il valore del punto dati impostato in base al quale vengono generati i dati casuali. |
| DiagnosticTelemetry |Attiva il simulatore del dispositivo per l'invio di un valore di telemetria aggiuntivo (externalTemp) |
| ChangeDeviceState |Modifica una proprietà di stato estesa per il dispositivo e invia il messaggio di informazioni sul dispositivo dal dispositivo. |

> [!NOTE]
> Per un confronto tra questi comandi (messaggi da cloud a dispositivo) e i metodi (metodi diretti), vedere [Indicazioni sulle comunicazioni da cloud a dispositivo][lnk-c2d-guidance].

## <a name="iot-hub"></a>Hub IoT

L'[hub IoT][lnk-iothub] acquisisce i dati inviati dai dispositivi nel cloud e li rende disponibili per i processi di Analisi di flusso di Azure. Ogni processo di Analisi di flusso di Azure usa un gruppo di consumer dell'hub IoT separato per leggere il flusso dei messaggi dai dispositivi.

L'hub IoT nella soluzione esegue anche le operazioni seguenti:

- Gestisce un registro delle identità in cui vengono archiviati gli ID e le chiavi di autenticazione di tutti i dispositivi autorizzati a connettersi al portale. Il registro delle identità permette di abilitare e disabilitare i dispositivi.
- Invia comandi ai dispositivi per conto del portale della soluzione.
- Richiama i metodi nei dispositivi per conto del portale della soluzione.
- Gestisce i dispositivi gemelli per tutti i dispositivi registrati. Nel dispositivo gemello vengono archiviati i valori delle proprietà segnalati da un dispositivo. Qui vengono archiviate anche le proprietà desiderate, impostate nel portale della soluzione, in modo che il dispositivo possa recuperarle alla connessione successiva.
- Pianifica processi per impostare proprietà per più dispositivi o richiamare metodi in più dispositivi.

## <a name="azure-stream-analytics"></a>Analisi di flusso di Azure

Nella soluzione di monitoraggio remoto i messaggi che l'hub IoT riceve dai dispositivi vengono inviati da [Analisi di flusso di Azure][lnk-asa] ad altri componenti di back-end per l'elaborazione o l'archiviazione. I diversi processi di Analisi di flusso di Azure eseguono operazioni specifiche in base al contenuto dei messaggi.

**Processo 1: Informazioni sul dispositivo** filtra i messaggi informativi sul dispositivo dal flusso di messaggi in arrivo e li invia a un endpoint di Hub eventi. Un dispositivo invia messaggi di informazioni sul dispositivo all'avvio e in risposta a un comando **SendDeviceInfo** . Questo processo usa la definizione di query seguente per identificare i messaggi di **informazioni sul dispositivo** :

```
SELECT * FROM DeviceDataStream Partition By PartitionId WHERE  ObjectType = 'DeviceInfo'
```

Questo processo invia l'output a un hub eventi per un'ulteriore elaborazione.

**Processo 2: Regole** restituisce valori di telemetria in entrata relativi a temperatura e umidità rispetto alle soglie per dispositivo. I valori di soglia vengono impostati nell'editor delle regole incluso nel portale della soluzione. Ogni coppia dispositivo/valore viene archiviata in base al timestamp in un BLOB che viene letto in Analisi di flusso come **dati di riferimento**. Il processo confronta tutti i valori non vuoti rispetto alla soglia impostata per il dispositivo. Se supera la condizione ">", il processo genera un evento di **avviso** che segnala che la soglia è stata superata e indica il dispositivo, il valore e i valori di timestamp. Questo processo usa la definizione di query seguente per identificare i messaggi di telemetria che devono attivare un allarme:

```
WITH AlarmsData AS 
(
SELECT
     Stream.IoTHub.ConnectionDeviceId AS DeviceId,
     'Temperature' as ReadingType,
     Stream.Temperature as Reading,
     Ref.Temperature as Threshold,
     Ref.TemperatureRuleOutput as RuleOutput,
     Stream.EventEnqueuedUtcTime AS [Time]
FROM IoTTelemetryStream Stream
JOIN DeviceRulesBlob Ref ON Stream.IoTHub.ConnectionDeviceId = Ref.DeviceID
WHERE
     Ref.Temperature IS NOT null AND Stream.Temperature > Ref.Temperature

UNION ALL

SELECT
     Stream.IoTHub.ConnectionDeviceId AS DeviceId,
     'Humidity' as ReadingType,
     Stream.Humidity as Reading,
     Ref.Humidity as Threshold,
     Ref.HumidityRuleOutput as RuleOutput,
     Stream.EventEnqueuedUtcTime AS [Time]
FROM IoTTelemetryStream Stream
JOIN DeviceRulesBlob Ref ON Stream.IoTHub.ConnectionDeviceId = Ref.DeviceID
WHERE
     Ref.Humidity IS NOT null AND Stream.Humidity > Ref.Humidity
)

SELECT *
INTO DeviceRulesMonitoring
FROM AlarmsData

SELECT *
INTO DeviceRulesHub
FROM AlarmsData
```

Il processo invia l'output a un hub eventi per un'ulteriore elaborazione e salva i dettagli di ogni avviso nell'archivio BLOB da cui il portale della soluzione può leggere le informazioni sull'avviso.

**Processo 3: Telemetria** agisce in due modi sul flusso in entrata dei dati di telemetria del dispositivo. Il primo invia tutti i messaggi di telemetria dai dispositivi all'archivio BLOB permanente per l'archiviazione a lungo termine. Il secondo calcola i valori di umidità media, minima e massima in una finestra temporale scorrevole di cinque minuti e invia questi dati all'archivio BLOB. Il portale della soluzione legge i dati di telemetria dall'archivio BLOB per popolare i grafici. Questo processo usa la definizione di query seguente:

```
WITH 
    [StreamData]
AS (
    SELECT
        *
    FROM [IoTHubStream]
    WHERE
        [ObjectType] IS NULL -- Filter out device info and command responses
) 

SELECT
    IoTHub.ConnectionDeviceId AS DeviceId,
    Temperature,
    Humidity,
    ExternalTemperature,
    EventProcessedUtcTime,
    PartitionId,
    EventEnqueuedUtcTime,
    * 
INTO
    [Telemetry]
FROM
    [StreamData]

SELECT
    IoTHub.ConnectionDeviceId AS DeviceId,
    AVG (Humidity) AS [AverageHumidity],
    MIN(Humidity) AS [MinimumHumidity],
    MAX(Humidity) AS [MaxHumidity],
    5.0 AS TimeframeMinutes 
INTO
    [TelemetrySummary]
FROM [StreamData]
WHERE
    [Humidity] IS NOT NULL
GROUP BY
    IoTHub.ConnectionDeviceId,
    SlidingWindow (mi, 5)
```

## <a name="event-hubs"></a>Hub eventi

I processi **Informazioni sul dispositivo** e **Regole** di Analisi di flusso di Azure inviano i dati ad Hub eventi, che li inoltra in modo affidabile al **processore di eventi** in esecuzione nel processo Web.

## <a name="azure-storage"></a>Archiviazione di Azure

La soluzione usa l'archivio BLOB di Azure per rendere persistenti tutti i dati di telemetria non elaborati e sintetizzati dai dispositivi presenti nella soluzione. Il portale legge i dati di telemetria dall'archivio BLOB per popolare i grafici. Per visualizzare gli avvisi, il portale della soluzione legge i dati dall'archivio BLOB che registra quando i valori dei dati di telemetria superano i valori soglia configurati. La soluzione usa anche l'archivio BLOB per registrare i valori soglia impostati nel portale della soluzione.

## <a name="webjobs"></a>WebJobs

Oltre a ospitare i simulatori dei dispositivi, i processi Web nella soluzione ospitano il **processore di eventi** eseguito in un processo Web di Azure che gestisce le risposte ai comandi. I messaggi di risposta ai comandi vengono usati per aggiornare la cronologia dei comandi del dispositivo archiviata nel database di Cosmos DB.

## <a name="cosmos-db"></a>Cosmos DB

La soluzione usa un database Cosmos DB per archiviare le informazioni nei dispositivi connessi alla soluzione. Tali informazioni includono la cronologia dei comandi inviati ai dispositivi dal portale della soluzione e dei metodi richiamati dal portale della soluzione.

## <a name="solution-portal"></a>Portale della soluzione

Il portale della soluzione è un'app Web che viene distribuita come parte della soluzione preconfigurata. Le pagine principali nel portale della soluzione sono il dashboard e l'elenco dei dispositivi.

### <a name="dashboard"></a>Dashboard

Questa pagina dell'app Web usa i controlli JavaScript di Power BI per visualizzare i dati di telemetria inviati dai dispositivi. Vedere in proposito il [repository PowerBI-visuals](https://www.github.com/Microsoft/PowerBI-visuals). La soluzione usa il processo di telemetria di Analisi di flusso di Azure per scrivere i dati di telemetria nell'archivio BLOB.

### <a name="device-list"></a>Elenco dei dispositivi

Da questa pagina del portale della soluzione è possibile:

* Effettuare il provisioning di un nuovo dispositivo. Questa azione permette di impostare l'ID univoco del dispositivo e di generare la chiave di autenticazione. Le informazioni sul dispositivo verranno scritte sia nel registro delle identità dell'hub IoT che nel database Cosmos DB specifico della soluzione.
* Gestire le proprietà dei dispositivi. Questa azione include la visualizzazione delle proprietà esistenti e l'aggiornamento con nuove proprietà.
* Inviare comandi a un dispositivo.
* Visualizzare la cronologia dei comandi per un dispositivo.
* Abilitare e disabilitare i dispositivi.

## <a name="next-steps"></a>Passaggi successivi

I post del blog TechNet offrono altri dettagli sulla soluzione preconfigurata per il monitoraggio remoto:

* [Approfondimento sul monitoraggio remoto in IoT Suite](http://social.technet.microsoft.com/wiki/contents/articles/32941.iot-suite-under-the-hood-remote-monitoring.aspx)
* [Aggiunta di dispositivi veri e simulati per il monitoraggio remoto in IoT Suite](http://social.technet.microsoft.com/wiki/contents/articles/32975.iot-suite-remote-monitoring-adding-live-and-simulated-devices.aspx)

È possibile proseguire con l'introduzione a IoT Suite vedendo gli articoli seguenti:

* [Connettere il dispositivo alla soluzione preconfigurata per il monitoraggio remoto][lnk-connect-rm]
* [Autorizzazioni per il sito azureiotsuite.com][lnk-permissions]

[lnk-preconfigured-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-iothub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-asa]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-webjobs]: https://azure.microsoft.com/documentation/articles/websites-webjobs-resources/
[lnk-connect-rm]: iot-suite-connecting-devices.md
[lnk-permissions]: iot-suite-permissions.md
[lnk-c2d-guidance]: ../iot-hub/iot-hub-devguide-c2d-guidance.md
[lnk-device-twins]:  ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-direct-methods]: ../iot-hub/iot-hub-devguide-direct-methods.md

