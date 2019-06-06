---
title: "Azure riferimento all'API v2 Status Monitor: Impostare la configurazione di | Microsoft Docs"
description: Riferimento API v2 di monitoraggio dello stato. Set-ApplicationInsightsMonitoringConfig. Monitorare le prestazioni di siti Web senza ridistribuire il sito Web. Funziona con le app web ASP.NET ospitate in locale, in macchine virtuali o in Azure.
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
ms.openlocfilehash: 562ce8a4267370be9b049e3b56f213f82deb89c0
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/06/2019
ms.locfileid: "66734996"
---
# <a name="status-monitor-v2-api-set-applicationinsightsmonitoringconfig-v021-alpha"></a>API v2 di Status Monitor: Set-ApplicationInsightsMonitoringConfig (v0.2.1-alpha)

Questo documento descrive un cmdlet che è un membro del [modulo di Az.ApplicationMonitor PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

> [!IMPORTANT]
> Stato monitoraggio v2 è attualmente in anteprima pubblica.
> Questa versione di anteprima viene fornita senza un contratto di servizio e non è consigliabile per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate e alcune potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="description"></a>Descrizione

Imposta il file di configurazione senza eseguire una reinstallazione completa.
Riavviare IIS per rendere effettive le modifiche.

> [!IMPORTANT] 
> Questo cmdlet richiede una sessione di PowerShell con autorizzazioni di amministratore.


## <a name="examples"></a>Esempi

### <a name="example-with-a-single-instrumentation-key"></a>Esempio con una chiave di strumentazione singola
In questo esempio, tutte le app nel computer corrente verranno assegnate una chiave di strumentazione singola.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

### <a name="example-with-an-instrumentation-key-map"></a>Esempio con una mappa di chiavi di strumentazione
Esempio:
- `MachineFilter` Trova la corrispondenza del computer corrente usando il `'.*'` con caratteri jolly.
- `AppFilter='WebAppExclude'` fornisce un `null` chiave di strumentazione. Non è possibile instrumentare l'app specificata.
- `AppFilter='WebAppOne'` Assegna l'app specificata una chiave di strumentazione univoca.
- `AppFilter='WebAppTwo'` Assegna l'app specificata una chiave di strumentazione univoca.
- Infine `AppFilter` Usa anche il `'.*'` con caratteri jolly per la corrispondenza di tutte le app web che non sono associate dalle regole precedenti e assegnare una chiave di strumentazione predefinita.
- Vengono aggiunti spazi per migliorare la leggibilità.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap 
    @(@{MachineFilter='.*';AppFilter='WebAppExclude'},
      @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'},
      @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'},
      @{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'})

```


## <a name="parameters"></a>Parametri

### <a name="-instrumentationkey"></a>-InstrumentationKey
**Obbligatorio.** Usare questo parametro per specificare una chiave di strumentazione singola per l'uso da tutte le app nel computer di destinazione.

### <a name="-instrumentationkeymap"></a>-InstrumentationKeyMap
**Obbligatorio.** Usare questo parametro per specificare più chiavi di strumentazione e il mapping delle chiavi di strumentazione usata da ogni app.
È possibile creare uno script di installazione singola per diversi computer impostando `MachineFilter`.

> [!IMPORTANT]
> Le app corrisponderanno rispetto alle regole nell'ordine in cui vengono fornite le regole. Pertanto, è necessario specificare le regole più specifiche prima e ultima le regole più generiche.

#### <a name="schema"></a>SCHEMA
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'})`

- **MachineFilter** è necessario un C# regex del computer o del nome della macchina virtuale.
    - '. * "corrisponderanno a tutti
    - 'ComputerName' corrisponderà solo i computer con il nome specificato.
- **AppFilter** è necessario un C# regex del computer o del nome della macchina virtuale.
    - '. * "corrisponderanno a tutti
    - 'ApplicationName' corrisponderà solo alle App IIS con il nome specificato.
- **Chiave di strumentazione** è necessaria per abilitare il monitoraggio delle App che corrispondono ai due filtri precedenti.
    - Lasciare questo valore è null se si desidera definire le regole per escludere il monitoraggio.


### <a name="-verbose"></a>-Verbose
**Parametro comune.** Usare questa opzione per visualizzare i log dettagliati.


## <a name="output"></a>Output

Per impostazione predefinita, nessun output.

#### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkey"></a>Esempio dettagliato di output impostando il file di configurazione tramite - chiave di strumentazione

```
VERBOSE: Operation: InstallWithIkey
VERBOSE: InstrumentationKeyMap parsed:
Filters:
0)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx AppFilter: .* MachineFilter: .*
VERBOSE: set config file
VERBOSE: Config File Path:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config
```

#### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkeymap"></a>Esempio dettagliato di output impostando il file di configurazione tramite - InstrumentationKeyMap

```
VERBOSE: Operation: InstallWithIkeyMap
VERBOSE: InstrumentationKeyMap parsed:
Filters:
0)InstrumentationKey:  AppFilter: WebAppExclude MachineFilter: .*
1)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2 AppFilter: WebAppTwo MachineFilter: .*
2)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault AppFilter: .* MachineFilter: .*
VERBOSE: set config file
VERBOSE: Config File Path:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config
```

## <a name="next-steps"></a>Passaggi successivi

  Visualizzare i dati di telemetria:
 - [Esplorare le metriche](../../azure-monitor/app/metrics-explorer.md) per monitorare le prestazioni e utilizzo.
- [Cercare eventi e log](../../azure-monitor/app/diagnostic-search.md) per diagnosticare i problemi.
- [Usare Analitica](../../azure-monitor/app/analytics.md) per più query avanzate.
- [Creare dashboard](../../azure-monitor/app/overview-dashboard.md).
 
 Aggiungere altri dati di telemetria:
 - [Creare test web](monitor-web-app-availability.md) per assicurarsi che il sito rimanga attivo.
- [Aggiungere dati di telemetria client web](../../azure-monitor/app/javascript.md) per visualizzare le eccezioni dal codice della pagina web e per consentire le chiamate di traccia.
- [Aggiungere Application Insights SDK al codice](../../azure-monitor/app/asp-net.md) in modo che è possibile inserire una traccia e registrare le chiamate
 
 Altri vantaggi v2 Status Monitor:
 - Usare la Guida alla [risolvere i problemi di](status-monitor-v2-troubleshoot.md) v2 Status Monitor.
 - [Ottenere la configurazione](status-monitor-v2-api-get-config.md) per confermare che le impostazioni sono state registrate correttamente.
 - [Ottenere lo stato](status-monitor-v2-api-get-status.md) per controllare il monitoraggio.
