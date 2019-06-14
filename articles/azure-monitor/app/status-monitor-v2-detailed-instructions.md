---
title: Istruzioni dettagliate Status Monitor di Azure v2 | Microsoft Docs
description: Istruzioni dettagliate per la Guida introduttiva a Status Monitor v2. Monitorare le prestazioni di siti Web senza ridistribuire il sito Web. Funziona con le app web ASP.NET ospitate in locale, in macchine virtuali o in Azure.
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
ms.openlocfilehash: c8199c960229f9cc53cf57f9da3e1f17ebd9f5c7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67074167"
---
# <a name="status-monitor-v2-detailed-instructions"></a>Stato monitoraggio v2: Istruzioni dettagliate

Questo articolo descrive come caricare in per PowerShell Gallery e scaricare il modulo ApplicationMonitor.
Descrive i parametri più comuni che è necessario per iniziare.
Contiene inoltre istruzioni manuale nel caso in cui si ha accesso a internet.

> [!IMPORTANT]
> Stato monitoraggio v2 è attualmente in anteprima pubblica.
> Questa versione di anteprima viene fornita senza un contratto di servizio e non è consigliabile per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate e alcune potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="get-an-instrumentation-key"></a>Ottenere una chiave di strumentazione

Per iniziare, è necessaria una chiave di strumentazione. Per altre informazioni, vedere [creare una risorsa di Application Insights](create-new-resource.md#copy-the-instrumentation-key).

## <a name="run-powershell-as-admin-with-an-elevated-execution-policy"></a>Eseguire PowerShell come amministratore con i criteri di esecuzione con privilegi elevati

**Esegui come amministratore**

PowerShell richiede autorizzazioni a livello di amministratore per apportare modifiche al computer in uso.

**Criteri di esecuzione**
- Descrizione: Per impostazione predefinita, l'esecuzione di script di PowerShell è disabilitata. È consigliabile consentire script RemoteSigned per solo l'ambito corrente.
- Riferimenti: [Sui criteri di esecuzione](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-6) e [Set-ExecutionPolicy](
https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6
).
- Comando: `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process`.
- Parametro facoltativo:
    - `-Force`. Consente di ignorare la richiesta di conferma.

**Esempi di errori**

```
Install-Module : The 'Install-Module' command was found in the module 'PowerShellGet', but the module could not be
loaded. For more information, run 'Import-Module PowerShellGet'.
    
Import-Module : File C:\Program Files\WindowsPowerShell\Modules\PackageManagement\1.3.1\PackageManagement.psm1 cannot
be loaded because running scripts is disabled on this system. For more information, see about_Execution_Policies at
https:/go.microsoft.com/fwlink/?LinkID=135170.
```


## <a name="prerequisites-for-powershell"></a>Prerequisiti di PowerShell

Controllare l'istanza di PowerShell eseguendo il `$PSVersionTable` comando.
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

Queste istruzioni sono stati scritte e testate in un computer che eseguono Windows 10 e le versioni elencate in precedenza.

## <a name="prerequisites-for-powershell-gallery"></a>Prerequisiti per PowerShell Gallery

Questi passaggi verranno preparare il server per scaricare i moduli da PowerShell Gallery.

> [!NOTE] 
> PowerShell Gallery è supportato in Windows 10, Windows Server 2016 e PowerShell 6.
> Per informazioni sulle versioni precedenti, vedere [installazione PowerShellGet](https://docs.microsoft.com/powershell/gallery/installing-psget).


1. Eseguire PowerShell come amministratore con i criteri di esecuzione con privilegi elevati.
2. Installare il provider di pacchetti NuGet.
    - Descrizione: È necessario questo provider per interagire con repository basati su NuGet come PowerShell Gallery.
    - Riferimenti: [Install-PackageProvider](https://docs.microsoft.com/powershell/module/packagemanagement/install-packageprovider?view=powershell-6).
    - Comando: `Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201`.
    - Parametri facoltativi:
        - `-Proxy`. Specifica un server proxy per la richiesta.
        - `-Force`. Consente di ignorare la richiesta di conferma.
    
    Se NuGet non è configurato, si riceverà questo prompt dei comandi:
        
        NuGet provider is required to continue
        PowerShellGet requires NuGet provider version '2.8.5.201' or newer to interact with NuGet-based repositories. The NuGet
         provider must be available in 'C:\Program Files\PackageManagement\ProviderAssemblies' or
        'C:\Users\t\AppData\Local\PackageManagement\ProviderAssemblies'. You can also install the NuGet provider by running
        'Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force'. Do you want PowerShellGet to install and import
         the NuGet provider now?
        [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
    
3. Configurare PowerShell Gallery come repository attendibile.
    - Descrizione: Per impostazione predefinita, PowerShell Gallery è un repository non attendibile.
    - Riferimenti: [Set-PSRepository](https://docs.microsoft.com/powershell/module/powershellget/set-psrepository?view=powershell-6).
    - Comando: `Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted`.
    - Parametro facoltativo:
        - `-Proxy`. Specifica un server proxy per la richiesta.

    Se PowerShell Gallery non è attendibile, si riceverà questo prompt dei comandi:

        Untrusted repository
        You are installing the modules from an untrusted repository. If you trust this repository, change its
        InstallationPolicy value by running the Set-PSRepository cmdlet. Are you sure you want to install the modules from
        'PSGallery'?
        [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "N"):

    È possibile confermare la modifica e controllo PSRepositories tutti eseguendo il `Get-PSRepository` comando.

4. Installare la versione più recente di PowerShellGet.
    - Descrizione: Questo modulo contiene gli strumenti usati per ottenere altri moduli da PowerShell Gallery. Versione 1.0.0.1 viene fornito con Windows 10 e Windows Server. Versione 1.6.0 o versione successiva è obbligatorio. Per determinare quale versione è installata, eseguire il `Get-Command -Module PowerShellGet` comando.
    - Riferimenti: [Installazione PowerShellGet](https://docs.microsoft.com/powershell/gallery/installing-psget).
    - Comando: `Install-Module -Name PowerShellGet`.
    - Parametri facoltativi:
        - `-Proxy`. Specifica un server proxy per la richiesta.
        - `-Force`. Consente di ignorare l'avviso "già installati" e viene installata la versione più recente.

    Se non si usa la versione più recente di PowerShellGet, si riceverà l'errore:
    
        Install-Module : A parameter cannot be found that matches parameter name 'AllowPrerelease'.
        At line:1 char:20
        Install-Module abc -AllowPrerelease
                           ~~~~~~~~~~~~~~~~
            CategoryInfo          : InvalidArgument: (:) [Install-Module], ParameterBindingException
            FullyQualifiedErrorId : NamedParameterNotFound,Install-Module
    
5. Riavviare PowerShell. Nella sessione corrente non è possibile caricare la nuova versione. Le nuove sessioni di PowerShell verranno caricata la versione più recente di PowerShellGet.

## <a name="download-and-install-the-module-via-powershell-gallery"></a>Scaricare e installare il modulo di PowerShell Gallery

Questi passaggi scaricherà il modulo Az.ApplicationMonitor da PowerShell Gallery.

1. Verificare che siano soddisfatti tutti i prerequisiti per PowerShell Gallery.
2. Eseguire PowerShell come amministratore con i criteri di esecuzione con privilegi elevati.
3. Installare il modulo Az.ApplicationMonitor.
    - Riferimenti: [Install-Module](https://docs.microsoft.com/powershell/module/powershellget/install-module?view=powershell-6).
    - Comando: `Install-Module -Name Az.ApplicationMonitor`.
    - Parametri facoltativi:
        - `-Proxy`. Specifica un server proxy per la richiesta.
        - `-AllowPrerelease`. Consente l'installazione delle versioni alfa e beta.
        - `-AcceptLicense`. Ignora la richiesta "Accettare la licenza"
        - `-Force`. Consente di ignorare l'avviso "Repository non attendibile".

## <a name="download-and-install-the-module-manually-offline-option"></a>Scaricare e installare manualmente il modulo (opzione offline)

Se per qualsiasi motivo non è possibile connettersi al modulo di PowerShell, è possibile scaricare e installare manualmente il modulo Az.ApplicationMonitor.

### <a name="manually-download-the-latest-nupkg-file"></a>Scaricare manualmente il file nupkg più recente

1. Passare a https://www.powershellgallery.com/packages/Az.ApplicationMonitor.
2. Selezionare la versione più recente del file nei **cronologia delle versioni** tabella.
3. Sotto **opzioni di installazione**, selezionare **Download manuale**.

### <a name="option-1-install-into-a-powershell-modules-directory"></a>Opzione 1: Installare in una directory di moduli di PowerShell
Installare il modulo di PowerShell scaricato manualmente in una directory di PowerShell in modo che potranno essere rilevata da sessioni di PowerShell.
Per altre informazioni, vedere [installazione di un modulo di PowerShell](https://docs.microsoft.com/powershell/developer/module/installing-a-powershell-module).


#### <a name="unzip-nupkg-as-a-zip-file-by-using-expand-archive-v1010"></a>Decomprimere nupkg come file zip con Expand-Archive (v1.0.1.0)

- Descrizione: La versione di base di Microsoft.PowerShell.Archive (v1.0.1.0) non è possibile decomprimere i file nupkg. Rinominare il file con estensione zip.
- Riferimenti: [Expand-Archive](https://docs.microsoft.com/powershell/module/microsoft.powershell.archive/expand-archive?view=powershell-6).
- Comando:

    ```
    $pathToNupkg = "C:\az.applicationmonitor.0.3.0-alpha.nupkg"
    $pathToZip = ([io.path]::ChangeExtension($pathToNupkg, "zip"))
    $pathToNupkg | rename-item -newname $pathToZip
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToZip -DestinationPath $pathInstalledModule
    ```

#### <a name="unzip-nupkg-by-using-expand-archive-v1100"></a>Decomprimere nupkg usando Expand-Archive (v1.1.0.0)

- Descrizione: Usare una versione corrente di Expand-Archive per decomprimere file nupkg senza modificare l'estensione.
- Riferimenti: [Espandere-Archive](https://docs.microsoft.com/powershell/module/microsoft.powershell.archive/expand-archive?view=powershell-6) e [Microsoft.PowerShell.Archive](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive/1.1.0.0).
- Comando:

    ```
    $pathToNupkg = "C:\az.applicationmonitor.0.2.1-alpha.nupkg"
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToNupkg -DestinationPath $pathInstalledModule
    ```

### <a name="option-2-unzip-and-import-nupkg-manually"></a>Opzione 2: Decomprimere e importare manualmente nupkg
Installare il modulo di PowerShell scaricato manualmente in una directory di PowerShell in modo che potranno essere rilevata da sessioni di PowerShell.
Per altre informazioni, vedere [installazione di un modulo di PowerShell](https://docs.microsoft.com/powershell/developer/module/installing-a-powershell-module).

Se si installa il modulo in qualsiasi altra directory, importare manualmente il modulo usando [Import-Module](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/import-module?view=powershell-6).

> [!IMPORTANT] 
> Le DLL verranno installato tramite i percorsi relativi.
> Store il contenuto del pacchetto nella directory di runtime desiderata e confermare che le autorizzazioni di accesso consentono di lettura ma non di scrittura.

1. Modificare l'estensione in "ZIP" ed estrarre il contenuto del pacchetto in directory di installazione desiderata.
2. Trovare il percorso del file di Az.ApplicationMonitor.psd1.
3. Eseguire PowerShell come amministratore con i criteri di esecuzione con privilegi elevati.
4. Caricare il modulo usando il `Import-Module Az.ApplicationMonitor.psd1` comando.
    

## <a name="route-traffic-through-a-proxy"></a>Instradare il traffico attraverso un proxy

Quando si esegue il monitoraggio di un computer sulla rete intranet privata, è necessario instradare il traffico HTTP attraverso un proxy.

I comandi di PowerShell per scaricare e installare Az.ApplicationMonitor da PowerShell Gallery supportano un `-Proxy` parametro.
Quando si scrivono gli script di installazione, esaminare le istruzioni precedenti.

Application Insights SDK dovrà inviare dati di telemetria dell'app a Microsoft. È consigliabile configurare le impostazioni proxy per l'app nel file Web. config. Per altre informazioni, vedere [domande frequenti di Application Insights: Pass-through proxy](https://docs.microsoft.com/azure/azure-monitor/app/troubleshoot-faq#proxy-passthrough).


## <a name="enable-monitoring"></a>Abilitare il monitoraggio

Usare il `Enable-ApplicationInsightsMonitoring` comando per abilitare il monitoraggio.

Vedere le [riferimento all'API](status-monitor-v2-api-enable-monitoring.md) per una descrizione dettagliata di come usare questo cmdlet.



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

- Usare la Guida alla [risolvere i problemi di](status-monitor-v2-troubleshoot.md) v2 Status Monitor.
