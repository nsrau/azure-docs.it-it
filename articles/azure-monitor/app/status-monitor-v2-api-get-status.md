---
title: "Riferimento all'API di v2 monitoraggio dello stato di Azure: Ottenere lo stato | Microsoft Docs"
description: Stato monitoraggio v2 API riferimento Get-ApplicationInsightsMonitoringStatus. Monitorare le prestazioni di siti Web senza ridistribuire il sito Web. Questa funzionalità può essere usata con app Web ASP.NET ospitate in locale, in macchine virtuali o in Azure.
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
ms.openlocfilehash: 1553bede87aab118b1fdddae20201a5d283a7b24
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2019
ms.locfileid: "64870463"
---
# <a name="status-monitor-v2-api-get-applicationinsightsmonitoringstatus-v021-alpha"></a>API v2 di Status Monitor: Get-ApplicationInsightsMonitoringStatus (v0.2.1-alpha)

Questo documento descrive un cmdlet che viene fornito come un membro del [modulo di Az.ApplicationMonitor PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

> [!IMPORTANT]
> Stato monitoraggio v2 è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [condizioni per l'utilizzo per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="description"></a>DESCRIZIONE

Questo cmdlet è disponibile per la risoluzione dei problemi il modulo di PowerShell in uso.
Questo cmdlet segnala le informazioni sulla versione e i file di chiave necessari per il monitoraggio.
Parametri aggiuntivi per forniscono report aggiuntivi sullo stato corrente del monitoraggio.

> [!IMPORTANT] 
> Questo cmdlet richiede una sessione di PowerShell con autorizzazioni di amministratore.

## <a name="examples"></a>Esempi


### <a name="example-basic-information"></a>Esempio: le informazioni di base
```
PS C:\> Get-ApplicationInsightsMonitoringStatus


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

### <a name="example-runtime-status"></a>Esempio: lo stato di runtime
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

## <a name="parameters"></a>Parametri 

### <a name="no-params"></a>(Senza parametri)

Dal **predefinito**, questo cmdlet indicherà i numeri di versione e i percorsi delle DLL necessari per il monitoraggio.

Usare questa opzione se è necessario identificare la versione di qualsiasi DLL, tra cui Application Insights SDK.


### <a name="-inspectprocess"></a>-InspectProcess

**Facoltativo**. Questo cmdlet segnala se IIS è in esecuzione.
Questo cmdlet scaricherà anche strumenti esterni per controllare se sono caricate le DLL necessarie al runtime IIS.


Se questo processo non riesce per qualsiasi motivo, è possibile eseguire questi comandi manualmente:
- iisreset.exe /status
- [handle64.exe](https://docs.microsoft.com/sysinternals/downloads/handle) w3wp -p | findstr /I "InstrumentationEngine per intelligenza artificiale. Application Insights"
- [listdlls64.exe](https://docs.microsoft.com/sysinternals/downloads/listdlls) w3wp | findstr /I "InstrumentationEngine per intelligenza artificiale ApplicationInsights"


### <a name="-force"></a>-Force

**Facoltativo**. Utilizzato solo con InspectProcess. Questo parametro verrà ignorato il messaggio di richiesta per scaricare gli strumenti aggiuntivi.


