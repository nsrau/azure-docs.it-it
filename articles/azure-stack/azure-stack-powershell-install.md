---
title: Installare PowerShell per Azure Stack | Documenti Microsoft
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
ms.date: 5/18/2018
ms.author: mabrigg
ms.reviewer: thoroet
ms.openlocfilehash: b3c09582f5135655640768bcbcbef91750827bfa
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2018
ms.locfileid: "34358891"
---
# <a name="install-powershell-for-azure-stack"></a>Installare PowerShell per Azure Stack

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

Moduli di Azure PowerShell compatibili Stack Azure necessarie per lavorare con lo Stack di Azure. In questa guida sono illustrati i passaggi necessari per installare PowerShell per Azure Stack.

In questo articolo contiene istruzioni dettagliate per installare PowerShell per Azure Stack.

> [!Note]
> Le operazioni seguenti richiedono PowerShell 5.0. Per controllare la versione in uso, eseguire $PSVersionTable.PSVersion e confrontare il **principali** versione.

I comandi di PowerShell per lo Stack di Azure vengono installati tramite PowerShell Gallery. È possibile utilizzare la procedura seguente per verificare se PSGallery viene registrato come un repository, aprire una sessione di PowerShell con privilegi elevata ed eseguire il comando seguente:

```PowerShell  
Get-PSRepository -Name "PSGallery"
```

Se il repository non è registrato, aprire una sessione di PowerShell con privilegi elevata ed eseguire il comando seguente:

```PowerShell  
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
```
> [!Note]  
> Questo passaggio richiede l'accesso a Internet. 

## <a name="uninstall-existing-versions-of-powershell"></a>Disinstallare le versioni esistenti di PowerShell

Prima di installare la versione richiesta, assicurarsi di disinstallare tutti i moduli PowerShell Stack Azure installati in precedenza. È possibile disinstallarle utilizzando uno dei due metodi seguenti:

 - Per disinstallare i moduli di PowerShell esistenti, chiudere tutte le sessioni attive di PowerShell ed eseguire il comando seguente:

  ```PowerShell
    Uninstall-Module AzureRM.AzureStackAdmin -Force
    Uninstall-Module AzureRM.AzureStackStorage -Force
    Uninstall-Module -Name AzureStack -Force
  ```

 - Eliminare tutte le cartelle che iniziano con "Azure" dal `C:\Program Files\WindowsPowerShell\Modules` e `C:\Users\AzureStackAdmin\Documents\WindowsPowerShell\Modules` cartelle. Eliminazione di queste cartelle rimuove tutti i moduli PowerShell esistenti.

Nelle sezioni seguenti vengono descritti i passaggi necessari per installare PowerShell per Azure Stack. PowerShell può essere installato nello Stack di Azure che viene gestita in connessa, parzialmente connesso o in uno scenario disconnesso.

## <a name="install-powershell-in-a-connected-scenario-with-internet-connectivity"></a>Installare PowerShell in uno scenario connesso (con connettività Internet)

Moduli di Azure Resource Manager compatibili Stack Azure vengono installati tramite i profili della versione API. Stack di Azure richiede il **2017-03-09-profilo** profilo di versione API, che è disponibile per l'installazione del modulo AzureRM.Bootstrapper. Per ulteriori informazioni sui profili di versione API e i cmdlet forniti dall'utente, consultare il [gestire i profili di versione API](user/azure-stack-version-profiles.md). Oltre ai moduli di Azure Resource Manager, è inoltre necessario installare i moduli di Azure PowerShell specifici dello Stack. Eseguire lo script di PowerShell seguente per installare i moduli nella workstation di sviluppo:

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

Se l'installazione ha esito positivo, i moduli di Azure Resource Manager e AzureStack vengono visualizzati nell'output.

## <a name="install-powershell-in-a-disconnected-or-a-partially-connected-scenario-with-limited-internet-connectivity"></a>Installare PowerShell in un disconnessa o uno scenario di connesso parziale (con connettività Internet limitata)

In uno scenario disconnesso, è necessario innanzitutto scaricare i moduli di PowerShell in un computer con connettività Internet e quindi li trasferiscono al Kit di sviluppo dello Stack Azure per l'installazione.

> [!IMPORTANT]  
> La versione del modulo Azure PowerShell Stack 1.3.0 viene fornito con un elenco delle modifiche di rilievo. Eseguire l'aggiornamento dal 1.2.11 versione, vedere la [Guida alla migrazione](https://aka.ms/azspowershellmigration).

1. Accedere a un computer in cui si dispone di connettività internet e usare lo script seguente per il download di Azure Resource Manager e AzureStack pacchetti nel computer locale:

   ```PowerShell  
   $Path = "<Path that is used to save the packages>"

   Save-Package `
     -ProviderName NuGet `
     -Source https://www.powershellgallery.com/api/v2 `
     -Name AzureRM `
     -Path $Path `
     -Force `
     -RequiredVersion 1.2.11

   Save-Package `
     -ProviderName NuGet `
     -Source https://www.powershellgallery.com/api/v2 `
     -Name AzureStack `
     -Path $Path `
     -Force `
     -RequiredVersion 1.3.0 
   ```

  > [!Important]  
  > Se non si usa Azure Stack con aggiornamento 1804 o versione successiva, modificare il **requiredversion** valore del parametro da `1.2.11`. 

2. Copiare i pacchetti scaricati in un dispositivo USB.

3. Accedere alla workstation e copiare i pacchetti dal dispositivo USB in un percorso nella workstation.

4. È ora necessario registrare questo percorso come il repository predefinito e installare i moduli di Azure Resource Manager e AzureStack da questo repository:

   ```PowerShell
   $SourceLocation = "<Location on the development kit that contains the PowerShell packages>"
   $RepoName = "MyNuGetSource"

   Register-PSRepository `
     -Name $RepoName `
     -SourceLocation $SourceLocation `
     -InstallationPolicy Trusted

   Install-Module AzureRM `
     -Repository $RepoName

   Install-Module AzureStack `
     -Repository $RepoName 
   ```

## <a name="configure-powershell-to-use-a-proxy-server"></a>Configurazione di PowerShell per l'utilizzo di un server proxy

Negli scenari che richiedono un server proxy per accedere a internet, è innanzitutto necessario configurare PowerShell per l'utilizzo di un server proxy esistente.

1. Aprire un prompt di PowerShell con privilegi elevati.
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
 - [Configurare l'ambiente di PowerShell dell'operatore Azure Stack](azure-stack-powershell-configure-admin.md) 
 - [Gestire i profili di versione API in Azure Stack](user/azure-stack-version-profiles.md)  
