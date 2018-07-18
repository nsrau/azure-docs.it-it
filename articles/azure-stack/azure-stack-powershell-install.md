---
title: Installare PowerShell per Azure Stack | Microsoft Docs
description: Informazioni su come installare PowerShell per Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 07/10/2018
ms.author: mabrigg
ms.reviewer: thoroet
ms.openlocfilehash: 09d5842f349917be0e5d94d919b0e9630347284b
ms.sourcegitcommit: 04fc1781fe897ed1c21765865b73f941287e222f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/13/2018
ms.locfileid: "39035480"
---
# <a name="install-powershell-for-azure-stack"></a>Installare PowerShell per Azure Stack

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

Azure Stack compatibile con i moduli Azure PowerShell sono necessari per lavorare con Azure Stack. In questa Guida, abbiamo consentono di eseguire i passaggi necessari per installare PowerShell per Azure Stack. I passaggi seguenti si applicano agli ambienti connessi a internet. Scorrere fino alla parte inferiore della pagina per ambienti non connessi.

Questo articolo contiene istruzioni dettagliate per installare PowerShell per Azure Stack.

> [!Note]  
> I passaggi seguenti richiedono almeno PowerShell 5.0. Per controllare la versione, eseguire $PSVersionTable.PSVersion e confrontare i **principali** versione. Se PowerShell 5.0 insufficienti, seguire le [collegamento](https://docs.microsoft.com/en-us/powershell/scripting/setup/installing-windows-powershell?view=powershell-6#upgrading-existing-windows-powershell) per eseguire l'aggiornamento a PowerShell 5.0.

I comandi di PowerShell per Azure Stack vengono installati tramite PowerShell Gallery. È possibile utilizzare la procedura seguente per convalidare se PSGallery è registrato come un repository, aprire una sessione di PowerShell con privilegi elevata ed eseguire il comando seguente:

```PowerShell
#requires -Version 5
#requires -RunAsAdministrator
#requires -Module PowerShellGet

Import-Module -Name PowerShellGet -ErrorAction Stop
Import-Module -Name PackageManagement -ErrorAction Stop 

Get-PSRepository -Name "PSGallery"
```

Se il repository non è registrato, aprire una sessione di PowerShell con privilegi elevata ed eseguire il comando seguente:

```PowerShell
Register-PsRepository -Default
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
```
> [!Note]  
> Questo passaggio richiede l'accesso a Internet. 

## <a name="uninstall-existing-versions-of-the-azure-stack-powershell-modules"></a>Disinstallare le versioni esistenti dei moduli di PowerShell per Azure Stack

Prima di installare la versione richiesta, assicurarsi di disinstallare tutti i moduli AzureRM dello Stack di Azure PowerShell installati in precedenza. È possibile disinstallarle usando uno dei due metodi seguenti:

 - Per disinstallare i moduli AzureRM di PowerShell esistenti, chiudere tutte le sessioni di PowerShell attive ed eseguire il comando seguente:

  ```PowerShell
    Uninstall-Module AzureRM.AzureStackAdmin -Force
    Uninstall-Module AzureRM.AzureStackStorage -Force
    Uninstall-Module -Name AzureStack -Force
  ```

 - Eliminare tutte le cartelle che iniziano con "Azure" dal `C:\Program Files\WindowsPowerShell\Modules` e `C:\Users\AzureStackAdmin\Documents\WindowsPowerShell\Modules` cartelle. Eliminazione di queste cartelle rimuove tutti i moduli di PowerShell esistenti.

Le sezioni seguenti descrivono i passaggi necessari per installare PowerShell per Azure Stack. PowerShell può essere installato su Azure Stack che viene gestito connessa, parzialmente connessi o in una situazione di disconnessione.

## <a name="install-the-azure-stack-powershell-modules-in-a-connected-scenario-with-internet-connectivity"></a>Installare i moduli di PowerShell per Azure Stack in uno scenario connesso (con connettività Internet)

Azure Stack compatibile con i moduli AzureRM vengono installati tramite i profili della versione API. Azure Stack richiede i **2017-03-09-profilo** profilo versione API, che è disponibile tramite l'installazione del modulo AzureRM.Bootstrapper. Per scoprire i profili delle versioni API e i cmdlet forniti da essi, vedere la [gestire i profili delle versioni API](user/azure-stack-version-profiles.md). Oltre i moduli AzureRM, è anche necessario installare i moduli di Azure PowerShell specifici dello Stack. Eseguire lo script di PowerShell seguente per installare i moduli nella workstation di sviluppo:

  ```PowerShell  
# Install the AzureRM.Bootstrapper module. Select Yes when prompted to install NuGet 
Install-Module -Name AzureRm.BootStrapper 

# Install and import the API Version Profile required by Azure Stack into the current PowerShell session. 
Use-AzureRmProfile -Profile 2017-03-09-profile -Force 

# Install Module Version 1.3.0 if Azure Stack is running 1804 at a minimum 
Install-Module -Name AzureStack -RequiredVersion 1.3.0 

# Install Module Version 1.2.11 if Azure Stack is running a lower version than 1804 
Install-Module -Name AzureStack -RequiredVersion 1.2.11 
  ```

Per verificare l'installazione, eseguire il comando seguente:

```PowerShell  
Get-Module -ListAvailable | where-Object {$_.Name -like "Azs*"}
```

Se l'installazione ha esito positivo, i moduli AzureRM e AzureStack vengono visualizzati nell'output.

## <a name="install-the-azure-stack-powershell-modules-in-a-disconnected-or-a-partially-connected-scenario-with-limited-internet-connectivity"></a>Installare i moduli di PowerShell per Azure Stack in un disconnesso o uno scenario di connesso parziale (con connettività Internet limitata)

In uno scenario disconnesso, è necessario innanzitutto scaricare i moduli di PowerShell in un computer con connettività Internet e quindi di trasferirle a Azure Stack Development Kit per l'installazione.

> [!IMPORTANT]  
> La versione del modulo Azure PowerShell 1.3.0 Stack include un elenco di modifiche di rilievo. Eseguire l'aggiornamento dal 1.2.11 versione, vedere la [Guida alla migrazione](https://aka.ms/azspowershellmigration).

1. Accedere a un computer in cui si dispone di connettività internet e usare lo script seguente per scaricare il AzureRM ed AzureStack pacchetti nel computer locale:

   ```PowerShell 
  #requires -Version 5
  #requires -RunAsAdministrator
  #requires -Module PowerShellGet
  #requires -Module PackageManagement
  
  Import-Module -Name PowerShellGet -ErrorAction Stop
  Import-Module -Name PackageManagement -ErrorAction Stop

   $Path = "<Path that is used to save the packages>"

   Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 `
     -Name AzureRM -Path $Path -Force -RequiredVersion 1.2.11

   Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 `
     -Name AzureStack -Path $Path -Force -RequiredVersion 1.3.0 
   ```

  > [!Important]  
  > Se non si esegue Azure Stack con aggiornamento 1804 o versione successiva, modificare il **requiredversion** valore del parametro da `1.2.11`. 

2. Copiare i pacchetti scaricati su un dispositivo USB.

3. Accedere alla workstation e copiare i pacchetti dal dispositivo USB in un percorso nella workstation.

4. A questo punto è necessario registrare questo percorso come il repository predefinito e installare i moduli AzureRM e AzureStack da questo repository:

   ```PowerShell
   #requires -Version 5
   #requires -RunAsAdministrator
   #requires -Module PowerShellGet
   #requires -Module PackageManagement

   $SourceLocation = "<Location on the development kit that contains the PowerShell packages>"
   $RepoName = "MyNuGetSource"

   Register-PSRepository -Name $RepoName -SourceLocation $SourceLocation  -InstallationPolicy Trusted

   Install-Module AzureRM -Repository $RepoName

   Install-Module AzureStack -Repository $RepoName 
   ```

## <a name="configure-powershell-to-use-a-proxy-server"></a>Configurare PowerShell per usare un server proxy

Negli scenari che richiedono un server proxy per accedere a internet, è necessario configurare innanzitutto il comando di PowerShell per usare un server proxy esistente.

1. Aprire un prompt di PowerShell con privilegi elevato.
2. Eseguire i comandi seguenti:

````PowerShell  
  #To use Windows credentials for proxy authentication
  [System.Net.WebRequest]::DefaultWebProxy.Credentials = [System.Net.CredentialCache]::DefaultCredentials

  #Alternatively, to prompt for separate credentials that can be used for #proxy authentication

  [System.Net.WebRequest]::DefaultWebProxy.Credentials = Get-Credential
````

## <a name="next-steps"></a>Passaggi successivi

 - [Scaricare strumenti di Azure Stack da GitHub](azure-stack-powershell-download.md)
 - [Configurare l'ambiente di PowerShell dell'utente Azure Stack](user/azure-stack-powershell-configure-user.md)  
 - [Configurare l'ambiente PowerShell dell'operatore Azure Stack](azure-stack-powershell-configure-admin.md) 
 - [Gestire i profili di versione API in Azure Stack](user/azure-stack-version-profiles.md)  
