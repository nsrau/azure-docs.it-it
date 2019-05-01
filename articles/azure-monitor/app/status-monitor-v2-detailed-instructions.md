---
title: Istruzioni dettagliate Status Monitor di Azure v2 | Microsoft Docs
description: Istruzioni dettagliate per la Guida introduttiva a Status Monitor v2. Monitorare le prestazioni di siti Web senza ridistribuire il sito Web. Questa funzionalità può essere usata con app Web ASP.NET ospitate in locale, in macchine virtuali o in Azure.
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
ms.openlocfilehash: 62c7c6b2f78ea90999f72597291e8347ddc14029
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2019
ms.locfileid: "64870433"
---
# <a name="status-monitor-v2-detailed-instructions"></a>Istruzioni dettagliate sullo stato monitoraggio v2

Questo documento come Dettagli esecuzione dell'onboarding a PowerShell Gallery e scaricare il modulo ApplicationMonitor. Abbiamo documentato i parametri più comuni necessari per iniziare.
Sono inoltre incluse istruzioni manuale nel caso in cui l'accesso a internet non è disponibile.

> [!IMPORTANT]
> Stato monitoraggio v2 è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [condizioni per l'utilizzo per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="instrumentation-key"></a>Chiave di strumentazione

Per iniziare, è necessario disporre di una chiave di strumentazione. Per altre informazioni, leggere [creare una risorsa di Application Insights](create-new-resource.md#copy-the-instrumentation-key).

## <a name="run-powershell-as-administrator-with-an-elevated-execution-policy"></a>Eseguire PowerShell come amministratore con i criteri di esecuzione con privilegi elevati

**Esegui come amministratore**: 
- Descrizione: PowerShell sarà necessario autorizzazioni di amministratore per apportare modifiche al computer in uso.

**I criteri di esecuzione**:
- Descrizione: Per impostazione predefinita, verrà disabilitata in esecuzione gli script di PowerShell. È consigliabile consentire script RemoteSigned per solo l'ambito corrente.
- Riferimenti: [Sui criteri di esecuzione](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-6) e [Set-ExecutionPolicy](
https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6
)
- Cmd: `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process`
- Parametri facoltativi:
    - `-Force` Ciò ignorerà la richiesta di conferma.

**Errori di esempio:**

```
Install-Module : The 'Install-Module' command was found in the module 'PowerShellGet', but the module could not be
loaded. For more information, run 'Import-Module PowerShellGet'.
    
Import-Module : File C:\Program Files\WindowsPowerShell\Modules\PackageManagement\1.3.1\PackageManagement.psm1 cannot
be loaded because running scripts is disabled on this system. For more information, see about_Execution_Policies at
https:/go.microsoft.com/fwlink/?LinkID=135170.
```


## <a name="prerequisites-for-powershell"></a>Prerequisiti di PowerShell

Controllare la versione corrente di PowerShell eseguendo il comando: `$PSVersionTable`.
Il comando produce l'output seguente:


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

Queste istruzioni sono stati scritte e testate in un computer Windows 10 con le versioni elencate in precedenza.

## <a name="prerequisites-for-powershell-gallery"></a>Prerequisiti per PowerShell Gallery

Questi passaggi verranno preparare il server per scaricare i moduli da PowerShell Gallery.

> [!NOTE] 
> Supporto per PowerShell Gallery è incluso in Windows 10, Windows Server 2016 e PowerShell 6. Per le versioni precedenti, leggere questo documento: [Installazione di PowerShellGet](https://docs.microsoft.com/powershell/gallery/installing-psget)


1. Eseguire PowerShell come amministratore con i criteri di esecuzione con privilegi elevati.
2. Provider di pacchetti NuGet 
    - Descrizione: Questo provider è necessaria per interagire con i repository basato su NuGet come PowerShell Gallery
    - Riferimenti: [Install-PackageProvider](https://docs.microsoft.com/powershell/module/packagemanagement/install-packageprovider?view=powershell-6)
    - Cmd: `Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201`
    - Parametri facoltativi:
        - `-Proxy` Specifica un server proxy per la richiesta.
        - `-Force` Ciò ignorerà la richiesta di conferma. 
    
    Se NuGet non è configurato, si riceverà questo prompt dei comandi:
        
        NuGet provider is required to continue
        PowerShellGet requires NuGet provider version '2.8.5.201' or newer to interact with NuGet-based repositories. The NuGet
         provider must be available in 'C:\Program Files\PackageManagement\ProviderAssemblies' or
        'C:\Users\t\AppData\Local\PackageManagement\ProviderAssemblies'. You can also install the NuGet provider by running
        'Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force'. Do you want PowerShellGet to install and import
         the NuGet provider now?
        [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
    
3. Repository attendibile
    - Descrizione: Per impostazione predefinita, PowerShell Gallery è un repository non attendibile.
    - Riferimenti: [Set-PSRepository](https://docs.microsoft.com/powershell/module/powershellget/set-psrepository?view=powershell-6)
    - Cmd: `Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted`
    - Parametri facoltativi:
        - `-Proxy` Specifica un server proxy per la richiesta.

    Se PowerShell Gallery non è attendibile, si riceverà questo prompt dei comandi:

        Untrusted repository
        You are installing the modules from an untrusted repository. If you trust this repository, change its
        InstallationPolicy value by running the Set-PSRepository cmdlet. Are you sure you want to install the modules from
        'PSGallery'?
        [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "N"):

    - Possibile confermare la modifica e controllo PSRepositories tutti eseguendo il comando: `Get-PSRepository`

4. Versione di PowerShellGet 
    - Descrizione: Questo modulo contiene gli strumenti usati per ottenere altri moduli da PowerShell Gallery. V1.0.0.1 viene fornito con Windows 10 e Windows Server. Versione minima necessaria è la 1.6.0. Per controllare la versione installata eseguire il comando: `Get-Command -Module PowerShellGet`
    - Riferimenti: [Installazione di PowerShellGet](https://docs.microsoft.com/powershell/gallery/installing-psget)
    - Cmd: `Install-Module -Name PowerShellGet`
    - Parametri facoltativi:
        - `-Proxy` Specifica un server proxy per la richiesta.
        - `-Force` Questo verrà ignorare l'avviso "già installati" e installare la versione più recente.

    Se non si usa la versione più recente di PowerShellGet, si riceverà l'errore:
    
        Install-Module : A parameter cannot be found that matches parameter name 'AllowPrerelease'.
        At line:1 char:20
        Install-Module abc -AllowPrerelease
                           ~~~~~~~~~~~~~~~~
            CategoryInfo          : InvalidArgument: (:) [Install-Module], ParameterBindingException
            FullyQualifiedErrorId : NamedParameterNotFound,Install-Module
    
5. Riavviare PowerShell. Non è possibile caricare la nuova versione nella sessione corrente. In ogni nuova sessione di Powershell avrà il più recente PowerShellGet caricato.

## <a name="download--install-via-powershell-gallery"></a>Scaricare e installare tramite PowerShell Gallery

Questi passaggi scaricherà il modulo Az.ApplicationMonitor da PowerShell Gallery.

1. Sono necessari i prerequisiti per PowerShell Gallery.
2. Eseguire PowerShell come amministratore con i criteri di esecuzione con privilegi elevati.
3. Installare il modulo Az.ApplicationMonitor
    - Riferimenti: [Install-Module](https://docs.microsoft.com/powershell/module/powershellget/install-module?view=powershell-6)
    - Cmd: `Install-Module -Name Az.ApplicationMonitor`
    - Parametri facoltativi:
        - `-Proxy` Specifica un server proxy per la richiesta.
        - `-AllowPrerelease` In questo modo l'installazione di versioni alfa e beta.
        - `-AcceptLicense` Questo verrà ignorato il prompt dei comandi "Accettare la licenza"
        - `-Force` Questo verrà ignorato l'avviso "Repository non attendibile"

## <a name="download--install-manually-offline-option"></a>Scaricare e installare manualmente (opzione offline)

Se per qualsiasi motivo non è possibile connettersi al modulo di PowerShell, è possibile scaricare e installare manualmente il modulo Az.ApplicationMonitor.

### <a name="manually-download-the-latest-nupkg"></a>Scaricare manualmente il pacchetto nupkg più recente

1. Passare a: https://www.powershellgallery.com/packages/Az.ApplicationMonitor
2. Selezionare l'ultima versione dalla cronologia delle versioni.
3. Trovare "Opzioni di installazione" e selezionare "Scarica manuale".

### <a name="option-1-install-into-powershell-modules-directory"></a>Opzione 1: installare nella directory di moduli di PowerShell
Installare il modulo di PowerShell scaricato manualmente in una directory di PowerShell in modo che possa essere individuabile da sessioni di PowerShell.
Per altre informazioni, vedere: [Installazione di un modulo di PowerShell](https://docs.microsoft.com/powershell/developer/module/installing-a-powershell-module)


#### <a name="unzip-nupkg-as-zip-using-expand-archive-v1010"></a>Decomprimere nupkg come zip con Expand-Archive (v1.0.1.0)

- Descrizione: La versione di base di Microsoft.PowerShell.Archive (v1.0.1.0) non è possibile decomprimere i file nupkg. Rinominare il file con estensione "ZIP".
- Riferimenti: [Expand-Archive](https://docs.microsoft.com/powershell/module/microsoft.powershell.archive/expand-archive?view=powershell-6)
- Cmd: 

    ```
    $pathToNupkg = "C:\az.applicationmonitor.0.2.1-alpha.nupkg"
    $pathToZip = ([io.path]::ChangeExtension($pathToNupkg, "zip"))
    $pathToNupkg | rename-item -newname $pathToZip
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToZip -DestinationPath $pathInstalledModule
    ```

#### <a name="unzip-nupkg-using-expand-archive-v1100"></a>Decomprimere nupkg usando Expand-Archive (v1.1.0.0).

- Descrizione: Usare una versione corrente di Expand-Archive per decomprimere ai pacchetti NuGet senza l'estensione di ridenominazione. 
- Riferimenti: [Espandere-Archive](https://docs.microsoft.com/powershell/module/microsoft.powershell.archive/expand-archive?view=powershell-6) e [Microsoft.PowerShell.Archive](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive/1.1.0.0)
- Cmd:

    ```
    $pathToNupkg = "C:\az.applicationmonitor.0.2.1-alpha.nupkg"
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToNupkg -DestinationPath $pathInstalledModule
    ```

### <a name="option-2-unzip-and-import-manually"></a>Opzione 2: decomprimere e importare manualmente
Installare il modulo di PowerShell scaricato manualmente in una directory di PowerShell in modo che possa essere individuabile da sessioni di PowerShell.
Per altre informazioni, vedere: [Installazione di un modulo di PowerShell](https://docs.microsoft.com/powershell/developer/module/installing-a-powershell-module)

Se l'installazione in qualsiasi altra directory, è necessario importare manualmente il modulo usando [Import-Module](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/import-module?view=powershell-6)

> [!IMPORTANT] 
> L'installazione installa DLL tramite i percorsi relativi. Store del contenuto di questo pacchetto nella directory del runtime desiderata e confermare che le autorizzazioni di accesso consentono di lettura ma non di scrittura.

1. Modificare l'estensione in "ZIP" ed estrarre il contenuto del pacchetto nella directory di installazione desiderata.
2. Trovare il percorso del file da "Az.ApplicationMonitor.psd1".
3. Eseguire PowerShell come amministratore con i criteri di esecuzione con privilegi elevati. 
4. Caricare il modulo tramite il comando: `Import-Module Az.ApplicationMonitor.psd1`.
    

## <a name="proxy"></a>Proxy

Quando si monitora un computer sulla rete intranet privata, sarà necessario indirizzare il traffico http attraverso un proxy.

I comandi di PowerShell per scaricare e installare il Az.ApplicationMonitor da PowerShell Gallery supportano un `-Proxy` parametro.
Esaminare le istruzioni riportate sopra quando scrittura degli script di installazione.

Application Insights SDK dovrà inviare dati di telemetria dell'applicazione per Microsoft. È consigliabile configurare le impostazioni proxy per l'applicazione nel file Web. config. Vedere [Application Insights, domande frequenti: Pass-through proxy](https://docs.microsoft.com/azure/azure-monitor/app/troubleshoot-faq#proxy-passthrough) per altre informazioni.


## <a name="enable-monitoring"></a>Abilita monitoraggio 

Cmd: `Enable-ApplicationInsightsMonitoring`

Revisione del nostro [riferimento all'API](status-monitor-v2-api-enable-monitoring.md) per una descrizione dettagliata di come usare questo cmdlet. 
