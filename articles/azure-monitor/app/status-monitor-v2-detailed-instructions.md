---
title: Istruzioni dettagliate per l'agente applicazione Azure Insights | Microsoft Docs
description: Istruzioni dettagliate su come iniziare a usare Application Insights Agent. Monitora le prestazioni del sito Web senza ridistribuire il sito Web. Funziona con le app Web ASP.NET ospitate in locale, in macchine virtuali o in Azure.
services: application-insights
documentationcenter: .net
author: TimothyMothra
manager: alexklim
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: tilee
ms.openlocfilehash: d7a6db65b92ec2d3a3fdcf55aea0523f57012be1
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388240"
---
# <a name="application-insights-agent-formerly-named-status-monitor-v2-detailed-instructions"></a>Application Insights Agent (denominato in precedenza Status Monitor v2): istruzioni dettagliate

Questo articolo descrive come eseguire l'onboarding nel PowerShell Gallery e scaricare il modulo ApplicationMonitor.
Sono inclusi i parametri più comuni necessari per iniziare.
Sono inoltre disponibili istruzioni per il download manuale nel caso in cui non si disponga di accesso a Internet.

## <a name="get-an-instrumentation-key"></a>Ottenere una chiave di strumentazione

Per iniziare, è necessaria una chiave di strumentazione. Per altre informazioni, vedere [creare una risorsa Application Insights](create-new-resource.md#copy-the-instrumentation-key).

## <a name="run-powershell-as-admin-with-an-elevated-execution-policy"></a>Eseguire PowerShell come amministratore con criteri di esecuzione elevati

### <a name="run-as-admin"></a>Esegui come amministratore

PowerShell richiede autorizzazioni a livello di amministratore per apportare modifiche al computer.
### <a name="execution-policy"></a>Criteri di esecuzione
- Descrizione: per impostazione predefinita, l'esecuzione degli script di PowerShell è disabilitata. È consigliabile consentire gli script RemoteSigned solo per l'ambito corrente.
- Riferimento: [informazioni sui criteri di esecuzione](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-6) e [Set-ExecutionPolicy](
https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6
).
- Comando: `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process`.
- Parametro facoltativo:
    - `-Force`. Ignora la richiesta di conferma.

**Errori di esempio**

```
Install-Module : The 'Install-Module' command was found in the module 'PowerShellGet', but the module could not be
loaded. For more information, run 'Import-Module PowerShellGet'.
    
Import-Module : File C:\Program Files\WindowsPowerShell\Modules\PackageManagement\1.3.1\PackageManagement.psm1 cannot
be loaded because running scripts is disabled on this system. For more information, see about_Execution_Policies at
https:/go.microsoft.com/fwlink/?LinkID=135170.
```


## <a name="prerequisites-for-powershell"></a>Prerequisiti di PowerShell

Controllare l'istanza di PowerShell eseguendo il comando `$PSVersionTable`.
Questo comando produce l'output seguente:


```
Name                           Value
----                           -----
PSVersion                      5.1.17763.316
PSEdition                      Desktop
PSCompatibleVersions           {1.0, 2.0, 3.0, 4.0...}
BuildVersion                   10.0.17763.316
CLRVersion                     4.0.30319.42000
WSManStackVersion              3.0
PSRemotingProtocolVersion      2.3
SerializationVersion           1.1.0.1
```

Queste istruzioni sono state scritte e testate in un computer che esegue Windows 10 e le versioni elencate in precedenza.

## <a name="prerequisites-for-powershell-gallery"></a>Prerequisiti per PowerShell Gallery

Questa procedura consente di preparare il server per scaricare i moduli da PowerShell Gallery.

> [!NOTE] 
> PowerShell Gallery è supportato in Windows 10, Windows Server 2016 e PowerShell 6.
> Per informazioni sulle versioni precedenti, vedere [installazione di PowerShellGet](https://docs.microsoft.com/powershell/gallery/installing-psget).


1. Eseguire PowerShell come amministratore con criteri di esecuzione elevati.
2. Installare il provider di pacchetti NuGet.
    - Descrizione: è necessario che questo provider interagisca con i repository basati su NuGet come PowerShell Gallery.
    - Riferimento: [Install-PackageProvider](https://docs.microsoft.com/powershell/module/packagemanagement/install-packageprovider?view=powershell-6).
    - Comando: `Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201`.
    - Parametri facoltativi:
        - `-Proxy`. Specifica un server proxy per la richiesta.
        - `-Force`. Ignora la richiesta di conferma.
    
    Questo prompt verrà visualizzato se NuGet non è configurato:
        
        NuGet provider is required to continue
        PowerShellGet requires NuGet provider version '2.8.5.201' or newer to interact with NuGet-based repositories. The NuGet
         provider must be available in 'C:\Program Files\PackageManagement\ProviderAssemblies' or
        'C:\Users\t\AppData\Local\PackageManagement\ProviderAssemblies'. You can also install the NuGet provider by running
        'Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force'. Do you want PowerShellGet to install and import
         the NuGet provider now?
        [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
    
3. Configurare PowerShell Gallery come repository attendibile.
    - Descrizione: per impostazione predefinita, PowerShell Gallery è un repository non attendibile.
    - Riferimento: [set-PSRepository](https://docs.microsoft.com/powershell/module/powershellget/set-psrepository?view=powershell-6).
    - Comando: `Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted`.
    - Parametro facoltativo:
        - `-Proxy`. Specifica un server proxy per la richiesta.

    Questo prompt verrà visualizzato se PowerShell Gallery non è attendibile:

        Untrusted repository
        You are installing the modules from an untrusted repository. If you trust this repository, change its
        InstallationPolicy value by running the Set-PSRepository cmdlet. Are you sure you want to install the modules from
        'PSGallery'?
        [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "N"):

    È possibile confermare questa modifica e controllare tutti i PSRepositories eseguendo il comando `Get-PSRepository`.

4. Installare la versione più recente di PowerShellGet.
    - Descrizione: questo modulo contiene gli strumenti usati per ottenere altri moduli da PowerShell Gallery. La versione 1.0.0.1 viene fornita con Windows 10 e Windows Server. È richiesta la versione 1.6.0 o successiva. Per determinare quale versione è installata, eseguire il comando `Get-Command -Module PowerShellGet`.
    - Informazioni di riferimento: [installazione di PowerShellGet](https://docs.microsoft.com/powershell/gallery/installing-psget).
    - Comando: `Install-Module -Name PowerShellGet`.
    - Parametri facoltativi:
        - `-Proxy`. Specifica un server proxy per la richiesta.
        - `-Force`. Ignora l'avviso "già installato" e installa la versione più recente.

    Questo errore viene visualizzato se non si usa la versione più recente di PowerShellGet:
    
        Install-Module : A parameter cannot be found that matches parameter name 'AllowPrerelease'.
        At line:1 char:20
        Install-Module abc -AllowPrerelease
                           ~~~~~~~~~~~~~~~~
            CategoryInfo          : InvalidArgument: (:) [Install-Module], ParameterBindingException
            FullyQualifiedErrorId : NamedParameterNotFound,Install-Module
    
5. Riavviare PowerShell. Non è possibile caricare la nuova versione nella sessione corrente. Le nuove sessioni di PowerShell caricherà la versione più recente di PowerShellGet.

## <a name="download-and-install-the-module-via-powershell-gallery"></a>Scaricare e installare il modulo tramite PowerShell Gallery

Con questa procedura viene scaricato il modulo AZ. ApplicationMonitor da PowerShell Gallery.

1. Verificare che siano soddisfatti tutti i prerequisiti per PowerShell Gallery.
2. Eseguire PowerShell come amministratore con criteri di esecuzione elevati.
3. Installare il modulo AZ. ApplicationMonitor.
    - Riferimento: [install-module](https://docs.microsoft.com/powershell/module/powershellget/install-module?view=powershell-6).
    - Comando: `Install-Module -Name Az.ApplicationMonitor`.
    - Parametri facoltativi:
        - `-Proxy`. Specifica un server proxy per la richiesta.
        - `-AllowPrerelease`. Consente l'installazione di versioni alfa e beta.
        - `-AcceptLicense`. Ignora la richiesta di accettazione della licenza
        - `-Force`. Ignora l'avviso "repository non attendibile".

## <a name="download-and-install-the-module-manually-offline-option"></a>Scaricare e installare il modulo manualmente (opzione offline)

Se per qualsiasi motivo non è possibile connettersi al modulo di PowerShell, è possibile scaricare e installare manualmente il modulo AZ. ApplicationMonitor.

### <a name="manually-download-the-latest-nupkg-file"></a>Scaricare manualmente il file nupkg più recente

1. Passare a https://www.powershellgallery.com/packages/Az.ApplicationMonitor.
2. Selezionare la versione più recente del file nella tabella di **cronologia delle versioni** .
3. In **Opzioni di installazione**selezionare **download manuale**.

### <a name="option-1-install-into-a-powershell-modules-directory"></a>Opzione 1: installare in una directory dei moduli di PowerShell
Installare il modulo PowerShell scaricato manualmente in una directory di PowerShell, in modo che possa essere individuato dalle sessioni di PowerShell.
Per altre informazioni, vedere [installazione di un modulo di PowerShell](https://docs.microsoft.com/powershell/developer/module/installing-a-powershell-module).


#### <a name="unzip-nupkg-as-a-zip-file-by-using-expand-archive-v1010"></a>Decomprimere nupkg come file zip usando Expand-Archive (v 1.0.1.0)

- Descrizione: la versione di base di Microsoft. PowerShell. Archive (v 1.0.1.0) non può decomprimere i file nupkg. Rinominare il file con l'estensione zip.
- Riferimento: [expand-Archive](https://docs.microsoft.com/powershell/module/microsoft.powershell.archive/expand-archive?view=powershell-6).
- Comando:

    ```
    $pathToNupkg = "C:\az.applicationmonitor.0.3.0-alpha.nupkg"
    $pathToZip = ([io.path]::ChangeExtension($pathToNupkg, "zip"))
    $pathToNupkg | rename-item -newname $pathToZip
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToZip -DestinationPath $pathInstalledModule
    ```

#### <a name="unzip-nupkg-by-using-expand-archive-v1100"></a>Decomprimere nupkg usando Expand-Archive (v 1.1.0.0)

- Descrizione: usare una versione corrente di Expand-Archive per decomprimere i file nupkg senza modificare l'estensione.
- Riferimento: [expand-Archive](https://docs.microsoft.com/powershell/module/microsoft.powershell.archive/expand-archive?view=powershell-6) e [Microsoft. PowerShell. Archive](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive/1.1.0.0).
- Comando:

    ```
    $pathToNupkg = "C:\az.applicationmonitor.0.2.1-alpha.nupkg"
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToNupkg -DestinationPath $pathInstalledModule
    ```

### <a name="option-2-unzip-and-import-nupkg-manually"></a>Opzione 2: decomprimere e importare manualmente nupkg
Installare il modulo PowerShell scaricato manualmente in una directory di PowerShell, in modo che possa essere individuato dalle sessioni di PowerShell.
Per altre informazioni, vedere [installazione di un modulo di PowerShell](https://docs.microsoft.com/powershell/developer/module/installing-a-powershell-module).

Se si sta installando il modulo in un'altra directory, importare manualmente il modulo usando [Import-Module](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/import-module?view=powershell-6).

> [!IMPORTANT] 
> Le dll vengono installate tramite percorsi relativi.
> Archiviare il contenuto del pacchetto nella directory di runtime prevista e verificare che le autorizzazioni di accesso consentano la lettura, ma non la scrittura.

1. Modificare l'estensione in ". zip" ed estrarre il contenuto del pacchetto nella directory di installazione desiderata.
2. Trovare il percorso del file AZ. ApplicationMonitor. psd1.
3. Eseguire PowerShell come amministratore con criteri di esecuzione elevati.
4. Caricare il modulo usando il comando `Import-Module Az.ApplicationMonitor.psd1`.
    

## <a name="route-traffic-through-a-proxy"></a>Instradare il traffico attraverso un proxy

Quando si esegue il monitoraggio di un computer nella Intranet privata, è necessario instradare il traffico HTTP attraverso un proxy.

I comandi di PowerShell per scaricare e installare AZ. ApplicationMonitor dalla PowerShell Gallery supportano un parametro `-Proxy`.
Esaminare le istruzioni precedenti quando si scrivono gli script di installazione.

Il Application Insights SDK dovrà inviare i dati di telemetria dell'app a Microsoft. Si consiglia di configurare le impostazioni proxy per l'app nel file Web. config. Per altre informazioni, vedere [domande frequenti su Application Insights: Passthrough del proxy](https://docs.microsoft.com/azure/azure-monitor/app/troubleshoot-faq#proxy-passthrough).


## <a name="enable-monitoring"></a>Abilitare il monitoraggio

Usare il comando `Enable-ApplicationInsightsMonitoring` per abilitare il monitoraggio.

Per una descrizione dettagliata di come usare questo cmdlet, vedere le informazioni di [riferimento sulle API](status-monitor-v2-api-enable-monitoring.md) .



## <a name="next-steps"></a>Passaggi successivi

 Visualizzare i dati di telemetria:

- [Esplora le metriche](../../azure-monitor/app/metrics-explorer.md) per monitorare le prestazioni e l'utilizzo.
- Eseguire [ricerche negli eventi e nei log](../../azure-monitor/app/diagnostic-search.md) per diagnosticare i problemi.
- [Usare Analytics](../../azure-monitor/app/analytics.md) per query più avanzate.
- [Creare dashboard](../../azure-monitor/app/overview-dashboard.md).

 Aggiungere altri dati di telemetria:

- [Creare test Web](monitor-web-app-availability.md) per assicurarsi che il sito rimanga attivo.
- Aggiungere i dati di [telemetria del client Web](../../azure-monitor/app/javascript.md) per visualizzare le eccezioni dal codice della pagina Web e per abilitare le chiamate di traccia.
- [Aggiungere il Application Insights SDK al codice per](../../azure-monitor/app/asp-net.md) poter inserire le chiamate di traccia e log.

Eseguire altre operazioni con Application Insights Agent:

- Usare la guida per [risolvere i problemi relativi](status-monitor-v2-troubleshoot.md) a Application Insights Agent.
