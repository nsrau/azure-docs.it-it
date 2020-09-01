---
title: Agente di applicazione Azure Insights-Guida introduttiva | Microsoft Docs
description: Guida introduttiva per Application Insights Agent. Monitora le prestazioni del sito Web senza ridistribuire il sito Web. Funziona con le app Web ASP.NET ospitate in locale, in macchine virtuali o in Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 690304ecef80d988f9a554cd10ce4689f5c72133
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2020
ms.locfileid: "89070140"
---
# <a name="get-started-with-azure-monitor-application-insights-agent-for-on-premises-servers"></a>Introduzione a monitoraggio di Azure Application Insights Agent per i server locali

Questo articolo contiene i comandi di avvio rapido che dovrebbero funzionare per la maggior parte degli ambienti.
Le istruzioni dipendono dalla PowerShell Gallery per la distribuzione degli aggiornamenti.
Questi comandi supportano il parametro di PowerShell `-Proxy` .

Per una spiegazione di questi comandi, istruzioni di personalizzazione e informazioni sulla risoluzione dei problemi, vedere le [istruzioni dettagliate](status-monitor-v2-detailed-instructions.md).

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="download-and-install-via-powershell-gallery"></a>Scaricare e installare tramite PowerShell Gallery

### <a name="install-prerequisites"></a>Installare i prerequisiti
Eseguire PowerShell come amministratore.
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
Install-Module -Name PowerShellGet -Force
``` 
Chiudere PowerShell.

### <a name="install-application-insights-agent"></a>Installare Application Insights Agent
Eseguire PowerShell come amministratore.
```powershell   
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Install-Module -Name Az.ApplicationMonitor -AllowPrerelease -AcceptLicense
``` 

### <a name="enable-monitoring"></a>Abilitare il monitoraggio
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```
    
        
## <a name="download-and-install-manually-offline-option"></a>Scaricare e installare manualmente (opzione offline)
### <a name="download-the-module"></a>Scaricare il modulo
Scaricare manualmente la versione più recente del modulo dal [PowerShell Gallery](https://www.powershellgallery.com/packages/Az.ApplicationMonitor).

### <a name="unzip-and-install-application-insights-agent"></a>Decomprimere e installare Application Insights Agent
```powershell
$pathToNupkg = "C:\Users\t\Desktop\Az.ApplicationMonitor.0.3.0-alpha.nupkg"
$pathToZip = ([io.path]::ChangeExtension($pathToNupkg, "zip"))
$pathToNupkg | rename-item -newname $pathToZip
$pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\Az.ApplicationMonitor"
Expand-Archive -LiteralPath $pathToZip -DestinationPath $pathInstalledModule
```
### <a name="enable-monitoring"></a>Abilitare il monitoraggio
```powershell
Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```



## <a name="next-steps"></a>Passaggi successivi

 Visualizzare i dati di telemetria:

- [Esplora le metriche](../platform/metrics-charts.md) per monitorare le prestazioni e l'utilizzo.
- Eseguire [ricerche negli eventi e nei log](./diagnostic-search.md) per diagnosticare i problemi.
- [Usare Analytics](../log-query/log-query-overview.md) per query più avanzate.
- [Creare dashboard](./overview-dashboard.md).

 Aggiungere altri dati di telemetria:

- [Creare test Web](monitor-web-app-availability.md) per assicurarsi che il sito rimanga attivo.
- Aggiungere i dati di [telemetria del client Web](./javascript.md) per visualizzare le eccezioni dal codice della pagina Web e per abilitare le chiamate di traccia.
- [Aggiungere il Application Insights SDK al codice per](./asp-net.md) poter inserire le chiamate di traccia e log.

Eseguire altre operazioni con Application Insights Agent:

- Esaminare le [istruzioni dettagliate](status-monitor-v2-detailed-instructions.md) per una spiegazione dei comandi disponibili qui.
- Usare la guida per [risolvere i problemi relativi](status-monitor-v2-troubleshoot.md) a Application Insights Agent.

