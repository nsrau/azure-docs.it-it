---
title: Panoramica di Azure Status Monitor v2 | Microsoft Docs
description: Panoramica di Status Monitor v2. Monitorare le prestazioni di siti Web senza ridistribuire il sito Web. Questa funzionalità può essere usata con app Web ASP.NET ospitate in locale, in macchine virtuali o in Azure.
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
ms.openlocfilehash: 2adc706c5da4fa53ace2a8a471789e276878c491
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2019
ms.locfileid: "66255853"
---
# <a name="status-monitor-v2"></a>Status Monitor v2

Stato monitoraggio v2 è pubblicato in un modulo di PowerShell la [PowerShell Gallery](https://www.powershellgallery.com/packages/Az.ApplicationMonitor) e le sostituzioni per [Status Monitor](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now). Questo modulo viene fornita senza codice di strumentazione di applicazioni web .NET ospitate in IIS.
I dati di telemetria verranno inviati al portale di Azure in cui è possibile [monitor](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) all'applicazione.

> [!IMPORTANT]
> Stato monitoraggio v2 è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [condizioni per l'utilizzo per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="powershell-gallery"></a>PowerShell Gallery

https://www.powershellgallery.com/packages/Az.ApplicationMonitor


## <a name="instructions"></a>Istruzioni
- Revisione del nostro [Introduzione a istruzioni](status-monitor-v2-get-started.md) per iniziare subito con esempi di codice conciso.
- Revisione del nostro [istruzioni dettagliate](status-monitor-v2-detailed-instructions.md) per un approfondimento su come iniziare a usare.

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

  **Sì**. Sono disponibili diverse opzioni per il download v2 Status Monitor. Se il computer ha accesso a internet, è possibile eseguire l'onboarding per l'uso di PowerShell Gallery `-Proxy` parametri. È possibile scaricare anche manualmente questo modulo e installarla nel computer o usare il modulo direttamente. Ognuna di queste opzioni è descritta nel nostro [istruzioni dettagliate](status-monitor-v2-detailed-instructions.md).
  
- Come verificare l'abilitazione è riuscita?

   Non abbiamo un cmdlet per verificare che abilitazione ha avuto esito positivo. È consigliabile usare [Live Metrics](https://docs.microsoft.com/azure/azure-monitor/app/live-stream) per osservarne rapidamente se l'applicazione invia i dati di telemetria.

   È anche possibile usare [Analitica](../log-query/get-started-portal.md) per elencare tutti i ruoli del cloud attualmente l'invio di dati di telemetria.
   ```Kusto
   union * | summarize count() by cloud_RoleName, cloud_RoleInstance
   ```

## <a name="next-steps"></a>Passaggi successivi

Visualizzare i dati di telemetria:

* [Esaminare le metriche](../../azure-monitor/app/metrics-explorer.md) per monitorare le prestazioni e l'utilizzo
* [Cercare eventi e log](../../azure-monitor/app/diagnostic-search.md) per diagnosticare i problemi
* Per informazioni sulle query più avanzate, vedere [Analytics](../../azure-monitor/app/analytics.md)
* [Creare i dashboard](../../azure-monitor/app/overview-dashboard.md)

Aggiungere altri dati di telemetria:

* [Creare test web](monitor-web-app-availability.md) per assicurarsi che il sito rimanga attivo.
* [Aggiungere dati di telemetria client web](../../azure-monitor/app/javascript.md) per visualizzare le eccezioni dal codice della pagina web e per consentire di inserire chiamate di traccia.
* [Aggiungere Application Insights SDK al codice](../../azure-monitor/app/asp-net.md) in modo che è possibile inserire una traccia e registrare le chiamate

