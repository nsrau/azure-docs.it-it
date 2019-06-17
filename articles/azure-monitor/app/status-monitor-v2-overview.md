---
title: Panoramica di Azure Status Monitor v2 | Microsoft Docs
description: Panoramica di Status Monitor v2. Monitorare le prestazioni di siti Web senza ridistribuire il sito Web. Funziona con le app web ASP.NET ospitate in locale, in macchine virtuali o in Azure.
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
ms.openlocfilehash: 2126408222433e6339723dc2da0d2611bb234fe8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66734154"
---
# <a name="status-monitor-v2"></a>Status Monitor v2

Stato monitoraggio v2 è un modulo pubblicato in di PowerShell la [PowerShell Gallery](https://www.powershellgallery.com/packages/Az.ApplicationMonitor).
Sostituisce [Status Monitor](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now).
Il modulo fornisce strumentazione senza codice di App web .NET ospitate in IIS.
Dati di telemetria al portale di Azure, in cui è possibile [monitor](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) all'app.

> [!IMPORTANT]
> Stato monitoraggio v2 è attualmente in anteprima pubblica.
> Questa versione di anteprima viene fornita senza un contratto di servizio e non è consigliabile per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate e alcune potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="powershell-gallery"></a>PowerShell Gallery

PowerShell Gallery è disponibile qui: https://www.powershellgallery.com/packages/Az.ApplicationMonitor.


## <a name="instructions"></a>Istruzioni
- Vedere le [Introduzione a istruzioni](status-monitor-v2-get-started.md) per iniziare con gli esempi di codice conciso.
- Vedere le [istruzioni dettagliate](status-monitor-v2-detailed-instructions.md) per un approfondimento su come iniziare a usare.

## <a name="powershell-api-reference"></a>Riferimento all'API di PowerShell
- [Disable-ApplicationInsightsMonitoring](status-monitor-v2-api-disable-monitoring.md)
- [Disable-InstrumentationEngine](status-monitor-v2-api-disable-instrumentation-engine.md)
- [Enable-ApplicationInsightsMonitoring](status-monitor-v2-api-enable-monitoring.md)
- [Enable-InstrumentationEngine](status-monitor-v2-api-enable-instrumentation-engine.md)
- [Get-ApplicationInsightsMonitoringConfig](status-monitor-v2-api-get-config.md)
- [Get-ApplicationInsightsMonitoringStatus](status-monitor-v2-api-get-status.md)
- [Set-ApplicationInsightsMonitoringConfig](status-monitor-v2-api-set-config.md)

## <a name="troubleshooting"></a>risoluzione dei problemi
- [Risoluzione dei problemi](status-monitor-v2-troubleshoot.md)
- [Problemi noti](status-monitor-v2-troubleshoot.md#known-issues)


## <a name="faq"></a>Domande frequenti

- Status Monitor v2 supporta installazioni del proxy?

  *Sì*. Esistono diversi modi per scaricare v2 Status Monitor. Se il computer ha accesso a internet, è possibile eseguire l'onboarding in PowerShell Gallery usando `-Proxy` parametri.
È possibile scaricare manualmente il modulo e installarla nel computer in uso o usarlo direttamente.
Ognuna di queste opzioni è descritta nel [istruzioni dettagliate](status-monitor-v2-detailed-instructions.md).
  
- Come verificare che l'abilitazione è stata completata?

   Non è disponibile alcun cmdlet per verificare l'abilitazione della ha avuto esito positivo.
Si consiglia di usare [Live Metrics](https://docs.microsoft.com/azure/azure-monitor/app/live-stream) per determinare rapidamente se l'app invia dati di telemetria.

   È anche possibile usare [Log Analitica](../log-query/get-started-portal.md) per elencare tutti i ruoli del cloud attualmente l'invio di dati di telemetria:
   ```Kusto
   union * | summarize count() by cloud_RoleName, cloud_RoleInstance
   ```

## <a name="next-steps"></a>Passaggi successivi

Visualizzare i dati di telemetria:

* [Esplorare le metriche](../../azure-monitor/app/metrics-explorer.md) per monitorare le prestazioni e utilizzo.
* [Cercare eventi e log](../../azure-monitor/app/diagnostic-search.md) per diagnosticare i problemi.
* [Usare Analitica](../../azure-monitor/app/analytics.md) per più query avanzate.
* [Creare dashboard](../../azure-monitor/app/overview-dashboard.md).

Aggiungere altri dati di telemetria:

* [Creare test web](monitor-web-app-availability.md) per assicurarsi che il sito rimanga attivo.
* [Aggiungere dati di telemetria client web](../../azure-monitor/app/javascript.md) per visualizzare le eccezioni dal codice della pagina web e per consentire le chiamate di traccia.
* [Aggiungere Application Insights SDK al codice](../../azure-monitor/app/asp-net.md) in modo che è possibile inserire una traccia e registrare le chiamate.

