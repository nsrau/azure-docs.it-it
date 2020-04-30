---
title: Panoramica dell'agente di applicazione Azure Insights | Microsoft Docs
description: Panoramica dell'agente di Application Insights. Monitora le prestazioni del sito Web senza ridistribuire il sito Web. Funziona con le app Web ASP.NET ospitate in locale, in macchine virtuali o in Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 09/16/2019
ms.openlocfilehash: 626a8c96fdc247db174e2cbcd2e5c99cec43d2ea
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81770954"
---
# <a name="deploy-azure-monitor-application-insights-agent-for-on-premises-servers"></a>Distribuire l'agente di Application Insights di monitoraggio di Azure per i server locali

> [!IMPORTANT]
> Queste linee guida sono consigliate per le distribuzioni di Application Insights Agent locali e non di Azure. Ecco l'approccio consigliato per le [distribuzioni di macchine virtuali di Azure e set di scalabilità di macchine virtuali](https://docs.microsoft.com/azure/azure-monitor/app/azure-vm-vmss-apps).

Application Insights Agent (denominato in precedenza Status Monitor v2) è un modulo di PowerShell pubblicato nella [PowerShell Gallery](https://www.powershellgallery.com/packages/Az.ApplicationMonitor).
Sostituisce [Status Monitor](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now).
I dati di telemetria vengono inviati all'portale di Azure, in cui è possibile [monitorare](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) l'app.

> [!NOTE]
> Il modulo supporta attualmente solo la strumentazione senza codice delle app Web .NET ospitate con IIS. Usare un SDK per instrumentare applicazioni ASP.NET Core, Java e node. js.

## <a name="powershell-gallery"></a>PowerShell Gallery

Application Insights Agent è disponibile qui: https://www.powershellgallery.com/packages/Az.ApplicationMonitor.

![PowerShell Gallery](https://img.shields.io/powershellgallery/v/Az.ApplicationMonitor.svg?color=Blue&label=Current%20Version&logo=PowerShell&style=for-the-badge)


## <a name="instructions"></a>Istruzioni
- Vedere le [istruzioni introduttive](status-monitor-v2-get-started.md) per iniziare a usare esempi di codice conciso.
- Vedere le [istruzioni dettagliate](status-monitor-v2-detailed-instructions.md) per approfondimenti su come iniziare.

## <a name="powershell-api-reference"></a>Informazioni di riferimento sulle API di PowerShell
- [Disable-ApplicationInsightsMonitoring](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference#disable-applicationinsightsmonitoring)
- [Disable-InstrumentationEngine](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference#disable-instrumentationengine)
- [Enable-ApplicationInsightsMonitoring](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference#enable-applicationinsightsmonitoring)
- [Enable-InstrumentationEngine](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference#enable-instrumentationengine)
- [Get-ApplicationInsightsMonitoringConfig](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference#get-applicationinsightsmonitoringconfig)
- [Get-ApplicationInsightsMonitoringStatus](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference#get-applicationinsightsmonitoringstatus)
- [Set-ApplicationInsightsMonitoringConfig](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference#set-applicationinsightsmonitoringconfig)
- [Start-ApplicationInsightsMonitoringTrace](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference#start-applicationinsightsmonitoringtrace)

## <a name="troubleshooting"></a>Risoluzione dei problemi
- [Risoluzione dei problemi](status-monitor-v2-troubleshoot.md)
- [Problemi noti](status-monitor-v2-troubleshoot.md#known-issues)


## <a name="faq"></a>Domande frequenti

- Application Insights agente supporta le installazioni proxy?

  *Sì*. Sono disponibili diversi modi per scaricare Application Insights Agent. Se il computer dispone di accesso a Internet, è possibile eseguire l'onboarding `-Proxy` nel PowerShell Gallery usando i parametri.
È anche possibile scaricare manualmente il modulo e installarlo nel computer o usarlo direttamente.
Ognuna di queste opzioni è descritta nelle [istruzioni dettagliate](status-monitor-v2-detailed-instructions.md).

- Status Monitor V2 supporta le applicazioni ASP.NET Core?

  *No*. Per istruzioni sull'abilitazione del monitoraggio delle applicazioni ASP.NET Core, vedere [Application Insights per ASP.NET Core applicazioni](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core). Non è necessario installare StatusMonitor per un'applicazione ASP.NET Core. Questo vale anche se ASP.NET Core applicazione è ospitata in IIS.

- Ricerca per categorie verificare l'esito positivo dell'abilitazione?

  - Il cmdlet [Get-ApplicationInsightsMonitoringStatus](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference#get-applicationinsightsmonitoringstatus) può essere usato per verificare che l'abilitazione sia stata completata.
  - È consigliabile usare le [metriche in tempo reale](https://docs.microsoft.com/azure/azure-monitor/app/live-stream) per determinare rapidamente se l'app invia dati di telemetria.

  - È anche possibile usare [log Analytics](../log-query/get-started-portal.md) per elencare tutti i ruoli cloud che inviano attualmente i dati di telemetria:
      ```Kusto
      union * | summarize count() by cloud_RoleName, cloud_RoleInstance
      ```

## <a name="next-steps"></a>Passaggi successivi

Visualizzare i dati di telemetria:

* [Esplora le metriche](../../azure-monitor/platform/metrics-charts.md) per monitorare le prestazioni e l'utilizzo.
* Eseguire [ricerche negli eventi e nei log](../../azure-monitor/app/diagnostic-search.md) per diagnosticare i problemi.
* [Usare Analytics](../../azure-monitor/app/analytics.md) per query più avanzate.
* [Creare dashboard](../../azure-monitor/app/overview-dashboard.md).

Aggiungere altri dati di telemetria:

* [Creare test Web](monitor-web-app-availability.md) per assicurarsi che il sito rimanga attivo.
* Aggiungere i dati di [telemetria del client Web](../../azure-monitor/app/javascript.md) per visualizzare le eccezioni dal codice della pagina Web e per abilitare le chiamate di traccia.
* [Aggiungere il Application Insights SDK al codice per](../../azure-monitor/app/asp-net.md) poter inserire le chiamate di traccia e log.

