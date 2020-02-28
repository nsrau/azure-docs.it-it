---
title: Informazioni di riferimento sull'API dell'agente applicazione Azure Insights
description: Informazioni di riferimento sull'API dell'agente Application Insights. Disable-ApplicationInsightsMonitoring. Monitora le prestazioni del sito Web senza ridistribuire il sito Web. Funziona con le app Web ASP.NET ospitate in locale, in macchine virtuali o in Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 0678186012678a68fa80a23685f8b346c8c5b859
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77671291"
---
# <a name="application-insights-agent-api-disable-applicationinsightsmonitoring"></a>API dell'agente di Application Insights: Disable-ApplicationInsightsMonitoring

Questo articolo descrive un cmdlet che fa parte del modulo di [PowerShell AZ. ApplicationMonitor](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

## <a name="description"></a>Descrizione

Disabilita il monitoraggio nel computer di destinazione.
Questo cmdlet consente di rimuovere le modifiche al file applicationHost. config di IIS e di rimuovere le chiavi del registro di sistema.

> [!IMPORTANT] 
> Questo cmdlet richiede una sessione di PowerShell con autorizzazioni di amministratore.

## <a name="examples"></a>Esempi

```powershell
PS C:\> Disable-ApplicationInsightsMonitoring
```

## <a name="parameters"></a>Parametri 

### <a name="-verbose"></a>-Verbose
**Parametro comune.** Usare questa opzione per visualizzare i log dettagliati.

## <a name="output"></a>Output


#### <a name="example-output-from-successfully-disabling-monitoring"></a>Output di esempio dalla disabilitazione del monitoraggio riuscita

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

 Eseguire altre operazioni con Application Insights Agent:
 - Usare la guida per [risolvere i problemi relativi](status-monitor-v2-troubleshoot.md) a Application Insights Agent.
