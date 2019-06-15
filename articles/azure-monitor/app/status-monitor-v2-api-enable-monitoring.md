---
title: "Azure riferimento all'API v2 Status Monitor: Abilitare il monitoraggio | Microsoft Docs"
description: Riferimento API v2 di monitoraggio dello stato. Enable-ApplicationInsightsMonitoring. Monitorare le prestazioni di siti Web senza ridistribuire il sito Web. Funziona con le app web ASP.NET ospitate in locale, in macchine virtuali o in Azure.
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
ms.openlocfilehash: e87bfad11eee5b86d35e6b4f2846b094c467e0ef
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66734177"
---
# <a name="status-monitor-v2-api-enable-applicationinsightsmonitoring-v021-alpha"></a>API v2 di Status Monitor: Enable-ApplicationInsightsMonitoring (v0.2.1-alpha)

Questo articolo descrive un cmdlet che è un membro del [modulo di Az.ApplicationMonitor PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

> [!IMPORTANT]
> Stato monitoraggio v2 è attualmente in anteprima pubblica.
> Questa versione di anteprima viene fornita senza un contratto di servizio e non è consigliabile per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate e alcune potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="description"></a>Descrizione

Senza codice consente di collegare il monitoraggio delle applicazioni IIS su un computer di destinazione.

Questo cmdlet verrà modificare il file ApplicationHost. config IIS e impostare alcune chiavi del Registro di sistema.
Verrà anche creato un file applicationinsights.ikey.config, che definisce la chiave di strumentazione usata da ogni app.
IIS caricherà il RedfieldModule all'avvio, che inserirà il SDK di Application Insights nelle applicazioni, come le applicazioni di avvio.
Riavviare IIS per rendere effettive le modifiche.

Dopo aver abilitato il monitoraggio, è consigliabile usare [Live Metrics](live-stream.md) per verificare rapidamente se l'app invia dati di telemetria.


> [!NOTE] 
> - Per iniziare, è necessaria una chiave di strumentazione. Per altre informazioni, vedere [crea una risorsa](create-new-resource.md#copy-the-instrumentation-key).
> - Questo cmdlet richiede di verificare e accettare la licenza e informativa sulla privacy.

> [!IMPORTANT] 
> Questo cmdlet richiede una sessione di PowerShell con autorizzazioni di amministratore e un criterio di esecuzione con privilegi elevati. Per altre informazioni, vedere [eseguire PowerShell come amministratore con i criteri di esecuzione con privilegi elevati](status-monitor-v2-detailed-instructions.md#run-powershell-as-admin-with-an-elevated-execution-policy).

## <a name="examples"></a>Esempi

### <a name="example-with-a-single-instrumentation-key"></a>Esempio con una chiave di strumentazione singola
In questo esempio, tutte le app nel computer corrente vengono assegnate una chiave di strumentazione singola.

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
    - 'ComputerName' corrisponderà solo i computer con il nome esatto specificato.
- **AppFilter** è necessario un C# regex del computer o del nome della macchina virtuale.
    - '. * "corrisponderanno a tutti
    - 'ApplicationName' corrisponderà solo alle App IIS con il nome esatto specificato.
- **Chiave di strumentazione** è necessaria per abilitare il monitoraggio delle App che corrispondono ai due filtri precedenti.
    - Lasciare questo valore è null se si desidera definire le regole per escludere il monitoraggio.


### <a name="-enableinstrumentationengine"></a>-EnableInstrumentationEngine
**Facoltativo.** Usare questa opzione per abilitare il motore di strumentazione raccogliere gli eventi e messaggi relativi a ciò che avviene durante l'esecuzione di un processo gestito. Questi eventi e i messaggi includono codici di risultato delle dipendenze, verbi HTTP e testo comando SQL.

Il motore di strumentazione comporta un overhead aggiuntivo ed è disattivata per impostazione predefinita.

### <a name="-acceptlicense"></a>-AcceptLicense
**Facoltativo.** Utilizzare questa opzione per accettare la licenza e informativa sulla privacy in installazioni headless.

### <a name="-verbose"></a>-Verbose
**Parametro comune.** Usare questa opzione per visualizzare i log dettagliati.

### <a name="-whatif"></a>-WhatIf 
**Parametro comune.** Usare questa opzione per testare e convalidare i parametri di input senza effettivamente abilitazione del monitoraggio.

## <a name="output"></a>Output


#### <a name="example-output-from-a-successful-enablement"></a>Esempio dell'output di un'attivazione riuscita

```
Initiating Disable Process
Applying transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config'
'C:\Windows\System32\inetsrv\config\applicationHost.config' backed up to 'C:\Windows\System32\inetsrv\config\applicationHost.config.backup-2019-03-26_08-59-52z'
in :1,237
No element in the source document matches '/configuration/location[@path='']/system.webServer/modules/add[@name='ManagedHttpModuleHelper']'
Not executing RemoveAll (transform line 1, 546)
Transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config' was successfully applied. Operation: 'disable'
GAC Module will not be removed, since this operation might cause IIS instabilities
Configuring IIS Environment for codeless attach...
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]
Configuring IIS Environment for instrumentation engine...
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]
Configuring registry for instrumentation engine...
Successfully disabled Application Insights Status Monitor
Installing GAC module 'C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.0\content\Runtime\Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll'
Applying transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config'
Found GAC module Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.ManagedHttpModuleHelper, Microsoft.AppInsights.IIS.ManagedHttpModuleHelper, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35
'C:\Windows\System32\inetsrv\config\applicationHost.config' backed up to 'C:\Windows\System32\inetsrv\config\applicationHost.config.backup-2019-03-26_08-59-52z_1'
Transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config' was successfully applied. Operation: 'enable'
Configuring IIS Environment for codeless attach...
Configuring IIS Environment for instrumentation engine...
Configuring registry for instrumentation engine...
Updating app pool permissions...
Successfully enabled Application Insights Status Monitor
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
- [Aggiungere Application Insights SDK al codice](../../azure-monitor/app/asp-net.md) in modo che è possibile inserire una traccia e registrare le chiamate.
 
 Altri vantaggi v2 Status Monitor:
 - Usare la Guida alla [risolvere i problemi di](status-monitor-v2-troubleshoot.md) v2 Status Monitor.
 - [Ottenere la configurazione](status-monitor-v2-api-get-config.md) per confermare che le impostazioni sono state registrate correttamente.
 - [Ottenere lo stato](status-monitor-v2-api-get-status.md) per controllare il monitoraggio.
