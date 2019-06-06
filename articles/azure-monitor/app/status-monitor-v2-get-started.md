---
title: Azure v2 di Status Monitor - Guida introduttiva | Microsoft Docs
description: Una Guida rapida per la versione v2 Status Monitor. Monitorare le prestazioni di siti Web senza ridistribuire il sito Web. Funziona con le app web ASP.NET ospitate in locale, in macchine virtuali o in Azure.
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
ms.openlocfilehash: 4da9d8e8efd5f70718f18b2e8e35ea6b5adf6757
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/06/2019
ms.locfileid: "66734967"
---
# <a name="get-started-with-status-monitor-v2"></a>Introduzione a Status Monitor v2

Questo articolo contiene i comandi di avvio rapido dovrebbe funzionare per la maggior parte degli ambienti.
Le istruzioni dipendono da PowerShell Gallery per distribuire gli aggiornamenti.
Questi comandi consentono di PowerShell `-Proxy` parametro.

Per una spiegazione di questi comandi, istruzioni per la personalizzazione e informazioni sulla risoluzione dei problemi, vedere la [istruzioni dettagliate](status-monitor-v2-detailed-instructions.md).

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

> [!IMPORTANT]
> Stato monitoraggio v2 è attualmente in anteprima pubblica.
> Questa versione di anteprima viene fornita senza un contratto di servizio e non è consigliabile per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate e alcune potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="download-and-install-via-powershell-gallery"></a>Scaricare e installare tramite PowerShell Gallery

### <a name="install-prerequisites"></a>Installare i componenti prerequisiti
Eseguire PowerShell come amministratore.
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
Install-Module -Name PowerShellGet -Force
``` 
Chiudere PowerShell.

### <a name="install-status-monitor-v2"></a>Stato dell'installazione del monitoraggio v2
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
    
        
## <a name="download-and-install-manually-offline-option"></a>Scarica e installa manualmente (opzione offline)
### <a name="download-the-module"></a>Scaricare il modulo
Scaricare manualmente la versione più recente del modulo da [PowerShell Gallery](https://www.powershellgallery.com/packages/Az.ApplicationMonitor).

### <a name="unzip-and-install-status-monitor-v2"></a>Decomprimere e installare Status Monitor v2
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

- [Esplorare le metriche](../../azure-monitor/app/metrics-explorer.md) per monitorare le prestazioni e utilizzo.
- [Cercare eventi e log](../../azure-monitor/app/diagnostic-search.md) per diagnosticare i problemi.
- [Usare Analitica](../../azure-monitor/app/analytics.md) per più query avanzate.
- [Creare dashboard](../../azure-monitor/app/overview-dashboard.md).

 Aggiungere altri dati di telemetria:

- [Creare test web](monitor-web-app-availability.md) per assicurarsi che il sito rimanga attivo.
- [Aggiungere dati di telemetria client web](../../azure-monitor/app/javascript.md) per visualizzare le eccezioni dal codice della pagina web e per consentire le chiamate di traccia.
- [Aggiungere Application Insights SDK al codice](../../azure-monitor/app/asp-net.md) in modo che è possibile inserire una traccia e registrare le chiamate.

Altri vantaggi v2 Status Monitor:

- Rivedere le [istruzioni dettagliate](status-monitor-v2-detailed-instructions.md) per una spiegazione dei comandi fare clic qui.
- Usare la Guida alla [risolvere i problemi di](status-monitor-v2-troubleshoot.md) v2 Status Monitor.
