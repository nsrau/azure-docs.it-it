---
title: Informazioni di riferimento sull'API dell'agente .Net di Azure Application InsightsAzure Application Insights .Net Agent Reference
description: Informazioni di riferimento sull'API dell'agente di Application Insights.Application Insights Agent API reference. Monitorare le prestazioni del sito Web senza ridistribuire il sito Web. Funziona con ASP.NET app Web ospitate in locale, nelle macchine virtuali o in Azure.Works with a web apps hosted on-premises, in VMs, or on Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 02762c4b3af735eb0b4c19aaf450b2b3a416a2be
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733674"
---
# <a name="azure-monitor-application-insights-agent-api-reference"></a>Azure Monitor Application Insights Agent API Reference

In questo articolo viene descritto un cmdlet membro del [modulo PowerShell Az.ApplicationMonitor](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

> [!NOTE] 
> - Per iniziare, è necessaria una chiave di strumentazione. Per ulteriori informazioni, vedere [Creare una risorsa](create-new-resource.md#copy-the-instrumentation-key).
> - Questo cmdlet richiede la revisione e l'accettazione della licenza e dell'informativa sulla privacy.

> [!IMPORTANT] 
> Questo cmdlet richiede una sessione di PowerShell con autorizzazioni di amministratore e criteri di esecuzione con privilegi elevati. Per altre informazioni, vedere [Eseguire PowerShell come amministratore con criteri](status-monitor-v2-detailed-instructions.md#run-powershell-as-admin-with-an-elevated-execution-policy)di esecuzione con privilegi elevati.
> - Questo cmdlet richiede la revisione e l'accettazione della licenza e dell'informativa sulla privacy.
> - Il motore di strumentazione aggiunge ulteriore sovraccarico ed è disattivato per impostazione predefinita.


## <a name="enable-instrumentationengine"></a>Enable-InstrumentationEngine (Abilitazione-InstrumentationEngine)

Abilita il motore di strumentazione impostando alcune chiavi del Registro di sistema.
Riavviare IIS per rendere effettive le modifiche.

Il motore di strumentazione può integrare i dati raccolti dagli SDK di .NET.
Raccoglie eventi e messaggi che descrivono l'esecuzione di un processo gestito. Questi eventi e messaggi includono codici risultato di dipendenza, verbi HTTP e [testo del comando SQL.](asp-net-dependencies.md#advanced-sql-tracking-to-get-full-sql-query)

Abilitare il motore di strumentazione se:Enable the instrumentation engine if:
- Il monitoraggio è già stato abilitato con il cmdlet Enable ma non è stato abilitato il motore di strumentazione.
- L'app è stata instrumentata manualmente con gli SDK di .NET e si vuole raccogliere dati di telemetria aggiuntivi.

### <a name="examples"></a>Esempi

```powershell
PS C:\> Enable-InstrumentationEngine
```

### <a name="parameters"></a>Parametri

#### <a name="-acceptlicense"></a>-AcceptLicense (Licenza accettata)
**Facoltativa.** Utilizzare questa opzione per accettare la licenza e l'informativa sulla privacy in installazioni headless.

#### <a name="-verbose"></a>-Verbose
**Parametro comune.** Utilizzare questa opzione per generare log dettagliati.

### <a name="output"></a>Output


##### <a name="example-output-from-successfully-enabling-the-instrumentation-engine"></a>Output di esempio dall'attivazione corretta del motore di strumentazioneExample output from successfully enabling the instrumentation engine

```
Configuring IIS Environment for instrumentation engine...
Configuring registry for instrumentation engine...
```

## <a name="enable-applicationinsightsmonitoring"></a>Enable-ApplicationInsightsMonitoring

Consente il monitoraggio delle connessione senza codice delle app IIS in un computer di destinazione.

Questo cmdlet modificherà il file applicationHost.config di IIS e imposterà alcune chiavi del Registro di sistema.
Verrà inoltre creato un file applicationinsights.ikey.config, che definisce la chiave di strumentazione utilizzata da ogni app.
IIS caricherà RedfieldModule all'avvio, che inserirà Application Insights SDK nelle applicazioni all'avvio delle applicazioni.
Riavviare IIS per rendere effettive le modifiche.

Dopo aver abilitato il monitoraggio, è consigliabile usare [metriche](live-stream.md) live per verificare rapidamente se l'app ci invia dati di telemetria.

### <a name="examples"></a>Esempi

#### <a name="example-with-a-single-instrumentation-key"></a>Esempio con una singola chiave di strumentazioneExample with a single instrumentation key
In questo esempio, a tutte le app nel computer corrente viene assegnata una singola chiave di strumentazione.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

#### <a name="example-with-an-instrumentation-key-map"></a>Esempio con una mappa delle chiavi di strumentazioneExample with an instrumentation key map
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


### <a name="parameters"></a>Parametri

#### <a name="-instrumentationkey"></a>-InstrumentationKey (Chiave di Strumentazione)
**Obbligatorio.** Usare questo parametro per fornire una singola chiave di strumentazione per l'utilizzo da parte di tutte le app nel computer di destinazione.

#### <a name="-instrumentationkeymap"></a>-InstrumentationKeyMap (mappa delle chiavi di strumentazione)
**Obbligatorio.** Usare questo parametro per fornire più chiavi di strumentazione e un mapping delle chiavi di strumentazione usate da ogni app.
È possibile creare un singolo script `MachineFilter`di installazione per più computer impostando .

> [!IMPORTANT]
> Le app corrisponderanno alle regole nell'ordine in cui vengono fornite le regole. Quindi è necessario specificare prima le regole più specifiche per ultime e le regole più generiche.

##### <a name="schema"></a>SCHEMA
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'}})`

- **MachineFilter** è un'espressione regolare c'è obbligatoria del nome del computer o della macchina virtuale.
    - '.' corrisponderà a tutti
    - 'ComputerName' corrisponderà solo ai computer con il nome esatto specificato.
- **AppFilter** è un'espressione regolare c'è obbligatoria del nome del sito IIS. È possibile ottenere un elenco di siti sul server eseguendo il comando [get-iissite](https://docs.microsoft.com/powershell/module/iisadministration/get-iissite).
    - '.' corrisponderà a tutti
    - 'SiteName' corrisponderà solo al sito IIS con il nome esatto specificato.
- **InstrumentationKey** è necessario per abilitare il monitoraggio delle app che corrispondono ai due filtri precedenti.
    - Lasciare null questo valore se si desidera definire regole per escludere il monitoraggio.


#### <a name="-enableinstrumentationengine"></a>-EnableInstrumentationEngine -EnableInstrumentationEngine
**Facoltativa.** Utilizzare questa opzione per consentire al motore di strumentazione di raccogliere eventi e messaggi su ciò che accade durante l'esecuzione di un processo gestito. Questi eventi e messaggi includono codici risultato di dipendenza, verbi HTTP e testo del comando SQL.

Il motore di strumentazione aggiunge overhead ed è disattivato per impostazione predefinita.

#### <a name="-acceptlicense"></a>-AcceptLicense (Licenza accettata)
**Facoltativa.** Utilizzare questa opzione per accettare la licenza e l'informativa sulla privacy in installazioni headless.

#### <a name="-ignoresharedconfig"></a>-IgnoreSharedConfig
Quando si dispone di un cluster di server Web, è possibile che si stia utilizzando una [configurazione condivisa.](https://docs.microsoft.com/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211)
Il HttpModule non può essere inserito in questa configurazione condivisa.
Questo script avrà esito negativo con il messaggio che sono necessarie ulteriori operazioni di installazione.
Utilizzare questa opzione per ignorare questo controllo e continuare l'installazione dei prerequisiti. Per altre informazioni, vedere [conflitto noto con iis-shared-configurationFor more information, see known conflict-with-iis-shared-configuration](status-monitor-v2-troubleshoot.md#conflict-with-iis-shared-configuration)

#### <a name="-verbose"></a>-Verbose
**Parametro comune.** Utilizzare questa opzione per visualizzare i registri dettagliati.

#### <a name="-whatif"></a>-WhatIf 
**Parametro comune.** Usare questa opzione per testare e convalidare i parametri di input senza abilitare effettivamente il monitoraggio.

### <a name="output"></a>Output

#### <a name="example-output-from-a-successful-enablement"></a>Output di esempio da un'abilitazione riuscitaExample output from a successful enablement

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

Disabilita il motore di strumentazione rimuovendo alcune chiavi del Registro di sistema.
Riavviare IIS per rendere effettive le modifiche.

### <a name="examples"></a>Esempi

```powershell
PS C:\> Disable-InstrumentationEngine
```

### <a name="parameters"></a>Parametri 

#### <a name="-verbose"></a>-Verbose
**Parametro comune.** Utilizzare questa opzione per generare log dettagliati.

### <a name="output"></a>Output


##### <a name="example-output-from-successfully-disabling-the-instrumentation-engine"></a>Output di esempio dalla corretta disattivazione del motore di strumentazioneExample output from successfully disabling the instrumentation engine

```powershell
Configuring IIS Environment for instrumentation engine...
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]'
Configuring registry for instrumentation engine...
```

## <a name="disable-applicationinsightsmonitoring"></a>Disable-ApplicationInsightsMonitoring

Disabilita il monitoraggio nel computer di destinazione.
Questo cmdlet rimuoverà le modifiche apportate a IIS applicationHost.config e rimuoverà le chiavi del Registro di sistema.

### <a name="examples"></a>Esempi

```powershell
PS C:\> Disable-ApplicationInsightsMonitoring
```

### <a name="parameters"></a>Parametri 

#### <a name="-verbose"></a>-Verbose
**Parametro comune.** Utilizzare questa opzione per visualizzare i registri dettagliati.

### <a name="output"></a>Output


##### <a name="example-output-from-successfully-disabling-monitoring"></a>Output di esempio dalla corretta disabilitazione del monitoraggioExample output from successfully disabling monitoring

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

Nessun parametro richiesto.

### <a name="output"></a>Output


##### <a name="example-output-from-reading-the-config-file"></a>Output di esempio dalla lettura del file di configurazioneExample output from reading the config file

```
RedfieldConfiguration:
Filters:
0)InstrumentationKey:  AppFilter: WebAppExclude MachineFilter: .*
1)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2 AppFilter: WebAppTwo MachineFilter: .*
2)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault AppFilter: .* MachineFilter: .*
```

## <a name="get-applicationinsightsmonitoringstatus"></a>Get-ApplicationInsightsMonitoringStatus

Questo cmdlet fornisce informazioni sulla risoluzione dello stato.
Utilizzare questo cmdlet per esaminare lo stato di monitoraggio, versione del modulo PowerShell, e per controllare il processo in esecuzione.
Questo cmdlet segnalerà informazioni sulla versione e informazioni sui file chiave necessari per il monitoraggio.

### <a name="examples"></a>Esempi

#### <a name="example-application-status"></a>Esempio: Stato dell'applicazione

Eseguire il `Get-ApplicationInsightsMonitoringStatus` comando per visualizzare lo stato di monitoraggio dei siti Web.

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

In questo esempio;
- **Identificatore macchina** è un ID anonimo utilizzato per identificare in modo univoco il server. Se crei una richiesta di supporto, avremo bisogno di questo ID per trovare i log per il tuo server.
- **Il sito Web predefinito** è arrestato in IIS
- **DemoWebApp111** è stato avviato in IIS, ma non ha ricevuto alcuna richiesta. Questo report mostra che non è presente alcun processo in esecuzione (ProcessId: non trovato).
- **DemoWebApp22** è in esecuzione e viene monitorato (Instrumented: true). In base alla configurazione utente, la chiave di strumentazione xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx123 è stata abbinata per questo sito.
- **DemoWebApp333** è stato instrumentato manualmente utilizzando Application Insights SDK. Status Monitor ha rilevato l'SDK e non monitora questo sito.


#### <a name="example-powershell-module-information"></a>Esempio: informazioni sui moduli di PowerShellExample: PowerShell module information

Eseguire il `Get-ApplicationInsightsMonitoringStatus -PowerShellModule` comando per visualizzare informazioni sul modulo corrente:

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

#### <a name="example-runtime-status"></a>Esempio: stato di runtimeExample: Runtime status

È possibile esaminare il processo sul computer instrumentato per verificare se tutte le DLL sono caricate. Se il monitoraggio funziona, è necessario caricare almeno 12 DLL.

Eseguire il `Get-ApplicationInsightsMonitoringStatus -InspectProcess`comando :


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
Utilizzare questa opzione per verificare se l'applicazione è stata instrumentata correttamente.
È inoltre possibile verificare quale chiave di strumentazione è stata abbinata al sito.


#### <a name="-powershellmodule"></a>-Modulo di PowerShell
**Facoltativo**. Utilizzare questa opzione per segnalare i numeri di versione e i percorsi delle DLL necessari per il monitoraggio.
Utilizzare questa opzione se è necessario identificare la versione di qualsiasi DLL, incluso Application Insights SDK.

#### <a name="-inspectprocess"></a>-InspectProcess

**Facoltativo**. Utilizzare questa opzione per segnalare se IIS è in esecuzione.
Verranno inoltre scaricati gli strumenti esterni per determinare se le DLL necessarie vengono caricate nel runtime di IIS.


Se questo processo non riesce per qualsiasi motivo, è possibile eseguire questi comandi manualmente:If this process fails for any reason, you can run these commands manually:
- iisreset.exe /status
- [handle64.exe](https://docs.microsoft.com/sysinternals/downloads/handle) -p w3wp findstr /I "InstrumentationEngine AI. ApplicationInsights"
- [listdlls64.exe](https://docs.microsoft.com/sysinternals/downloads/listdlls) w3wp findstr /I "InstrumentationEngine AI ApplicationInsights"


#### <a name="-force"></a>-Force

**Facoltativo**. Utilizzato solo con InspectProcess. Utilizzare questa opzione per ignorare il prompt dell'utente visualizzato prima del download di altri strumenti.


## <a name="set-applicationinsightsmonitoringconfig"></a>Set-ApplicationInsightsMonitoringConfig

Imposta il file di configurazione senza eseguire una reinstallazione completa.
Riavviare IIS per rendere effettive le modifiche.

> [!IMPORTANT] 
> Questo cmdlet richiede una sessione di PowerShell con autorizzazioni di amministratore.


### <a name="examples"></a>Esempi

#### <a name="example-with-a-single-instrumentation-key"></a>Esempio con una singola chiave di strumentazioneExample with a single instrumentation key
In questo esempio, a tutte le app nel computer corrente verrà assegnata una singola chiave di strumentazione.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

#### <a name="example-with-an-instrumentation-key-map"></a>Esempio con una mappa delle chiavi di strumentazioneExample with an instrumentation key map
Esempio:
- `MachineFilter`corrisponde al computer corrente `'.*'` utilizzando il carattere jolly.
- `AppFilter='WebAppExclude'`fornisce `null` una chiave di strumentazione. L'app specificata non verrà instrumentata.
- `AppFilter='WebAppOne'`assegna all'app specificata una chiave di strumentazione univoca.
- `AppFilter='WebAppTwo'`assegna all'app specificata una chiave di strumentazione univoca.
- Infine, `AppFilter` usa `'.*'` anche il carattere jolly per trovare la corrispondenza con tutte le app Web che non corrispondono alle regole precedenti e assegnare una chiave di strumentazione predefinita.
- Gli spazi vengono aggiunti per la leggibilità.

```powershell
Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap `
       @(@{MachineFilter='.*';AppFilter='WebAppExclude'},
          @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'}},
          @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'}},
          @{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'}})
```

### <a name="parameters"></a>Parametri

#### <a name="-instrumentationkey"></a>-InstrumentationKey (Chiave di Strumentazione)
**Obbligatorio.** Usare questo parametro per fornire una singola chiave di strumentazione per l'utilizzo da parte di tutte le app nel computer di destinazione.

#### <a name="-instrumentationkeymap"></a>-InstrumentationKeyMap (mappa delle chiavi di strumentazione)
**Obbligatorio.** Usare questo parametro per fornire più chiavi di strumentazione e un mapping delle chiavi di strumentazione usate da ogni app.
È possibile creare un singolo script `MachineFilter`di installazione per più computer impostando .

> [!IMPORTANT]
> Le app corrisponderanno alle regole nell'ordine in cui vengono fornite le regole. Quindi è necessario specificare prima le regole più specifiche per ultime e le regole più generiche.

##### <a name="schema"></a>SCHEMA
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'})`

- **MachineFilter** è un'espressione regolare c'è obbligatoria del nome del computer o della macchina virtuale.
    - '.' corrisponderà a tutti
    - 'ComputerName' corrisponderà solo ai computer con il nome specificato.
- **AppFilter** è un'espressione regolare c'è obbligatoria del nome del computer o della macchina virtuale.
    - '.' corrisponderà a tutti
    - 'ApplicationName' corrisponderà solo alle app IIS con il nome specificato.
- **InstrumentationKey** è necessario per abilitare il monitoraggio delle app che corrispondono ai due filtri precedenti.
    - Lasciare null questo valore se si desidera definire regole per escludere il monitoraggio.


#### <a name="-verbose"></a>-Verbose
**Parametro comune.** Utilizzare questa opzione per visualizzare i registri dettagliati.


### <a name="output"></a>Output

Per impostazione predefinita, nessun output.

##### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkey"></a>Esempio di output dettagliato dall'impostazione del file di configurazione tramite -InstrumentationKeyExample verbose output from setting the config file via -InstrumentationKey

```
VERBOSE: Operation: InstallWithIkey
VERBOSE: InstrumentationKeyMap parsed:
Filters:
0)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx AppFilter: .* MachineFilter: .*
VERBOSE: set config file
VERBOSE: Config File Path:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config
```

##### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkeymap"></a>Esempio di output dettagliato dall'impostazione del file di configurazione tramite -InstrumentationKeyMapExample verbose output from setting the config file via -InstrumentationKeyMap

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

Raccoglie gli [eventi ETW](https://docs.microsoft.com/windows/desktop/etw/event-tracing-portal) dal runtime di collegamento senza codice. Questo cmdlet è un'alternativa all'esecuzione di [PerfView](https://github.com/microsoft/perfview).

Gli eventi raccolti verranno stampati sulla console in tempo reale e salvati in un file ETL. Il file ETL di output può essere aperto da [PerfView](https://github.com/microsoft/perfview) per ulteriori indagini.

Questo cmdlet verrà eseguito fino a quando non raggiunge rà`Ctrl + C`la durata del timeout (5 minuti predefiniti) o verrà arrestato manualmente ( ).

### <a name="examples"></a>Esempi

#### <a name="how-to-collect-events"></a>Come raccogliere gli eventi

Normalmente ti chiediamo di raccogliere gli eventi per indagare sul motivo per cui l'applicazione non viene instrumentata.

Il runtime di connessione senza codice genererà eventi ETW all'avvio di IIS e all'avvio dell'applicazione.

Per raccogliere questi eventi:
1. In una console cmd con `iisreset /stop` privilegi di amministratore, eseguire Per disattivare IIS e tutte le applicazioni Web.
2. Esegui questo cmdlet
3. In una console cmd con `iisreset /start` privilegi di amministratore, eseguire Per avviare IIS.
4. Prova a passare all'app.
5. Al termine del caricamento dell'app, puoi`Ctrl + C`interromperlo manualmente ( ) o attendere il timeout.

#### <a name="what-events-to-collect"></a>Eventi da raccogliere

Sono disponibili tre opzioni per la raccolta di eventi:You have three options when collecting events:
1. Utilizzare l'opzione `-CollectSdkEvents` per raccogliere gli eventi generati da Application Insights SDK.
2. Utilizzare l'opzione `-CollectRedfieldEvents` per raccogliere gli eventi generati da Status Monitor e Redfield Runtime. Questi registri sono utili per la diagnosi di IIS e l'avvio dell'applicazione.
3. Utilizzare entrambe le opzioni per raccogliere entrambi i tipi di evento.
4. Per impostazione predefinita, se non viene specificata alcuna opzione verranno raccolti entrambi i tipi di evento.


### <a name="parameters"></a>Parametri

#### <a name="-maxdurationinminutes"></a>-MaxDurationInMinuti
**Facoltativa.** Utilizzare questo parametro per impostare per quanto tempo questo script deve raccogliere gli eventi. Il valore predefinito è 5 minuti.

#### <a name="-logdirectory"></a>-LogDirectory (informazioni in cui è stato fatto clic
**Facoltativa.** Utilizzare questa opzione per impostare la directory di output del file ETL. Per impostazione predefinita, questo file verrà creato nella directory Moduli di PowerShell.By default, this file will be created in the PowerShell Modules directory. Il percorso completo verrà visualizzato durante l'esecuzione dello script.


#### <a name="-collectsdkevents"></a>-CollectSdkEvents
**Facoltativa.** Utilizzare questa opzione per raccogliere gli eventi di Application Insights SDK.

#### <a name="-collectredfieldevents"></a>-CollectRedfieldEvents
**Facoltativa.** Utilizzare questa opzione per raccogliere eventi da Status Monitor e dal runtime di Redfield.

#### <a name="-verbose"></a>-Verbose
**Parametro comune.** Utilizzare questa opzione per generare log dettagliati.



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
 - [Esplora le metriche](../../azure-monitor/app/metrics-explorer.md) per monitorare le prestazioni e l'utilizzo.
- [Cerca eventi e log](../../azure-monitor/app/diagnostic-search.md) per diagnosticare i problemi.
- Usa [l'analisi](../../azure-monitor/app/analytics.md) per query più avanzate.
- [Creare dashboard](../../azure-monitor/app/overview-dashboard.md).
 
 Aggiungere altri dati di telemetria:
 - [Creare test Web](monitor-web-app-availability.md) per assicurarsi che il sito rimanga attivo.
- [Aggiungere dati di telemetria del client Web](../../azure-monitor/app/javascript.md) per visualizzare le eccezioni dal codice della pagina Web e abilitare le chiamate di traccia.
- [Aggiungere Application Insights SDK al codice](../../azure-monitor/app/asp-net.md) in modo da poter inserire chiamate di traccia e log.
 
 Fai di più con Application Insights Agent:
 - Usare la guida per [risolvere i problemi relativi](status-monitor-v2-troubleshoot.md) all'agente application Insights.Use our guide to troubleshoot Application Insights Agent.






