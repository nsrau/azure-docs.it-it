---
title: Come visualizzare l'entità servizio di un'identità gestita tramite PowerShell
description: Istruzioni dettagliate su come visualizzare l'entità servizio di un'identità gestita tramite PowerShell.
services: active-directory
documentationcenter: ''
author: daveba
manager: daveba
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/29/2018
ms.author: daveba
ms.openlocfilehash: 0cf1915e4013451dbb09f2c4af3df2bad6166475
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2019
ms.locfileid: "54438950"
---
# <a name="view-the-service-principal-of-a-managed-identity-using-powershell"></a>Visualizzare l'entità servizio di un'identità gestita tramite PowerShell

Le identità gestite per le risorse di Azure offrono ai servizi di Azure un'identità gestita automaticamente in Azure Active Directory. È possibile usare questa identità per l'autenticazione a qualsiasi servizio che supporti l'autenticazione di Azure AD senza dover inserire le credenziali nel codice. 

In questo articolo viene descritto come visualizzare l'entità servizio di un'identità gestita tramite PowerShell.

## <a name="prerequisites"></a>Prerequisiti

- Se non si ha familiarità con le identità gestite per le risorse di Azure, vedere la [sezione sulla panoramica](overview.md).
- Se non si ha già un account Azure, è possibile [registrarsi per ottenerne uno gratuito](https://azure.microsoft.com/free/).
- Abilitare l'[identità assegnata dal sistema nella macchina virtuale](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity) o nell'[applicazione](/azure/app-service/overview-managed-identity#adding-a-system-assigned-identity).
- Se si sceglie di installare e usare PowerShell in locale, per questa esercitazione è necessario il modulo Azure PowerShell 5.7.0 o versione successiva. Eseguire ` Get-Module -ListAvailable AzureRM` per trovare la versione. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps). 
- Se si esegue PowerShell in locale, è anche necessario: 
    - Eseguire `Login-AzureRmAccount` per creare una connessione con Azure.
    - Installare la [versione più recente di PowerShellGet](/powershell/gallery/installing-psget#for-systems-with-powershell-50-or-newer-you-can-install-the-latest-powershellget).
    - Eseguire `Install-Module -Name PowerShellGet -AllowPrerelease` per ottenere la versione non definitiva del modulo `PowerShellGet` (potrebbe essere necessario `Exit` uscire dalla sessione corrente di PowerShell dopo aver eseguito questo comando per installare il modulo `AzureRM.ManagedServiceIdentity`).
    - Eseguire `Install-Module -Name AzureRM.ManagedServiceIdentity -AllowPrerelease` per installare la versione non definitiva del modulo `AzureRM.ManagedServiceIdentity` per eseguire le operazioni di identità gestite assegnate dall'utente in questo articolo.

## <a name="view-the-service-principal"></a>Visualizzare l'entità servizio

Il comando seguente mostra come visualizzare l'entità servizio di una macchina virtuale o un'applicazione in cui è abilitata un'identità assegnata. Sostituire `<VM or application name>` con i propri valori.

```PowerShell
Get-AzureRmADServicePrincipal -DisplayName <VM or application name>
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla visualizzazione delle entità servizio di Azure AD usando PowerShell, vedere [Get-AzureRmADServicePrincipal](/powershell/module/azurerm.resources/get-azurermadserviceprincipal).


