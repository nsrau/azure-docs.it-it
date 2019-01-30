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
ms.date: 01/05/2019
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 01/05/2019
ms.openlocfilehash: c6bee5c66661f59a6287d624bf5a55428ebbe56e
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/30/2019
ms.locfileid: "55238119"
---
# <a name="use-api-version-profiles-for-powershell-in-azure-stack"></a>Usare i profili delle versioni API per PowerShell in Azure Stack

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

I profili delle versioni API forniscono un modo per gestire le differenze di versione tra Azure e Azure Stack. Un profilo di versione API è un set di moduli AzureRM di PowerShell con le versioni API specifiche. Ogni piattaforma di cloud ha un set di profili di versione API supportati. Ad esempio, Azure Stack supporta una versione del profilo con data di validità specifico, ad esempio **2018-03-01-hybrid**, e Azure supporta le **più recente** profilo di versione API. Quando si installa un profilo, vengono installati i moduli AzureRM di PowerShell che corrispondono al profilo specificato.

## <a name="install-the-powershell-module-required-to-use-api-version-profiles"></a>Installare il modulo di PowerShell necessario per usare i profili delle versioni API

Il **AzureRM.Bootstrapper** modulo che è disponibile tramite la raccolta di PowerShell fornisce cmdlet di PowerShell necessari per lavorare con i profili della versione API. Usare il cmdlet seguente per installare il **AzureRM.Bootstrapper** modulo:

```PowerShell
Install-Module -Name AzureRm.BootStrapper
```

## <a name="azure-stack-version-and-profile-versions"></a>La versione di Azure Stack e versioni di profili

Nella tabella seguente elenca la versione richiesta di profilo API e il moniker di modulo di amministrazione PowerShell usato per le versioni recenti di Azure Stack. Se si usa questo articolo con una versione prima di 1808, aggiornare il profilo di versione e il moniker con il valore corretto.

| No. versione | Profilo di versione API | PS moniker del modulo di amministrazione |
| --- | --- | --- |
| 1811 o versione successiva | 2018-03-01-hybrid | 1.6.0 |
| 1808 o versione successiva | 2018-03-01-hybrid | 1.5.0 |
| 1804 o versione successiva | 2017-03-09-profilo | 1.4.0 |
| Versioni precedenti 1804 | 2017-03-09-profilo | 1.2.11 |

> [!NOTE]  
> Eseguire l'aggiornamento dal 1.2.11 versione, vedere la [Guida alla migrazione](https://aka.ms/azpsh130migration).

## <a name="install-a-profile"></a>Installare un profilo

Usare la **Install-AzureRmProfile** cmdlet con il **2018-03-01-hybrid** profilo di versione API per installare i moduli AzureRM necessari per Azure Stack. I moduli di operatore di Azure Stack non sono installati con questo profilo di versione API. È necessario che vengano installati separatamente come specificato nel passaggio 3 della [installazione di PowerShell per Azure Stack](../azure-stack-powershell-install.md) articolo.

```PowerShell
Install-AzureRMProfile -Profile 2018-03-01-hybrid
```

## <a name="install-and-import-modules-in-a-profile"></a>Installare e importare i moduli in un profilo

Usare la **utilizzare-AzureRmProfile** cmdlet per installare e importare i moduli che sono associati a un profilo di versione API. È possibile importare un solo profilo di versione API in una sessione di PowerShell. Per importare un profilo di versione API diverso, è necessario aprire una nuova sessione di PowerShell. Il **utilizzare-AzureRMProfile** cmdlet esegue le attività seguenti:

1. Verifica se sono installati i moduli di PowerShell associati al profilo di versione API specificato nell'ambito corrente.  
2. Scarica e installa i moduli se non sono già installati.
3. Importa i moduli nella sessione di PowerShell corrente.

```PowerShell
# Installs and imports the specified API version profile into the current PowerShell session.
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Scope CurrentUser

# Installs and imports the specified API version profile into the current PowerShell session without any prompts
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Scope CurrentUser -Force
```

Per installare e importare i moduli AzureRM selezionati da un profilo di versione API, eseguire la **utilizzare-AzureRMProfile** cmdlet con il **modulo** parametro:

```PowerShell
# Installs and imports the compute, storage and network modules from the specified API version profile into your current PowerShell session.
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Module AzureRM.Compute, AzureRM.Storage, AzureRM.Network
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

Usare la **Update-AzureRmProfile** cmdlet per aggiornare i moduli in un profilo di versione API per la versione più recente dei moduli disponibili in PSGallery. Si consiglia sempre di eseguire la **Update-AzureRmProfile** cmdlet in una nuova sessione di PowerShell per evitare conflitti durante l'importazione di moduli. Il **Update-AzureRmProfile** cmdlet esegue le attività seguenti:

1. Verifica se le versioni più recenti dei moduli sono installate nel profilo di versione API specificato per l'ambito corrente.  
2. Viene richiesto di installare se non sono già installati.  
3. Installa e Importa i moduli aggiornati nella sessione di PowerShell corrente.  

```PowerShell
Update-AzureRmProfile -Profile 2018-03-01-hybrid
```

<!-- To remove the previously installed versions of the modules before updating to the latest available version, use the Update-AzureRmProfile cmdlet along with the **-RemovePreviousVersions** parameter:

```PowerShell 
Update-AzureRmProfile -Profile 2018-03-01-hybrid -RemovePreviousVersions
``` -->

Questo cmdlet esegue le attività seguenti:  

1. Verifica se le versioni più recenti dei moduli sono installate nel profilo di versione API specificato per l'ambito corrente.  
2. Rimuove le versioni precedenti dei moduli del profilo di versione API corrente e nella sessione di PowerShell corrente.  
3. viene richiesto di installare la versione più recente.  
4. Installa e Importa i moduli aggiornati nella sessione di PowerShell corrente.  

## <a name="uninstall-profiles"></a>Disinstallare i profili

Usare la **Uninstall-AzureRmProfile** cmdlet per disinstallare il profilo di versione API specificato.

```PowerShell
Uninstall-AzureRmProfile -Profile  2018-03-01-hybrid
```

## <a name="next-steps"></a>Passaggi successivi

* [Installare PowerShell per Azure Stack](azure-stack-powershell-install.md)
* [Configurare l'ambiente di PowerShell dell'utente Azure Stack](azure-stack-powershell-configure-user.md)  
