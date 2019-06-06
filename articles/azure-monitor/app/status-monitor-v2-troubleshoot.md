---
title: Problemi noti e risoluzione dei problemi di Azure Status Monitor v2 | Microsoft Docs
description: I problemi noti di Status Monitor v2 e risoluzione dei problemi relativi esempi. Monitorare le prestazioni di siti Web senza ridistribuire il sito Web. Funziona con le app web ASP.NET ospitate in locale, in macchine virtuali o in Azure.
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
ms.openlocfilehash: f2115ee14b58030f695c9410870615f03d353cd2
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/06/2019
ms.locfileid: "66734956"
---
# <a name="troubleshooting-status-monitor-v2"></a>Risoluzione dei problemi di stato monitoraggio v2

Quando si abilita il monitoraggio, si potrebbero riscontrare problemi che impediscono la raccolta dei dati.
Questo articolo sono elencati tutti i problemi noti e vengono forniti esempi di risoluzione dei problemi.
Se si riscontra un problema che non è elencato qui, è possibile contattare Microsoft sul [GitHub](https://github.com/Microsoft/ApplicationInsights-Home/issues).


> [!IMPORTANT]
> Stato monitoraggio v2 è attualmente in anteprima pubblica.
> Questa versione di anteprima viene fornita senza un contratto di servizio e non è consigliabile per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate e alcune potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="known-issues"></a>Problemi noti

### <a name="conflicting-dlls-in-an-apps-bin-directory"></a>DLL in conflitto nella directory bin di un'app

Se alcune di queste DLL sono presenti nella directory bin, il monitoraggio potrebbe avere esito negativo:

- Microsoft.ApplicationInsights.dll
- Microsoft.AspNet.TelemetryCorrelation.dll
- System.Diagnostics.DiagnosticSource.dll

Alcune di queste DLL sono inclusi nei modelli di app predefinite di Visual Studio, anche se l'app non li Usa.
È possibile usare gli strumenti di risoluzione dei problemi per vedere sintomatico del comportamento:

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

- App e IISReset caricare (senza i dati di telemetria). Analizzare con Sysinternals (Handle.exe e ListDLLs.exe):
    ```
    .\handle64.exe -p w3wp | findstr /I "InstrumentationEngine AI. ApplicationInsights"
    E54: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll

    .\Listdlls64.exe w3wp | findstr /I "InstrumentationEngine AI ApplicationInsights"
    0x0000000009be0000  0x127000  C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\MicrosoftInstrumentationEngine_x64.dll
    0x0000000009b90000  0x4f000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.ExtensionsHost_x64.dll
    0x0000000004d20000  0xb2000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.Extensions.Base_x64.dll
    ```

### <a name="conflict-with-iis-shared-configuration"></a>Sono in conflitto con la configurazione condivisa di IIS

Se si dispone di un cluster di server web, potrebbe essere in uso un [configurazione condivisa](https://docs.microsoft.com/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211).
Il modulo HTTP non possono essere inseriti in questa configurazione condivisa.
Eseguire il comando Enable in ogni server web per installare la DLL nella Global Assembly Cache di ciascun server.

Dopo aver eseguito il comando Enable, completare questi passaggi:
1. Passare alla directory di configurazione condivisa e trovare il file ApplicationHost. config.
2. Aggiungere la seguente riga alla sezione moduli della configurazione:
    ```
    <modules>
        <!-- Registered global managed http module handler. The 'Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll' must be installed in the GAC before this config is applied. -->
        <add name="ManagedHttpModuleHelper" type="Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.ManagedHttpModuleHelper, Microsoft.AppInsights.IIS.ManagedHttpModuleHelper, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" preCondition="managedHandler,runtimeVersionv4.0" />
    </modules>
    ```
## <a name="troubleshooting"></a>risoluzione dei problemi
    
### <a name="troubleshooting-powershell"></a>Risoluzione dei problemi di PowerShell

#### <a name="determine-which-modules-are-available"></a>Determinare quali moduli sono disponibili
È possibile usare il `Get-Module -ListAvailable` comando per determinare quali moduli sono installati.

#### <a name="import-a-module-into-the-current-session"></a>Importare un modulo nella sessione corrente
Se un modulo non è stato caricato in una sessione di PowerShell, è possibile caricarlo manualmente usando il `Import-Module <path to psd1>` comando.


### <a name="troubleshooting-the-status-monitor-v2-module"></a>Risoluzione dei problemi del modulo v2 Status Monitor

#### <a name="list-the-commands-available-in-the-status-monitor-v2-module"></a>Elenco dei comandi disponibili nel modulo v2 Status Monitor
Eseguire il comando `Get-Command -Module Az.ApplicationMonitor` per ottenere i comandi disponibili:

```
Command type     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Disable-ApplicationInsightsMonitoring              0.2.1      Az.ApplicationMonitor
Cmdlet          Disable-InstrumentationEngine                      0.2.1      Az.ApplicationMonitor
Cmdlet          Enable-ApplicationInsightsMonitoring               0.2.1      Az.ApplicationMonitor
Cmdlet          Enable-InstrumentationEngine                       0.2.1      Az.ApplicationMonitor
Cmdlet          Get-ApplicationInsightsMonitoringConfig            0.2.1      Az.ApplicationMonitor
Cmdlet          Get-ApplicationInsightsMonitoringStatus            0.2.1      Az.ApplicationMonitor
Cmdlet          Set-ApplicationInsightsMonitoringConfig            0.2.1      Az.ApplicationMonitor
```

#### <a name="determine-the-current-version-of-the-status-monitor-v2-module"></a>Determinare la versione corrente del modulo v2 Status Monitor
Eseguire il `Get-ApplicationInsightsMonitoringStatus` comando per visualizzare le informazioni seguenti sul modulo:
   - Versione del modulo PowerShell
   - Versione di Application Insights SDK
   - Percorsi dei file del modulo di PowerShell
    
Rivedere le [riferimento all'API](status-monitor-v2-api-get-status.md) per una descrizione dettagliata di come usare questo cmdlet.


### <a name="troubleshooting-running-processes"></a>Risoluzione dei problemi dei processi in esecuzione

È possibile controllare i processi nel computer instrumentati per determinare se tutte le DLL vengono caricate.
Se il monitoraggio funziona, almeno 12 DLL deve essere caricata.

Usare il `Get-ApplicationInsightsMonitoringStatus -InspectProcess` comando per controllare le DLL.

Rivedere le [riferimento all'API](status-monitor-v2-api-get-status.md) per una descrizione dettagliata di come usare questo cmdlet.


### <a name="collect-etw-logs-by-using-perfview"></a>Raccogliere i log ETW con PerfView

#### <a name="setup"></a>Configurazione

1. Scaricare PerfView.exe e da PerfView64.exe [GitHub](https://github.com/Microsoft/perfview/releases).
2. Avviare PerfView64.exe.
3. Espandere **le opzioni avanzate**.
4. Deselezionare le caselle di controllo:
    - **file ZIP**
    - **Merge**
    - **Raccolta di simboli .NET**
5. Impostare questi **provider aggiuntivi**: `61f6ca3b-4b5f-5602-fa60-759a2a2d1fbd,323adc25-e39b-5c87-8658-2c1af1a92dc5,925fa42b-9ef6-5fa7-10b8-56449d7a2040,f7d60e07-e910-5aca-bdd2-9de45b46c560,7c739bb9-7861-412e-ba50-bf30d95eae36,61f6ca3b-4b5f-5602-fa60-759a2a2d1fbd,323adc25-e39b-5c87-8658-2c1af1a92dc5,252e28f4-43f9-5771-197a-e8c7e750a984`


#### <a name="collecting-logs"></a>La raccolta dei log

1. In una console dei comandi con privilegi di amministratore, eseguire il `iisreset /stop` comando per disattivare IIS e tutte le app web.
2. In PerfView, selezionare **Avvia raccolta**.
3. In una console dei comandi con privilegi di amministratore, eseguire il `iisreset /start` comando per avviare IIS.
4. Provare a passare all'app.
5. Dopo che l'app viene caricata, tornare a PerfView e selezionare **arresta raccolta**.



## <a name="next-steps"></a>Passaggi successivi

- Rivedere le [riferimento all'API](status-monitor-v2-overview.md#powershell-api-reference) per altre informazioni sui parametri, potrebbe aver ignorato.
- Se si riscontra un problema che non è elencato qui, è possibile contattare Microsoft sul [GitHub](https://github.com/Microsoft/ApplicationInsights-Home/issues).
