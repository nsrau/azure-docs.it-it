---
title: Come creare, elencare o eliminare un'identità gestita assegnata dall'utente tramite Azure PowerShell
description: Istruzioni dettagliate su come creare, elencare ed eliminare un'identità gestita assegnata dall'utente mediante Azure PowerShell.
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
ms.date: 04/16/2018
ms.author: daveba
ms.openlocfilehash: d98cb449552bdbf4021a7f97a3253796bacc6e6d
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2019
ms.locfileid: "54427191"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-azure-powershell"></a>Creare, elencare o eliminare un'identità gestita assegnata dall'utente tramite Azure PowerShell

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Le identità gestite per le risorse Azure forniscono ai servizi di Azure un'identità gestita in Azure Active Directory. È possibile usare questa identità per l'autenticazione ai servizi che supportano l'autenticazione di Azure AD senza dover inserire le credenziali nel codice. 

In questo articolo è illustrato come creare, elencare ed eliminare un'identità gestita assegnata dall'utente tramite Azure PowerShell.

## <a name="prerequisites"></a>Prerequisiti

- Se non si ha familiarità con le identità gestite per le risorse di Azure, vedere la [sezione sulla panoramica](overview.md). **Assicurarsi di conoscere la [differenza tra identità assegnata dal sistema e identità gestita assegnata dall'utente](overview.md#how-does-it-work)**.
- Se non si ha un account Azure, [registrarsi per ottenere un account gratuito](https://azure.microsoft.com/free/) prima di continuare.
- Installare [la versione più recente di Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM), se non è già installata.
- Se si sceglie di installare e usare PowerShell in locale, per questa esercitazione è necessario il modulo Azure PowerShell 5.7.0 o versione successiva. Eseguire ` Get-Module -ListAvailable AzureRM` per trovare la versione. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps). 
- Se si esegue PowerShell in locale, è anche necessario: 
    - Eseguire `Login-AzureRmAccount` per creare una connessione con Azure.
    - Installare la [versione più recente di PowerShellGet](/powershell/gallery/installing-psget#for-systems-with-powershell-50-or-newer-you-can-install-the-latest-powershellget).
    - Eseguire `Install-Module -Name PowerShellGet -AllowPrerelease` per ottenere la versione non definitiva del modulo `PowerShellGet` (potrebbe essere necessario `Exit` uscire dalla sessione corrente di PowerShell dopo aver eseguito questo comando per installare il modulo `AzureRM.ManagedServiceIdentity`).
    - Eseguire `Install-Module -Name AzureRM.ManagedServiceIdentity -AllowPrerelease` per installare la versione non definitiva del modulo `AzureRM.ManagedServiceIdentity` per eseguire le operazioni di identità gestite assegnate dall'utente in questo articolo.

## <a name="create-a-user-assigned-managed-identity"></a>Creare un'identità gestita assegnata dall'utente

Per creare un'identità gestita assegnata dall'utente, all'account deve essere assegnato il ruolo [Collaboratore di identità gestite](/azure/role-based-access-control/built-in-roles#managed-identity-contributor).

Per creare un'identità gestita assegnata dall'utente usare il comando [New-AzureRmUserAssignedIdentity](/powershell/module/azurerm.managedserviceidentity/new-azurermuserassignedidentity). Il parametro `ResourceGroupName` specifica il gruppo di risorse in cui creare l'identità gestita assegnata dall'utente, mentre il parametro `-Name` ne specifica il nome. Sostituire i valori dei parametri `<RESOURCE GROUP>` e `<USER ASSIGNED IDENTITY NAME>` con valori personalizzati:

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

 ```azurepowershell-interactive
New-AzureRmUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-managed-identities"></a>Elencare le identità gestite assegnate dall'utente

Per elencare/leggere un'identità gestita assegnata dall'utente, all'account deve essere assegnato il ruolo [Operatore di identità gestite](/azure/role-based-access-control/built-in-roles#managed-identity-operator) o [Collaboratore di identità gestite](/azure/role-based-access-control/built-in-roles#managed-identity-contributor).

Per elencare le identità gestite assegnate dall'utente, usare il comando [Get-AzureRmUserAssigned](/powershell/module/azurerm.managedserviceidentity/get-azurermuserassignedidentity).  Il parametro `-ResourceGroupName` specifica il gruppo di risorse in cui è stata creata l'identità gestita assegnata dall'utente. Sostituire `<RESOURCE GROUP>` con il proprio valore:

```azurepowershell-interactive
Get-AzureRmUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP>
```
Nella risposta, le identità gestite assegnate dall'utente hanno il valore `"Microsoft.ManagedIdentity/userAssignedIdentities"` restituito per la chiave, `Type`.

`Type :Microsoft.ManagedIdentity/userAssignedIdentities`

## <a name="delete-a-user-assigned-managed-identity"></a>Eliminare un'identità gestita assegnata dall'utente

Per eliminare un'identità gestita assegnata dall'utente, all'account deve essere assegnato il ruolo [Collaboratore di identità gestite](/azure/role-based-access-control/built-in-roles#managed-identity-contributor).

Per eliminare un'identità gestita assegnata dall'utente usare il comando [Remove-AzureRmUserAssignedIdentity](/powershell/module/azurerm.managedserviceidentity/remove-azurermuserassignedidentity).  Il parametro `-ResourceGroupName` specifica il gruppo di risorse in cui è stata creata l'identità assegnata dall'utente, mentre il parametro `-Name` ne specifica il nome. Sostituire i valori dei parametri `<RESOURCE GROUP>` e `<USER ASSIGNED IDENTITY NAME>` con valori personalizzati:

 ```azurepowershell-interactive
Remove-AzurRmUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
> [!NOTE]
> L'eliminazione di un'identità gestita assegnata dall'utente non eliminerà il riferimento proveniente da qualunque risorsa a cui è stato assegnato. Le assegnazioni delle identità devono essere rimosse separatamente.

## <a name="next-steps"></a>Passaggi successivi

Per un elenco completo e altri dettagli sui comandi delle identità gestite di Azure PowerShell per le risorse di Azure, vedere [AzureRM.ManagedServiceIdentity](/powershell/module/azurerm.managedserviceidentity#managed_service_identity).
