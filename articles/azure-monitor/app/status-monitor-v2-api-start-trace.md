---
title: 'Informazioni di riferimento sulle API di Azure Status Monitor V2: Avvia traccia | Microsoft Docs'
description: Riferimento all'API Status Monitor V2. Start-Trace. Raccogliere i log ETW da Status Monitor e Application Insights SDK.
services: application-insights
documentationcenter: .net
author: TimothyMothra
manager: alexklim
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: tilee
ms.openlocfilehash: f4c43e6bdb70687606041c2f0859ab072db2b587
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/23/2019
ms.locfileid: "71200366"
---
# <a name="status-monitor-v2-api-start-applicationinsightsmonitoringtrace"></a>API Status Monitor V2: Start-ApplicationInsightsMonitoringTrace

Questo articolo descrive un cmdlet che fa parte del modulo di [PowerShell AZ. ApplicationMonitor](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

## <a name="description"></a>Descrizione

Raccoglie [gli eventi ETW](https://docs.microsoft.com/windows/desktop/etw/event-tracing-portal) dal runtime di associazione non codificabile. Questo cmdlet rappresenta un'alternativa all'esecuzione di [PerfView](https://github.com/microsoft/perfview).

Gli eventi raccolti verranno stampati nella console in tempo reale e salvati in un file ETL. Il file ETL di output può essere aperto da [PerfView](https://github.com/microsoft/perfview) per un'analisi più approfondita.

Questo cmdlet verrà eseguito fino a quando non raggiungerà la durata del timeout (valore predefinito di 5 minuti`Ctrl + C`) o verrà interrotto manualmente ().

> [!IMPORTANT] 
> Questo cmdlet richiede una sessione di PowerShell con autorizzazioni di amministratore.

## <a name="examples"></a>Esempi

### <a name="how-to-collect-events"></a>Come raccogliere gli eventi

Normalmente si richiede di raccogliere gli eventi per esaminare il motivo per cui l'applicazione non viene instrumentata.

Il runtime di associazione codificata genera eventi ETW all'avvio di IIS e all'avvio dell'applicazione.

Per raccogliere questi eventi:
1. In una console CMD con privilegi di amministratore eseguire `iisreset /stop` per disattivare IIS e tutte le app Web.
2. Esegui questo cmdlet
3. In una console CMD con privilegi di amministratore eseguire `iisreset /start` per avviare IIS.
4. Provare a passare all'app.
5. Al termine del caricamento dell'app, è possibile arrestarla manualmente`Ctrl + C`() o attendere il timeout.

### <a name="what-events-to-collect"></a>Eventi da raccogliere

Sono disponibili tre opzioni per la raccolta di eventi:
1. Usare l'opzione `-CollectSdkEvents` per raccogliere gli eventi generati da Application Insights SDK.
2. Usare l'opzione `-CollectRedfieldEvents` per raccogliere gli eventi generati da status monitor e dal runtime di Redfield. Questi log sono utili per la diagnosi di IIS e l'avvio dell'applicazione.
3. Usare entrambe le opzioni per raccogliere entrambi i tipi di evento.
4. Per impostazione predefinita, se non viene specificata alcuna opzione, verranno raccolti entrambi i tipi di evento.


## <a name="parameters"></a>Parametri

### <a name="-maxdurationinminutes"></a>-MaxDurationInMinutes
**Facoltativo.** Utilizzare questo parametro per impostare per quanto tempo lo script deve raccogliere gli eventi. Il valore predefinito è 5 minuti.

### <a name="-logdirectory"></a>-LogDirectory
**Facoltativo.** Usare questa opzione per impostare la directory di output del file ETL. Per impostazione predefinita, questo file verrà creato nella directory dei moduli di PowerShell. Il percorso completo verrà visualizzato durante l'esecuzione dello script.


### <a name="-collectsdkevents"></a>-CollectSdkEvents
**Facoltativo.** Usare questa opzione per raccogliere gli eventi di Application Insights SDK.

### <a name="-collectredfieldevents"></a>-CollectRedfieldEvents
**Facoltativo.** Usare questa opzione per raccogliere gli eventi da Status Monitor e dal runtime di Redfield.

### <a name="-verbose"></a>-Verbose
**Parametro comune.** Usare questa opzione per restituire i log dettagliati.



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

Risoluzione dei problemi aggiuntiva:

- Esaminare i passaggi aggiuntivi per la risoluzione dei problemi qui: https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-troubleshoot
- Esaminare le informazioni di [riferimento sulle API](status-monitor-v2-overview.md#powershell-api-reference) per informazioni sui parametri che potrebbero essere stati persi.
- Se è necessaria ulteriore assistenza, è possibile contattarci su [GitHub](https://github.com/Microsoft/ApplicationInsights-Home/issues).



 Eseguire altre operazioni con Status Monitor V2:
 - Usare la guida per la [risoluzione dei problemi](status-monitor-v2-troubleshoot.md) Status Monitor V2.
 - [Ottenere la configurazione](status-monitor-v2-api-get-config.md) per verificare che le impostazioni siano state registrate correttamente.
 - [Ottenere lo stato](status-monitor-v2-api-get-status.md) per controllare il monitoraggio.
