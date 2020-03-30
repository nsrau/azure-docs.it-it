---
title: Informazioni di riferimento sull'API di Azure Application Insights AgentAzure Application Insights Agent API reference
description: Informazioni di riferimento sull'API dell'agente di Application Insights.Application Insights Agent API reference. Start-Trace. Raccogliere i log ETW da Status Monitor e Application Insights SDK.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: b9680101f1a22dd6d9c1617c8afc13a10ad1c594
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671223"
---
# <a name="application-insights-agent-api-start-applicationinsightsmonitoringtrace"></a>API dell'agente di Application Insights: Start-ApplicationInsightsMonitoringTrace

In questo articolo viene descritto un cmdlet membro del [modulo PowerShell Az.ApplicationMonitor](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

## <a name="description"></a>Descrizione

Raccoglie gli [eventi ETW](https://docs.microsoft.com/windows/desktop/etw/event-tracing-portal) dal runtime di collegamento senza codice. Questo cmdlet è un'alternativa all'esecuzione di [PerfView](https://github.com/microsoft/perfview).

Gli eventi raccolti verranno stampati sulla console in tempo reale e salvati in un file ETL. Il file ETL di output può essere aperto da [PerfView](https://github.com/microsoft/perfview) per ulteriori indagini.

Questo cmdlet verrà eseguito fino a quando non raggiunge rà`Ctrl + C`la durata del timeout (5 minuti predefiniti) o verrà arrestato manualmente ( ).

> [!IMPORTANT] 
> Questo cmdlet richiede una sessione di PowerShell con autorizzazioni di amministratore.

## <a name="examples"></a>Esempi

### <a name="how-to-collect-events"></a>Come raccogliere gli eventi

Normalmente ti chiediamo di raccogliere gli eventi per indagare sul motivo per cui l'applicazione non viene instrumentata.

Il runtime di connessione senza codice genererà eventi ETW all'avvio di IIS e all'avvio dell'applicazione.

Per raccogliere questi eventi:
1. In una console cmd con `iisreset /stop` privilegi di amministratore, eseguire Per disattivare IIS e tutte le applicazioni Web.
2. Esegui questo cmdlet
3. In una console cmd con `iisreset /start` privilegi di amministratore, eseguire Per avviare IIS.
4. Prova a passare all'app.
5. Al termine del caricamento dell'app, puoi`Ctrl + C`interromperlo manualmente ( ) o attendere il timeout.

### <a name="what-events-to-collect"></a>Eventi da raccogliere

Sono disponibili tre opzioni per la raccolta di eventi:You have three options when collecting events:
1. Utilizzare l'opzione `-CollectSdkEvents` per raccogliere gli eventi generati da Application Insights SDK.
2. Utilizzare l'opzione `-CollectRedfieldEvents` per raccogliere gli eventi generati da Status Monitor e Redfield Runtime. Questi registri sono utili per la diagnosi di IIS e l'avvio dell'applicazione.
3. Utilizzare entrambe le opzioni per raccogliere entrambi i tipi di evento.
4. Per impostazione predefinita, se non viene specificata alcuna opzione verranno raccolti entrambi i tipi di evento.


## <a name="parameters"></a>Parametri

### <a name="-maxdurationinminutes"></a>-MaxDurationInMinuti
**Opzionale.** Utilizzare questo parametro per impostare per quanto tempo questo script deve raccogliere gli eventi. Il valore predefinito è 5 minuti.

### <a name="-logdirectory"></a>-LogDirectory (informazioni in cui è stato fatto clic
**Opzionale.** Utilizzare questa opzione per impostare la directory di output del file ETL. Per impostazione predefinita, questo file verrà creato nella directory Moduli di PowerShell.By default, this file will be created in the PowerShell Modules directory. Il percorso completo verrà visualizzato durante l'esecuzione dello script.


### <a name="-collectsdkevents"></a>-CollectSdkEvents
**Opzionale.** Utilizzare questa opzione per raccogliere gli eventi di Application Insights SDK.

### <a name="-collectredfieldevents"></a>-CollectRedfieldEvents
**Opzionale.** Utilizzare questa opzione per raccogliere eventi da Status Monitor e dal runtime di Redfield.

### <a name="-verbose"></a>-Verbose
**Parametro comune.** Utilizzare questa opzione per generare log dettagliati.



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

Risoluzione dei problemi aggiuntivi:

- Per ulteriori informazioni, vedere la procedura di risoluzione dei problemi:Review additional troubleshooting steps here:https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-troubleshoot
- Esaminare il [riferimento all'API](status-monitor-v2-overview.md#powershell-api-reference) per informazioni sui parametri che potrebbero essere stati persi.
- Se hai bisogno di ulteriore aiuto, puoi contattarci su [GitHub](https://github.com/Microsoft/ApplicationInsights-Home/issues).



 Fai di più con Application Insights Agent:
 - Usare la guida per [risolvere i problemi relativi](status-monitor-v2-troubleshoot.md) all'agente application Insights.Use our guide to troubleshoot Application Insights Agent.
 - [Ottenere la configurazione](status-monitor-v2-api-get-config.md) per verificare che le impostazioni siano state registrate correttamente.
 - [Ottenere lo stato](status-monitor-v2-api-get-status.md) per controllare il monitoraggio.
