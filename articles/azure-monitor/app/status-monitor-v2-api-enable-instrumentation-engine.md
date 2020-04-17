---
title: Informazioni di riferimento sull'API di Azure Application Insights AgentAzure Application Insights Agent API reference
description: Informazioni di riferimento sull'API dell'agente di Application Insights.Application Insights Agent API reference. Enable-InstrumentationEngine. Monitorare le prestazioni del sito Web senza ridistribuire il sito Web. Funziona con ASP.NET app Web ospitate in locale, nelle macchine virtuali o in Azure.Works with a web apps hosted on-premises, in VMs, or on Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 70102395b94c89d4ce0edfddeda1df0be0e2f216
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536761"
---
# <a name="application-insights-agent-api-enable-instrumentationengine"></a>API dell'agente di Application Insights: Enable-InstrumentationEngine

In questo articolo viene descritto un cmdlet membro del [modulo PowerShell Az.ApplicationMonitor](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

## <a name="description"></a>Descrizione

Abilita il motore di strumentazione impostando alcune chiavi del Registro di sistema.
Riavviare IIS per rendere effettive le modifiche.

Il motore di strumentazione può integrare i dati raccolti dagli SDK di .NET.
Raccoglie eventi e messaggi che descrivono l'esecuzione di un processo gestito. Questi eventi e messaggi includono codici risultato di dipendenza, verbi HTTP e [testo del comando SQL.](asp-net-dependencies.md#advanced-sql-tracking-to-get-full-sql-query)

Abilitare il motore di strumentazione se:Enable the instrumentation engine if:
- Il monitoraggio è già stato abilitato con il cmdlet Enable ma non è stato abilitato il motore di strumentazione.
- L'app è stata instrumentata manualmente con gli SDK di .NET e si vuole raccogliere dati di telemetria aggiuntivi.

> [!IMPORTANT] 
> Questo cmdlet richiede una sessione di PowerShell con autorizzazioni di amministratore.

> [!NOTE] 
> - Questo cmdlet richiede la revisione e l'accettazione della licenza e dell'informativa sulla privacy.
> - Il motore di strumentazione aggiunge ulteriore sovraccarico ed è disattivato per impostazione predefinita.

## <a name="examples"></a>Esempi

```powershell
PS C:\> Enable-InstrumentationEngine
```

## <a name="parameters"></a>Parametri

### <a name="-acceptlicense"></a>-AcceptLicense (Licenza accettata)
**Opzionale.** Utilizzare questa opzione per accettare la licenza e l'informativa sulla privacy in installazioni headless.

### <a name="-verbose"></a>-Verbose
**Parametro comune.** Utilizzare questa opzione per generare log dettagliati.

## <a name="output"></a>Output


#### <a name="example-output-from-successfully-enabling-the-instrumentation-engine"></a>Output di esempio dall'attivazione corretta del motore di strumentazioneExample output from successfully enabling the instrumentation engine

```
Configuring IIS Environment for instrumentation engine...
Configuring registry for instrumentation engine...
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
 - [Ottenere la configurazione](status-monitor-v2-api-get-config.md) per verificare che le impostazioni siano state registrate correttamente.
 - [Ottenere lo stato](status-monitor-v2-api-get-status.md) per controllare il monitoraggio.
