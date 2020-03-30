---
title: Informazioni di riferimento sull'API di Azure Application Insights AgentAzure Application Insights Agent API reference
description: Informazioni di riferimento sull'API dell'agente di Application Insights.Application Insights Agent API reference. Enable-ApplicationInsightsMonitoring.Enable-ApplicationInsightsMonitoring. Monitorare le prestazioni del sito Web senza ridistribuire il sito Web. Funziona con ASP.NET app Web ospitate in locale, nelle macchine virtuali o in Azure.Works with a web apps hosted on-premises, in VMs, or on Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 8bbdc96a49fffc91f80d24a9eb0926766f86ee16
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671308"
---
# <a name="application-insights-agent-api-enable-applicationinsightsmonitoring"></a>API dell'agente di Application Insights: Enable-ApplicationInsightsMonitoring

In questo articolo viene descritto un cmdlet membro del [modulo PowerShell Az.ApplicationMonitor](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

## <a name="description"></a>Descrizione

Consente il monitoraggio delle connessione senza codice delle app IIS in un computer di destinazione.

Questo cmdlet modificherà il file applicationHost.config di IIS e imposterà alcune chiavi del Registro di sistema.
Verrà inoltre creato un file applicationinsights.ikey.config, che definisce la chiave di strumentazione utilizzata da ogni app.
IIS caricherà RedfieldModule all'avvio, che inserirà Application Insights SDK nelle applicazioni all'avvio delle applicazioni.
Riavviare IIS per rendere effettive le modifiche.

Dopo aver abilitato il monitoraggio, è consigliabile usare [metriche](live-stream.md) live per verificare rapidamente se l'app ci invia dati di telemetria.


> [!NOTE] 
> - Per iniziare, è necessaria una chiave di strumentazione. Per ulteriori informazioni, vedere [Creare una risorsa](create-new-resource.md#copy-the-instrumentation-key).
> - Questo cmdlet richiede la revisione e l'accettazione della licenza e dell'informativa sulla privacy.

> [!IMPORTANT] 
> Questo cmdlet richiede una sessione di PowerShell con autorizzazioni di amministratore e criteri di esecuzione con privilegi elevati. Per altre informazioni, vedere [Eseguire PowerShell come amministratore con criteri](status-monitor-v2-detailed-instructions.md#run-powershell-as-admin-with-an-elevated-execution-policy)di esecuzione con privilegi elevati.

## <a name="examples"></a>Esempi

### <a name="example-with-a-single-instrumentation-key"></a>Esempio con una singola chiave di strumentazioneExample with a single instrumentation key
In questo esempio, a tutte le app nel computer corrente viene assegnata una singola chiave di strumentazione.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

### <a name="example-with-an-instrumentation-key-map"></a>Esempio con una mappa delle chiavi di strumentazioneExample with an instrumentation key map
Esempio:
- `MachineFilter`corrisponde al computer corrente `'.*'` utilizzando il carattere jolly.
- `AppFilter='WebAppExclude'`fornisce `null` una chiave di strumentazione. L'app specificata non verrà instrumentata.
- `AppFilter='WebAppOne'`assegna all'app specificata una chiave di strumentazione univoca.
- `AppFilter='WebAppTwo'`assegna all'app specificata una chiave di strumentazione univoca.
- Infine, `AppFilter` usa `'.*'` anche il carattere jolly per trovare la corrispondenza con tutte le app Web che non corrispondono alle regole precedenti e assegnare una chiave di strumentazione predefinita.
- Gli spazi vengono aggiunti per la leggibilità.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap 
    @(@{MachineFilter='.*';AppFilter='WebAppExclude'},
      @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'}},
      @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'}},
      @{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'}})

```


## <a name="parameters"></a>Parametri

### <a name="-instrumentationkey"></a>-InstrumentationKey (Chiave di Strumentazione)
**Obbligatorio.** Usare questo parametro per fornire una singola chiave di strumentazione per l'utilizzo da parte di tutte le app nel computer di destinazione.

### <a name="-instrumentationkeymap"></a>-InstrumentationKeyMap (mappa delle chiavi di strumentazione)
**Obbligatorio.** Usare questo parametro per fornire più chiavi di strumentazione e un mapping delle chiavi di strumentazione usate da ogni app.
È possibile creare un singolo script `MachineFilter`di installazione per più computer impostando .

> [!IMPORTANT]
> Le app corrisponderanno alle regole nell'ordine in cui vengono fornite le regole. Quindi è necessario specificare prima le regole più specifiche per ultime e le regole più generiche.

#### <a name="schema"></a>SCHEMA
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'}})`

- **MachineFilter** è un'espressione regolare c'è obbligatoria del nome del computer o della macchina virtuale.
    - '.' corrisponderà a tutti
    - 'ComputerName' corrisponderà solo ai computer con il nome esatto specificato.
- **AppFilter** è un'espressione regolare c'è obbligatoria del nome del sito IIS. È possibile ottenere un elenco di siti sul server eseguendo il comando [get-iissite](https://docs.microsoft.com/powershell/module/iisadministration/get-iissite).
    - '.' corrisponderà a tutti
    - 'SiteName' corrisponderà solo al sito IIS con il nome esatto specificato.
- **InstrumentationKey** è necessario per abilitare il monitoraggio delle app che corrispondono ai due filtri precedenti.
    - Lasciare null questo valore se si desidera definire regole per escludere il monitoraggio.


### <a name="-enableinstrumentationengine"></a>-EnableInstrumentationEngine -EnableInstrumentationEngine
**Opzionale.** Utilizzare questa opzione per consentire al motore di strumentazione di raccogliere eventi e messaggi su ciò che accade durante l'esecuzione di un processo gestito. Questi eventi e messaggi includono codici risultato di dipendenza, verbi HTTP e testo del comando SQL.

Il motore di strumentazione aggiunge overhead ed è disattivato per impostazione predefinita.

### <a name="-acceptlicense"></a>-AcceptLicense (Licenza accettata)
**Opzionale.** Utilizzare questa opzione per accettare la licenza e l'informativa sulla privacy in installazioni headless.

### <a name="-ignoresharedconfig"></a>-IgnoreSharedConfig
Quando si dispone di un cluster di server Web, è possibile che si stia utilizzando una [configurazione condivisa.](https://docs.microsoft.com/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211)
Il HttpModule non può essere inserito in questa configurazione condivisa.
Questo script avrà esito negativo con il messaggio che sono necessarie ulteriori operazioni di installazione.
Utilizzare questa opzione per ignorare questo controllo e continuare l'installazione dei prerequisiti. Per altre informazioni, vedere [conflitto noto con iis-shared-configurationFor more information, see known conflict-with-iis-shared-configuration](status-monitor-v2-troubleshoot.md#conflict-with-iis-shared-configuration)

### <a name="-verbose"></a>-Verbose
**Parametro comune.** Utilizzare questa opzione per visualizzare i registri dettagliati.

### <a name="-whatif"></a>-WhatIf 
**Parametro comune.** Usare questa opzione per testare e convalidare i parametri di input senza abilitare effettivamente il monitoraggio.

## <a name="output"></a>Output


#### <a name="example-output-from-a-successful-enablement"></a>Output di esempio da un'abilitazione riuscitaExample output from a successful enablement

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
- [Cerca eventi e log](../../azure-monitor/app/diagnostic-search.md) per diagnosticare i problemi.
- [Usa Analytics](../../azure-monitor/app/analytics.md) per query più avanzate.
- [Creare dashboard](../../azure-monitor/app/overview-dashboard.md).
 
 Aggiungere altri dati di telemetria:
 - [Creare test Web](monitor-web-app-availability.md) per assicurarsi che il sito rimanga attivo.
- [Aggiungere dati di telemetria del client Web](../../azure-monitor/app/javascript.md) per visualizzare le eccezioni dal codice della pagina Web e abilitare le chiamate di traccia.
- [Aggiungere Application Insights SDK al codice](../../azure-monitor/app/asp-net.md) in modo da poter inserire chiamate di traccia e log.
 
 Fai di più con Application Insights Agent:
 - Usare la guida per [risolvere i problemi relativi](status-monitor-v2-troubleshoot.md) all'agente application Insights.Use our guide to troubleshoot Application Insights Agent.
 - [Ottenere la configurazione](status-monitor-v2-api-get-config.md) per verificare che le impostazioni siano state registrate correttamente.
 - [Ottenere lo stato](status-monitor-v2-api-get-status.md) per controllare il monitoraggio.
