---
title: Risoluzione dei problemi dell'agente applicazione Azure Insights e problemi noti | Microsoft Docs
description: Problemi noti relativi all'agente Application Insights e agli esempi di risoluzione dei problemi. Monitora le prestazioni del sito Web senza ridistribuire il sito Web. Funziona con le app Web ASP.NET ospitate in locale, in macchine virtuali o in Azure.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 30172bf65be52ba1ddd2b9127c3e2b5a284d48dc
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899582"
---
# <a name="troubleshooting-application-insights-agent-formerly-named-status-monitor-v2"></a>Risoluzione dei problemi relativi a Application Insights Agent (denominato in precedenza Status Monitor v2)

Quando si Abilita il monitoraggio, è possibile che si verifichino problemi che impediscono la raccolta dei dati.
Questo articolo elenca tutti i problemi noti e fornisce esempi di risoluzione dei problemi.
Se si riscontra un problema non elencato qui, è possibile contattarci su [GitHub](https://github.com/Microsoft/ApplicationInsights-Home/issues).

## <a name="known-issues"></a>Problemi noti

### <a name="conflicting-dlls-in-an-apps-bin-directory"></a>Dll in conflitto nella directory bin di un'app

Se una di queste dll è presente nella directory bin, il monitoraggio potrebbe non riuscire:

- Microsoft. ApplicationInsights. dll
- Microsoft. AspNet. TelemetryCorrelation. dll
- System. Diagnostics. DiagnosticSource. dll

Alcune di queste dll sono incluse nei modelli di app predefinite di Visual Studio, anche se l'app non le USA.
Per visualizzare il comportamento sintomatico, è possibile usare gli strumenti di risoluzione dei problemi:

- PerfView
    ```
    ThreadID="7,500" 
    ProcessorNumber="0" 
    msg="Found 'System.Diagnostics.DiagnosticSource, Version=4.0.2.1, Culture=neutral, PublicKeyToken=cc7b13ffcd2ddd51' assembly, skipping attaching redfield binaries" 
    ExtVer="2.8.13.5972" 
    SubscriptionId="" 
    AppName="" 
    FormattedMessage="Found 'System.Diagnostics.DiagnosticSource, Version=4.0.2.1, Culture=neutral, PublicKeyToken=cc7b13ffcd2ddd51' assembly, skipping attaching redfield binaries" 
    ```

- IISReset e caricamento di app (senza telemetria). Esaminare con Sysinternals (handle. exe e ListDLLs. exe):
    ```
    .\handle64.exe -p w3wp | findstr /I "InstrumentationEngine AI. ApplicationInsights"
    E54: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll

    .\Listdlls64.exe w3wp | findstr /I "InstrumentationEngine AI ApplicationInsights"
    0x0000000009be0000  0x127000  C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\MicrosoftInstrumentationEngine_x64.dll
    0x0000000009b90000  0x4f000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.ExtensionsHost_x64.dll
    0x0000000004d20000  0xb2000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.Extensions.Base_x64.dll
    ```

### <a name="conflict-with-iis-shared-configuration"></a>Conflitto con la configurazione condivisa di IIS

Se si dispone di un cluster di server Web, è possibile che si stia utilizzando una [configurazione condivisa](https://docs.microsoft.com/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211).
Non è possibile inserire HttpModule in questa configurazione condivisa.
Eseguire il comando Abilita in ogni server Web per installare la DLL nella GAC di ogni server.

Dopo aver eseguito il comando di abilitazione, completare i passaggi seguenti:
1. Passare alla directory di configurazione condivisa e trovare il file applicationHost. config.
2. Aggiungere questa riga alla sezione moduli della configurazione:
    ```
    <modules>
        <!-- Registered global managed http module handler. The 'Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll' must be installed in the GAC before this config is applied. -->
        <add name="ManagedHttpModuleHelper" type="Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.ManagedHttpModuleHelper, Microsoft.AppInsights.IIS.ManagedHttpModuleHelper, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" preCondition="managedHandler,runtimeVersionv4.0" />
    </modules>
    ```

### <a name="iis-nested-applications"></a>Applicazioni annidate IIS

Non è necessario instrumentare le applicazioni annidate in IIS nella versione 1,0.
Questo problema è stato monitorato [qui](https://github.com/microsoft/ApplicationInsights-Home/issues/369).

### <a name="advanced-sdk-configuration-isnt-available"></a>La configurazione avanzata dell'SDK non è disponibile.

La configurazione dell'SDK non è esposta all'utente finale nella versione 1,0.
Questo problema è stato monitorato [qui](https://github.com/microsoft/ApplicationInsights-Home/issues/375).

    
    
## <a name="troubleshooting"></a>risoluzione dei problemi
    
### <a name="troubleshooting-powershell"></a>Risoluzione dei problemi relativi a PowerShell

#### <a name="determine-which-modules-are-available"></a>Determinare quali moduli sono disponibili
È possibile usare il comando `Get-Module -ListAvailable` per determinare quali moduli sono installati.

#### <a name="import-a-module-into-the-current-session"></a>Importa un modulo nella sessione corrente
Se un modulo non è stato caricato in una sessione di PowerShell, è possibile caricarlo manualmente usando il comando `Import-Module <path to psd1>`.


### <a name="troubleshooting-the-application-insights-agent-module"></a>Risoluzione dei problemi del modulo agente Application Insights

#### <a name="list-the-commands-available-in-the-application-insights-agent-module"></a>Elencare i comandi disponibili nel modulo Application Insights Agent
Eseguire il comando `Get-Command -Module Az.ApplicationMonitor` per ottenere i comandi disponibili:

```
CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Disable-ApplicationInsightsMonitoring              0.4.0      Az.ApplicationMonitor
Cmdlet          Disable-InstrumentationEngine                      0.4.0      Az.ApplicationMonitor
Cmdlet          Enable-ApplicationInsightsMonitoring               0.4.0      Az.ApplicationMonitor
Cmdlet          Enable-InstrumentationEngine                       0.4.0      Az.ApplicationMonitor
Cmdlet          Get-ApplicationInsightsMonitoringConfig            0.4.0      Az.ApplicationMonitor
Cmdlet          Get-ApplicationInsightsMonitoringStatus            0.4.0      Az.ApplicationMonitor
Cmdlet          Set-ApplicationInsightsMonitoringConfig            0.4.0      Az.ApplicationMonitor
Cmdlet          Start-ApplicationInsightsMonitoringTrace           0.4.0      Az.ApplicationMonitor
```

#### <a name="determine-the-current-version-of-the-application-insights-agent-module"></a>Determinare la versione corrente del modulo agente Application Insights
Eseguire il comando `Get-ApplicationInsightsMonitoringStatus -PowerShellModule` per visualizzare le informazioni seguenti sul modulo:
   - Versione del modulo PowerShell
   - Versione di Application Insights SDK
   - Percorsi dei file del modulo PowerShell
    
Per una descrizione dettagliata di come usare questo cmdlet, vedere le informazioni di [riferimento sulle API](status-monitor-v2-api-get-status.md) .


### <a name="troubleshooting-running-processes"></a>Risoluzione dei problemi relativi ai processi in esecuzione

È possibile esaminare i processi nel computer instrumentato per determinare se tutte le dll sono state caricate.
Se il monitoraggio è funzionante, è necessario caricare almeno 12 dll.

Usare il comando `Get-ApplicationInsightsMonitoringStatus -InspectProcess` per controllare le dll.

Per una descrizione dettagliata di come usare questo cmdlet, vedere le informazioni di [riferimento sulle API](status-monitor-v2-api-get-status.md) .


### <a name="collect-etw-logs-by-using-perfview"></a>Raccogliere i log ETW usando PerfView

#### <a name="setup"></a>Configurazione

1. Scaricare PerfView. exe e PerfView64. exe da [GitHub](https://github.com/Microsoft/perfview/releases).
2. Avviare PerfView64. exe.
3. Espandere **Opzioni avanzate**.
4. Deselezionare le caselle di controllo seguenti:
    - **Zip**
    - **Merge**
    - **Raccolta di simboli .NET**
5. Impostare i **provider aggiuntivi**seguenti: `61f6ca3b-4b5f-5602-fa60-759a2a2d1fbd,323adc25-e39b-5c87-8658-2c1af1a92dc5,925fa42b-9ef6-5fa7-10b8-56449d7a2040,f7d60e07-e910-5aca-bdd2-9de45b46c560,7c739bb9-7861-412e-ba50-bf30d95eae36,61f6ca3b-4b5f-5602-fa60-759a2a2d1fbd,323adc25-e39b-5c87-8658-2c1af1a92dc5,252e28f4-43f9-5771-197a-e8c7e750a984`


#### <a name="collecting-logs"></a>Raccolta di log

1. In una console dei comandi con privilegi di amministratore eseguire il comando `iisreset /stop` per disattivare IIS e tutte le app Web.
2. In PerfView selezionare **Avvia raccolta**.
3. In una console dei comandi con privilegi di amministratore eseguire il comando `iisreset /start` per avviare IIS.
4. Provare a passare all'app.
5. Al termine del caricamento dell'app, tornare a PerfView e selezionare **Arresta raccolta**.



## <a name="next-steps"></a>Passaggi successivi

- Esaminare le informazioni di [riferimento sulle API](status-monitor-v2-overview.md#powershell-api-reference) per informazioni sui parametri che potrebbero essere stati persi.
- Se si riscontra un problema non elencato qui, è possibile contattarci su [GitHub](https://github.com/Microsoft/ApplicationInsights-Home/issues).
