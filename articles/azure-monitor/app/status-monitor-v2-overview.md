---
title: Panoramica di Azure Status Monitor V2 | Microsoft Docs
description: Panoramica di Status Monitor V2. Monitora le prestazioni del sito Web senza ridistribuire il sito Web. Funziona con le app Web ASP.NET ospitate in locale, in macchine virtuali o in Azure.
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
ms.openlocfilehash: 0264cf3a972c35edb3ad6dc600ca39bdaa076dfd
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68333695"
---
# <a name="status-monitor-v2"></a>Status Monitor v2

Status Monitor V2 è un modulo di PowerShell pubblicato nel [PowerShell Gallery](https://www.powershellgallery.com/packages/Az.ApplicationMonitor).
Sostituisce [Status Monitor](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now).
Il modulo fornisce strumentazione senza codice per app Web .NET ospitate con IIS.
I dati di telemetria vengono inviati all'portale di Azure, in cui è possibile [monitorare](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) l'app.

## <a name="powershell-gallery"></a>PowerShell Gallery

Status Monitor V2 è disponibile qui: https://www.powershellgallery.com/packages/Az.ApplicationMonitor.

![PowerShell Gallery](https://img.shields.io/powershellgallery/v/Az.ApplicationMonitor.svg?color=Blue&label=Current%20Version&logo=PowerShell&style=for-the-badge)


## <a name="instructions"></a>Istruzioni
- Vedere le [istruzioni introduttive](status-monitor-v2-get-started.md) per iniziare a usare esempi di codice conciso.
- Vedere le [istruzioni dettagliate](status-monitor-v2-detailed-instructions.md) per approfondimenti su come iniziare.

## <a name="powershell-api-reference"></a>Informazioni di riferimento sulle API di PowerShell
- [Disable-ApplicationInsightsMonitoring](status-monitor-v2-api-disable-monitoring.md)
- [Disable-InstrumentationEngine](status-monitor-v2-api-disable-instrumentation-engine.md)
- [Enable-ApplicationInsightsMonitoring](status-monitor-v2-api-enable-monitoring.md)
- [Enable-InstrumentationEngine](status-monitor-v2-api-enable-instrumentation-engine.md)
- [Get-ApplicationInsightsMonitoringConfig](status-monitor-v2-api-get-config.md)
- [Get-ApplicationInsightsMonitoringStatus](status-monitor-v2-api-get-status.md)
- [Set-ApplicationInsightsMonitoringConfig](status-monitor-v2-api-set-config.md)
- [Start-ApplicationInsightsMonitoringTrace](status-monitor-v2-api-start-trace.md)

## <a name="troubleshooting"></a>risoluzione dei problemi
- [Risoluzione dei problemi](status-monitor-v2-troubleshoot.md)
- [Problemi noti](status-monitor-v2-troubleshoot.md#known-issues)


## <a name="faq"></a>Domande frequenti

- Status Monitor V2 supporta le installazioni proxy?

  *Sì*. Sono disponibili diversi modi per scaricare Status Monitor V2. Se il computer dispone di accesso a Internet, è possibile eseguire l'onboarding `-Proxy` nel PowerShell Gallery usando i parametri.
È anche possibile scaricare manualmente il modulo e installarlo nel computer o usarlo direttamente.
Ognuna di queste opzioni è descritta nelle [istruzioni dettagliate](status-monitor-v2-detailed-instructions.md).
  
- Ricerca per categorie verificare l'esito positivo dell'abilitazione?

  - Il cmdlet [Get-ApplicationInsightsMonitoringStatus](status-monitor-v2-api-get-status.md) può essere usato per verificare che l'abilitazione sia stata completata.
  - È consigliabile usare le [metriche in tempo reale](https://docs.microsoft.com/azure/azure-monitor/app/live-stream) per determinare rapidamente se l'app invia dati di telemetria.

  - È anche possibile usare [log Analytics](../log-query/get-started-portal.md) per elencare tutti i ruoli cloud che inviano attualmente i dati di telemetria:
      ```Kusto
      union * | summarize count() by cloud_RoleName, cloud_RoleInstance
      ```

## <a name="next-steps"></a>Passaggi successivi

Visualizzare i dati di telemetria:

* [Esplora le metriche](../../azure-monitor/app/metrics-explorer.md) per monitorare le prestazioni e l'utilizzo.
* Eseguire [ricerche negli eventi e nei log](../../azure-monitor/app/diagnostic-search.md) per diagnosticare i problemi.
* [Usare Analytics](../../azure-monitor/app/analytics.md) per query più avanzate.
* [Creare dashboard](../../azure-monitor/app/overview-dashboard.md).

Aggiungere altri dati di telemetria:

* [Creare test Web](monitor-web-app-availability.md) per assicurarsi che il sito rimanga attivo.
* Aggiungere i dati di telemetria del [client Web](../../azure-monitor/app/javascript.md) per visualizzare le eccezioni dal codice della pagina Web e per abilitare le chiamate di traccia.
* [Aggiungere il Application Insights SDK al codice per](../../azure-monitor/app/asp-net.md) poter inserire le chiamate di traccia e log.

