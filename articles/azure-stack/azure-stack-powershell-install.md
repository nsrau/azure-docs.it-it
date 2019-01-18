---
title: Installare PowerShell per Azure Stack | Microsoft Docs
description: Informazioni su come installare PowerShell per Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 01/17/2018
ms.author: sethm
ms.reviewer: thoroet
ms.openlocfilehash: 9c99de88ee1e3054a04512c72b9f9f41886663da
ms.sourcegitcommit: 9f07ad84b0ff397746c63a085b757394928f6fc0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/17/2019
ms.locfileid: "54391068"
---
# <a name="install-powershell-for-azure-stack"></a>Installare PowerShell per Azure Stack

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

Per usare il cloud, è necessario installare i moduli di PowerShell compatibile con Azure Stack. Compatibilità viene abilitata tramite una funzionalità denominata *profili API*.

I profili delle API forniscono un modo per gestire le differenze di versione tra Azure e Azure Stack. Un profilo di versione API è un set di moduli di PowerShell per Azure Resource Manager con le versioni API specifiche. Ogni piattaforma di cloud ha un set di profili di versione API supportati. Ad esempio, Azure Stack supporta una versione del profilo con data di validità specifico, ad esempio **2018-03-01-hybrid**. Quando si installa un profilo, vengono installati i moduli di PowerShell per Azure Resource Manager che corrispondono al profilo specificato.

È possibile installare Azure Stack i moduli di PowerShell compatibili in Internet connesso, parzialmente connessi o scenari disconnessi. Questo articolo illustra le istruzioni dettagliate per installare PowerShell per Azure Stack per questi scenari.

## <a name="1-verify-your-prerequisites"></a>1. Verificare i prerequisiti

Prima iniziare a usare Azure Stack e PowerShell, sono necessari i prerequisiti seguenti:

- **PowerShell versione 5.0** per controllare la versione in uso, eseguire **$PSVersionTable.PSVersion** e confrontare le **principali** versione. Se PowerShell 5.0 insufficienti, seguire le [installazione di Windows PowerShell](https://docs.microsoft.com/powershell/scripting/setup/installing-windows-powershell?view=powershell-6#upgrading-existing-windows-powershell).

  > [!Note]
  > PowerShell 5.0 richiede un computer Windows.

- **Eseguire Powershell in un prompt dei comandi con privilegi elevati**.
  È necessario eseguire PowerShell con privilegi amministrativi.

- **L'accesso a PowerShell Gallery** è necessario l'accesso per il [PowerShell Gallery](https://www.powershellgallery.com). La raccolta è il repository centrale per i contenuti PowerShell. Il **PowerShellGet** modulo contiene i cmdlet per l'individuazione, installazione, aggiornamento e la pubblicazione di elementi PowerShell quali moduli, risorse DSC, capacità del ruolo e script di PowerShell Gallery e altri private repository. Se si usa PowerShell in uno scenario disconnesso, è necessario recuperare risorse da un computer con una connessione a Internet e archiviarle in un percorso accessibile nel computer disconnesso.


## <a name="2-validate-the-powershell-gallery-accessibility"></a>2. Convalidare l'accessibilità di PowerShell Gallery

Verificare se PSGallery è registrato come un repository.

> [!Note]
> Questo passaggio richiede l'accesso a Internet.

Aprire un prompt di PowerShell con privilegi elevato ed eseguire i cmdlet seguenti:

```PowerShell
Import-Module -Name PowerShellGet -ErrorAction Stop
Import-Module -Name PackageManagement -ErrorAction Stop
Get-PSRepository -Name "PSGallery"
```

Se il repository non è registrato, aprire una sessione di PowerShell con privilegi elevata ed eseguire il comando seguente:

```PowerShell
Register-PsRepository -Default
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
```

## <a name="3-uninstall-existing-versions-of-the-azure-stack-powershell-modules"></a>3. Disinstallare le versioni esistenti dei moduli di PowerShell per Azure Stack

Prima di installare la versione richiesta, assicurarsi di disinstallare tutti i moduli AzureRM dello Stack di Azure PowerShell installati in precedenza. È possibile disinstallarle usando uno dei due metodi seguenti:

1. Per disinstallare i moduli AzureRM di PowerShell esistenti, chiudere tutte le sessioni di PowerShell attive ed eseguire i cmdlet seguenti:

    ```PowerShell
    Get-Module -Name Azs.* -ListAvailable | Uninstall-Module -Force -Verbose
    Get-Module -Name Azure* -ListAvailable | Uninstall-Module -Force -Verbose
    ```
    Se si riscontra un errore, ad esempio 'il modulo è già in uso',. Chiudere le sessioni di PowerShell che usano i moduli e rieseguire lo script precedente.

2. Eliminare tutte le cartelle che iniziano con `Azure` oppure `Azs.` dalle `C:\Program Files\WindowsPowerShell\Modules` e `C:\Users\{yourusername}\Documents\WindowsPowerShell\Modules` cartelle. Eliminazione di queste cartelle rimuove tutti i moduli di PowerShell esistenti.

## <a name="4-connected-install-powershell-for-azure-stack-with-internet-connectivity"></a>4. Connessione: Installare PowerShell per Azure Stack con connettività Internet

Azure Stack richiede i **2018-03-01-hybrid** profilo versione API per Azure Stack 1808 o versione successiva. Il profilo è disponibile tramite l'installazione di **AzureRM.Bootstrapper** modulo. Inoltre, per i moduli AzureRM, è anche necessario installare i moduli di PowerShell di Azure Stack-specifici. Il profilo di versione API e i moduli di PowerShell per Azure Stack è necessario verranno dipendono dalla versione di Azure Stack si è in esecuzione.

Eseguire lo script di PowerShell seguente per installare i moduli nella workstation di sviluppo:

- Azure Stack 1811 o versione successivo.

    ```PowerShell
    # Install the AzureRM.Bootstrapper module. Select Yes when prompted to install NuGet
    Install-Module -Name AzureRm.BootStrapper

    # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
    Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force

    Install-Module -Name AzureStack -RequiredVersion 1.6.0
    ```

    Per usare le funzionalità di ulteriore spazio di archiviazione (indicate nella sezione connesso), di download e installare anche i pacchetti seguenti.

    ```PowerShell
    # Install the Azure.Storage module version 4.5.0
    Install-Module -Name Azure.Storage -RequiredVersion 4.5.0 -Force -AllowClobber

    # Install the AzureRm.Storage module version 5.0.4
    Install-Module -Name AzureRM.Storage -RequiredVersion 5.0.4 -Force -AllowClobber

    # Remove incompatible storage module installed by AzureRM.Storage
    Uninstall-Module Azure.Storage -RequiredVersion 4.6.1 -Force

    # Load the modules explicitly specifying the versions
    Import-Module -Name Azure.Storage -RequiredVersion 4.5.0
    Import-Module -Name AzureRM.Storage -RequiredVersion 5.0.4
    ```

> [!Note]
> Per eseguire l'aggiornamento di Azure PowerShell dal **2017-03-09-profilo** per **2018-03-01-hybrid**, vedere il [Guida alla migrazione](https://github.com/azure/azure-powershell/blob/AzureRM/documentation/migration-guides/Stack/migration-guide.2.3.0.md).

- Azure Stack 1811 o versione successivo.

    ```PowerShell
    # Install the AzureRM.Bootstrapper module. Select Yes when prompted to install NuGet
    Install-Module -Name AzureRm.BootStrapper

    # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
    Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force

    Install-Module -Name AzureStack -RequiredVersion 1.6.0
    ```

- Azure Stack 1809 o versioni precedenti.

    ```PowerShell
    # Install the AzureRM.Bootstrapper module. Select Yes when prompted to install NuGet
    Install-Module -Name AzureRm.BootStrapper

    # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
    Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force

    Install-Module -Name AzureStack -RequiredVersion 1.5.0
    ```

Confermare l'installazione eseguendo il comando seguente:

```PowerShell
Get-Module -Name "Azure*" -ListAvailable
Get-Module -Name "Azs*" -ListAvailable
```

Se l'installazione ha esito positivo, i moduli AzureRM e AzureStack vengono visualizzati nell'output.

## <a name="5-disconnected-install-powershell-without-an-internet-connection"></a>5. Disconnesso: Installare PowerShell senza una connessione Internet

In uno scenario disconnesso, è necessario innanzitutto scaricare i moduli di PowerShell in un computer con connettività Internet e quindi di trasferirle a Azure Stack Development Kit per l'installazione.

Accedere a un computer con connettività Internet e usare gli script seguenti per scaricare i pacchetti di Azure Resource Manager e AzureStack, a seconda della versione di Azure Stack:

  - Azure Stack 1811 o versione successivo.

    ```PowerShell
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop

    $Path = "<Path that is used to save the packages>"
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.3.0
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.6.0
    ```

    Per usare le funzionalità di ulteriore spazio di archiviazione (indicate nella sezione connesso), di download e installare anche i pacchetti seguenti.

    ```PowerShell
    $Path = "<Path that is used to save the packages>"
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name Azure.Storage -Path $Path -Force -RequiredVersion 4.5.0
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRm.Storage -Path $Path -Force -RequiredVersion 5.0.4
    ```

  - Azure Stack 1811 o versione successivo.

    ```PowerShell
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop

    $Path = "<Path that is used to save the packages>"
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.3.0
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.6.0
    ```

  - Azure Stack 1809 o versioni precedenti.

    ```PowerShell
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop

    $Path = "<Path that is used to save the packages>"
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.3.0
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.5.0
    ```

2. Copiare i pacchetti scaricati in un dispositivo USB.

3. Accedere alla workstation e copiare i pacchetti dal dispositivo USB in un percorso nella workstation.

4. A questo punto registrare questo percorso come il repository predefinito e installare i moduli AzureRM e AzureStack da questo repository:

   ```PowerShell
   #requires -Version 5
   #requires -RunAsAdministrator
   #requires -Module PowerShellGet
   #requires -Module PackageManagement

   $SourceLocation = "<Location on the development kit that contains the PowerShell packages>"
   $RepoName = "MyNuGetSource"

   Register-PSRepository -Name $RepoName -SourceLocation $SourceLocation  -InstallationPolicy Trusted

   Install-Module -Name AzureRM -Repository $RepoName

   Install-Module -Name AzureStack -Repository $RepoName
   ```

## <a name="6-configure-powershell-to-use-a-proxy-server"></a>6. Configurare PowerShell per usare un server proxy

Negli scenari che richiedono un server proxy per accedere a Internet, è innanzitutto necessario configurare PowerShell per l'uso di un server proxy esistente:

1. Aprire un prompt di PowerShell con privilegi elevato.
2. Eseguire i comandi seguenti:

   ```PowerShell
   #To use Windows credentials for proxy authentication
   [System.Net.WebRequest]::DefaultWebProxy.Credentials = [System.Net.CredentialCache]::DefaultCredentials

   #Alternatively, to prompt for separate credentials that can be used for #proxy authentication
   [System.Net.WebRequest]::DefaultWebProxy.Credentials = Get-Credential
   ```

## <a name="next-steps"></a>Passaggi successivi

 - [Scaricare strumenti di Azure Stack da GitHub](azure-stack-powershell-download.md)
 - [Configurare l'ambiente di PowerShell dell'utente Azure Stack](user/azure-stack-powershell-configure-user.md)
 - [Configurare l'ambiente PowerShell dell'operatore Azure Stack](azure-stack-powershell-configure-admin.md)
 - [Gestire i profili di versione API in Azure Stack](user/azure-stack-version-profiles.md)