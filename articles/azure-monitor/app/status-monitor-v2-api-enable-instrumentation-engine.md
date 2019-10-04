---
title: 'Informazioni di riferimento sulle API di Azure Status Monitor V2: Abilitare il motore di strumentazione | Microsoft Docs'
description: Riferimento all'API Status Monitor V2. Enable-InstrumentationEngine. Monitora le prestazioni del sito Web senza ridistribuire il sito Web. Funziona con le app Web ASP.NET ospitate in locale, in macchine virtuali o in Azure.
services: application-insights
documentationcenter: .net
author: TimothyMothra
manager: alexklim
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: tilee
ms.openlocfilehash: 3c0f4e015d3e01e86daaf101c15e16857540a520
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71033158"
---
# <a name="status-monitor-v2-api-enable-instrumentationengine"></a>API Status Monitor V2: Enable-InstrumentationEngine

Questo articolo descrive un cmdlet che fa parte del modulo di [PowerShell AZ. ApplicationMonitor](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

## <a name="description"></a>Descrizione

Abilita il motore di strumentazione impostando alcune chiavi del registro di sistema.
Riavviare IIS per rendere effettive le modifiche.

Il motore di strumentazione può integrare i dati raccolti dagli SDK .NET.
Raccoglie eventi e messaggi che descrivono l'esecuzione di un processo gestito. Questi eventi e messaggi includono codici di risultato di dipendenza, verbi HTTP e [testo del comando SQL](asp-net-dependencies.md#advanced-sql-tracking-to-get-full-sql-query).

Abilitare il motore di strumentazione se:
- Il monitoraggio è già stato abilitato con il cmdlet Enable ma non è stato abilitato il motore di strumentazione.
- L'app è stata instrumentata manualmente con gli SDK .NET e si vogliono raccogliere dati di telemetria aggiuntivi.

> [!IMPORTANT] 
> Questo cmdlet richiede una sessione di PowerShell con autorizzazioni di amministratore.

> [!NOTE] 
> - Questo cmdlet richiede la revisione e l'accettazione delle condizioni di licenza e informativa sulla privacy.
> - Il motore di strumentazione aggiunge ulteriore overhead ed è disattivato per impostazione predefinita.

## <a name="examples"></a>Esempi

```powershell
PS C:\> Enable-InstrumentationEngine
```

## <a name="parameters"></a>Parametri

### <a name="-acceptlicense"></a>-AcceptLicense
**Facoltativo.** Usare questa opzione per accettare la licenza e l'informativa sulla privacy nelle installazioni senza intestazione.

### <a name="-verbose"></a>-Verbose
**Parametro comune.** Usare questa opzione per restituire i log dettagliati.

## <a name="output"></a>Output


#### <a name="example-output-from-successfully-enabling-the-instrumentation-engine"></a>Esempio di output di abilitazione del motore di strumentazione

```
Configuring IIS Environment for instrumentation engine...
Configuring registry for instrumentation engine...
```

## <a name="next-steps"></a>Passaggi successivi

  Visualizzare i dati di telemetria:
 - [Esplora le metriche](../../azure-monitor/app/metrics-explorer.md) per monitorare le prestazioni e l'utilizzo.
- Eseguire [ricerche negli eventi e nei log](../../azure-monitor/app/diagnostic-search.md) per diagnosticare i problemi.
- Usare [Analytics](../../azure-monitor/app/analytics.md) per query più avanzate.
- [Creare dashboard](../../azure-monitor/app/overview-dashboard.md).
 
 Aggiungere altri dati di telemetria:
 - [Creare test Web](monitor-web-app-availability.md) per assicurarsi che il sito rimanga attivo.
- Aggiungere i dati di telemetria del [client Web](../../azure-monitor/app/javascript.md) per visualizzare le eccezioni dal codice della pagina Web e per abilitare le chiamate di traccia.
- [Aggiungere il Application Insights SDK al codice per](../../azure-monitor/app/asp-net.md) poter inserire le chiamate di traccia e log.
 
 Eseguire altre operazioni con Status Monitor V2:
 - Usare la guida per la [risoluzione dei problemi](status-monitor-v2-troubleshoot.md) Status Monitor V2.
 - [Ottenere la configurazione](status-monitor-v2-api-get-config.md) per verificare che le impostazioni siano state registrate correttamente.
 - [Ottenere lo stato](status-monitor-v2-api-get-status.md) per controllare il monitoraggio.
