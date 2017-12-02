---
title: Installare PowerShell per Azure Stack | Documenti Microsoft
description: Informazioni su come installare PowerShell per Azure Stack.
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2017
ms.author: sngun
ms.openlocfilehash: abbc1eac3ff1e8de90bd420dc1fcce7dbeda234c
ms.sourcegitcommit: be0d1aaed5c0bbd9224e2011165c5515bfa8306c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2017
---
# <a name="install-powershell-for-azure-stack"></a>Installare PowerShell per Azure Stack  

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

Moduli di Azure PowerShell compatibili Stack Azure necessarie per lavorare con lo Stack di Azure. In questa guida sono illustrati i passaggi necessari per installare PowerShell per Azure Stack. È possibile utilizzare i passaggi descritti in questo articolo del Kit di sviluppo dello Stack di Azure o da un client esterno basato su Windows, se si è connessi tramite VPN.

In questo articolo contiene istruzioni dettagliate per installare PowerShell per Azure Stack. Tuttavia, se si desidera installare e configurare PowerShell rapidamente, è possibile utilizzare lo script fornito nel [diventare operativi con PowerShell](azure-stack-powershell-configure-quickstart.md) argomento. 

> [!NOTE]
> Le operazioni seguenti richiedono PowerShell 5.0. Per controllare la versione in uso, eseguire $PSVersionTable.PSVersion e confrontare la versione "Principale".

I comandi di PowerShell per lo Stack di Azure vengono installati tramite PowerShell gallery. Per il repository PSGallery regiser, aprire una sessione di PowerShell con privilegi elevata del kit di sviluppo o da un client esterno basato su Windows se si sono connessi tramite VPN ed eseguire il comando seguente:

```powershell
Set-PSRepository `
  -Name "PSGallery" `
  -InstallationPolicy Trusted
```

## <a name="uninstall-existing-versions-of-powershell"></a>Disinstallare le versioni esistenti di PowerShell

Prima di installare la versione richiesta, assicurarsi di disinstallare i moduli di PowerShell di Azure esistenti. È possibile disinstallarle utilizzando uno dei due metodi seguenti:

* Per disinstallare i moduli di PowerShell esistenti, accedere al kit di sviluppo o per client basati su Windows esterno se si prevede di stabilire una connessione VPN. Chiudere tutte le sessioni attive di PowerShell ed eseguire il comando seguente: 

   ```powershell
   Get-Module -ListAvailable | where-Object {$_.Name -like “Azure*”} | Uninstall-Module
   ```

* Accedere al kit di sviluppo o per client basati su Windows esterno se si prevede di stabilire una connessione VPN. Eliminare tutte le cartelle che iniziano con "Azure" dal `C:\Program Files (x86)\WindowsPowerShell\Modules` e `C:\Users\AzureStackAdmin\Documents\WindowsPowerShell\Modules` cartelle. Eliminazione di queste cartelle rimuove tutti i moduli PowerShell esistenti dal ambiti utente "globali" e "AzureStackAdmin". 

Nelle sezioni seguenti vengono descritti i passaggi necessari per installare PowerShell per Azure Stack. PowerShell può essere installato nello Stack di Azure che viene gestita in connessa, parzialmente connesso o in uno scenario disconnesso. 

## <a name="install-powershell-in-a-connected-scenario-with-internet-connectivity"></a>Installare PowerShell in uno scenario connesso (con connettività internet)

Moduli di Azure Resource Manager compatibili Stack Azure vengono installati tramite i profili della versione API. Stack di Azure richiede il **2017-03-09-profilo** profilo di versione API, che è disponibile per l'installazione del modulo AzureRM.Bootstrapper. Per ulteriori informazioni sui profili di versione API e i cmdlet forniti dall'utente, consultare il [gestire i profili di versione API](azure-stack-version-profiles.md). Oltre ai moduli di Azure Resource Manager, è inoltre necessario installare i moduli di Azure PowerShell specifici dello Stack. Eseguire lo script di PowerShell seguente per installare i moduli nella workstation di sviluppo:

> [!IMPORTANT]
> La versione del modulo PowerShell di Azure Resource Manager 1.2.11 viene fornito con un elenco di modifiche di rilievo. Eseguire l'aggiornamento dal 1.2.10 versione, vedere la Guida alla migrazione al [https://aka.ms/azspowershellmigration](https://aka.ms/azspowershellmigration).

  ```powershell
  # Install the AzureRM.Bootstrapper module. Select Yes when prompted to install NuGet 
  Install-Module `
    -Name AzureRm.BootStrapper

  # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
  Use-AzureRmProfile `
    -Profile 2017-03-09-profile -Force

  Install-Module `
    -Name AzureStack `
    -RequiredVersion 1.2.11
  ```

Per verificare l'installazione, eseguire il comando seguente:

  ```powershell
  Get-Module `
    -ListAvailable | where-Object {$_.Name -like “Azure*”}
  ```
  Se l'installazione ha esito positivo, i moduli di Azure Resource Manager e AzureStack vengono visualizzati nell'output.

## <a name="install-powershell-in-a-disconnected-or-a-partially-connected-scenario-with-limited-internet-connectivity"></a>Installare PowerShell in un disconnesso o per uno scenario di connesso parziale (con connettività internet limitata)

In uno scenario disconnesso, è necessario innanzitutto scaricare i moduli di PowerShell in un computer con connettività internet e quindi li trasferiscono al Kit di sviluppo dello Stack di Azure per l'installazione.

> [!IMPORTANT]
> La versione del modulo PowerShell di Azure Resource Manager 1.2.11 viene fornito con un elenco di modifiche di rilievo. Eseguire l'aggiornamento dal 1.2.10 versione, vedere la Guida alla migrazione al [https://aka.ms/azspowershellmigration](https://aka.ms/azspowershellmigration).

1. Accedere a un computer in cui si dispone di connettività internet e usare lo script seguente per il download di Azure Resource Manager e AzureStack pacchetti nel computer locale:

   ```powershell
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
     -RequiredVersion 1.2.11 
   ```

2. Copiare i pacchetti scaricati in un dispositivo USB.

3. Accedere al kit di sviluppo e copiare i pacchetti dal dispositivo USB in un percorso nel kit di sviluppo. 

4. È ora necessario registrare questo percorso come il repository predefinito e installare i moduli di Azure Resource Manager e AzureStack da questo repository:

   ```powershell
   $SourceLocation = "<Location on the development kit that contains the PowerShell packages>"
   $RepoName = "MyNuGetSource"

   Register-PSRepository `
     -Name $RepoName `
     -SourceLocation $SourceLocation `
     -InstallationPolicy Trusted

   ```powershell
   Install-Module AzureRM `
     -Repository $RepoName

   Install-Module AzureStack `
     -Repository $RepoName 
   ```

## <a name="next-steps"></a>Passaggi successivi

* [Scaricare strumenti di Azure Stack da GitHub](azure-stack-powershell-download.md)
* [Configurare l'ambiente di PowerShell dell'utente Azure Stack](user/azure-stack-powershell-configure-user.md)  
* [Configurare l'ambiente di PowerShell dell'operatore Azure Stack](azure-stack-powershell-configure-admin.md) 
* [Gestire i profili di versione API in Azure Stack](azure-stack-version-profiles.md)  
