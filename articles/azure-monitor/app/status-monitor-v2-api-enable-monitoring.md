---
title: 'Informazioni di riferimento sulle API di Azure Status Monitor V2: Abilita monitoraggio | Microsoft Docs'
description: Riferimento all'API Status Monitor V2. Enable-ApplicationInsightsMonitoring. Monitora le prestazioni del sito Web senza ridistribuire il sito Web. Funziona con le app Web ASP.NET ospitate in locale, in macchine virtuali o in Azure.
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
ms.openlocfilehash: d3963889e3604fb67cb526b992e7ca27b1212b59
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326366"
---
# <a name="status-monitor-v2-api-enable-applicationinsightsmonitoring"></a>API Status Monitor V2: Enable-ApplicationInsightsMonitoring

Questo articolo descrive un cmdlet che fa parte del modulo di [PowerShell AZ. ApplicationMonitor](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

## <a name="description"></a>Descrizione

Consente il monitoraggio di associazione non codificato delle app IIS in un computer di destinazione.

Questo cmdlet consente di modificare il file applicationHost. config di IIS e di impostare alcune chiavi del registro di sistema.
Creerà anche un file applicationinsights. Ikey. config, che definisce la chiave di strumentazione usata da ogni app.
IIS caricherà il RedfieldModule all'avvio, in modo da inserire il Application Insights SDK nelle applicazioni quando le applicazioni vengono avviate.
Riavviare IIS per rendere effettive le modifiche.

Dopo aver abilitato il monitoraggio, è consigliabile usare le [metriche in tempo reale](live-stream.md) per verificare rapidamente se l'app sta inviando dati di telemetria.


> [!NOTE] 
> - Per iniziare, è necessaria una chiave di strumentazione. Per altre informazioni, vedere [creare una risorsa](create-new-resource.md#copy-the-instrumentation-key).
> - Questo cmdlet richiede la revisione e l'accettazione delle condizioni di licenza e informativa sulla privacy.

> [!IMPORTANT] 
> Questo cmdlet richiede una sessione di PowerShell con autorizzazioni di amministratore e un criterio di esecuzione con privilegi elevati. Per altre informazioni, vedere [eseguire PowerShell come amministratore con criteri di esecuzione elevati](status-monitor-v2-detailed-instructions.md#run-powershell-as-admin-with-an-elevated-execution-policy).

## <a name="examples"></a>Esempi

### <a name="example-with-a-single-instrumentation-key"></a>Esempio con una singola chiave di strumentazione
In questo esempio, a tutte le app nel computer corrente viene assegnata una sola chiave di strumentazione.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

### <a name="example-with-an-instrumentation-key-map"></a>Esempio con una mappa delle chiavi di strumentazione
Esempio:
- `MachineFilter`corrisponde al computer corrente usando il `'.*'` carattere jolly.
- `AppFilter='WebAppExclude'`fornisce una `null` chiave di strumentazione. L'app specificata non verrà instrumentata.
- `AppFilter='WebAppOne'`assegna all'app specificata una chiave di strumentazione univoca.
- `AppFilter='WebAppTwo'`assegna all'app specificata una chiave di strumentazione univoca.
- Infine, `AppFilter` usa anche il `'.*'` carattere jolly per trovare la corrispondenza con tutte le app Web che non corrispondono alle regole precedenti e assegnare una chiave di strumentazione predefinita.
- Gli spazi vengono aggiunti per migliorare la leggibilità.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap 
    @(@{MachineFilter='.*';AppFilter='WebAppExclude'},
      @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'}},
      @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'}},
      @{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'}})

```


## <a name="parameters"></a>Parametri

### <a name="-instrumentationkey"></a>-InstrumentationKey
**Obbligatorio.** Usare questo parametro per fornire una singola chiave di strumentazione da usare per tutte le app nel computer di destinazione.

### <a name="-instrumentationkeymap"></a>-InstrumentationKeyMap
**Obbligatorio.** Usare questo parametro per specificare più chiavi di strumentazione e un mapping delle chiavi di strumentazione usate da ogni app.
È possibile creare un singolo script di installazione per diversi computer impostando `MachineFilter`.

> [!IMPORTANT]
> Le app corrisponderanno alle regole nell'ordine in cui vengono fornite le regole. Pertanto, è necessario specificare prima le regole più specifiche e le regole più generiche.

#### <a name="schema"></a>SCHEMA
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'}})`

- **MachineFilter** è un'espressione C# regolare obbligatoria del nome del computer o della macchina virtuale.
    - '. *' corrisponderà a tutti
    - ' ComputerName ' corrisponde solo ai computer con il nome esatto specificato.
- **AppFilter** è un'espressione C# regolare obbligatoria del nome del sito IIS. È possibile ottenere un elenco dei siti nel server eseguendo il comando [Get-iissite](https://docs.microsoft.com/powershell/module/iisadministration/get-iissite).
    - '. *' corrisponderà a tutti
    - ' SiteName ' corrisponderà solo al sito IIS con il nome esatto specificato.
- **InstrumentationKey** è necessario per abilitare il monitoraggio delle app che corrispondono ai due filtri precedenti.
    - Lasciare questo valore null se si desidera definire regole per escludere il monitoraggio.


### <a name="-enableinstrumentationengine"></a>-EnableInstrumentationEngine
**Facoltativo.** Usare questa opzione per consentire al motore di strumentazione di raccogliere gli eventi e i messaggi relativi a ciò che accade durante l'esecuzione di un processo gestito. Questi eventi e messaggi includono codici di risultato di dipendenza, verbi HTTP e testo del comando SQL.

Il motore di strumentazione aggiunge un sovraccarico ed è disattivato per impostazione predefinita.

### <a name="-acceptlicense"></a>-AcceptLicense
**Facoltativo.** Usare questa opzione per accettare la licenza e l'informativa sulla privacy nelle installazioni senza intestazione.

### <a name="-ignoresharedconfig"></a>-IgnoreSharedConfig
Quando si dispone di un cluster di server Web, è possibile che si stia utilizzando una [configurazione condivisa](https://docs.microsoft.com/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211).
Non è possibile inserire HttpModule in questa configurazione condivisa.
Questo script avrà esito negativo con il messaggio che è necessario eseguire ulteriori passaggi di installazione.
Usare questa opzione per ignorare questa verifica e continuare l'installazione dei prerequisiti. Per altre informazioni, vedere [conflitto noto-con-IIS-Shared-Configuration](status-monitor-v2-troubleshoot.md#conflict-with-iis-shared-configuration)

### <a name="-verbose"></a>-Verbose
**Parametro comune.** Usare questa opzione per visualizzare i log dettagliati.

### <a name="-whatif"></a>-WhatIf 
**Parametro comune.** Usare questa opzione per verificare e convalidare i parametri di input senza abilitare effettivamente il monitoraggio.

## <a name="output"></a>Output


#### <a name="example-output-from-a-successful-enablement"></a>Esempio di output di una corretta abilitazione

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
 - [Esplora le metriche](../../azure-monitor/app/metrics-explorer.md) per monitorare le prestazioni e l'utilizzo.
- Eseguire [ricerche negli eventi e nei log](../../azure-monitor/app/diagnostic-search.md) per diagnosticare i problemi.
- [Usare Analytics](../../azure-monitor/app/analytics.md) per query più avanzate.
- [Creare dashboard](../../azure-monitor/app/overview-dashboard.md).
 
 Aggiungere altri dati di telemetria:
 - [Creare test Web](monitor-web-app-availability.md) per assicurarsi che il sito rimanga attivo.
- Aggiungere i dati di telemetria del [client Web](../../azure-monitor/app/javascript.md) per visualizzare le eccezioni dal codice della pagina Web e per abilitare le chiamate di traccia.
- [Aggiungere il Application Insights SDK al codice per](../../azure-monitor/app/asp-net.md) poter inserire le chiamate di traccia e log.
 
 Eseguire altre operazioni con Status Monitor V2:
 - Usare la guida per la [risoluzione dei problemi](status-monitor-v2-troubleshoot.md) Status Monitor V2.
 - [Ottenere la configurazione](status-monitor-v2-api-get-config.md) per verificare che le impostazioni siano state registrate correttamente.
 - [Ottenere lo stato](status-monitor-v2-api-get-status.md) per controllare il monitoraggio.
