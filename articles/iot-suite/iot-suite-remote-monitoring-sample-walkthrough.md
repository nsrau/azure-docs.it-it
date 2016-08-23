<properties
 pageTitle="Procedura dettagliata della soluzione preconfigurata per il monitoraggio remoto| Microsoft Azure"
 description="Descrizione della soluzione preconfigurata per il monitoraggio remoto di Azure IoT e relativa architettura."
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
 ms.date="08/17/2016"
 ms.author="dobett"/>

# Procedura dettagliata della soluzione preconfigurata per il monitoraggio remoto

## Introduzione

La [soluzione preconfigurata][lnk-preconfigured-solutions] per il monitoraggio remoto IoT Suite è un'implementazione di una soluzione di monitoraggio end-to-end per più computer in località remote. La soluzione combina i servizi chiave di Azure per offrire un'implementazione generica dello scenario aziendale ed è un punto di partenza per la propria implementazione. È possibile [personalizzare][lnk-customize] la soluzione per soddisfare requisiti aziendali specifici.

Questo articolo illustra alcuni degli elementi principali della soluzione di monitoraggio remoto per comprenderne il funzionamento. Queste informazioni consentono di:

- Risolvere i problemi nella soluzione.
- Pianificare come personalizzare la soluzione per soddisfare requisiti specifici.
- Progettare la propria soluzione IoT che usa i servizi di Azure.

## Architettura logica

Il diagramma seguente illustra i componenti logici della soluzione preconfigurata:

![Architettura logica](media/iot-suite-remote-monitoring-sample-walkthrough/remote-monitoring-architecture.png)


## Dispositivi simulati

Nella soluzione preconfigurata il dispositivo simulato rappresenta un dispositivo di raffreddamento (ad esempio, un condizionatore d'aria di un edificio o un'unita di trattamento aria di una struttura). Quando si distribuisce la soluzione preconfigurata, viene effettuato automaticamente anche il provisioning di quattro dispositivi simulati eseguiti in un [processo Web di Azure][lnk-webjobs]. I dispositivi simulati semplificano l'esplorazione del comportamento della soluzione senza la necessità di distribuire dispositivi fisici. Per distribuire un dispositivo fisico reale, vedere l'esercitazione [Connettere il dispositivo alla soluzione preconfigurata per il monitoraggio remoto][lnk-connect-rm].

Ogni dispositivo simulato può inviare i messaggi seguenti all'hub IoT:

| Message | Descrizione |
|----------|-------------|
| Startup | Quando il dispositivo viene avviato, invia al back-end un messaggio di **informazioni sul dispositivo** contenente informazioni su se stesso. Questi dati includono l'ID dispositivo, i metadati del dispositivo, un elenco dei comandi supportati dal dispositivo e la configurazione corrente del dispositivo. |
| Presenza | Un dispositivo invia periodicamente un messaggio di **presenza** per segnalare se il dispositivo può rilevare la presenza di un sensore. |
| Telemetria | Un dispositivo invia periodicamente un messaggio di **telemetria** che segnala i valori simulati per la temperatura e l'umidità raccolti dai sensori simulati del dispositivo. |


I dispositivi simulati inviano le proprietà del dispositivo seguenti in un messaggio di **informazioni sul dispositivo**:

| Proprietà | Scopo |
|------------------------|--------- |
| ID dispositivo | ID fornito o assegnato al momento della creazione di un dispositivo nella soluzione. |
| Produttore | Produttore del dispositivo |
| Numero di modello | Numero di modello del dispositivo. |
| Numero di serie | Numero di serie del dispositivo. |
| Firmware | Versione corrente del firmware del dispositivo. |
| Piattaforma | Architettura della piattaforma del dispositivo. |
| Processore | Processore in esecuzione nel dispositivo. |
| RAM installata | Quantità di RAM installata sul dispositivo. |
| Stato abilitato dell'hub | Proprietà dello stato dell'hub IoT del dispositivo. |
| Data e ora creazione | Data e ora in cui il dispositivo è stato creato nella soluzione. |
| Ora ultimo aggiornamento | Ora dell'ultimo aggiornamento delle proprietà per il dispositivo. |
| Latitudine | Posizione di latitudine del dispositivo. |
| Longitudine | Posizione di longitudine del dispositivo. |

Il simulatore effettua il seeding di queste proprietà nei dispositivi simulati con valori di esempio. Ogni volta che il simulatore inizializza un dispositivo simulato, quest'ultimo registra i metadati predefiniti nell'hub IoT. Si noti come questa registrazione sovrascriva gli eventuali aggiornamenti di metadati eseguiti nel portale per il dispositivo.


I dispositivi simulati possono gestire i comandi seguenti inviati dal dashboard della soluzione tramite l'hub IoT:

| Comando | Descrizione |
|------------------------|-----------------------------------------------------|
| PingDevice | Invia un _ping_ per il dispositivo per verificare che sia attivo. |
| StartTelemetry | Avvia l'invio dei dati di telemetria dal dispositivo. |
| StopTelemetry | Arresta l'invio dei dati di telemetria dal dispositivo. |
| ChangeSetPointTemp | Modifica il valore del punto dati impostato in base al quale vengono generati i dati casuali. |
| DiagnosticTelemetry | Attiva il simulatore del dispositivo per l'invio di un valore di telemetria aggiuntivo (externalTemp) |
| ChangeDeviceState | Modifica una proprietà di stato estesa per il dispositivo e invia il messaggio di informazioni sul dispositivo dal dispositivo. |

L'acknowledgment dei comandi del dispositivo nel back-end della soluzione viene fornito tramite l'hub IoT.

## Hub IoT

L'[hub IoT][lnk-iothub] acquisisce i dati inviati dai dispositivi nel cloud e li rende disponibili per i processi di Analisi di flusso di Azure. L'hub IoT invia anche i comandi ai dispositivi per conto del portale dei dispositivi. Ogni processo di Analisi di flusso di Azure usa un gruppo di consumer dell'hub IoT separato per leggere il flusso dei messaggi dai dispositivi.

## Analisi di flusso di Azure

Nella soluzione di monitoraggio remoto, i messaggi che l'hub IoT riceve dai dispositivi vengono inviati da [Analisi di flusso di Azure][lnk-asa] ad altri componenti di back-end per l'elaborazione o l'archiviazione. I diversi processi di Analisi di flusso di Azure eseguono operazioni specifiche in base al contenuto dei messaggi.

**Processo 1: Informazioni sul dispositivo** filtra i messaggi informativi sul dispositivo dal flusso di messaggi in arrivo e li invia a un endpoint di Hub eventi. Un dispositivo invia messaggi di informazioni sul dispositivo all'avvio e in risposta a un comando **SendDeviceInfo**. Questo processo usa la definizione di query seguente per identificare i messaggi di **informazioni sul dispositivo**:

```
SELECT * FROM DeviceDataStream Partition By PartitionId WHERE  ObjectType = 'DeviceInfo'
```

Questo processo invia l'output a un hub eventi per un'ulteriore elaborazione.

**Processo 2: Regole** restituisce valori di telemetria in entrata relativi a temperatura e umidità rispetto alle soglie per dispositivo. I valori di soglia vengono impostati nell'editor delle regole incluso nel dashboard della soluzione. Ogni coppia dispositivo/valore viene archiviata in base al timestamp in un BLOB che viene letto in Analisi di flusso come **dati di riferimento**. Il processo confronta tutti i valori non vuoti rispetto alla soglia impostata per il dispositivo. Se supera la condizione ">", il processo genera un evento di **avviso** che indica che la soglia è stata superata e indica il dispositivo, il valore e i valori di timestamp. Questo processo usa la definizione di query seguente per identificare i messaggi di telemetria che devono attivare un allarme:

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

Il processo invia l'output a un hub eventi per un'ulteriore elaborazione e salva i dettagli di ogni avviso nell'archivio BLOB da cui il dashboard della soluzione può leggere le informazioni dell'avviso.

**Processo 3: Telemetria** agisce in due modi sul flusso in entrata dei dati di telemetria del dispositivo. Il primo invia tutti i messaggi di telemetria dai dispositivi all'archivio BLOB permanente per l'archiviazione a lungo termine. Il secondo calcola i valori di umidità media, minima e massima in una finestra temporale scorrevole di cinque minuti e invia questi dati all'archivio BLOB. Il dashboard della soluzione legge i dati di telemetria dall'archivio BLOB per popolare i grafici. Questo processo usa la definizione di query seguente:

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

## Hub eventi

I processi **Informazioni sul dispositivo** e **Regole** di Analisi di flusso di Azure inviano i dati ad Hub eventi per l'inoltro affidabile al **processore di eventi** in esecuzione nel processo Web.

## Archiviazione di Azure

La soluzione usa l'archivio BLOB di Azure per rendere persistenti tutti i dati di telemetria non elaborati e sintetizzati dai dispositivi presenti nella soluzione. Il dashboard legge i dati di telemetria dall'archivio BLOB per popolare i grafici. Per visualizzare gli avvisi, il dashboard legge i dati dall'archivio BLOB che registra quando i valori dei dati di telemetria hanno superato i valori soglia configurati. La soluzione usa anche l'archivio BLOB per registrare i valori soglia impostati nel dashboard.

## WebJobs

Oltre a ospitare i simulatori dei dispositivi, i processi Web nella soluzione ospitano il **processore di eventi** eseguito in un processo Web di Azure che gestisce i messaggi informativi dei dispositivi e le risposte dei comandi. Usa:

- Messaggi informativi sul dispositivo per aggiornare il registro del dispositivo (archiviato nel database DocumentDB) con le informazioni correnti sul dispositivo.
- Messaggi di risposta ai comandi per aggiornare la cronologia dei comandi dispositivo (archiviata nel database DocumentDB).

## DocumentDB

La soluzione usa un database DocumentDB per archiviare le informazioni sui dispositivi connessi alla soluzione. Queste informazioni includono i metadati dei dispositivi e la cronologia dei comandi inviati ai dispositivi dal dashboard.

## App Web

### Dashboard di monitoraggio remoto
Questa pagina dell'applicazione Web usa i controlli JavaScript di Power BI per visualizzare i dati di telemetria inviati dai dispositivi. Vedere [PowerBI-visuals repo](https://www.github.com/Microsoft/PowerBI-visuals) (Repository di oggetti visivi di Power BI). La soluzione usa il processo di telemetria di Analisi di flusso di Azure per scrivere i dati di telemetria nell'archivio BLOB.


### Portale di amministrazione dei dispositivi

Questa app Web consente di:

- Effettuare il provisioning di un nuovo dispositivo. Questa azione permette di impostare l'ID univoco del dispositivo e di generare la chiave di autenticazione. Le informazioni sul dispositivo verranno scritte sia nel registro delle identità dell'hub IoT che nel database DocumentDB specifico della soluzione.
- Gestire le proprietà dei dispositivi. Questa azione include la visualizzazione delle proprietà esistenti e l'aggiornamento con nuove proprietà.
- Inviare comandi a un dispositivo.
- Visualizzare la cronologia dei comandi per un dispositivo.
- Abilitare e disabilitare i dispositivi.

## Passaggi successivi

I post del blog TechNet offrono altri dettagli sulla soluzione preconfigurata per il monitoraggio remoto:

- [Approfondimento sul monitoraggio remoto in IoT Suite](http://social.technet.microsoft.com/wiki/contents/articles/32941.iot-suite-under-the-hood-remote-monitoring.aspx)
- [Aggiunta di dispositivi veri e simulati per il monitoraggio remoto in IoT Suite](http://social.technet.microsoft.com/wiki/contents/articles/32975.iot-suite-remote-monitoring-adding-live-and-simulated-devices.aspx)

È possibile proseguire con l'introduzione a IoT Suite vedendo gli articoli seguenti:

- [Connettere il dispositivo alla soluzione preconfigurata per il monitoraggio remoto][lnk-connect-rm]
- [Autorizzazioni per il sito azureiotsuite.com][lnk-permissions]

[lnk-preconfigured-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-iothub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-asa]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-webjobs]: https://azure.microsoft.com/documentation/articles/websites-webjobs-resources/
[lnk-connect-rm]: iot-suite-connecting-devices.md
[lnk-permissions]: iot-suite-permissions.md

<!---HONumber=AcomDC_0817_2016-->