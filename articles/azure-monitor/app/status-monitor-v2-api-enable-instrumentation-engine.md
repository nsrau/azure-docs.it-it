---
title: "Azure riferimento all'API v2 Status Monitor: Abilitare il motore di strumentazione | Microsoft Docs"
description: Riferimento API v2 di monitoraggio dello stato. Enable-InstrumentationEngine. Monitorare le prestazioni di siti Web senza ridistribuire il sito Web. Funziona con le app web ASP.NET ospitate in locale, in macchine virtuali o in Azure.
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
ms.openlocfilehash: 1e30490dbd51f541afd0b7036769cfc638a75877
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66514376"
---
# <a name="status-monitor-v2-api-enable-instrumentationengine-v021-alpha"></a>API v2 di Status Monitor: Enable-InstrumentationEngine (v0.2.1-alpha)

Questo articolo descrive un cmdlet che è un membro del [modulo di Az.ApplicationMonitor PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

> [!IMPORTANT]
> Stato monitoraggio v2 è attualmente in anteprima pubblica.
> Questa versione di anteprima viene fornita senza un contratto di servizio e non è consigliabile per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate e alcune potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="description"></a>Descrizione

Consente al motore di strumentazione impostando alcune chiavi del Registro di sistema.
Riavviare IIS per rendere effettive le modifiche.

Il motore di strumentazione possibile integrare i dati raccolti dagli SDK .NET.
Raccoglie gli eventi e i messaggi che descrivono l'esecuzione di un processo gestito. Questi eventi e i messaggi includono codici di risultato delle dipendenze, verbi HTTP e testo comando SQL.

Abilitare il motore di strumentazione se:
- Già stato attivato il monitoraggio con il cmdlet Abilita ma non abilitare il motore di strumentazione.
- È stata instrumentata manualmente l'app con gli SDK di .NET e si desidera raccogliere dati di telemetria aggiuntivi.

> [!IMPORTANT] 
> Questo cmdlet richiede una sessione di PowerShell con autorizzazioni di amministratore.

> [!NOTE] 
> - Questo cmdlet richiede di verificare e accettare la licenza e informativa sulla privacy.
> - Il motore di strumentazione consente di aggiungere overhead ed è disattivata per impostazione predefinita.

## <a name="examples"></a>Esempi

```powershell
PS C:\> Enable-InstrumentationEngine
```

## <a name="parameters"></a>Parametri

### <a name="-acceptlicense"></a>-AcceptLicense
**Facoltativo.** Utilizzare questa opzione per accettare la licenza e informativa sulla privacy in installazioni headless.

### <a name="-verbose"></a>-Verbose
**Parametro comune.** Usare questa opzione è attivata per i log dettagliati di output.

## <a name="output"></a>Output


#### <a name="example-output-from-successfully-enabling-the-instrumentation-engine"></a>Output di esempio da abilitato correttamente il motore di strumentazione

```
Configuring IIS Environment for instrumentation engine...
Configuring registry for instrumentation engine...
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
 - [Ottenere la configurazione](status-monitor-v2-api-get-config.md) per confermare che le impostazioni sono state registrate correttamente.
 - [Ottenere lo stato](status-monitor-v2-api-get-status.md) per controllare il monitoraggio.
