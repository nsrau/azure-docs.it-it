---
title: Riferimento all'API dell'agente .NET di applicazione Azure Insights
description: Informazioni di riferimento sull'API dell'agente Application Insights. Monitora le prestazioni del sito Web senza ridistribuire il sito Web. Funziona con le app Web ASP.NET ospitate in locale, in macchine virtuali o in Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 32fd0da0095c34c4ef199eb703881e048473f0a4
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86499359"
---
# <a name="azure-monitor-application-insights-agent-api-reference"></a>Informazioni di riferimento sull'API dell'agente Application Insights di monitoraggio di Azure

Questo articolo descrive un cmdlet che fa parte del modulo di [PowerShell AZ. ApplicationMonitor](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

> [!NOTE] 
> - Per iniziare, è necessaria una chiave di strumentazione. Per altre informazioni, vedere [creare una risorsa](create-new-resource.md#copy-the-instrumentation-key).
> - Questo cmdlet richiede la revisione e l'accettazione delle condizioni di licenza e informativa sulla privacy.

> [!IMPORTANT] 
> Questo cmdlet richiede una sessione di PowerShell con autorizzazioni di amministratore e un criterio di esecuzione con privilegi elevati. Per altre informazioni, vedere [eseguire PowerShell come amministratore con criteri di esecuzione elevati](status-monitor-v2-detailed-instructions.md#run-powershell-as-admin-with-an-elevated-execution-policy).
> - Questo cmdlet richiede la revisione e l'accettazione delle condizioni di licenza e informativa sulla privacy.
> - Il motore di strumentazione aggiunge ulteriore overhead ed è disattivato per impostazione predefinita.


## <a name="enable-instrumentationengine"></a>Enable-InstrumentationEngine

Abilita il motore di strumentazione impostando alcune chiavi del registro di sistema.
Riavviare IIS per rendere effettive le modifiche.

Il motore di strumentazione può integrare i dati raccolti dagli SDK .NET.
Raccoglie eventi e messaggi che descrivono l'esecuzione di un processo gestito. Questi eventi e messaggi includono codici di risultato di dipendenza, verbi HTTP e [testo del comando SQL](asp-net-dependencies.md#advanced-sql-tracking-to-get-full-sql-query).

Abilitare il motore di strumentazione se:
- Il monitoraggio è già stato abilitato con il cmdlet Enable ma non è stato abilitato il motore di strumentazione.
- L'app è stata instrumentata manualmente con gli SDK .NET e si vogliono raccogliere dati di telemetria aggiuntivi.

### <a name="examples"></a>Esempi

```powershell
PS C:\> Enable-InstrumentationEngine
```

### <a name="parameters"></a>Parametri

#### <a name="-acceptlicense"></a>-AcceptLicense
**Opzionale.** Usare questa opzione per accettare la licenza e l'informativa sulla privacy nelle installazioni senza intestazione.

#### <a name="-verbose"></a>-Verbose
**Parametro comune.** Usare questa opzione per restituire i log dettagliati.

### <a name="output"></a>Output


##### <a name="example-output-from-successfully-enabling-the-instrumentation-engine"></a>Esempio di output di abilitazione del motore di strumentazione

```
Configuring IIS Environment for instrumentation engine...
Configuring registry for instrumentation engine...
```

## <a name="enable-applicationinsightsmonitoring"></a>Enable-ApplicationInsightsMonitoring

Consente il monitoraggio di associazione non codificato delle app IIS in un computer di destinazione.

Questo cmdlet modificherà il applicationHost.config IIS e imposterà alcune chiavi del registro di sistema.
Creerà anche un file di applicationinsights.ikey.config, che definisce la chiave di strumentazione usata da ogni app.
IIS caricherà il RedfieldModule all'avvio, in modo da inserire il Application Insights SDK nelle applicazioni quando le applicazioni vengono avviate.
Riavviare IIS per rendere effettive le modifiche.

Dopo aver abilitato il monitoraggio, è consigliabile usare le [metriche in tempo reale](live-stream.md) per verificare rapidamente se l'app sta inviando dati di telemetria.

### <a name="examples"></a>Esempi

#### <a name="example-with-a-single-instrumentation-key"></a>Esempio con una singola chiave di strumentazione
In questo esempio, a tutte le app nel computer corrente viene assegnata una sola chiave di strumentazione.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

#### <a name="example-with-an-instrumentation-key-map"></a>Esempio con una mappa delle chiavi di strumentazione
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


### <a name="parameters"></a>Parametri

#### <a name="-instrumentationkey"></a>-InstrumentationKey
**Obbligatoria.** Usare questo parametro per fornire una singola chiave di strumentazione da usare per tutte le app nel computer di destinazione.

#### <a name="-instrumentationkeymap"></a>-InstrumentationKeyMap
**Obbligatoria.** Usare questo parametro per specificare più chiavi di strumentazione e un mapping delle chiavi di strumentazione usate da ogni app.
È possibile creare un singolo script di installazione per diversi computer impostando `MachineFilter` .

> [!IMPORTANT]
> Le app corrisponderanno alle regole nell'ordine in cui vengono fornite le regole. Pertanto, è necessario specificare prima le regole più specifiche e le regole più generiche.

##### <a name="schema"></a>SCHEMA
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'}})`

- **MachineFilter** è un'espressione regolare C# obbligatoria del nome del computer o della macchina virtuale.
    - '. *' corrisponderà a tutti
    - ' ComputerName ' corrisponde solo ai computer con il nome esatto specificato.
- **AppFilter** è un'espressione regolare C# obbligatoria del nome del sito IIS. È possibile ottenere un elenco dei siti nel server eseguendo il comando [Get-iissite](/powershell/module/iisadministration/get-iissite).
    - '. *' corrisponderà a tutti
    - ' SiteName ' corrisponderà solo al sito IIS con il nome esatto specificato.
- **InstrumentationKey** è necessario per abilitare il monitoraggio delle app che corrispondono ai due filtri precedenti.
    - Lasciare questo valore null se si desidera definire regole per escludere il monitoraggio.


#### <a name="-enableinstrumentationengine"></a>-EnableInstrumentationEngine
**Opzionale.** Usare questa opzione per consentire al motore di strumentazione di raccogliere gli eventi e i messaggi relativi a ciò che accade durante l'esecuzione di un processo gestito. Questi eventi e messaggi includono codici di risultato di dipendenza, verbi HTTP e testo del comando SQL.

Il motore di strumentazione aggiunge un sovraccarico ed è disattivato per impostazione predefinita.

#### <a name="-acceptlicense"></a>-AcceptLicense
**Opzionale.** Usare questa opzione per accettare la licenza e l'informativa sulla privacy nelle installazioni senza intestazione.

#### <a name="-ignoresharedconfig"></a>-IgnoreSharedConfig
Quando si dispone di un cluster di server Web, è possibile che si stia utilizzando una [configurazione condivisa](/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211).
Non è possibile inserire HttpModule in questa configurazione condivisa.
Questo script avrà esito negativo con il messaggio che è necessario eseguire ulteriori passaggi di installazione.
Usare questa opzione per ignorare questa verifica e continuare l'installazione dei prerequisiti. Per altre informazioni, vedere [conflitto noto-con-IIS-Shared-Configuration](status-monitor-v2-troubleshoot.md#conflict-with-iis-shared-configuration)

#### <a name="-verbose"></a>-Verbose
**Parametro comune.** Usare questa opzione per visualizzare i log dettagliati.

#### <a name="-whatif"></a>-WhatIf 
**Parametro comune.** Usare questa opzione per verificare e convalidare i parametri di input senza abilitare effettivamente il monitoraggio.

### <a name="output"></a>Output

#### <a name="example-output-from-a-successful-enablement"></a>Esempio di output di una corretta abilitazione

```powershell
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

## <a name="disable-instrumentationengine"></a>Disable-InstrumentationEngine

Disabilita il motore di strumentazione rimuovendo alcune chiavi del registro di sistema.
Riavviare IIS per rendere effettive le modifiche.

### <a name="examples"></a>Esempi

```powershell
PS C:\> Disable-InstrumentationEngine
```

### <a name="parameters"></a>Parametri 

#### <a name="-verbose"></a>-Verbose
**Parametro comune.** Usare questa opzione per restituire i log dettagliati.

### <a name="output"></a>Output


##### <a name="example-output-from-successfully-disabling-the-instrumentation-engine"></a>Output di esempio dalla disabilitazione corretta del motore di strumentazione

```powershell
Configuring IIS Environment for instrumentation engine...
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]'
Configuring registry for instrumentation engine...
```

## <a name="disable-applicationinsightsmonitoring"></a>Disable-ApplicationInsightsMonitoring

Disabilita il monitoraggio nel computer di destinazione.
Questo cmdlet consente di rimuovere le modifiche apportate al applicationHost.config IIS e di rimuovere le chiavi del registro di sistema.

### <a name="examples"></a>Esempi

```powershell
PS C:\> Disable-ApplicationInsightsMonitoring
```

### <a name="parameters"></a>Parametri 

#### <a name="-verbose"></a>-Verbose
**Parametro comune.** Usare questa opzione per visualizzare i log dettagliati.

### <a name="output"></a>Output


##### <a name="example-output-from-successfully-disabling-monitoring"></a>Output di esempio dalla disabilitazione del monitoraggio riuscita

```powershell
Initiating Disable Process
Applying transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config'
'C:\Windows\System32\inetsrv\config\applicationHost.config' backed up to 'C:\Windows\System32\inetsrv\config\applicationHost.config.backup-2019-03-26_08-59-00z'
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
```


## <a name="get-applicationinsightsmonitoringconfig"></a>Get-ApplicationInsightsMonitoringConfig

Ottiene il file di configurazione e stampa i valori nella console.

### <a name="examples"></a>Esempi

```powershell
PS C:\> Get-ApplicationInsightsMonitoringConfig
```

### <a name="parameters"></a>Parametri

Nessun parametro necessario.

### <a name="output"></a>Output


##### <a name="example-output-from-reading-the-config-file"></a>Output di esempio della lettura del file di configurazione

```
RedfieldConfiguration:
Filters:
0)InstrumentationKey:  AppFilter: WebAppExclude MachineFilter: .*
1)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2 AppFilter: WebAppTwo MachineFilter: .*
2)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault AppFilter: .* MachineFilter: .*
```

## <a name="get-applicationinsightsmonitoringstatus"></a>Get-ApplicationInsightsMonitoringStatus

Questo cmdlet fornisce informazioni sulla risoluzione dei problemi relativi a Status Monitor.
Usare questo cmdlet per esaminare lo stato del monitoraggio, la versione del modulo di PowerShell e per esaminare il processo in esecuzione.
Questo cmdlet consente di segnalare le informazioni sulla versione e le informazioni sui file di chiave necessari per il monitoraggio.

### <a name="examples"></a>Esempi

#### <a name="example-application-status"></a>Esempio: stato dell'applicazione

Eseguire il comando `Get-ApplicationInsightsMonitoringStatus` per visualizzare lo stato di monitoraggio dei siti Web.

```powershell

PS C:\Windows\system32> Get-ApplicationInsightsMonitoringStatus

IIS Websites:

SiteName               : Default Web Site
ApplicationPoolName    : DefaultAppPool
SiteId                 : 1
SiteState              : Stopped

SiteName               : DemoWebApp111
ApplicationPoolName    : DemoWebApp111
SiteId                 : 2
SiteState              : Started
ProcessId              : not found

SiteName               : DemoWebApp222
ApplicationPoolName    : DemoWebApp222
SiteId                 : 3
SiteState              : Started
ProcessId              : 2024
Instrumented           : true
InstrumentationKey     : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx123

SiteName               : DemoWebApp333
ApplicationPoolName    : DemoWebApp333
SiteId                 : 4
SiteState              : Started
ProcessId              : 5184
AppAlreadyInstrumented : true
```

In questo esempio:
- **Identificatore computer** è un ID anonimo utilizzato per identificare in modo univoco il server. Se si crea una richiesta di supporto, sarà necessario questo ID per trovare i log del server.
- Il **sito Web predefinito** è stato arrestato in IIS
- **DemoWebApp111** è stato avviato in IIS, ma non ha ricevuto alcuna richiesta. Questo report mostra che non è presente alcun processo in esecuzione (ProcessId: non trovato).
- **DemoWebApp222** è in esecuzione e viene monitorato (instrumentato: true). In base alla configurazione dell'utente, la chiave di strumentazione xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx123 è stata trovata per questo sito.
- **DemoWebApp333** è stato instrumentato manualmente con Application Insights SDK. Status Monitor ha rilevato l'SDK e non monitorerà il sito.


#### <a name="example-powershell-module-information"></a>Esempio: informazioni sui moduli di PowerShell

Eseguire il comando `Get-ApplicationInsightsMonitoringStatus -PowerShellModule` per visualizzare le informazioni sul modulo corrente:

```powershell

PS C:\> Get-ApplicationInsightsMonitoringStatus -PowerShellModule

PowerShell Module version:
0.4.0-alpha

Application Insights SDK version:
2.9.0.3872

Executing PowerShell Module Assembly:
Microsoft.ApplicationInsights.Redfield.Configurator.PowerShell, Version=2.8.14.11432, Culture=neutral, PublicKeyToken=31bf3856ad364e35

PowerShell Module Directory:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.2\content\PowerShell

Runtime Paths:
ParentDirectory (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content

ConfigurationPath (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config

ManagedHttpModuleHelperPath (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll

RedfieldIISModulePath (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll

InstrumentationEngine86Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation32\MicrosoftInstrumentationEngine_x86.dll

InstrumentationEngine64Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\MicrosoftInstrumentationEngine_x64.dll

InstrumentationEngineExtensionHost86Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation32\Microsoft.ApplicationInsights.ExtensionsHost_x86.dll

InstrumentationEngineExtensionHost64Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.ExtensionsHost_x64.dll

InstrumentationEngineExtensionConfig86Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation32\Microsoft.InstrumentationEngine.Extensions.config

InstrumentationEngineExtensionConfig64Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.InstrumentationEngine.Extensions.config

ApplicationInsightsSdkPath (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.dll
```

#### <a name="example-runtime-status"></a>Esempio: stato di runtime

È possibile esaminare il processo nel computer instrumentato per verificare se tutte le dll sono state caricate. Se il monitoraggio è funzionante, è necessario caricare almeno 12 dll.

Eseguire il comando `Get-ApplicationInsightsMonitoringStatus -InspectProcess` :


```
PS C:\> Get-ApplicationInsightsMonitoringStatus -InspectProcess

iisreset.exe /status
Status for IIS Admin Service ( IISADMIN ) : Running
Status for Windows Process Activation Service ( WAS ) : Running
Status for Net.Msmq Listener Adapter ( NetMsmqActivator ) : Running
Status for Net.Pipe Listener Adapter ( NetPipeActivator ) : Running
Status for Net.Tcp Listener Adapter ( NetTcpActivator ) : Running
Status for World Wide Web Publishing Service ( W3SVC ) : Running

handle64.exe -accepteula -p w3wp
BF0: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.ServerTelemetryChannel.dll
C58: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.AzureAppServices.dll
C68: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.DependencyCollector.dll
C78: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.WindowsServer.dll
C98: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.Web.dll
CBC: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.PerfCounterCollector.dll
DB0: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.Agent.Intercept.dll
B98: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll
BB4: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.Contracts.dll
BCC: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.Redfield.Lightup.dll
BE0: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.dll

listdlls64.exe -accepteula w3wp
0x0000000019ac0000  0x127000  C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\MicrosoftInstrumentationEngine_x64.dll
0x00000000198b0000  0x4f000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.ExtensionsHost_x64.dll
0x000000000c460000  0xb2000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.Extensions.Base_x64.dll
0x000000000ad60000  0x108000  C:\Windows\TEMP\2.4.0.0.Microsoft.ApplicationInsights.Extensions.Intercept_x64.dll
```

### <a name="parameters"></a>Parametri

#### <a name="no-parameters"></a>(Nessun parametro)

Per impostazione predefinita, questo cmdlet segnalerà lo stato di monitoraggio delle applicazioni Web.
Usare questa opzione per verificare se l'applicazione è stata instrumentata correttamente.
È anche possibile esaminare la chiave di strumentazione di cui è stata trovata una corrispondenza per il sito.


#### <a name="-powershellmodule"></a>-PowerShellModule
**Facoltativo**. Usare questa opzione per segnalare i numeri di versione e i percorsi delle DLL necessarie per il monitoraggio.
Usare questa opzione se è necessario identificare la versione di qualsiasi DLL, incluso il Application Insights SDK.

#### <a name="-inspectprocess"></a>-InspectProcess

**Facoltativo**. Utilizzare questa opzione per segnalare se IIS è in esecuzione.
Verranno inoltre scaricati gli strumenti esterni per determinare se le DLL necessarie sono state caricate nel runtime IIS.


Se il processo ha esito negativo per qualsiasi motivo, è possibile eseguire questi comandi manualmente:
- iisreset.exe/status
- [handle64.exe](/sysinternals/downloads/handle) -p w3wp | findstr/I "InstrumentationEngine AI. ApplicationInsights
- [listdlls64.exe](/sysinternals/downloads/listdlls) w3wp | findstr/I "InstrumentationEngine AI ApplicationInsights"


#### <a name="-force"></a>-Force

**Facoltativo**. Utilizzato solo con InspectProcess. Usare questa opzione per ignorare la richiesta dell'utente visualizzata prima del download di altri strumenti.


## <a name="set-applicationinsightsmonitoringconfig"></a>Set-ApplicationInsightsMonitoringConfig

Imposta il file di configurazione senza eseguire una reinstallazione completa.
Riavviare IIS per rendere effettive le modifiche.

> [!IMPORTANT] 
> Questo cmdlet richiede una sessione di PowerShell con autorizzazioni di amministratore.


### <a name="examples"></a>Esempi

#### <a name="example-with-a-single-instrumentation-key"></a>Esempio con una singola chiave di strumentazione
In questo esempio, a tutte le app nel computer corrente verrà assegnata una sola chiave di strumentazione.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

#### <a name="example-with-an-instrumentation-key-map"></a>Esempio con una mappa delle chiavi di strumentazione
Esempio:
- `MachineFilter`corrisponde al computer corrente usando il `'.*'` carattere jolly.
- `AppFilter='WebAppExclude'`fornisce una `null` chiave di strumentazione. L'app specificata non verrà instrumentata.
- `AppFilter='WebAppOne'`assegna all'app specificata una chiave di strumentazione univoca.
- `AppFilter='WebAppTwo'`assegna all'app specificata una chiave di strumentazione univoca.
- Infine, `AppFilter` usa anche il `'.*'` carattere jolly per trovare la corrispondenza con tutte le app Web che non corrispondono alle regole precedenti e assegnare una chiave di strumentazione predefinita.
- Gli spazi vengono aggiunti per migliorare la leggibilità.

```powershell
Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap `
       @(@{MachineFilter='.*';AppFilter='WebAppExclude'},
          @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'}},
          @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'}},
          @{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'}})
```

### <a name="parameters"></a>Parametri

#### <a name="-instrumentationkey"></a>-InstrumentationKey
**Obbligatoria.** Usare questo parametro per fornire una singola chiave di strumentazione da usare per tutte le app nel computer di destinazione.

#### <a name="-instrumentationkeymap"></a>-InstrumentationKeyMap
**Obbligatoria.** Usare questo parametro per specificare più chiavi di strumentazione e un mapping delle chiavi di strumentazione usate da ogni app.
È possibile creare un singolo script di installazione per diversi computer impostando `MachineFilter` .

> [!IMPORTANT]
> Le app corrisponderanno alle regole nell'ordine in cui vengono fornite le regole. Pertanto, è necessario specificare prima le regole più specifiche e le regole più generiche.

##### <a name="schema"></a>SCHEMA
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'})`

- **MachineFilter** è un'espressione regolare C# obbligatoria del nome del computer o della macchina virtuale.
    - '. *' corrisponderà a tutti
    - ' ComputerName ' corrisponderà solo ai computer con il nome specificato.
- **AppFilter** è un'espressione regolare C# obbligatoria del nome del computer o della macchina virtuale.
    - '. *' corrisponderà a tutti
    - ' ApplicationName ' corrisponderà solo alle app IIS con il nome specificato.
- **InstrumentationKey** è necessario per abilitare il monitoraggio delle app che corrispondono ai due filtri precedenti.
    - Lasciare questo valore null se si desidera definire regole per escludere il monitoraggio.


#### <a name="-verbose"></a>-Verbose
**Parametro comune.** Usare questa opzione per visualizzare i log dettagliati.


### <a name="output"></a>Output

Per impostazione predefinita, non viene restituito alcun output.

##### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkey"></a>Esempio di output dettagliato dall'impostazione del file di configurazione tramite-InstrumentationKey

```
VERBOSE: Operation: InstallWithIkey
VERBOSE: InstrumentationKeyMap parsed:
Filters:
0)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx AppFilter: .* MachineFilter: .*
VERBOSE: set config file
VERBOSE: Config File Path:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config
```

##### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkeymap"></a>Esempio di output dettagliato dall'impostazione del file di configurazione tramite-InstrumentationKeyMap

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

## <a name="start-applicationinsightsmonitoringtrace"></a>Start-ApplicationInsightsMonitoringTrace

Raccoglie [gli eventi ETW](/windows/desktop/etw/event-tracing-portal) dal runtime di associazione non codificabile. Questo cmdlet rappresenta un'alternativa all'esecuzione di [PerfView](https://github.com/microsoft/perfview).

Gli eventi raccolti verranno stampati nella console in tempo reale e salvati in un file ETL. Il file ETL di output può essere aperto da [PerfView](https://github.com/microsoft/perfview) per un'analisi più approfondita.

Questo cmdlet verrà eseguito fino a quando non raggiungerà la durata del timeout (valore predefinito di 5 minuti) o verrà interrotto manualmente ( `Ctrl + C` ).

### <a name="examples"></a>Esempi

#### <a name="how-to-collect-events"></a>Come raccogliere gli eventi

Normalmente si richiede di raccogliere gli eventi per esaminare il motivo per cui l'applicazione non viene instrumentata.

Il runtime di associazione codificata genera eventi ETW all'avvio di IIS e all'avvio dell'applicazione.

Per raccogliere questi eventi:
1. In una console CMD con privilegi di amministratore eseguire `iisreset /stop` per disattivare IIS e tutte le app Web.
2. Esegui questo cmdlet
3. In una console CMD con privilegi di amministratore eseguire `iisreset /start` per avviare IIS.
4. Provare a passare all'app.
5. Al termine del caricamento dell'app, è possibile arrestarla manualmente ( `Ctrl + C` ) o attendere il timeout.

#### <a name="what-events-to-collect"></a>Eventi da raccogliere

Sono disponibili tre opzioni per la raccolta di eventi:
1. Usare l'opzione `-CollectSdkEvents` per raccogliere gli eventi generati da Application Insights SDK.
2. Usare l'opzione `-CollectRedfieldEvents` per raccogliere gli eventi generati da status monitor e dal runtime di Redfield. Questi log sono utili per la diagnosi di IIS e l'avvio dell'applicazione.
3. Usare entrambe le opzioni per raccogliere entrambi i tipi di evento.
4. Per impostazione predefinita, se non viene specificata alcuna opzione, verranno raccolti entrambi i tipi di evento.


### <a name="parameters"></a>Parametri

#### <a name="-maxdurationinminutes"></a>-MaxDurationInMinutes
**Opzionale.** Utilizzare questo parametro per impostare per quanto tempo lo script deve raccogliere gli eventi. Il valore predefinito è 5 minuti.

#### <a name="-logdirectory"></a>-LogDirectory
**Opzionale.** Usare questa opzione per impostare la directory di output del file ETL. Per impostazione predefinita, questo file verrà creato nella directory dei moduli di PowerShell. Il percorso completo verrà visualizzato durante l'esecuzione dello script.


#### <a name="-collectsdkevents"></a>-CollectSdkEvents
**Opzionale.** Usare questa opzione per raccogliere gli eventi di Application Insights SDK.

#### <a name="-collectredfieldevents"></a>-CollectRedfieldEvents
**Opzionale.** Usare questa opzione per raccogliere gli eventi da Status Monitor e dal runtime di Redfield.

#### <a name="-verbose"></a>-Verbose
**Parametro comune.** Usare questa opzione per restituire i log dettagliati.



### <a name="output"></a>Output


#### <a name="example-of-application-startup-logs"></a>Esempio di log di avvio dell'applicazione
```powershell
PS C:\Windows\system32> Start-ApplicationInsightsMonitoringTrace -ColectRedfieldEvents
Starting...
Log File: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\logs\20190627_144217_ApplicationInsights_ETW_Trace.etl
Tracing enabled, waiting for events.
Tracing will timeout in 5 minutes. Press CTRL+C to cancel.

2:42:31 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Resolved variables to: MicrosoftAppInsights_ManagedHttpModulePath='C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll', MicrosoftAppInsights_ManagedHttpModuleType='Microsoft.ApplicationInsights.RedfieldIISModule.RedfieldIISModule'
2:42:31 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Resolved variables to: MicrosoftDiagnosticServices_ManagedHttpModulePath2='', MicrosoftDiagnosticServices_ManagedHttpModuleType2=''
2:42:31 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Environment variable 'MicrosoftDiagnosticServices_ManagedHttpModulePath2' or 'MicrosoftDiagnosticServices_ManagedHttpModuleType2' is null, skipping managed dll loading
2:42:31 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace MulticastHttpModule.constructor, success, 70 ms
2:42:31 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace Current assembly 'Microsoft.ApplicationInsights.RedfieldIISModule, Version=2.8.18.27202, Culture=neutral, PublicKeyToken=f23a46de0be5d6f3' location 'C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll'
2:42:31 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace Matched filter '.*'~'STATUSMONITORTE', '.*'~'DemoWithSql'
2:42:31 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace Lightup assembly calculated path: 'C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.Redfield.Lightup.dll'
2:42:31 PM EVENT: Microsoft-ApplicationInsights-FrameworkLightup Trace Loaded applicationInsights.config from assembly's resource Microsoft.ApplicationInsights.Redfield.Lightup, Version=2.8.18.27202, Culture=neutral, PublicKeyToken=f23a46de0be5d6f3/Microsoft.ApplicationInsights.Redfield.Lightup.ApplicationInsights-recommended.config
2:42:34 PM EVENT: Microsoft-ApplicationInsights-FrameworkLightup Trace Successfully attached ApplicationInsights SDK
2:42:34 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace RedfieldIISModule.LoadLightupAssemblyAndGetLightupHttpModuleClass, success, 2687 ms
2:42:34 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace RedfieldIISModule.CreateAndInitializeApplicationInsightsHttpModules(lightupHttpModuleClass), success
2:42:34 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace ManagedHttpModuleHelper, multicastHttpModule.Init() success, 3288 ms
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Resolved variables to: MicrosoftAppInsights_ManagedHttpModulePath='C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll', MicrosoftAppInsights_ManagedHttpModuleType='Microsoft.ApplicationInsights.RedfieldIISModule.RedfieldIISModule'
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Resolved variables to: MicrosoftDiagnosticServices_ManagedHttpModulePath2='', MicrosoftDiagnosticServices_ManagedHttpModuleType2=''
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Environment variable 'MicrosoftDiagnosticServices_ManagedHttpModulePath2' or 'MicrosoftDiagnosticServices_ManagedHttpModuleType2' is null, skipping managed dll loading
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace MulticastHttpModule.constructor, success, 0 ms
2:42:35 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace RedfieldIISModule.CreateAndInitializeApplicationInsightsHttpModules(lightupHttpModuleClass), success
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace ManagedHttpModuleHelper, multicastHttpModule.Init() success, 0 ms
Timeout Reached. Stopping...
```

## <a name="next-steps"></a>Passaggi successivi

  Visualizzare i dati di telemetria:
 - [Esplora le metriche](../platform/metrics-charts.md) per monitorare le prestazioni e l'utilizzo.
- Eseguire [ricerche negli eventi e nei log](../../azure-monitor/app/diagnostic-search.md) per diagnosticare i problemi.
- Usare [Analytics](../log-query/log-query-overview.md) per query più avanzate.
- [Creare dashboard](../../azure-monitor/app/overview-dashboard.md).
 
 Aggiungere altri dati di telemetria:
 - [Creare test Web](monitor-web-app-availability.md) per assicurarsi che il sito rimanga attivo.
- Aggiungere i dati di [telemetria del client Web](../../azure-monitor/app/javascript.md) per visualizzare le eccezioni dal codice della pagina Web e per abilitare le chiamate di traccia.
- [Aggiungere il Application Insights SDK al codice per](../../azure-monitor/app/asp-net.md) poter inserire le chiamate di traccia e log.
 
 Eseguire altre operazioni con Application Insights Agent:
 - Usare la guida per [risolvere i problemi relativi](status-monitor-v2-troubleshoot.md) a Application Insights Agent.
