---
title: Panoramica dell'agente di applicazione Azure Insights | Microsoft Docs
description: Panoramica dell'agente di Application Insights. Monitora le prestazioni del sito Web senza ridistribuire il sito Web. Funziona con le app Web ASP.NET ospitate in locale, in macchine virtuali o in Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 09/16/2019
ms.openlocfilehash: f0ce969a4ab016d6a036d33a0e9bf027bb580a20
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96186219"
---
# <a name="deploy-azure-monitor-application-insights-agent-for-on-premises-servers"></a>Distribuire l'agente di Application Insights di monitoraggio di Azure per i server locali

> [!IMPORTANT]
> Queste linee guida sono consigliate per le distribuzioni di Application Insights Agent locali e non di Azure. Ecco l'approccio consigliato per le [distribuzioni di macchine virtuali di Azure e set di scalabilità di macchine virtuali](./azure-vm-vmss-apps.md).

Application Insights Agent (denominato in precedenza Status Monitor v2) è un modulo di PowerShell pubblicato nella [PowerShell Gallery](https://www.powershellgallery.com/packages/Az.ApplicationMonitor).
Sostituisce [Status Monitor](./monitor-performance-live-website-now.md).
I dati di telemetria vengono inviati all'portale di Azure, in cui è possibile [monitorare](./app-insights-overview.md) l'app.

> [!NOTE]
> Il modulo supporta attualmente solo la strumentazione senza codice delle app Web .NET ospitate con IIS. Usare un SDK per instrumentare ASP.NET Core, Java e Node.js applicazioni.

## <a name="powershell-gallery"></a>PowerShell Gallery

Application Insights Agent è disponibile qui: https://www.powershellgallery.com/packages/Az.ApplicationMonitor .

![PowerShell Gallery](https://img.shields.io/powershellgallery/v/Az.ApplicationMonitor.svg?color=Blue&label=Current%20Version&logo=PowerShell&style=for-the-badge)


## <a name="instructions"></a>Istruzioni
- Vedere le [istruzioni introduttive](status-monitor-v2-get-started.md) per iniziare a usare esempi di codice conciso.
- Vedere le [istruzioni dettagliate](status-monitor-v2-detailed-instructions.md) per approfondimenti su come iniziare.

## <a name="powershell-api-reference"></a>Informazioni di riferimento sulle API di PowerShell
- [Disable-ApplicationInsightsMonitoring](./status-monitor-v2-api-reference.md#disable-applicationinsightsmonitoring)
- [Disable-InstrumentationEngine](./status-monitor-v2-api-reference.md#disable-instrumentationengine)
- [Enable-ApplicationInsightsMonitoring](./status-monitor-v2-api-reference.md#enable-applicationinsightsmonitoring)
- [Enable-InstrumentationEngine](./status-monitor-v2-api-reference.md#enable-instrumentationengine)
- [Get-ApplicationInsightsMonitoringConfig](./status-monitor-v2-api-reference.md#get-applicationinsightsmonitoringconfig)
- [Get-ApplicationInsightsMonitoringStatus](./status-monitor-v2-api-reference.md#get-applicationinsightsmonitoringstatus)
- [Set-ApplicationInsightsMonitoringConfig](./status-monitor-v2-api-reference.md#set-applicationinsightsmonitoringconfig)
- [Start-ApplicationInsightsMonitoringTrace](./status-monitor-v2-api-reference.md#start-applicationinsightsmonitoringtrace)

## <a name="troubleshooting"></a>Risoluzione dei problemi
- [Risoluzione dei problemi](status-monitor-v2-troubleshoot.md)
- [Problemi noti](status-monitor-v2-troubleshoot.md#known-issues)


## <a name="faq"></a>Domande frequenti

- Application Insights agente supporta le installazioni proxy?

  *Sì*. Sono disponibili diversi modi per scaricare Application Insights Agent. Se il computer dispone di accesso a Internet, è possibile eseguire l'onboarding nel PowerShell Gallery usando i `-Proxy` parametri.
È anche possibile scaricare manualmente il modulo e installarlo nel computer o usarlo direttamente.
Ognuna di queste opzioni è descritta nelle [istruzioni dettagliate](status-monitor-v2-detailed-instructions.md).

- Status Monitor V2 supporta le applicazioni ASP.NET Core?

  *No*. Per istruzioni sull'abilitazione del monitoraggio delle applicazioni ASP.NET Core, vedere [Application Insights per ASP.NET Core applicazioni](./asp-net-core.md). Non è necessario installare StatusMonitor per un'applicazione ASP.NET Core. Questo vale anche se ASP.NET Core applicazione è ospitata in IIS.

- Ricerca per categorie verificare l'esito positivo dell'abilitazione?

  - Il cmdlet [Get-ApplicationInsightsMonitoringStatus](./status-monitor-v2-api-reference.md#get-applicationinsightsmonitoringstatus) può essere usato per verificare che l'abilitazione sia stata completata.
  - È consigliabile usare le [metriche in tempo reale](./live-stream.md) per determinare rapidamente se l'app invia dati di telemetria.

  - È anche possibile usare [log Analytics](../log-query/log-analytics-tutorial.md) per elencare tutti i ruoli cloud che inviano attualmente i dati di telemetria:
      ```Kusto
      union * | summarize count() by cloud_RoleName, cloud_RoleInstance
      ```

## <a name="next-steps"></a>Passaggi successivi

Visualizzare i dati di telemetria:

* [Esplora le metriche](../platform/metrics-charts.md) per monitorare le prestazioni e l'utilizzo.
* Eseguire [ricerche negli eventi e nei log](./diagnostic-search.md) per diagnosticare i problemi.
* [Usare Analytics](../log-query/log-query-overview.md) per query più avanzate.
* [Creare dashboard](./overview-dashboard.md).

Aggiungere altri dati di telemetria:

* [Creare test Web](monitor-web-app-availability.md) per assicurarsi che il sito rimanga attivo.
* Aggiungere i dati di [telemetria del client Web](./javascript.md) per visualizzare le eccezioni dal codice della pagina Web e per abilitare le chiamate di traccia.
* [Aggiungere il Application Insights SDK al codice per](./asp-net.md) poter inserire le chiamate di traccia e log.