---
title: "Azure riferimento all'API v2 Status Monitor: Ottenere la configurazione | Microsoft Docs"
description: Riferimento API v2 di monitoraggio dello stato. Get-ApplicationInsightsMonitoringConfig. Monitorare le prestazioni di siti Web senza ridistribuire il sito Web. Funziona con le app web ASP.NET ospitate in locale, in macchine virtuali o in Azure.
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
ms.openlocfilehash: 1f7061c9823ddc8ff7f8f42976041f1c9ff68fc0
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66514392"
---
# <a name="status-monitor-v2-api-get-applicationinsightsmonitoringconfig-v021-alpha"></a>API v2 di Status Monitor: Get-ApplicationInsightsMonitoringConfig (v0.2.1-alpha)

Questo articolo descrive un cmdlet che è un membro del [modulo di Az.ApplicationMonitor PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

> [!IMPORTANT]
> Stato monitoraggio v2 è attualmente in anteprima pubblica.
> Questa versione di anteprima viene fornita senza un contratto di servizio e non è consigliabile per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate e alcune potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="description"></a>Descrizione

Ottiene il file di configurazione e vengono stampati i valori nella console.

> [!IMPORTANT] 
> Questo cmdlet richiede una sessione di PowerShell con autorizzazioni di amministratore.

## <a name="examples"></a>Esempi

```powershell
PS C:\> Get-ApplicationInsightsMonitoringConfig
```

## <a name="parameters"></a>Parametri

Nessun parametro obbligato.

## <a name="output"></a>Output


#### <a name="example-output-from-reading-the-config-file"></a>Output di esempio di leggere il file di configurazione

```
RedfieldConfiguration:
Filters:
0)InstrumentationKey:  AppFilter: WebAppExclude MachineFilter: .*
1)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2 AppFilter: WebAppTwo MachineFilter: .*
2)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault AppFilter: .* MachineFilter: .*
```

## <a name="next-steps"></a>Passaggi successivi

  Visualizzare i dati di telemetria:
 - [Esplorare le metriche](../../azure-monitor/app/metrics-explorer.md) per monitorare le prestazioni e utilizzo.
- [Cercare eventi e log](../../azure-monitor/app/diagnostic-search.md) per diagnosticare i problemi.
- Uso [analitica](../../azure-monitor/app/analytics.md) per più query avanzate.
- [Creare dashboard](../../azure-monitor/app/overview-dashboard.md).
 
 Aggiungere altri dati di telemetria:
 - [Creare test web](monitor-web-app-availability.md) per assicurarsi che il sito rimanga attivo.
- [Aggiungere dati di telemetria client web](../../azure-monitor/app/javascript.md) per visualizzare le eccezioni dal codice della pagina web e per consentire le chiamate di traccia.
- [Aggiungere Application Insights SDK al codice](../../azure-monitor/app/asp-net.md) in modo che è possibile inserire una traccia e registrare le chiamate.
 
 Altri vantaggi v2 Status Monitor:
 - Usare la Guida alla [risolvere i problemi di](status-monitor-v2-troubleshoot.md) v2 Status Monitor.
 - Apportare modifiche al file di configurazione usando il [impostare la configurazione di](status-monitor-v2-api-set-config.md) cmdlet.
