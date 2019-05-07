---
title: Problemi noti e risoluzione dei problemi di Azure Status Monitor v2 | Microsoft Docs
description: I problemi noti di Status Monitor v2 e risoluzione dei problemi relativi esempi. Monitorare le prestazioni di siti Web senza ridistribuire il sito Web. Questa funzionalità può essere usata con app Web ASP.NET ospitate in locale, in macchine virtuali o in Azure.
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
ms.openlocfilehash: f8e057bf2e71d5a00c1e2a8ef72a5c9415a5a207
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65145116"
---
# <a name="troubleshooting-status-monitor-v2"></a>Risoluzione dei problemi di stato monitoraggio v2

Quando si abilita il monitoraggio, si potrebbero riscontrare problemi che impediscono la raccolta dei dati. Questo documento Elenca tutti i problemi noti e risoluzione dei problemi negli esempi.
Se si riscontra un problema non elencato qui, è possibile contattarci [qui](https://github.com/Microsoft/ApplicationInsights-Home/issues).


> [!IMPORTANT]
> Stato monitoraggio v2 è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [condizioni per l'utilizzo per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="known-issues"></a>Problemi noti

### <a name="conflicting-dlls-in-an-applications-bin-directory"></a>DLL in conflitto nella directory bin di un'applicazione

Se alcune di queste DLL sono presenti nella directory bin, il monitoraggio non riesca.

- Microsoft.ApplicationInsights.dll
- Microsoft.AspNet.TelemetryCorrelation.dll
- System.Diagnostics.DiagnosticSource.dll

Alcune di queste DLL sono inclusi in modelli di applicazione predefiniti di Visual Studio, anche se l'applicazione non li Usa.
Comportamento sintomatico può essere visualizzato utilizzando gli strumenti di risoluzione dei problemi:

- PerfView:
    ```
    ThreadID="7,500" 
    ProcessorNumber="0" 
    msg="Found 'System.Diagnostics.DiagnosticSource, Version=4.0.2.1, Culture=neutral, PublicKeyToken=cc7b13ffcd2ddd51' assembly, skipping attaching redfield binaries" 
    ExtVer="2.8.13.5972" 
    SubscriptionId="" 
    AppName="" 
    FormattedMessage="Found 'System.Diagnostics.DiagnosticSource, Version=4.0.2.1, Culture=neutral, PublicKeyToken=cc7b13ffcd2ddd51' assembly, skipping attaching redfield binaries" 
    ```

- (nessuna TELEMETRIA) di caricare iisreset + app. Analizzare con Sysinternals (Handle.exe e ListDLLs.exe)
    ```
    .\handle64.exe -p w3wp | findstr /I "InstrumentationEngine AI. ApplicationInsights"
    E54: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll

    .\Listdlls64.exe w3wp | findstr /I "InstrumentationEngine AI ApplicationInsights"
    0x0000000009be0000  0x127000  C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\MicrosoftInstrumentationEngine_x64.dll
    0x0000000009b90000  0x4f000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.ExtensionsHost_x64.dll
    0x0000000004d20000  0xb2000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.Extensions.Base_x64.dll
    ```

### <a name="conflict-with-iis-shared-configuration"></a>Sono in conflitto con la configurazione condivisa di IIS

Se si dispone di un cluster di server web, potrebbe essere in uso un [configurazione condivisa](https://docs.microsoft.com/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211). In questa configurazione condivisa non è possibile inserire automaticamente il modulo HTTP. Ogni server web prima di tutto necessario eseguire il comando di abilitazione per installare la DLL nella relativa Global Assembly Cache.

Dopo aver eseguito il comando Enable, 
1. Passare alla directory di configurazione condivisa e trovare le `applicationHost.config` file.
2. Aggiungere la seguente riga alla configurazione nella sezione moduli:
    ```
    <modules>
        <!-- Registered global managed http module handler. The 'Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll' must be installed in the GAC before this config is applied. -->
        <add name="ManagedHttpModuleHelper" type="Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.ManagedHttpModuleHelper, Microsoft.AppInsights.IIS.ManagedHttpModuleHelper, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" preCondition="managedHandler,runtimeVersionv4.0" />
    </modules>
    ```
    


## <a name="troubleshooting"></a>risoluzione dei problemi
    
### <a name="troubleshooting-powershell"></a>Risoluzione dei problemi di PowerShell

#### <a name="how-to-inspect-what-modules-are-available"></a>Come è possibile controllare quali moduli sono disponibili?
È possibile controllare i moduli installati utilizzando il comando: `Get-Module -ListAvailable`

#### <a name="how-to-import-a-module-into-the-current-session"></a>Come importare un modulo nella sessione corrente?
Se il modulo non è stato caricato in una sessione di PowerShell, può caricare manualmente usando il comando: `Import-Module <path to psd1>`


### <a name="troubleshooting-the-status-monitor-v2-module"></a>Risoluzione dei problemi del modulo v2 Status Monitor

#### <a name="how-to-review-what-commands-are-available-in-the-status-monitor-v2-module"></a>Come verificare quali comandi sono disponibili nel modulo v2 Status Monitor?
- Eseguire il comando: `Get-Command -Module Az.ApplicationMonitor` per ottenere i comandi disponibili:

    ```
    CommandType     Name                                               Version    Source
    -----------     ----                                               -------    ------
    Cmdlet          Disable-ApplicationInsightsMonitoring              0.2.1      Az.ApplicationMonitor
    Cmdlet          Disable-InstrumentationEngine                      0.2.1      Az.ApplicationMonitor
    Cmdlet          Enable-ApplicationInsightsMonitoring               0.2.1      Az.ApplicationMonitor
    Cmdlet          Enable-InstrumentationEngine                       0.2.1      Az.ApplicationMonitor
    Cmdlet          Get-ApplicationInsightsMonitoringConfig            0.2.1      Az.ApplicationMonitor
    Cmdlet          Get-ApplicationInsightsMonitoringStatus            0.2.1      Az.ApplicationMonitor
    Cmdlet          Set-ApplicationInsightsMonitoringConfig            0.2.1      Az.ApplicationMonitor
    ```

#### <a name="what-is-the-current-version-of-the-status-monitor-v2-module"></a>Che cos'è la versione corrente del modulo v2 Status Monitor?
- Eseguire il comando: `Get-ApplicationInsightsMonitoringStatus` per ottenere un output di informazioni su questo modulo:

    ```
    PowerShell Module version:
    0.2.1-alpha

    Application Insights SDK version:
    2.9.0.3872

    Executing PowerShell Module Assembly:
    Microsoft.ApplicationInsights.Redfield.Configurator.PowerShell, Version=2.8.14.9129, Culture=neutral, PublicKeyToken=31bf3856ad364e35

    PowerShell Module Directory:
    C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.1\content\PowerShell

    Runtime Paths:
    ParentDirectory: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.1\content Exists: True
    ConfigurationPath: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.1\content\applicationInsights.ikey.config Exists: False
    ManagedHttpModuleHelperPath: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.1\content\Runtime\Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll Exists: True
    RedfieldIISModulePath: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.1\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll Exists: True
    InstrumentationEngine86Path: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.1\content\Instrumentation32\MicrosoftInstrumentationEngine_x86.dll Exists: True
    InstrumentationEngine64Path: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.1\content\Instrumentation64\MicrosoftInstrumentationEngine_x64.dll Exists: True
    InstrumentationEngineExtensionHost86Path: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.1\content\Instrumentation32\Microsoft.ApplicationInsights.ExtensionsHost_x86.dll Exists: True
    InstrumentationEngineExtensionHost64Path: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.1\content\Instrumentation64\Microsoft.ApplicationInsights.ExtensionsHost_x64.dll Exists: True
    InstrumentationEngineExtensionConfig86Path: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.1\content\Instrumentation32\Microsoft.InstrumentationEngine.Extensions.config Exists: True
    InstrumentationEngineExtensionConfig64Path: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.1\content\Instrumentation64\Microsoft.InstrumentationEngine.Extensions.config Exists: True
    ApplicationInsightsSdkPath: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.1\content\Runtime\Microsoft.ApplicationInsights.dll Exists: True
    ```




### <a name="troubleshooting-running-processes"></a>Risoluzione dei problemi dei processi in esecuzione

È possibile esaminare il processo nel computer instrumentati per vedere se tutte le DLL vengono caricate.
Se il monitoraggio funziona, almeno 12 DLL deve essere caricata.

- Cmd: `Get-ApplicationInsightsMonitoringStatus -InspectProcess`

    ```
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

### <a name="collect-etw-logs-with-perfview"></a>Raccogliere i log ETW con PerfView

#### <a name="setup"></a>Configurazione

1. Scaricare PerfView.exe e PerfView64.exe da https://github.com/Microsoft/perfview/releases
2. Avviare PerfView64.exe
3. Espandere "Opzioni avanzate"
4. Deselezionare l'opzione:
    - Zip
    - Unione
    - Raccolta di simboli .NET
5. Set di provider aggiuntivi: `61f6ca3b-4b5f-5602-fa60-759a2a2d1fbd,323adc25-e39b-5c87-8658-2c1af1a92dc5,925fa42b-9ef6-5fa7-10b8-56449d7a2040,f7d60e07-e910-5aca-bdd2-9de45b46c560,7c739bb9-7861-412e-ba50-bf30d95eae36,61f6ca3b-4b5f-5602-fa60-759a2a2d1fbd,323adc25-e39b-5c87-8658-2c1af1a92dc5,252e28f4-43f9-5771-197a-e8c7e750a984`


#### <a name="collecting-logs"></a>Raccolta dei log

1. Nella console cmd con privilegi di amministratore eseguire `iisreset /stop` disattivare tutte le app web e IIS.
2. In PerfView, fare clic su "Start Collection"
3. Nella console cmd con privilegi di amministratore eseguire `iisreset /start` per avviare IIS.
4. Provare a passare all'app.
5. Dopo che l'app al termine del caricamento, tornare a PerfView e fare clic su "Interrompi raccolta"



## <a name="next-steps"></a>Passaggi successivi

- Revisione del nostro [riferimento all'API](status-monitor-v2-overview.md#powershell-api-reference) per trovare un parametro, potrebbe aver ignorato.
- Se si riscontra un problema non elencato qui, è possibile contattarci [qui](https://github.com/Microsoft/ApplicationInsights-Home/issues).
