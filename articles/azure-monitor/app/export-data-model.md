---
title: Modello di dati di Azure Application Insights | Microsoft Docs
description: Descrive le proprietà esportate da esportazione continua in JSON e usate come filtri.
ms.topic: conceptual
ms.date: 01/08/2019
ms.openlocfilehash: 9891bea1d52c61197fa32fa5c0764df5450b563c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81536846"
---
# <a name="application-insights-export-data-model"></a>Modello di dati di esportazione di Application Insights
Questa tabella elenca le proprietà di telemetria inviate al portale dagli SDK di [Application Insights](../../azure-monitor/app/app-insights-overview.md) .
Queste proprietà saranno visualizzate nell'output dei dati di [Esportazione continua](export-telemetry.md).
Sono visibili anche nei filtri delle proprietà in [Esplora metriche](../../azure-monitor/platform/metrics-charts.md) e [Ricerca diagnostica](../../azure-monitor/app/diagnostic-search.md).

Punti da notare:

* `[0]` in queste tabelle indica un punto nel percorso in cui è necessario inserire un indice, ma non è sempre 0.
* Le durate sono espresse in decimi di microsecondo, quindi 10000000 == 1 secondo.
* Date e ore sono in formato UTC e vengono specificate nel formato ISO `yyyy-MM-DDThh:mm:ss.sssZ`


## <a name="example"></a>Esempio
    // A server report about an HTTP request
    {
    "request": [
      {
        "urlData": { // derived from 'url'
          "host": "contoso.org",
          "base": "/",
          "hashTag": ""
        },
        "responseCode": 200, // Sent to client
        "success": true, // Default == responseCode<400
        // Request id becomes the operation id of child events
        "id": "fCOhCdCnZ9I=",  
        "name": "GET Home/Index",
        "count": 1, // 100% / sampling rate
        "durationMetric": {
          "value": 1046804.0, // 10000000 == 1 second
          // Currently the following fields are redundant:
          "count": 1.0,
          "min": 1046804.0,
          "max": 1046804.0,
          "stdDev": 0.0,
          "sampledValue": 1046804.0
        },
        "url": "/"
      }
    ],
    "internal": {
      "data": {
        "id": "7f156650-ef4c-11e5-8453-3f984b167d05",
        "documentVersion": "1.61"
      }
    },
    "context": {
      "device": { // client browser
        "type": "PC",
        "screenResolution": { },
        "roleInstance": "WFWEB14B.fabrikam.net"
      },
      "application": { },
      "location": { // derived from client ip
        "continent": "North America",
        "country": "United States",
        // last octagon is anonymized to 0 at portal:
        "clientip": "168.62.177.0",
        "province": "",
        "city": ""
      },
      "data": {
        "isSynthetic": true, // we identified source as a bot
        // percentage of generated data sent to portal:
        "samplingRate": 100.0,
        "eventTime": "2016-03-21T10:05:45.7334717Z" // UTC
      },
      "user": {
        "isAuthenticated": false,
        "anonId": "us-tx-sn1-azr", // bot agent id
        "anonAcquisitionDate": "0001-01-01T00:00:00Z",
        "authAcquisitionDate": "0001-01-01T00:00:00Z",
        "accountAcquisitionDate": "0001-01-01T00:00:00Z"
      },
      "operation": {
        "id": "fCOhCdCnZ9I=",
        "parentId": "fCOhCdCnZ9I=",
        "name": "GET Home/Index"
      },
      "cloud": { },
      "serverDevice": { },
      "custom": { // set by custom fields of track calls
        "dimensions": [ ],
        "metrics": [ ]
      },
      "session": {
        "id": "65504c10-44a6-489e-b9dc-94184eb00d86",
        "isFirst": true
      }
    }
  }

## <a name="context"></a>Context
Tutti i tipi di telemetria sono accompagnati da una sezione di contesto. Non tutti questi campi vengono trasmessi con ogni punto dati.

| Path | Type | Note |
| --- | --- | --- |
| context.custom.dimensions [0] |oggetto [ ] |Coppie di stringhe chiave-valore impostate dal parametro delle proprietà personalizzate. La lunghezza massima delle chiavi 100, la lunghezza massima dei valori è 1024. Più di 100 valori univoci. La proprietà può essere cercata, ma non può essere usata per la segmentazione. Massimo 200 chiavi per ikey. |
| context.custom.metrics [0] |oggetto [ ] |Coppie di chiave-valore impostate dai parametri delle misurazioni personalizzate e da TrackMetrics. La lunghezza massima delle chiavi 100, i valori possono essere numerici. |
| context.data.eventTime |stringa |UTC |
| context.data.isSynthetic |boolean |La richiesta proviene da un robot o un test Web. |
| context.data.samplingRate |d'acquisto |Percentuale di telemetria generata dall'SDK inviato al portale. L'intervallo è 0,0-100,0. |
| context.device |oggetto |Dispositivo client |
| context.device.browser |stringa |IE, Chrome, ... |
| context.device.browserVersion |stringa |Chrome 48.0, ... |
| context.device.deviceModel |stringa | |
| context.device.deviceName |stringa | |
| context.device.id |stringa | |
| context.device.locale |stringa |en-GB, de-DE, ... |
| context.device.network |stringa | |
| context.device.oemName |stringa | |
| context.device.os |stringa | |
| context.device.osVersion |stringa |Sistema operativo host |
| context.device.roleInstance |stringa |ID dell'host server |
| context.device.roleName |stringa | |
| context.device.screenResolution |stringa | |
| context.device.type |stringa |PC, Browser,... |
| context.location |oggetto |Derivata da `clientip`. |
| context.location.city |stringa |Derivato da `clientip`, se noto |
| context.location.clientip |stringa |L'ultimo ottagono viene reso anonimo come 0. |
| context.location.continent |stringa | |
| context.location.country |stringa | |
| context.location.province |stringa |Stato o provincia |
| context.operation.id |stringa |Gli elementi con lo stesso `operation id` vengono visualizzati come elementi correlati nel portale. In genere `request id`. |
| context.operation.name |stringa |URL o nome richiesta |
| context.operation.parentId |stringa |Consente elementi correlati annidati. |
| context.session.id |stringa |`Id`di un gruppo di operazioni dalla stessa origine. Un periodo di 30 minuti senza operazioni segnala la fine di una sessione. |
| context.session.isFirst |boolean | |
| context.user.accountAcquisitionDate |stringa | |
| context.user.accountId |stringa | |
| context.user.anonAcquisitionDate |stringa | |
| context.user.anonId |stringa | |
| context.user.authAcquisitionDate |stringa |[Utente autenticato](../../azure-monitor/app/api-custom-events-metrics.md#authenticated-users) |
| context.user.authId |stringa | |
| context.user.isAuthenticated |boolean | |
| context.user.storeRegion |stringa | |
| internal.data.documentVersion |stringa | |
| internal.data.id |stringa | `Unique id`assegnato quando un elemento viene inserito in Application Insights |

## <a name="events"></a>Events
Eventi personalizzati generati da [TrackEvent()](../../azure-monitor/app/api-custom-events-metrics.md#trackevent).

| Path | Type | Note |
| --- | --- | --- |
| event [0] count |integer |100/(frequenza di[campionamento](../../azure-monitor/app/sampling.md) ). Ad esempio, 4 =&gt; 25%. |
| event [0] name |stringa |Nome evento.  Lunghezza massima: 250. |
| event [0] url |stringa | |
| event [0] urlData.base |stringa | |
| event [0] urlData.host |stringa | |

## <a name="exceptions"></a>Eccezioni
Segnala le [eccezioni](../../azure-monitor/app/asp-net-exceptions.md) nel server e nel browser.

| Path | Type | Note |
| --- | --- | --- |
| basicException [0] assembly |stringa | |
| basicException [0] count |integer |100/(frequenza di[campionamento](../../azure-monitor/app/sampling.md) ). Ad esempio, 4 =&gt; 25%. |
| basicException [0] exceptionGroup |stringa | |
| basicException [0] exceptionType |stringa | |
| basicException [0] failedUserCodeMethod |stringa | |
| basicException [0] failedUserCodeAssembly |stringa | |
| basicException [0] handledAt |stringa | |
| basicException [0] hasFullStack |boolean | |
| basicexception [0]`id` |stringa | |
| basicException [0] method |stringa | |
| basicException [0] message |stringa |Messaggio dell'eccezione. Lunghezza massima: 10 K. |
| basicException [0] outerExceptionMessage |stringa | |
| basicException [0] outerExceptionThrownAtAssembly |stringa | |
| basicException [0] outerExceptionThrownAtMethod |stringa | |
| basicException [0] outerExceptionType |stringa | |
| basicException [0] outerId |stringa | |
| basicException [0] parsedStack [0] assembly |stringa | |
| basicException [0] parsedStack [0] fileName |stringa | |
| basicException [0] parsedStack [0] level |integer | |
| basicException [0] parsedStack [0] line |integer | |
| basicException [0] parsedStack [0] method |stringa | |
| basicException [0] stack |stringa |Lunghezza massima: 10 K. |
| basicException [0] typeName |stringa | |

## <a name="trace-messages"></a>Messaggi di traccia
Inviati da [TrackTrace](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace) e dagli [adattatori di registrazione](../../azure-monitor/app/asp-net-trace-logs.md).

| Path | Type | Note |
| --- | --- | --- |
| message [0] loggerName |stringa | |
| message [0] parameters |stringa | |
| message [0] raw |stringa |Messaggio del log, lunghezza massima 10.000 caratteri. |
| message [0] severityLevel |stringa | |

## <a name="remote-dependency"></a>Dipendenza remota
Inviata da TrackDependency. Usata per segnalare le prestazioni e l'utilizzo delle [chiamate alle dipendenze](../../azure-monitor/app/asp-net-dependencies.md) nel server e delle chiamate AJAX nel browser.

| Path | Type | Note |
| --- | --- | --- |
| remoteDependency [0] async |boolean | |
| remoteDependency [0] baseName |stringa | |
| remoteDependency [0] commandName |stringa |Ad esempio "home/index" |
| remoteDependency [0] count |integer |100/(frequenza di[campionamento](../../azure-monitor/app/sampling.md) ). Ad esempio, 4 =&gt; 25%. |
| remoteDependency [0] dependencyTypeName |stringa |HTTP, SQL... |
| remoteDependency [0] durationMetric.value |d'acquisto |Tempo intercorso tra la chiamata e il completamento della risposta da parte di una dipendenza |
| remoteDependency [0]`id` |stringa | |
| remoteDependency [0] name |stringa |URL. Lunghezza massima: 250. |
| remoteDependency [0] resultCode |stringa |Dalla dipendenza HTTP |
| remoteDependency [0] success |boolean | |
| remoteDependency [0] type |stringa |HTTP, SQL... |
| remoteDependency [0] url |stringa |Lunghezza massima: 2000 |
| remoteDependency [0] urlData.base |stringa |Lunghezza massima: 2000 |
| remoteDependency [0] urlData.hashTag |stringa | |
| remoteDependency [0] urlData.host |stringa |Lunghezza massima: 200 |

## <a name="requests"></a>Requests
Inviate da [TrackRequest](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest). I moduli standard le usano per segnalare il tempo di risposta del server, calcolato nel server.

| Path | Type | Note |
| --- | --- | --- |
| request [0] count |integer |100/(frequenza di[campionamento](../../azure-monitor/app/sampling.md) ). Ad esempio, 4 =&gt; 25%. |
| request [0] durationMetric.value |d'acquisto |Tempo tra l'arrivo della richiesta e la risposta. 1e7 == 1 s |
| richiesta [0]`id` |stringa |`Operation id` |
| request [0] name |stringa |GET/POST + base URL.  Lunghezza massima: 250 |
| request [0] responseCode |integer |Risposta HTTP inviata al client |
| request [0] success |boolean |Valore predefinito == (responseCode &lt; 400) |
| request [0] url |stringa |Host non incluso |
| request [0] urlData.base |stringa | |
| request [0] urlData.hashTag |stringa | |
| request [0] urlData.host |stringa | |

## <a name="page-view-performance"></a>Prestazioni visualizzazioni pagina
Inviate dal browser. Misura il tempo necessario per elaborare una pagina, da quando l'utente avvia la richiesta al completamento della visualizzazione (escluse le chiamate AJAX asincrone).

I valori del contesto indicano la versione del sistema operativo client e del browser.

| Path | Type | Note |
| --- | --- | --- |
| clientPerformance [0] clientProcess.value |integer |Tempo compreso tra la fine della ricezione del codice HTML e la visualizzazione della pagina. |
| clientPerformance [0] name |stringa | |
| clientPerformance [0] networkConnection.value |integer |Tempo necessario per stabilire una connessione di rete. |
| clientPerformance [0] receiveRequest.value |integer |Tempo compreso tra la fine dell'invio della richiesta e la ricezione del codice HTML nella risposta. |
| clientPerformance [0] sendRequest.value |integer |Tempo necessario per inviare la richiesta HTTP. |
| clientPerformance [0] total.value |integer |Tempo compreso tra l'inizio dell'invio della richiesta e la visualizzazione della pagina. |
| clientPerformance [0] url |stringa |URL di questa richiesta |
| clientPerformance [0] urlData.base |stringa | |
| clientPerformance [0] urlData.hashTag |stringa | |
| clientPerformance [0] urlData.host |stringa | |
| clientPerformance [0] urlData.protocol |stringa | |

## <a name="page-views"></a>Visualizzazioni pagina
Inviate da trackPageView() o [stopTrackPage](../../azure-monitor/app/api-custom-events-metrics.md#page-views)

| Path | Type | Note |
| --- | --- | --- |
| view [0] count |integer |100/(frequenza di[campionamento](../../azure-monitor/app/sampling.md) ). Ad esempio, 4 =&gt; 25%. |
| view [0] durationMetric.value |integer |Valore facoltativo impostato in trackPageView() o da startTrackPage() - stopTrackPage(). Non corrisponde ai valori di clientPerformance. |
| view [0] name |stringa |Titolo della pagina.  Lunghezza massima: 250 |
| view [0] url |stringa | |
| view [0] urlData.base |stringa | |
| view [0] urlData.hashTag |stringa | |
| view [0] urlData.host |stringa | |

## <a name="availability"></a>Disponibilità
Segnala i [test Web di disponibilità](../../azure-monitor/app/monitor-web-app-availability.md).

| Path | Type | Note |
| --- | --- | --- |
| availability [0] availabilityMetric.name |stringa |availability |
| availability [0] availabilityMetric.value |d'acquisto |1,0 o 0,0 |
| availability [0] count |integer |100/(frequenza di[campionamento](../../azure-monitor/app/sampling.md) ). Ad esempio, 4 =&gt; 25%. |
| availability [0] dataSizeMetric.name |stringa | |
| availability [0] dataSizeMetric.value |integer | |
| availability [0] durationMetric.name |stringa | |
| availability [0] durationMetric.value |d'acquisto |Durata del test. 1e7==1 s |
| availability [0] message |stringa |Diagnostica di errori |
| availability [0] result |stringa |Esito positivo o negativo |
| availability [0] runLocation |stringa |Origine geografica della richiesta HTTP |
| availability [0] testName |stringa | |
| availability [0] testRunId |stringa | |
| availability [0] testTimestamp |stringa | |

## <a name="metrics"></a>Metriche
Generata da TrackMetric().

Il valore della metrica è disponibile in context.custom.metrics[0]

Ad esempio:

    {
     "metric": [ ],
     "context": {
     ...
     "custom": {
        "dimensions": [
          { "ProcessId": "4068" }
        ],
        "metrics": [
          {
            "dispatchRate": {
              "value": 0.001295,
              "count": 1.0,
              "min": 0.001295,
              "max": 0.001295,
              "stdDev": 0.0,
              "sampledValue": 0.001295,
              "sum": 0.001295
            }
          }
         } ] }
    }

## <a name="about-metric-values"></a>Informazioni sui valori della metrica
I valori della metrica, nei report della metrica e altrove, vengono segnalati con una struttura di oggetti standard. Ad esempio:

      "durationMetric": {
        "name": "contoso.org",
        "type": "Aggregation",
        "value": 468.71603053650279,
        "count": 1.0,
        "min": 468.71603053650279,
        "max": 468.71603053650279,
        "stdDev": 0.0,
        "sampledValue": 468.71603053650279
      }

Attualmente (ma la situazione potrebbe cambiare in futuro) di tutti i valori segnalati dai moduli SDK standard sono utili `count==1` e solo i campi `name` e `value`. L'unico caso in cui sarebbero diversi è quello in cui si scrivono chiamate TrackMetric personalizzate in cui si impostano gli altri parametri.

Lo scopo degli altri campi è quello di consentire l'aggregazione della metrica nell'SDK, per ridurre il traffico verso il portale. È possibile, ad esempio, calcolare la media di diverse letture successive prima di inviare il report di ogni metrica. Si calcolerà quindi la deviazione minima, massima e standard e il valore di aggregazione (somma o media) e si imposterà il conteggio sul numero di letture rappresentate dal report.

Nelle tabelle precedenti sono stati omessi il conteggio dei campi usati raramente, i valori minimo e massimo, stdDev e sampledValue.

Se è necessario ridurre il volume della telemetria, anziché aggregare in anticipo la metrica, è possibile usare il [campionamento](../../azure-monitor/app/sampling.md) .

### <a name="durations"></a>Durate
Se non indicato diversamente, le durate vengono espresse in decimi di microsecondo, quindi 10000000,0 corrisponde a 1 secondo.

## <a name="see-also"></a>Vedere anche
* [Application Insights](../../azure-monitor/app/app-insights-overview.md)
* [Esportazione continua](export-telemetry.md)
* [Esempi di codice](export-telemetry.md#code-samples)
