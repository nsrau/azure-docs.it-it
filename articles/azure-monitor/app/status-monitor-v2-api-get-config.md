---
title: Informazioni di riferimento sull'API di Azure Application Insights AgentAzure Application Insights Agent API reference
description: Informazioni di riferimento sull'API dell'agente di Application Insights.Application Insights Agent API reference. Get-ApplicationInsightsMonitoringConfig. Monitorare le prestazioni del sito Web senza ridistribuire il sito Web. Funziona con ASP.NET app Web ospitate in locale, nelle macchine virtuali o in Azure.Works with a web apps hosted on-premises, in VMs, or on Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 0cb5991b381acc9a0ec36113d0e15a7731b5074d
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537492"
---
# <a name="application-insights-agent-api-get-applicationinsightsmonitoringconfig"></a>API dell'agente di Application Insights: Get-ApplicationInsightsMonitoringConfig

In questo articolo viene descritto un cmdlet membro del [modulo PowerShell Az.ApplicationMonitor](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

## <a name="description"></a>Descrizione

Ottiene il file di configurazione e stampa i valori nella console.

> [!IMPORTANT] 
> Questo cmdlet richiede una sessione di PowerShell con autorizzazioni di amministratore.

## <a name="examples"></a>Esempi

```powershell
PS C:\> Get-ApplicationInsightsMonitoringConfig
```

## <a name="parameters"></a>Parametri

Nessun parametro richiesto.

## <a name="output"></a>Output


#### <a name="example-output-from-reading-the-config-file"></a>Output di esempio dalla lettura del file di configurazioneExample output from reading the config file

```
RedfieldConfiguration:
Filters:
0)InstrumentationKey:  AppFilter: WebAppExclude MachineFilter: .*
1)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2 AppFilter: WebAppTwo MachineFilter: .*
2)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault AppFilter: .* MachineFilter: .*
```

## <a name="next-steps"></a>Passaggi successivi

  Visualizzare i dati di telemetria:
 - [Esplora le metriche](../../azure-monitor/platform/metrics-charts.md) per monitorare le prestazioni e l'utilizzo.
- [Cerca eventi e log](../../azure-monitor/app/diagnostic-search.md) per diagnosticare i problemi.
- Usa [l'analisi](../../azure-monitor/app/analytics.md) per query più avanzate.
- [Creare dashboard](../../azure-monitor/app/overview-dashboard.md).
 
 Aggiungere altri dati di telemetria:
 - [Creare test Web](monitor-web-app-availability.md) per assicurarsi che il sito rimanga attivo.
- [Aggiungere dati di telemetria del client Web](../../azure-monitor/app/javascript.md) per visualizzare le eccezioni dal codice della pagina Web e abilitare le chiamate di traccia.
- [Aggiungere Application Insights SDK al codice](../../azure-monitor/app/asp-net.md) in modo da poter inserire chiamate di traccia e log.
 
 Fai di più con Application Insights Agent:
 - Usare la guida per [risolvere i problemi relativi](status-monitor-v2-troubleshoot.md) all'agente application Insights.Use our guide to troubleshoot Application Insights Agent.
 - Apportare modifiche alla configurazione utilizzando il cmdlet [Set config.](status-monitor-v2-api-set-config.md)
