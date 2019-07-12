---
title: "Azure riferimento all'API v2 Status Monitor: Avviare una traccia | Microsoft Docs"
description: Riferimento API v2 di monitoraggio dello stato. Traccia di inizio. Raccogliere i log ETW da Status Monitor e Application Insights SDK.
services: application-insights
documentationcenter: .net
author: MS-TimothyMothra
manager: alexklim
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: tilee
ms.openlocfilehash: b6787134707273a76290adb723a9bc9012252ebd
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807047"
---
# <a name="status-monitor-v2-api-start-applicationinsightsmonitoringtrace-v040-alpha"></a>API v2 di Status Monitor: Start-ApplicationInsightsMonitoringTrace (v0.4.0-alpha)

Questo articolo descrive un cmdlet che è un membro del [modulo di Az.ApplicationMonitor PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

> [!IMPORTANT]
> Stato monitoraggio v2 è attualmente in anteprima pubblica.
> Questa versione di anteprima viene fornita senza un contratto di servizio e non è consigliabile per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate e alcune potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="description"></a>Descrizione

Raccoglie [eventi ETW](https://docs.microsoft.com/windows/desktop/etw/event-tracing-portal) dal senza il codice collegare runtime. Questo cmdlet è un'alternativa all'esecuzione [PerfView](https://github.com/microsoft/perfview).

Gli eventi raccolti verranno stampati nella console in tempo reale e viene salvato in un file con estensione ETL. File con estensione ETL output può essere aperto da [PerfView](https://github.com/microsoft/perfview) per un'analisi più approfondita.

Questo cmdlet verrà eseguito finché non raggiunge la durata del timeout (impostazione predefinita 5 minuti) o viene arrestata manualmente (`Ctrl + C`).

> [!IMPORTANT] 
> Questo cmdlet richiede una sessione di PowerShell con autorizzazioni di amministratore.

## <a name="examples"></a>Esempi

### <a name="how-to-collect-events"></a>Come raccogliere gli eventi

In genere sarebbe sufficiente richiedere raccogliere gli eventi per esaminare il motivo per cui l'applicazione non è in corso instrumentata.

Collegare il senza codice runtime genererà gli eventi ETW all'avvio di IIS e all'avvio dell'applicazione.

Per raccogliere questi eventi:
1. Nella console cmd con privilegi di amministratore eseguire `iisreset /stop` disattivare tutte le app web e IIS.
2. Eseguire questo cmdlet
3. Nella console cmd con privilegi di amministratore eseguire `iisreset /start` per avviare IIS.
4. Provare a passare all'app.
5. Dopo che l'app al termine del caricamento, si può arrestare manualmente lo (`Ctrl + C`) oppure attendere il timeout.

### <a name="what-events-to-collect"></a>Gli eventi da raccogliere

Quando la raccolta di eventi sono disponibili tre opzioni:
1. Usare l'opzione `-CollectSdkEvents` per raccogliere gli eventi generati da Application Insights SDK.
2. Usare l'opzione `-CollectRedfieldEvents` per raccogliere gli eventi generati dal monitoraggio dello stato e il Redfield Runtime. Questi log sono utili durante la diagnosi di IIS e avvio dell'applicazione.
3. Usare entrambe le opzioni per raccogliere entrambi i tipi di eventi.
4. Per impostazione predefinita, se viene specificato alcun commutatore che entrambi i tipi di evento verranno raccolti.


## <a name="parameters"></a>Parametri

### <a name="-maxdurationinminutes"></a>-MaxDurationInMinutes
**Facoltativo.** Usare questo parametro per impostare la durata questo script deve raccogliere gli eventi. Il valore predefinito è 5 minuti.

### <a name="-logdirectory"></a>-LogDirectory
**Facoltativo.** Usare questa opzione per impostare la directory di output del file con estensione ETL. Per impostazione predefinita, questo file verrà creato nella directory dei moduli di PowerShell. Verrà visualizzato il percorso completo durante l'esecuzione dello script.


### <a name="-collectsdkevents"></a>-CollectSdkEvents
**Facoltativo.** Usare questa opzione per raccogliere gli eventi di Application Insights SDK.

### <a name="-collectredfieldevents"></a>-CollectRedfieldEvents
**Facoltativo.** Usare questa opzione per raccogliere gli eventi da monitoraggio di stato e il runtime Redfield.

### <a name="-verbose"></a>-Verbose
**Parametro comune.** Usare questa opzione è attivata per i log dettagliati di output.



## <a name="output"></a>Output


### <a name="example-of-application-startup-logs"></a>Esempio di log di avvio dell'applicazione
```
PS C:\Windows\system32> Start-ApplicationInsightsMonitoringTrace -ColectRedfieldEvents
Starting...
Log File: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\logs\20190627_144217_ApplicationInsights_ETW_Trace.etl
Tracing enabled, waiting for events.
Tracing will timeout in 5 minutes. Press CTRL+C to cancel.

2:42:31 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Resolved variables to: MicrosoftAppInsights_ManagedHttpModulePath='C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll', MicrosoftAppInsights_ManagedHttpModuleType='Microsoft.ApplicationInsights.RedfieldIISModule.RedfieldIISModule'
2:42:31 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Resolved variables to: MicrosoftDiagnosticServices_ManagedHttpModulePath2='', MicrosoftDiagnosticServices_ManagedHttpModuleType2=''
2:42:31 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Environment variable 'MicrosoftDiagnosticServices_ManagedHttpModulePath2' or 'MicrosoftDiagnosticServices_ManagedHttpModuleType2' is null, skipping managed dll loading
2:42:31 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace MulticastHttpModule.constructor, success, 70 ms
2:42:31 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace Current assembly 'Microsoft.ApplicationInsights.RedfieldIISModule, Version=2.8.18.27202, Culture=neutral, PublicKeyToken=f23a46de0be5d6f3' location 'C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll'
2:42:31 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace Matched filter '.*'~'STATUSMONITORTE', '.*'~'DemoWithSql'
2:42:31 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace Lightup assembly calculated path: 'C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.Redfield.Lightup.dll'
2:42:31 PM EVENT: Microsoft-ApplicationInsights-FrameworkLightup Trace Loaded applicationInsights.config from assembly's resource Microsoft.ApplicationInsights.Redfield.Lightup, Version=2.8.18.27202, Culture=neutral, PublicKeyToken=f23a46de0be5d6f3/Microsoft.ApplicationInsights.Redfield.Lightup.ApplicationInsights-recommended.config
2:42:34 PM EVENT: Microsoft-ApplicationInsights-FrameworkLightup Trace Successfully attached ApplicationInsights SDK
2:42:34 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace RedfieldIISModule.LoadLightupAssemblyAndGetLightupHttpModuleClass, success, 2687 ms
2:42:34 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace RedfieldIISModule.CreateAndInitializeApplicationInsightsHttpModules(lightupHttpModuleClass), success
2:42:34 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace ManagedHttpModuleHelper, multicastHttpModule.Init() success, 3288 ms
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Resolved variables to: MicrosoftAppInsights_ManagedHttpModulePath='C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll', MicrosoftAppInsights_ManagedHttpModuleType='Microsoft.ApplicationInsights.RedfieldIISModule.RedfieldIISModule'
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Resolved variables to: MicrosoftDiagnosticServices_ManagedHttpModulePath2='', MicrosoftDiagnosticServices_ManagedHttpModuleType2=''
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Environment variable 'MicrosoftDiagnosticServices_ManagedHttpModulePath2' or 'MicrosoftDiagnosticServices_ManagedHttpModuleType2' is null, skipping managed dll loading
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace MulticastHttpModule.constructor, success, 0 ms
2:42:35 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace RedfieldIISModule.CreateAndInitializeApplicationInsightsHttpModules(lightupHttpModuleClass), success
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace ManagedHttpModuleHelper, multicastHttpModule.Init() success, 0 ms
Timeout Reached. Stopping...
```


## <a name="next-steps"></a>Passaggi successivi

Risoluzione dei problemi aggiuntive:

- Esaminare qui passaggi di risoluzione dei problemi aggiuntivi: https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-troubleshoot
- Rivedere le [riferimento all'API](status-monitor-v2-overview.md#powershell-api-reference) per altre informazioni sui parametri, potrebbe aver ignorato.
- Se è necessaria assistenza aggiuntiva, è possibile contattare Microsoft sul [GitHub](https://github.com/Microsoft/ApplicationInsights-Home/issues).



 Altri vantaggi v2 Status Monitor:
 - Usare la Guida alla [risolvere i problemi di](status-monitor-v2-troubleshoot.md) v2 Status Monitor.
 - [Ottenere la configurazione](status-monitor-v2-api-get-config.md) per confermare che le impostazioni sono state registrate correttamente.
 - [Ottenere lo stato](status-monitor-v2-api-get-status.md) per controllare il monitoraggio.
