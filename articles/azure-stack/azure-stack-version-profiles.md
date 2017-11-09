---
title: Uso di profili di versione API in Azure Stack | Documenti Microsoft
description: Informazioni sui profili di versione API nello Stack di Azure.
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
ms.date: 09/25/2017
ms.author: sngun
ms.openlocfilehash: b9a010409dc7f49333ab188b89280f3ad54816c1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="manage-api-version-profiles-in-azure-stack"></a>Gestire i profili di versione API in Azure Stack

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

I profili della versione API consentono di gestire le differenze di versione tra Azure e Azure Stack. Un profilo di versione API è un set di moduli AzureRM PowerShell con le versioni API specifiche. Ogni piattaforma cloud dispone di un set di profili di versione API supportati. Ad esempio, Stack di Azure supporta una versione specifica del profilo con data di validità, ad esempio **2017-03-09-profilo**, e Azure supporta il **più recente** profilo versione API. Quando si installa un profilo, vengono installati i moduli AzureRM PowerShell che corrispondono al profilo specificato.

## <a name="install-the-powershell-module-required-to-use-api-version-profiles"></a>Installare il modulo di PowerShell, è necessario utilizzare i profili della versione API

Il **AzureRM.Bootstrapper** modulo che è disponibile tramite la raccolta di PowerShell fornisce cmdlet di PowerShell necessari per lavorare con i profili di versione API. Usare il cmdlet seguente per installare il modulo AzureRM.Bootstrapper:

```PowerShell
Install-Module -Name AzureRm.BootStrapper
```
Il modulo AzureRM.Bootstrapper è in anteprima. dettagli e funzionalità sono soggette a modifiche. Per scaricare e installare la versione più recente di questo modulo da PowerShell Gallery, eseguire il cmdlet seguente:

```PowerShell
Update-Module -Name "AzureRm.BootStrapper"
```

## <a name="install-a-profile"></a>Installare un profilo

Utilizzare il **installazione AzureRmProfile** cmdlet con il **2017-03-09-profilo** profilo versione API per installare i moduli di Azure Resource Manager necessari dallo Stack di Azure. Si noti che i moduli di operatore dello Stack di Azure non vengono installati con questo profilo versione API e devono essere installati separatamente come specificato nel passaggio 3 del [installare PowerShell per Azure Stack](azure-stack-powershell-install.md) articolo.

```PowerShell 
Install-AzureRMProfile -Profile 2017-03-09-profile
```
## <a name="install-and-import-modules-in-a-profile"></a>Installare e importare i moduli in un profilo

Utilizzare il **utilizzare AzureRmProfile** cmdlet per installare e importare i moduli che sono associati a un profilo di versione API. È possibile importare solo un profilo di versione API in una sessione di PowerShell. Per importare un profilo di versione API diverso, è necessario aprire una nuova sessione di PowerShell. Il cmdlet di uso AzureRMProfile esegue le seguenti attività:  
1. Controlla se i moduli di PowerShell associati al profilo di versione API specificato vengono installati nell'ambito corrente.  
2. Scarica e installa i moduli se non sono già installati.   
3. Importa i moduli nella sessione corrente di PowerShell. 

```PowerShell
# Installs and imports the specified API version profile into the current PowerShell session.
Use-AzureRmProfile -Profile 2017-03-09-profile -Scope CurrentUser

# Installs and imports the specified API version profile into the current PowerShell session without any prompts
Use-AzureRmProfile -Profile 2017-03-09-profile -Scope CurrentUser -Force
```

Per installare e importare i moduli di Azure Resource Manager selezionati da un profilo di versione API, eseguire il cmdlet di uso AzureRMProfile con il **modulo** parametro:

```PowerShell
# Installs and imports the compute, Storage and Network modules from the specified API version profile into your current PowerShell session.
Use-AzureRmProfile -Profile 2017-03-09-profile -Module AzureRM.Compute, AzureRM.Storage, AzureRM.Network
```

## <a name="get-the-installed-profiles"></a>Ottenere l'elenco dei profili installati

Utilizzare il **Get AzureRmProfile** per ottenere l'elenco dei profili di versione API disponibili: 

```PowerShell
# lists all API version profiles provided by the AzureRM.BootStrapper module.
Get-AzureRmProfile -ListAvailable 

# lists the API version profiles which are installed on your machine
Get-AzureRmProfile
```
## <a name="update-profiles"></a>Aggiornare i profili

Utilizzare il **aggiornamento AzureRmProfile** cmdlet per aggiornare i moduli in un profilo di versione API per la versione più recente dei moduli disponibili nel PSGallery. È consigliabile per eseguire sempre il **aggiornamento AzureRmProfile** cmdlet in una nuova sessione di PowerShell per evitare conflitti durante l'importazione di moduli. Il cmdlet Update-AzureRmProfile esegue le seguenti attività:

1. Controlla se le versioni più recenti dei moduli installate nel profilo versione API specificato per l'ambito corrente.  
2. Viene richiesto di installare, se non sono già installati.  
3. Installa e Importa i moduli aggiornati nella sessione di PowerShell corrente.  

```PowerShell
Update-AzureRmProfile -Profile 2017-03-09-profile
```

Per rimuovere le versioni dei moduli installate in precedenza prima di aggiornare alla versione più recente disponibile, utilizzare il cmdlet Update-AzureRmProfile insieme al **- RemovePreviousVersions** parametro:

```PowerShell 
Update-AzureRmProfile -Profile 2017-03-09-profile -RemovePreviousVersions
```

Questo cmdlet esegue le seguenti attività:  

1. Controlla se le versioni più recenti dei moduli installate nel profilo versione API specificato per l'ambito corrente.  
2. Rimuove le versioni dei moduli dal profilo versione API corrente e nella sessione di PowerShell corrente.  
4. viene richiesto di installare la versione più recente.  
5. Installa e Importa i moduli aggiornati nella sessione di PowerShell corrente.  
 
## <a name="uninstall-profiles"></a>Disinstallare i profili

Utilizzare il **Disinstalla AzureRmProfile** cmdlet per disinstallare il profilo di versione API specificato.

```PowerShell 
Uninstall-AzureRmProfile -Profile 2017-03-09-profile
```

## <a name="next-steps"></a>Passaggi successivi
* [Installare PowerShell per Azure Stack](azure-stack-powershell-install.md)
* [Configurare l'ambiente di PowerShell dell'utente Azure Stack](user/azure-stack-powershell-configure-user.md)  
