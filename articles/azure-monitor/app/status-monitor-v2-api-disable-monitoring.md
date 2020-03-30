---
title: Informazioni di riferimento sull'API di Azure Application Insights AgentAzure Application Insights Agent API reference
description: Informazioni di riferimento sull'API dell'agente di Application Insights.Application Insights Agent API reference. Disable-ApplicationInsightsMonitoring.Disable-ApplicationInsightsMonitoring. Monitorare le prestazioni del sito Web senza ridistribuire il sito Web. Funziona con ASP.NET app Web ospitate in locale, nelle macchine virtuali o in Azure.Works with a web apps hosted on-premises, in VMs, or on Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 0678186012678a68fa80a23685f8b346c8c5b859
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671291"
---
# <a name="application-insights-agent-api-disable-applicationinsightsmonitoring"></a>API dell'agente di Application Insights: Disable-ApplicationInsightsMonitoring

In questo articolo viene descritto un cmdlet membro del [modulo PowerShell Az.ApplicationMonitor](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

## <a name="description"></a>Descrizione

Disabilita il monitoraggio nel computer di destinazione.
Questo cmdlet rimuoverà le modifiche apportate a IIS applicationHost.config e rimuoverà le chiavi del Registro di sistema.

> [!IMPORTANT] 
> Questo cmdlet richiede una sessione di PowerShell con autorizzazioni di amministratore.

## <a name="examples"></a>Esempi

```powershell
PS C:\> Disable-ApplicationInsightsMonitoring
```

## <a name="parameters"></a>Parametri 

### <a name="-verbose"></a>-Verbose
**Parametro comune.** Utilizzare questa opzione per visualizzare i registri dettagliati.

## <a name="output"></a>Output


#### <a name="example-output-from-successfully-disabling-monitoring"></a>Output di esempio dalla corretta disabilitazione del monitoraggioExample output from successfully disabling monitoring

```
Initiating Disable Process
Applying transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config'
'C:\Windows\System32\inetsrv\config\applicationHost.config' backed up to 'C:\Windows\System32\inetsrv\config\applicationHost.config.backup-2019-03-26_08-59-00z'
in :1,237
No element in the source document matches '/configuration/location[@path='']/system.webServer/modules/add[@name='ManagedHttpModuleHelper']'
Not executing RemoveAll (transform line 1, 546)
Transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config' was successfully applied. Operation: 'disable'
GAC Module will not be removed, since this operation might cause IIS instabilities
Configuring IIS Environment for codeless attach...
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]
Configuring IIS Environment for instrumentation engine...
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]
Configuring registry for instrumentation engine...
Successfully disabled Application Insights Status Monitor
```


## <a name="next-steps"></a>Passaggi successivi

 Fai di più con Application Insights Agent:
 - Usare la guida per [risolvere i problemi relativi](status-monitor-v2-troubleshoot.md) all'agente application Insights.Use our guide to troubleshoot Application Insights Agent.
