---
title: "Riferimento all'API di v2 monitoraggio dello stato di Azure: Abilitare il monitoraggio | Microsoft Docs"
description: Stato monitoraggio v2 API riferimento Enable-ApplicationInsightsMonitoring. Monitorare le prestazioni di siti Web senza ridistribuire il sito Web. Questa funzionalità può essere usata con app Web ASP.NET ospitate in locale, in macchine virtuali o in Azure.
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
ms.openlocfilehash: 0a443df0c55dc916ef6d12d53811e9d9932370e7
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2019
ms.locfileid: "66255907"
---
# <a name="status-monitor-v2-api-enable-applicationinsightsmonitoring-v021-alpha"></a>API v2 di Status Monitor: Enable-ApplicationInsightsMonitoring (v0.2.1-alpha)

Questo documento descrive un cmdlet che viene fornito come un membro del [modulo di Az.ApplicationMonitor PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

> [!IMPORTANT]
> Stato monitoraggio v2 è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [condizioni per l'utilizzo per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="description"></a>Descrizione

Enable codice senza collegare il monitoraggio di applicazioni IIS in un computer di destinazione.
Questo cmdlet verrà modificare il file ApplicationHost. config IIS e impostare alcune chiavi del Registro di sistema.
Questo cmdlet verrà inoltre creato un applicationinsights.ikey.config, che definisce da quale applicazione viene usata la chiave di strumentazione.
IIS caricherà il RedfieldModule all'avvio e inserirà il SDK di Application Insights nelle applicazioni, come le applicazioni avviate.
Riavviare IIS per rendere effettive le modifiche.

Dopo l'abilitazione del monitoraggio, è consigliabile usare [Live Metrics](live-stream.md) per osservarne rapidamente se l'applicazione invia i dati di telemetria.


> [!NOTE] 
> Per iniziare, è necessario disporre di una chiave di strumentazione. Per altre informazioni, leggere [creare una nuova risorsa](create-new-resource.md#copy-the-instrumentation-key).


> [!IMPORTANT] 
> Questo cmdlet richiede una sessione di PowerShell con autorizzazioni di amministratore e con i criteri di esecuzione con privilegi elevati. Lettura [qui](status-monitor-v2-detailed-instructions.md#run-powershell-as-administrator-with-an-elevated-execution-policy) per altre informazioni.

> [!NOTE] 
> Questo cmdlet è necessario leggere e accettare la licenza e informativa sulla privacy.


## <a name="examples"></a>Esempi

### <a name="example-with-single-instrumentation-key"></a>Esempio con la chiave di strumentazione singola
In questo esempio, tutte le applicazioni nel computer corrente verranno assegnate una chiave di strumentazione singola.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

### <a name="example-with-instrumentation-key-map"></a>Esempio con mappa di chiavi di strumentazione
In questo esempio 
- `MachineFilter` corrisponderà a computer corrente usando il `'.*'` con caratteri jolly.
- `AppFilter='WebAppExclude'` fornisce un `null` chiave di strumentazione. Non è possibile instrumentare l'app.
- `AppFilter='WebAppOne'` verrà assegnato a questa app specifica una chiave di strumentazione univoca.
- `AppFilter='WebAppTwo'` si assegna una chiave di strumentazione univoca questa specifica app.
- Infine `AppFilter` Usa anche il `'.*'` con caratteri jolly per la corrispondenza di tutte le altre App web non trovare una corrispondenza con le regole precedenti e assegna una chiave di strumentazione predefinita.
- Per migliorare la leggibilità solo spazi.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap 
    @(@{MachineFilter='.*';AppFilter='WebAppExclude'},
      @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'},
      @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'},
      @{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'})

```


## <a name="parameters"></a>Parametri 

### <a name="-instrumentationkey"></a>-InstrumentationKey
**Obbligatorio.** Usare questo parametro per specificare un'unica iKey per l'uso in tutte le applicazioni nel computer di destinazione.

### <a name="-instrumentationkeymap"></a>-InstrumentationKeyMap
**Obbligatorio.** Usare questo parametro per specificare più valori iKey e un mapping tra le app da usare la chiave di strumentazione. È possibile creare uno script di installazione singola per diversi computer impostando il MachineFilter. 

> [!IMPORTANT] 
> Le applicazioni corrisponderanno rispetto alle regole nell'ordine in cui è indicati. Di conseguenza è necessario specificare le regole più specifiche prima e ultima le regole più generiche.

#### <a name="schema"></a>SCHEMA
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'})`

- **MachineFilter** è un'espressione regolare obbligatorio c# del nome del computer o macchina virtuale.
    - '. * "corrisponderanno a tutti
    - 'ComputerName' corrisponderà solo i computer con lo stesso nome esatto.
- **AppFilter** è un'espressione regolare obbligatorio c# del nome del computer o macchina virtuale.
    - '. * "corrisponderanno a tutti
    - 'ApplicationName' corrisponderanno solo le applicazioni di IIS con quel nome esatto.
- **Chiave di strumentazione** è necessaria per abilitare il monitoraggio delle applicazioni che corrispondono ai due filtri precedenti.
    - Lasciare questo valore null se si vuole definire regole per escludere il monitoraggio


### <a name="-enableinstrumentationengine"></a>-EnableInstrumentationEngine
**Facoltativo.** Usare questa opzione per abilitare il motore di strumentazione raccogliere eventi e messaggi di cosa che avviene durante l'esecuzione di un processo gestito. Tra cui i codici di risultato delle dipendenze, verbi HTTP e testo comando SQL. Il motore di strumentazione verrà aggiunto un ulteriore sovraccarico ed è disattivata per impostazione predefinita.

### <a name="-acceptlicense"></a>-AcceptLicense
**Facoltativo.** Utilizzare questa opzione per accettare la licenza e informativa sulla privacy in installazioni headless.

### <a name="-verbose"></a>-Verbose
**Parametro comune.** Usare questa opzione è attivata per i log dettagliati di output.

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
 - [Esaminare le metriche](../../azure-monitor/app/metrics-explorer.md) per monitorare le prestazioni e l'utilizzo
- [Cercare eventi e log](../../azure-monitor/app/diagnostic-search.md) per diagnosticare i problemi
- Per informazioni sulle query più avanzate, vedere [Analytics](../../azure-monitor/app/analytics.md)
- [Creare i dashboard](../../azure-monitor/app/overview-dashboard.md)
 
 Aggiungere altri dati di telemetria:
 - [Creare test web](monitor-web-app-availability.md) per assicurarsi che il sito rimanga attivo.
- [Aggiungere dati di telemetria client web](../../azure-monitor/app/javascript.md) per visualizzare le eccezioni dal codice della pagina web e per consentire di inserire chiamate di traccia.
- [Aggiungere Application Insights SDK al codice](../../azure-monitor/app/asp-net.md) in modo che è possibile inserire una traccia e registrare le chiamate
 
 Altri vantaggi v2 Status Monitor:
 - Usare la Guida alla [risoluzione dei problemi](status-monitor-v2-troubleshoot.md) v2 Status Monitor.
 - [Ottenere la configurazione](status-monitor-v2-api-get-config.md) per confermare che le impostazioni sono state registrate correttamente.
 - [Ottenere lo stato](status-monitor-v2-api-get-status.md) per controllare il monitoraggio.
