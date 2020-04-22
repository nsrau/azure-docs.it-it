---
title: Panoramica dell'agente di Azure Application Insights - Documenti Microsoft
description: Panoramica dell'agente di Application Insights. Monitorare le prestazioni del sito Web senza ridistribuire il sito Web. Funziona con ASP.NET app Web ospitate in locale, nelle macchine virtuali o in Azure.Works with a web apps hosted on-premises, in VMs, or on Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 09/16/2019
ms.openlocfilehash: 626a8c96fdc247db174e2cbcd2e5c99cec43d2ea
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770954"
---
# <a name="deploy-azure-monitor-application-insights-agent-for-on-premises-servers"></a>Distribuire Azure Monitor Application Insights Agent per i server localiDeploy Azure Monitor Application Insights Agent for on-premises servers

> [!IMPORTANT]
> Queste indicazioni sono consigliate per le distribuzioni cloud locali e non di Azure dell'agente Application Insights.This guidance is recommended for On-Premises and non-Azure cloud deployments of Application Insights Agent. Ecco l'approccio consigliato per le distribuzioni di set di scalabilità di macchine virtuali e macchine virtuali di Azure.Here's the recommended approach for [Azure virtual machine and virtual machine scale set deployments](https://docs.microsoft.com/azure/azure-monitor/app/azure-vm-vmss-apps).

Application Insights Agent (precedentemente denominato Status Monitor V2) è un modulo di PowerShell pubblicato in [PowerShell Gallery](https://www.powershellgallery.com/packages/Az.ApplicationMonitor).
Sostituisce [Il Monitor di stato](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now).
La telemetria viene inviata al portale di Azure, in cui è possibile [monitorare](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) l'app.

> [!NOTE]
> Il modulo supporta attualmente solo la strumentazione senza codice delle app Web .NET ospitate con IIS. Utilizzare un SDK per instrumentare ASP.NET applicazioni Core, Java e Node.js.

## <a name="powershell-gallery"></a>PowerShell Gallery

Application Insights Agent si https://www.powershellgallery.com/packages/Az.ApplicationMonitortrova qui: .

![PowerShell Gallery](https://img.shields.io/powershellgallery/v/Az.ApplicationMonitor.svg?color=Blue&label=Current%20Version&logo=PowerShell&style=for-the-badge)


## <a name="instructions"></a>Istruzioni
- Vedere le [istruzioni introduttive](status-monitor-v2-get-started.md) per iniziare con esempi di codice concisi.
- Consulta le [istruzioni dettagliate](status-monitor-v2-detailed-instructions.md) per un'immersione approfondita su come iniziare.

## <a name="powershell-api-reference"></a>Informazioni di riferimento sulle API di PowerShellPowerShell API reference
- [Disable-ApplicationInsightsMonitoring](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference#disable-applicationinsightsmonitoring)
- [Disable-InstrumentationEngine](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference#disable-instrumentationengine)
- [Enable-ApplicationInsightsMonitoring](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference#enable-applicationinsightsmonitoring)
- [Enable-InstrumentationEngine (Abilitazione-InstrumentationEngine)](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference#enable-instrumentationengine)
- [Get-ApplicationInsightsMonitoringConfig](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference#get-applicationinsightsmonitoringconfig)
- [Get-ApplicationInsightsMonitoringStatus](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference#get-applicationinsightsmonitoringstatus)
- [Set-ApplicationInsightsMonitoringConfig](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference#set-applicationinsightsmonitoringconfig)
- [Start-ApplicationInsightsMonitoringTrace](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference#start-applicationinsightsmonitoringtrace)

## <a name="troubleshooting"></a>Risoluzione dei problemi
- [Risoluzione dei problemi](status-monitor-v2-troubleshoot.md)
- [Problemi noti](status-monitor-v2-troubleshoot.md#known-issues)


## <a name="faq"></a>Domande frequenti

- Application Insights Agent supporta le installazioni proxy?

  *Sì*. Esistono diversi modi per scaricare Application Insights Agent.There are multiple ways to download Application Insights Agent. Se il computer dispone dell'accesso a Internet, è `-Proxy` possibile eseguire l'onboarding a PowerShell Gallery usando i parametri.
È inoltre possibile scaricare manualmente il modulo e installarlo sul computer o utilizzarlo direttamente.
Ognuna di queste opzioni è descritta nelle [istruzioni dettagliate.](status-monitor-v2-detailed-instructions.md)

- Status Monitor v2 supporta le applicazioni ASP.NET Core?

  *No*. Per istruzioni sull'abilitazione del monitoraggio delle applicazioni ASP.NET Core, vedere [Application Insights per le applicazioni ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core). Non è necessario installare StatusMonitor per un'applicazione ASP.NET Core.There's no need to install StatusMonitor for an ASP.NET Core application. Questo vale anche se ASP.NET'applicazione Core è ospitata in IIS.

- Come è possibile verificare che l'abilitazione sia stata eseguita correttamente?

  - Il cmdlet [Get-ApplicationInsightsMonitoringStatus](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference#get-applicationinsightsmonitoringstatus) può essere utilizzato per verificare che l'abilitazione sia stata eseguita correttamente.
  - È consigliabile usare [metriche dinamiche](https://docs.microsoft.com/azure/azure-monitor/app/live-stream) per determinare rapidamente se l'app invia dati di telemetria.

  - È anche possibile usare Log Analytics per elencare tutti i ruoli cloud che attualmente inviano dati di telemetria:You can also use [Log Analytics](../log-query/get-started-portal.md) to list all the cloud roles currently sending telemetry:
      ```Kusto
      union * | summarize count() by cloud_RoleName, cloud_RoleInstance
      ```

## <a name="next-steps"></a>Passaggi successivi

Visualizzare i dati di telemetria:

* [Esplora le metriche](../../azure-monitor/platform/metrics-charts.md) per monitorare le prestazioni e l'utilizzo.
* [Cerca eventi e log](../../azure-monitor/app/diagnostic-search.md) per diagnosticare i problemi.
* [Usa Analytics](../../azure-monitor/app/analytics.md) per query più avanzate.
* [Creare dashboard](../../azure-monitor/app/overview-dashboard.md).

Aggiungere altri dati di telemetria:

* [Creare test Web](monitor-web-app-availability.md) per assicurarsi che il sito rimanga attivo.
* [Aggiungere dati di telemetria del client Web](../../azure-monitor/app/javascript.md) per visualizzare le eccezioni dal codice della pagina Web e abilitare le chiamate di traccia.
* [Aggiungere Application Insights SDK al codice](../../azure-monitor/app/asp-net.md) in modo da poter inserire chiamate di traccia e log.

