---
title: Risoluzione dei problemi e problemi noti di Azure Application Insights Agent Documenti Microsoft
description: Problemi noti dell'agente application Insights e esempi di risoluzione dei problemi. Monitorare le prestazioni del sito Web senza ridistribuire il sito Web. Funziona con ASP.NET app Web ospitate in locale, nelle macchine virtuali o in Azure.Works with a web apps hosted on-premises, in VMs, or on Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 217629ba5c386557455cc2d2b8bd47f85fa8f84e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671155"
---
# <a name="troubleshooting-application-insights-agent-formerly-named-status-monitor-v2"></a>Risoluzione dei problemi di Application Insights Agent (precedentemente denominato Status Monitor v2)Troubleshooting Application Insights Agent (formerly named Status Monitor v2)

Quando si abilita il monitoraggio, potrebbero verificarsi problemi che impediscono la raccolta dei dati.
In questo articolo vengono elencati tutti i problemi noti e vengono forniti esempi di risoluzione dei problemi.
Se ti imbatti in un problema che non è elencato qui, puoi contattarci su [GitHub](https://github.com/Microsoft/ApplicationInsights-Home/issues).

## <a name="known-issues"></a>Problemi noti

### <a name="conflicting-dlls-in-an-apps-bin-directory"></a>DLL in conflitto nella directory bin di un'app

Se una di queste DLL è presente nella directory bin, il monitoraggio potrebbe non riuscire:

- Microsoft.ApplicationInsights.dll
- Microsoft.AspNet.TelemetryCorrelation.dll
- System.Diagnostics.DiagnosticSource.dll

Alcune di queste DLL sono incluse nei modelli di app predefiniti di Visual Studio, anche se l'app non le usa.
È possibile utilizzare gli strumenti di risoluzione dei problemi per visualizzare il comportamento sintomatico:You can use troubleshooting tools to see symptomatic behavior:

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

- IISReset e caricamento dell'app (senza telemetria). Eseguire l'analisi con Sysinternals (Handle.exe e ListDLLs.exe):
    ```
    .\handle64.exe -p w3wp | findstr /I "InstrumentationEngine AI. ApplicationInsights"
    E54: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll

    .\Listdlls64.exe w3wp | findstr /I "InstrumentationEngine AI ApplicationInsights"
    0x0000000009be0000  0x127000  C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\MicrosoftInstrumentationEngine_x64.dll
    0x0000000009b90000  0x4f000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.ExtensionsHost_x64.dll
    0x0000000004d20000  0xb2000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.Extensions.Base_x64.dll
    ```

### <a name="conflict-with-iis-shared-configuration"></a>Conflitto con la configurazione condivisa di IIS

Se si dispone di un cluster di server Web, è possibile che si stia utilizzando una [configurazione condivisa.](https://docs.microsoft.com/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211)
Il HttpModule non può essere inserito in questa configurazione condivisa.
Eseguire il comando Enable in ogni server Web per installare la DLL nella Global Assembly Cache di ogni server.

Dopo aver eseguito il comando Abilita, completare questi passaggi:After you run the Enable command, complete these steps:
1. Passare alla directory di configurazione condivisa e individuare il file applicationHost.config.
2. Aggiungi questa riga alla sezione dei moduli della tua configurazione:
    ```
    <modules>
        <!-- Registered global managed http module handler. The 'Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll' must be installed in the GAC before this config is applied. -->
        <add name="ManagedHttpModuleHelper" type="Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.ManagedHttpModuleHelper, Microsoft.AppInsights.IIS.ManagedHttpModuleHelper, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" preCondition="managedHandler,runtimeVersionv4.0" />
    </modules>
    ```

### <a name="iis-nested-applications"></a>Applicazioni annidate IIS

Non vengono instrumentate le applicazioni annidate in IIS nella versione 1.0.
Stiamo monitorando questo problema [qui](https://github.com/microsoft/ApplicationInsights-Home/issues/369).

### <a name="advanced-sdk-configuration-isnt-available"></a>Advanced SDK Configuration non è disponibile.

La configurazione SDK non è esposta all'utente finale nella versione 1.0.
Stiamo monitorando questo problema [qui](https://github.com/microsoft/ApplicationInsights-Home/issues/375).

    
    
## <a name="troubleshooting"></a>Risoluzione dei problemi
    
### <a name="troubleshooting-powershell"></a>Risoluzione dei problemi di PowerShellTroubleshooting PowerShell

#### <a name="determine-which-modules-are-available"></a>Determinare quali moduli sono disponibili
È possibile `Get-Module -ListAvailable` utilizzare il comando per determinare quali moduli sono installati.

#### <a name="import-a-module-into-the-current-session"></a>Importare un modulo nella sessione corrente
Se un modulo non è stato caricato in una sessione di `Import-Module <path to psd1>` PowerShell, è possibile caricarlo manualmente usando il comando.


### <a name="troubleshooting-the-application-insights-agent-module"></a>Risoluzione dei problemi relativi al modulo Application Insights Agent

#### <a name="list-the-commands-available-in-the-application-insights-agent-module"></a>Elencare i comandi disponibili nel modulo Agente di Application InsightsList the commands available in the Application Insights Agent module
Eseguire il `Get-Command -Module Az.ApplicationMonitor` comando per ottenere i comandi disponibili:

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

#### <a name="determine-the-current-version-of-the-application-insights-agent-module"></a>Determinare la versione corrente del modulo Application Insights Agent
Eseguire `Get-ApplicationInsightsMonitoringStatus -PowerShellModule` il comando per visualizzare le seguenti informazioni sul modulo:
   - Versione del modulo PowerShell
   - Versione di Application Insights SDK
   - Percorsi dei file del modulo PowerShell
    
Esaminare le informazioni di [riferimento sull'API](status-monitor-v2-api-get-status.md) per una descrizione dettagliata di come utilizzare questo cmdlet.


### <a name="troubleshooting-running-processes"></a>Risoluzione dei problemi relativi ai processi in esecuzioneTroubleshooting running processes

È possibile esaminare i processi nel computer instrumentato per determinare se tutte le DLL sono caricate.
Se il monitoraggio funziona, è necessario caricare almeno 12 DLL.

Utilizzare `Get-ApplicationInsightsMonitoringStatus -InspectProcess` il comando per controllare le DLL.

Esaminare le informazioni di [riferimento sull'API](status-monitor-v2-api-get-status.md) per una descrizione dettagliata di come utilizzare questo cmdlet.


### <a name="collect-etw-logs-by-using-perfview"></a>Raccogliere i registri ETW tramite PerfViewCollect ETW logs by using PerfView

#### <a name="setup"></a>Configurazione

1. Scaricare PerfView.exe e PerfView64.exe da [GitHub](https://github.com/Microsoft/perfview/releases).
2. Avviare PerfView64.exe.
3. Espandere **Opzioni avanzate**.
4. Deselezionare queste caselle di controllo:
    - **Zip**
    - **Unione**
    - **Insieme di simboli .NET**
5. Impostare questi **provider aggiuntivi**:`61f6ca3b-4b5f-5602-fa60-759a2a2d1fbd,323adc25-e39b-5c87-8658-2c1af1a92dc5,925fa42b-9ef6-5fa7-10b8-56449d7a2040,f7d60e07-e910-5aca-bdd2-9de45b46c560,7c739bb9-7861-412e-ba50-bf30d95eae36,61f6ca3b-4b5f-5602-fa60-759a2a2d1fbd,323adc25-e39b-5c87-8658-2c1af1a92dc5,252e28f4-43f9-5771-197a-e8c7e750a984`


#### <a name="collecting-logs"></a>Raccolta dei registri

1. In una console di comando con `iisreset /stop` privilegi di amministratore, eseguire il comando per disattivare IIS e tutte le app Web.
2. In PerfView selezionare **Avvia raccolta**.
3. In una console dei comandi con `iisreset /start` privilegi di amministratore, eseguire il comando per avviare IIS.
4. Prova a passare all'app.
5. Dopo aver caricato l'app, torna a PerfView e seleziona **Interrompi raccolta**.



## <a name="next-steps"></a>Passaggi successivi

- Esaminare il [riferimento all'API](status-monitor-v2-overview.md#powershell-api-reference) per informazioni sui parametri che potrebbero essere stati persi.
- Se ti imbatti in un problema che non è elencato qui, puoi contattarci su [GitHub](https://github.com/Microsoft/ApplicationInsights-Home/issues).
