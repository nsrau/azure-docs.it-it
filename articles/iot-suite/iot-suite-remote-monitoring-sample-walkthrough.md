<properties
 pageTitle="Procedura dettagliata della soluzione preconfigurata per il monitoraggio remoto| Microsoft Azure"
 description="Descrizione della soluzione preconfigurata per il monitoraggio remoto di Azure IoT e relativa architettura."
 services=""
 suite="iot-suite"
 documentationCenter=""
 authors="stevehob"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-suite"
 ms.devlang="na"
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="03/02/2016"
 ms.author="stevehob"/>

# Procedura dettagliata della soluzione preconfigurata per il monitoraggio remoto

## Introduzione

La soluzione preconfigurata di monitoraggio remoto IoT Suite consente il monitoraggio end-to-end di base per uno scenario aziendale in cui si gestiscono più computer in località remote. La soluzione combina i servizi chiave di Azure IoT Suite per fornire un'implementazione generica dello scenario aziendale ed è un punto di partenza per i clienti che intendono implementare questo tipo di soluzione IoT per soddisfare i propri requisiti aziendali specifici.

## Architettura logica

Il diagramma seguente illustra i componenti logici della soluzione preconfigurata:

![](media/iot-suite-remote-monitoring-sample-walkthrough/remote-monitoring-architecture.png)


### Dispositivi simulati

Nella soluzione preconfigurata il dispositivo simulato rappresenta un dispositivo di raffreddamento (ad esempio, un condizionatore d'aria di un edificio o un'unita di trattamento aria di una struttura). Ogni dispositivo simulato invia i messaggi di telemetria seguenti all'hub IoT:


| Message | Descrizione |
|----------|-------------|
| Startup | Quando il dispositivo viene avviato, invia un messaggio **device-info** contenente informazioni su se stesso, ad esempio l'ID del dispositivo, i metadati, un elenco di comandi supportati dal dispositivo e la relativa configurazione corrente. |


I dispositivi simulati inviano le proprietà del dispositivo seguenti come metadati:

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

Il simulatore effettua il seeding di queste proprietà nei dispositivi simulati con valori di esempio. Ogni volta che il simulatore inizializza un dispositivo simulato, quest'ultimo registra i metadati predefiniti nell'hub IoT. Si noti che questa registrazione sovrascrive gli eventuali aggiornamenti di metadati eseguiti nel portale per il dispositivo.


I dispositivi simulati possono gestire i comandi seguenti inviati da un hub IoT:

| Comando | Descrizione |
|------------------------|-----------------------------------------------------|
| PingDevice | Invia un _ping_ per il dispositivo per verificare che sia attivo. |
| StartTelemetry | Avvia l'invio dei dati di telemetria dal dispositivo. |
| StopTelemetry | Arresta l'invio dei dati di telemetria dal dispositivo. |
| ChangeSetPointTemp | Modifica il valore del punto dati impostato in base al quale vengono generati i dati casuali. |
| DiagnosticTelemetry | Attiva il simulatore del dispositivo per l'invio di un valore di telemetria aggiuntivo (externalTemp) |
| ChangeDeviceState | Modifica una proprietà di stato estesa per il dispositivo e invia il messaggio di informazioni sul dispositivo dal dispositivo. |


Il riconoscimento di comando del dispositivo viene fornito tramite l'hub IoT.


### Processi di Analisi di flusso di Azure


**Processo 1: Informazioni sul dispositivo** filtra i messaggi informativi sul dispositivo dal flusso di messaggi in arrivo e li invia a un endpoint dell'hub eventi. Un dispositivo invia messaggi di informazioni di dispositivo all'avvio e in risposta a un comando **SendDeviceInfo**. Questo processo usa la definizione di query seguente:

```
SELECT * FROM DeviceDataStream Partition By PartitionId WHERE  ObjectType = 'DeviceInfo'
```

**Processo 2: Regole** restituisce valori di telemetria in entrata relativi a temperatura e umidità rispetto alle soglie per dispositivo. I valori di soglia vengono impostati nell'editor delle regole incluso nella soluzione. Ogni coppia dispositivo/valore viene archiviata in base al timestamp in un BLOB che viene letto in Analisi di flusso come **Dati di riferimento**. Il processo confronta tutti i valori non vuoti rispetto alla soglia impostata per il dispositivo. Se supera la condizione ' >', il processo genera un evento di **allarme** che indica che la soglia è stata superata e fornisce il dispositivo, il valore e i valori di timestamp. Questo processo usa la definizione di query seguente:

```
WITH AlarmsData AS 
(
SELECT
     Stream.DeviceID,
     'Temperature' as ReadingType,
     Stream.Temperature as Reading,
     Ref.Temperature as Threshold,
     Ref.TemperatureRuleOutput as RuleOutput,
     Stream.EventEnqueuedUtcTime AS [Time]
FROM IoTTelemetryStream Stream
JOIN DeviceRulesBlob Ref ON Stream.DeviceID = Ref.DeviceID
WHERE
     Ref.Temperature IS NOT null AND Stream.Temperature > Ref.Temperature

UNION ALL

SELECT
     Stream.DeviceID,
     'Humidity' as ReadingType,
     Stream.Humidity as Reading,
     Ref.Humidity as Threshold,
     Ref.HumidityRuleOutput as RuleOutput,
     Stream.EventEnqueuedUtcTime AS [Time]
FROM IoTTelemetryStream Stream
JOIN DeviceRulesBlob Ref ON Stream.DeviceID = Ref.DeviceID
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

**Processo 3: Telemetria** agisce in due modi sul flusso in entrata dei dati di telemetria del dispositivo. Il primo invia tutti i messaggi di telemetria dai dispositivi all'archivio BLOB permanente. Il secondo calcola i valori di umidità media, minima e massima in una finestra temporale scorrevole di cinque minuti. I dati vengono inviati anche all'archivio BLOB. Questo processo usa la definizione di query seguente:

```
WITH 
    [StreamData]
AS (
    SELECT
        *
    FROM 
      [IoTHubStream] 
    WHERE
        [ObjectType] IS NULL -- Filter out device info and command responses
) 

SELECT
    *
INTO
    [Telemetry]
FROM
    [StreamData]

SELECT
    DeviceId,
    AVG (Humidity) AS [AverageHumidity], 
    MIN(Humidity) AS [MinimumHumidity], 
    MAX(Humidity) AS [MaxHumidity], 
    5.0 AS TimeframeMinutes 
INTO
    [TelemetrySummary]
FROM
    [StreamData]
WHERE
    [Humidity] IS NOT NULL
GROUP BY
    DeviceId, 
    SlidingWindow (mi, 5)
```

### Processore di eventi

Il **processore di eventi** gestisce i messaggi informativi sul dispositivo e le risposte ai comandi. Usa:

- Messaggi informativi sul dispositivo per aggiornare il registro del dispositivo (archiviato nel database DocumentDB) con le informazioni correnti sul dispositivo.
- Messaggi di risposta ai comandi per aggiornare la cronologia dei comandi dispositivo (archiviata nel database DocumentDB).

## Ecco come iniziare

In questa sezione illustra i componenti della soluzione, viene descritto il caso di utilizzo previsto e fornisce esempi.

### Dashboard di monitoraggio remoto
Questa pagina dell'applicazione Web usa i controlli javascript di PowerBI (vedere la pagina relativa al [repository di elementi visivi di PowerBI](https://www.github.com/Microsoft/PowerBI-visuals)) per visualizzare i dati di output dei processi di Analisi di flusso nell'archivio BLOB.


### Portale di amministrazione dei dispositivi

Questa app Web consente di:

- Effettuare il provisioning di un nuovo dispositivo che imposta l'ID univoco del dispositivo e genera la chiave di autenticazione.
- Gestire le proprietà del dispositivo che include la visualizzazione delle proprietà esistenti e l'aggiornamento con nuove proprietà.
- Inviare comandi a un dispositivo.
- Visualizzare la cronologia dei comandi per un dispositivo.

### Osservare il comportamento della soluzione cloud
È possibile visualizzare le risorse con provisioning visitando il [portale di Azure](https://portal.azure.com) e passando al gruppo di risorse con il nome della soluzione specificata.

![](media/iot-suite-remote-monitoring-sample-walkthrough/azureportal_01.png)

Quando si esegue l'esempio per la prima volta, sono disponibili quattro dispositivi simulati e preconfigurati:

![](media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_01.png)

Per aggiungere un nuovo dispositivo simulato, è possibile usare il portale di amministrazione dei dispositivi:

![](media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_02.png)

Inizialmente, lo stato del nuovo dispositivo nel portale di amministrazione dei dispositivi è **in sospeso**:

![](media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_03.png)

Quando l'applicazione ha completato la distribuzione del dispositivo simulato, si vedrà che lo stato del dispositivo cambia in **In esecuzione** nel portale di amministrazione dei dispositivi, come illustrato nella schermata seguente. Il processo **DeviceInfo** di Analisi di flusso invia informazioni sullo stato dal dispositivo al portale di amministrazione dei dispositivi.

![](media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_04.png)

Tramite il portale della soluzione è possibile inviare comandi, ad esempio **ChangeSetPointTemp** al dispositivo:

![](media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_05.png)

Quando il dispositivo segnala che il comando è stato eseguito correttamente, lo stato cambia in **Operazione riuscita**:

![](media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_06.png)

Tramite il portale della soluzione è possibile cercare i dispositivi con caratteristiche specifiche, ad esempio un numero di modello:

![](media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_07.png)

È possibile disabilitare un dispositivo e dopo aver disabilitato è possibile rimuoverlo:

![](media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_08.png)

<!---HONumber=AcomDC_0309_2016-->