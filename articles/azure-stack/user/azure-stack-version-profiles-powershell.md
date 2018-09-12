---
title: Usando i profili delle versioni API con PowerShell in Azure Stack | Microsoft Docs
description: Imparare a utilizzare i profili delle versioni API con PowerShell in Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: EBAEA4D2-098B-4B5A-A197-2CEA631A1882
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2018
ms.author: sethm
ms.reviewer: sijuman
ms.openlocfilehash: 994893eb73356fde9acc593569dc5fb1c5a0106f
ms.sourcegitcommit: 794bfae2ae34263772d1f214a5a62ac29dcec3d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2018
ms.locfileid: "44391131"
---
# <a name="use-api-version-profiles-for-powershell-in-azure-stack"></a>Usare i profili delle versioni API per PowerShell in Azure Stack

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

I profili delle versioni API forniscono un modo per gestire le differenze di versione tra Azure e Azure Stack. Un profilo di versione API è un set di moduli AzureRM di PowerShell con le versioni API specifiche. Ogni piattaforma di cloud ha un set di profili di versione API supportati. Ad esempio, Azure Stack supporta una versione del profilo con data di validità specifico, ad esempio **2017-03-09-profile**, e Azure supporta le **più recente** profilo di versione API. Quando si installa un profilo, vengono installati i moduli AzureRM di PowerShell che corrispondono al profilo specificato.

 

## <a name="install-the-powershell-module-required-to-use-api-version-profiles"></a>Installare il modulo di PowerShell necessario per usare i profili delle versioni API

Il **AzureRM.Bootstrapper** modulo che è disponibile tramite la raccolta di PowerShell fornisce cmdlet di PowerShell necessari per lavorare con i profili della versione API. Usare il cmdlet seguente per installare il modulo AzureRM.Bootstrapper:

```PowerShell
Install-Module -Name AzureRm.BootStrapper
```

## <a name="install-a-profile"></a>Installare un profilo

Usare la **Install-AzureRmProfile** cmdlet con il **2017-03-09-profilo** profilo di versione API per installare i moduli AzureRM necessari per Azure Stack. I moduli di operatore di Azure Stack non sono installati con questo profilo di versione API. È necessario che vengano installati separatamente come specificato nel passaggio 3 della [installazione di PowerShell per Azure Stack](azure-stack-powershell-install.md) articolo.

```PowerShell 
Install-AzureRMProfile -Profile 2017-03-09-profile
```
## <a name="install-and-import-modules-in-a-profile"></a>Installare e importare i moduli in un profilo

Usare la **utilizzare-AzureRmProfile** cmdlet per installare e importare i moduli che sono associati a un profilo di versione API. È possibile importare un solo profilo di versione API in una sessione di PowerShell. Per importare un profilo di versione API diverso, è necessario aprire una nuova sessione di PowerShell. Il cmdlet di uso-AzureRMProfile esegue le attività seguenti:  
1. Verifica se sono installati i moduli di PowerShell associati al profilo di versione API specificato nell'ambito corrente.  
2. Scarica e installa i moduli se non sono già installati.   
3. Importa i moduli nella sessione di PowerShell corrente. 

```PowerShell
# Installs and imports the specified API version profile into the current PowerShell session.
Use-AzureRmProfile -Profile 2017-03-09-profile -Scope CurrentUser

# Installs and imports the specified API version profile into the current PowerShell session without any prompts
Use-AzureRmProfile -Profile 2017-03-09-profile -Scope CurrentUser -Force
```

Per installare e importare i moduli AzureRM selezionati da un profilo di versione API, eseguire il cmdlet di uso-AzureRMProfile con il **modulo** parametro:

```PowerShell
# Installs and imports the compute, Storage and Network modules from the specified API version profile into your current PowerShell session.
Use-AzureRmProfile -Profile 2017-03-09-profile -Module AzureRM.Compute, AzureRM.Storage, AzureRM.Network
```

## <a name="get-the-installed-profiles"></a>Ottenere i profili installati

Usare la **Get-AzureRmProfile** per ottenere l'elenco dei profili di versione API disponibili: 

```PowerShell
# lists all API version profiles provided by the AzureRM.BootStrapper module.
Get-AzureRmProfile -ListAvailable 

# lists the API version profiles which are installed on your machine
Get-AzureRmProfile
```
## <a name="update-profiles"></a>Aggiornare i profili

Usare la **Update-AzureRmProfile** cmdlet per aggiornare i moduli in un profilo di versione API per la versione più recente dei moduli disponibili in PSGallery. È consigliabile eseguire sempre il **Update-AzureRmProfile** cmdlet in una nuova sessione di PowerShell per evitare conflitti durante l'importazione di moduli. Il cmdlet Update-AzureRmProfile esegue le attività seguenti:

1. Verifica se le versioni più recenti dei moduli sono installate nel profilo di versione API specificato per l'ambito corrente.  
2. Viene richiesto di installare se non sono già installati.  
3. Installa e Importa i moduli aggiornati nella sessione di PowerShell corrente.  

```PowerShell
Update-AzureRmProfile -Profile 2017-03-09-profile
```

Per rimuovere le versioni dei moduli installate in precedenza prima di aggiornare la versione più recente disponibile, usare il cmdlet Update-AzureRmProfile lungo con la **- RemovePreviousVersions** parametro:

```PowerShell 
Update-AzureRmProfile -Profile 2017-03-09-profile -RemovePreviousVersions
```

Questo cmdlet esegue le attività seguenti:  

1. Verifica se le versioni più recenti dei moduli sono installate nel profilo di versione API specificato per l'ambito corrente.  
2. Rimuove le versioni precedenti dei moduli del profilo di versione API corrente e nella sessione di PowerShell corrente.  
4. viene richiesto di installare la versione più recente.  
5. Installa e Importa i moduli aggiornati nella sessione di PowerShell corrente.  
 
## <a name="uninstall-profiles"></a>Disinstallare i profili

Usare la **Uninstall-AzureRmProfile** cmdlet per disinstallare il profilo di versione API specificato.

```PowerShell 
Uninstall-AzureRmProfile -Profile 2017-03-09-profile
```

## <a name="next-steps"></a>Passaggi successivi
* [Installare PowerShell per Azure Stack](azure-stack-powershell-install.md)
* [Configurare l'ambiente di PowerShell dell'utente Azure Stack](azure-stack-powershell-configure-user.md)  
