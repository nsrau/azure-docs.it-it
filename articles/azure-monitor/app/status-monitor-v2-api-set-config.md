---
title: Informazioni di riferimento sull'API di Azure Application Insights AgentAzure Application Insights Agent API reference
description: Informazioni di riferimento sull'API dell'agente di Application Insights.Application Insights Agent API reference. Set-ApplicationInsightsMonitoringConfig. Monitorare le prestazioni del sito Web senza ridistribuire il sito Web. Funziona con ASP.NET app Web ospitate in locale, nelle macchine virtuali o in Azure.Works with a web apps hosted on-premises, in VMs, or on Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: c47b9b5f297fa62c474e6c29737d6d11b887130d
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537475"
---
# <a name="application-insights-agent-api-set-applicationinsightsmonitoringconfig"></a>API dell'agente di Application Insights: Set-ApplicationInsightsMonitoringConfig

In questo documento viene descritto un cmdlet membro del [modulo PowerShell Az.ApplicationMonitor](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

## <a name="description"></a>Descrizione

Imposta il file di configurazione senza eseguire una reinstallazione completa.
Riavviare IIS per rendere effettive le modifiche.

> [!IMPORTANT] 
> Questo cmdlet richiede una sessione di PowerShell con autorizzazioni di amministratore.


## <a name="examples"></a>Esempi

### <a name="example-with-a-single-instrumentation-key"></a>Esempio con una singola chiave di strumentazioneExample with a single instrumentation key
In questo esempio, a tutte le app nel computer corrente verrà assegnata una singola chiave di strumentazione.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

### <a name="example-with-an-instrumentation-key-map"></a>Esempio con una mappa delle chiavi di strumentazioneExample with an instrumentation key map
Esempio:
- `MachineFilter`corrisponde al computer corrente `'.*'` utilizzando il carattere jolly.
- `AppFilter='WebAppExclude'`fornisce `null` una chiave di strumentazione. L'app specificata non verrà instrumentata.
- `AppFilter='WebAppOne'`assegna all'app specificata una chiave di strumentazione univoca.
- `AppFilter='WebAppTwo'`assegna all'app specificata una chiave di strumentazione univoca.
- Infine, `AppFilter` usa `'.*'` anche il carattere jolly per trovare la corrispondenza con tutte le app Web che non corrispondono alle regole precedenti e assegnare una chiave di strumentazione predefinita.
- Gli spazi vengono aggiunti per la leggibilità.

```powershell
Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap `
       @(@{MachineFilter='.*';AppFilter='WebAppExclude'},
          @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'}},
          @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'}},
          @{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'}})
```

## <a name="parameters"></a>Parametri

### <a name="-instrumentationkey"></a>-InstrumentationKey (Chiave di Strumentazione)
**Obbligatorio.** Usare questo parametro per fornire una singola chiave di strumentazione per l'utilizzo da parte di tutte le app nel computer di destinazione.

### <a name="-instrumentationkeymap"></a>-InstrumentationKeyMap (mappa delle chiavi di strumentazione)
**Obbligatorio.** Usare questo parametro per fornire più chiavi di strumentazione e un mapping delle chiavi di strumentazione usate da ogni app.
È possibile creare un singolo script `MachineFilter`di installazione per più computer impostando .

> [!IMPORTANT]
> Le app corrisponderanno alle regole nell'ordine in cui vengono fornite le regole. Quindi è necessario specificare prima le regole più specifiche per ultime e le regole più generiche.

#### <a name="schema"></a>SCHEMA
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'})`

- **MachineFilter** è un'espressione regolare c'è obbligatoria del nome del computer o della macchina virtuale.
    - '.' corrisponderà a tutti
    - 'ComputerName' corrisponderà solo ai computer con il nome specificato.
- **AppFilter** è un'espressione regolare c'è obbligatoria del nome del computer o della macchina virtuale.
    - '.' corrisponderà a tutti
    - 'ApplicationName' corrisponderà solo alle app IIS con il nome specificato.
- **InstrumentationKey** è necessario per abilitare il monitoraggio delle app che corrispondono ai due filtri precedenti.
    - Lasciare null questo valore se si desidera definire regole per escludere il monitoraggio.


### <a name="-verbose"></a>-Verbose
**Parametro comune.** Utilizzare questa opzione per visualizzare i registri dettagliati.


## <a name="output"></a>Output

Per impostazione predefinita, nessun output.

#### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkey"></a>Esempio di output dettagliato dall'impostazione del file di configurazione tramite -InstrumentationKeyExample verbose output from setting the config file via -InstrumentationKey

```
VERBOSE: Operation: InstallWithIkey
VERBOSE: InstrumentationKeyMap parsed:
Filters:
0)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx AppFilter: .* MachineFilter: .*
VERBOSE: set config file
VERBOSE: Config File Path:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config
```

#### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkeymap"></a>Esempio di output dettagliato dall'impostazione del file di configurazione tramite -InstrumentationKeyMapExample verbose output from setting the config file via -InstrumentationKeyMap

```
VERBOSE: Operation: InstallWithIkeyMap
VERBOSE: InstrumentationKeyMap parsed:
Filters:
0)InstrumentationKey:  AppFilter: WebAppExclude MachineFilter: .*
1)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2 AppFilter: WebAppTwo MachineFilter: .*
2)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault AppFilter: .* MachineFilter: .*
VERBOSE: set config file
VERBOSE: Config File Path:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config
```

## <a name="next-steps"></a>Passaggi successivi

  Visualizzare i dati di telemetria:
 - [Esplora le metriche](../../azure-monitor/platform/metrics-charts.md) per monitorare le prestazioni e l'utilizzo.
- [Cerca eventi e log](../../azure-monitor/app/diagnostic-search.md) per diagnosticare i problemi.
- [Usa Analytics](../../azure-monitor/app/analytics.md) per query più avanzate.
- [Creare dashboard](../../azure-monitor/app/overview-dashboard.md).
 
 Aggiungere altri dati di telemetria:
 - [Creare test Web](monitor-web-app-availability.md) per assicurarsi che il sito rimanga attivo.
- [Aggiungere dati di telemetria del client Web](../../azure-monitor/app/javascript.md) per visualizzare le eccezioni dal codice della pagina Web e abilitare le chiamate di traccia.
- [Aggiungere Application Insights SDK al codice](../../azure-monitor/app/asp-net.md) per inserire chiamate di traccia e logAdd the Application Insights SDK to your code so you can insert trace and log calls
 
 Fai di più con Application Insights Agent:
 - Usare la guida per [risolvere i problemi relativi](status-monitor-v2-troubleshoot.md) all'agente application Insights.Use our guide to troubleshoot Application Insights Agent.
 - [Ottenere la configurazione](status-monitor-v2-api-get-config.md) per verificare che le impostazioni siano state registrate correttamente.
 - [Ottenere lo stato](status-monitor-v2-api-get-status.md) per controllare il monitoraggio.
