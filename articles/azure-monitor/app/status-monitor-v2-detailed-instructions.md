---
title: Istruzioni dettagliate di Azure Application Insights Agent Documenti Microsoft
description: Istruzioni dettagliate per iniziare a usare Application Insights Agent. Monitorare le prestazioni del sito Web senza ridistribuire il sito Web. Funziona con ASP.NET app Web ospitate in locale, nelle macchine virtuali o in Azure.Works with a web apps hosted on-premises, in VMs, or on Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: cd5ca5039b537859d5b31c901ed1f93877ecb629
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275724"
---
# <a name="application-insights-agent-formerly-named-status-monitor-v2-detailed-instructions"></a>Application Insights Agent (precedentemente denominato Status Monitor v2): istruzioni dettagliate

In questo articolo viene descritto come eseguire l'onboarding in PowerShell Gallery e scaricare il modulo ApplicationMonitor.
Sono inclusi i parametri più comuni di cui avrai bisogno per iniziare.
Abbiamo anche fornito istruzioni di download manuale nel caso in cui non si dispone di accesso a Internet.

## <a name="get-an-instrumentation-key"></a>Ottenere una chiave di strumentazioneGet an instrumentation key

Per iniziare, è necessaria una chiave di strumentazione. Per altre informazioni, vedere [Creare una risorsa di Application Insights.](create-new-resource.md#copy-the-instrumentation-key)

## <a name="run-powershell-as-admin-with-an-elevated-execution-policy"></a>Eseguire PowerShell come amministratore con criteri di esecuzione con privilegi elevatiRun PowerShell as Admin with an elevated execution policy

### <a name="run-as-admin"></a>Esegui come amministratore

PowerShell necessita di autorizzazioni a livello di amministratore per apportare modifiche al computer.
### <a name="execution-policy"></a>Criteri di esecuzione
- Descrizione: per impostazione predefinita, l'esecuzione di script di PowerShell è disabilitata. È consigliabile consentire script RemoteSigned solo per l'ambito Current.
- Riferimento: [Informazioni sui criteri di esecuzione](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-6) e Su [Set-ExecutionPolicy](
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

Queste istruzioni sono state scritte e testate su un computer che esegue Windows 10 e le versioni sopra elencate.

## <a name="prerequisites-for-powershell-gallery"></a>Prerequisiti per PowerShell GalleryPrerequisites for PowerShell Gallery

Questi passaggi prepareranno il server per scaricare i moduli da PowerShell Gallery.These steps will prepare your server to download modules from PowerShell Gallery.

> [!NOTE] 
> PowerShell Gallery è supportato in Windows 10, Windows Server 2016 e PowerShell 6.
> Per informazioni sulle versioni precedenti, vedere [Installazione di PowerShellGet](/powershell/scripting/gallery/installing-psget).


1. Eseguire PowerShell come amministratore con criteri di esecuzione con privilegi elevati.
2. Installare il provider del pacchetto NuGet.
    - Descrizione: questo provider deve interagire con archivi basati su NuGet come PowerShell Gallery.
    - Riferimento: [Install-PackageProvider](https://docs.microsoft.com/powershell/module/packagemanagement/install-packageprovider?view=powershell-6).
    - Comando: `Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201`.
    - Parametri facoltativi:
        - `-Proxy`. Specifica un server proxy per la richiesta.
        - `-Force`. Ignora la richiesta di conferma.
    
    Riceverai questo messaggio se NuGet non è configurato:
        
        NuGet provider is required to continue
        PowerShellGet requires NuGet provider version '2.8.5.201' or newer to interact with NuGet-based repositories. The NuGet
         provider must be available in 'C:\Program Files\PackageManagement\ProviderAssemblies' or
        'C:\Users\t\AppData\Local\PackageManagement\ProviderAssemblies'. You can also install the NuGet provider by running
        'Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force'. Do you want PowerShellGet to install and import
         the NuGet provider now?
        [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
    
3. Configurare PowerShell Gallery come repository attendibile.
    - Descrizione: per impostazione predefinita, PowerShell Gallery è un repository non attendibile.
    - Riferimento: [Set-PSRepository](https://docs.microsoft.com/powershell/module/powershellget/set-psrepository?view=powershell-6).
    - Comando: `Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted`.
    - Parametro facoltativo:
        - `-Proxy`. Specifica un server proxy per la richiesta.

    Questo messaggio viene visualizzato se PowerShell Gallery non è attendibile:You'll receive this prompt if PowerShell Gallery isn't trusted:

        Untrusted repository
        You are installing the modules from an untrusted repository. If you trust this repository, change its
        InstallationPolicy value by running the Set-PSRepository cmdlet. Are you sure you want to install the modules from
        'PSGallery'?
        [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "N"):

    È possibile confermare questa modifica e controllare tutti `Get-PSRepository` i PSRepositories eseguendo il comando.

4. Installare la versione più recente di PowerShellGet.Install the newest version of PowerShellGet.
    - Descrizione: questo modulo contiene gli strumenti usati per ottenere altri moduli da PowerShell Gallery. La versione 1.0.0.1 viene fornita con Windows 10 e Windows Server. È richiesta la versione 1.6.0 o successiva. Per determinare la versione installata, eseguire il `Get-Command -Module PowerShellGet` comando.
    - Riferimento: [Installazione di PowerShellGet](/powershell/scripting/gallery/installing-psget).
    - Comando: `Install-Module -Name PowerShellGet`.
    - Parametri facoltativi:
        - `-Proxy`. Specifica un server proxy per la richiesta.
        - `-Force`. Ignora l'avviso "già installato" e installa la versione più recente.

    Questo errore viene visualizzato se non si utilizza la versione più recente di PowerShellGet:You'll receive this error if you're not using the newest version of PowerShellGet:
    
        Install-Module : A parameter cannot be found that matches parameter name 'AllowPrerelease'.
        At line:1 char:20
        Install-Module abc -AllowPrerelease
                           ~~~~~~~~~~~~~~~~
            CategoryInfo          : InvalidArgument: (:) [Install-Module], ParameterBindingException
            FullyQualifiedErrorId : NamedParameterNotFound,Install-Module
    
5. Riavviare PowerShell.Restart PowerShell. Non è possibile caricare la nuova versione nella sessione corrente. Le nuove sessioni di PowerShell caricheranno la versione più recente di PowerShellGet.New PowerShell sessions will load the latest version of PowerShellGet.

## <a name="download-and-install-the-module-via-powershell-gallery"></a>Scaricare e installare il modulo tramite PowerShell Gallery

Questi passaggi scaricheranno il modulo Az.ApplicationMonitor da PowerShell Gallery.

1. Verificare che tutti i prerequisiti per PowerShell Gallery siano soddisfatti.
2. Eseguire PowerShell come amministratore con criteri di esecuzione con privilegi elevati.
3. Installare il modulo Az.ApplicationMonitor.
    - Riferimento: [Install-Module](https://docs.microsoft.com/powershell/module/powershellget/install-module?view=powershell-6).
    - Comando: `Install-Module -Name Az.ApplicationMonitor`.
    - Parametri facoltativi:
        - `-Proxy`. Specifica un server proxy per la richiesta.
        - `-AllowPrerelease`. Consente l'installazione di versioni alfa e beta.
        - `-AcceptLicense`. Ignora il prompt "Accetta licenza"
        - `-Force`. Ignora l'avviso "Repository non attendibile".

## <a name="download-and-install-the-module-manually-offline-option"></a>Scaricare e installare il modulo manualmente (opzione offline)

Se per qualsiasi motivo non è possibile connettersi al modulo di PowerShell, è possibile scaricare e installare manualmente il modulo Az.ApplicationMonitor.

### <a name="manually-download-the-latest-nupkg-file"></a>Scarica manualmente l'ultimo file nupkg

1. Passare a https://www.powershellgallery.com/packages/Az.ApplicationMonitor.
2. Selezionare la versione più recente del file nella tabella **Cronologia versioni.**
3. In **Opzioni di installazione**selezionare Download **manuale**.

### <a name="option-1-install-into-a-powershell-modules-directory"></a>Opzione 1: installare in una directory dei moduli di PowerShellOption 1: Install into a PowerShell modules directory
Installare il modulo PowerShell scaricato manualmente in una directory di PowerShell in modo che sia individuabile dalle sessioni di PowerShell.Install the manually downloaded PowerShell module into a PowerShell directory so it will be discoverable by PowerShell sessions.
Per ulteriori informazioni, vedere Installazione di un modulo di [PowerShell](/powershell/scripting/developer/module/installing-a-powershell-module).


#### <a name="unzip-nupkg-as-a-zip-file-by-using-expand-archive-v1010"></a>Decomprimere nupkg come file zip utilizzando Expand-Archive (v1.0.1.0)

- Descrizione: la versione di base di Microsoft.PowerShell.Archive (v1.0.1.0) non è in grado di decomprimere i file nupkg. Rinominare il file con estensione zip.
- Riferimento: [Expand-Archive](https://docs.microsoft.com/powershell/module/microsoft.powershell.archive/expand-archive?view=powershell-6).
- Comando:

    ```
    $pathToNupkg = "C:\az.applicationmonitor.0.3.0-alpha.nupkg"
    $pathToZip = ([io.path]::ChangeExtension($pathToNupkg, "zip"))
    $pathToNupkg | rename-item -newname $pathToZip
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToZip -DestinationPath $pathInstalledModule
    ```

#### <a name="unzip-nupkg-by-using-expand-archive-v1100"></a>Decomprimere i nupkg utilizzando Expand-Archive (v1.1.0.0)

- Descrizione: utilizzare una versione corrente di Expand-Archive per decomprimere i file nupkg senza modificare l'estensione.
- Riferimento: [Expand-Archive](https://docs.microsoft.com/powershell/module/microsoft.powershell.archive/expand-archive?view=powershell-6) e [Microsoft.PowerShell.Archive](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive/1.1.0.0).
- Comando:

    ```
    $pathToNupkg = "C:\az.applicationmonitor.0.2.1-alpha.nupkg"
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToNupkg -DestinationPath $pathInstalledModule
    ```

### <a name="option-2-unzip-and-import-nupkg-manually"></a>Opzione 2: decomprimere e importare i nupkg manualmente
Installare il modulo PowerShell scaricato manualmente in una directory di PowerShell in modo che sia individuabile dalle sessioni di PowerShell.Install the manually downloaded PowerShell module into a PowerShell directory so it will be discoverable by PowerShell sessions.
Per ulteriori informazioni, vedere Installazione di un modulo di [PowerShell](/powershell/scripting/developer/module/installing-a-powershell-module).

Se si installa il modulo in qualsiasi altra directory, importarlo manualmente utilizzando [Import-Module](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/import-module?view=powershell-6).

> [!IMPORTANT] 
> Le DLL verranno installate tramite percorsi relativi.
> Archiviare il contenuto del pacchetto nella directory di runtime desiderata e verificare che le autorizzazioni di accesso consentano la lettura ma non la scrittura.

1. Modificare l'estensione in ".zip" ed estrarre il contenuto del pacchetto nella directory di installazione desiderata.
2. Trovare il percorso del file Az.ApplicationMonitor.psd1.
3. Eseguire PowerShell come amministratore con criteri di esecuzione con privilegi elevati.
4. Caricare il modulo `Import-Module Az.ApplicationMonitor.psd1` utilizzando il comando .
    

## <a name="route-traffic-through-a-proxy"></a>Instradare il traffico attraverso un proxy

Quando si monitora un computer nella rete Intranet privata, è necessario instradare il traffico HTTP attraverso un proxy.

I comandi di PowerShell per scaricare e installare Az.ApplicationMonitor da PowerShell Gallery supportano un `-Proxy` parametro.
Esaminare le istruzioni precedenti quando si scrivono gli script di installazione.

Application Insights SDK dovrà inviare i dati di telemetria dell'app a Microsoft. È consigliabile configurare le impostazioni proxy per l'app nel file web.config. Per altre informazioni, vedere [Domande frequenti su Application Insights: passthrough proxy](https://docs.microsoft.com/azure/azure-monitor/app/troubleshoot-faq#proxy-passthrough).


## <a name="enable-monitoring"></a>Abilitare il monitoraggio

Utilizzare `Enable-ApplicationInsightsMonitoring` il comando per abilitare il monitoraggio.

Vedere le informazioni di [riferimento sull'API](status-monitor-v2-api-enable-monitoring.md) per una descrizione dettagliata di come utilizzare questo cmdlet.



## <a name="next-steps"></a>Passaggi successivi

 Visualizzare i dati di telemetria:

- [Esplora le metriche](../../azure-monitor/app/metrics-explorer.md) per monitorare le prestazioni e l'utilizzo.
- [Cerca eventi e log](../../azure-monitor/app/diagnostic-search.md) per diagnosticare i problemi.
- [Usa Analytics](../../azure-monitor/app/analytics.md) per query più avanzate.
- [Creare dashboard](../../azure-monitor/app/overview-dashboard.md).

 Aggiungere altri dati di telemetria:

- [Creare test Web](monitor-web-app-availability.md) per assicurarsi che il sito rimanga attivo.
- [Aggiungere dati di telemetria del client Web](../../azure-monitor/app/javascript.md) per visualizzare le eccezioni dal codice della pagina Web e abilitare le chiamate di traccia.
- [Aggiungere Application Insights SDK al codice](../../azure-monitor/app/asp-net.md) in modo da poter inserire chiamate di traccia e log.

Fai di più con Application Insights Agent:

- Usare la guida per [risolvere i problemi relativi](status-monitor-v2-troubleshoot.md) all'agente application Insights.Use our guide to troubleshoot Application Insights Agent.
