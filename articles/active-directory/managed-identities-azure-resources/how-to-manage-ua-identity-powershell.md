---
title: Come creare, elencare o eliminare un'identità gestita assegnata dall'utente tramite Azure PowerShell
description: Istruzioni dettagliate su come creare, elencare ed eliminare un'identità gestita assegnata dall'utente mediante Azure PowerShell.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/16/2018
ms.author: priyamo
ms.collection: M365-identity-device-management
ms.openlocfilehash: a0d1fee9526c308f025abaf1ded1f02ee0617b2f
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56202582"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-azure-powershell"></a>Creare, elencare o eliminare un'identità gestita assegnata dall'utente tramite Azure PowerShell

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Le identità gestite per le risorse Azure forniscono ai servizi di Azure un'identità gestita in Azure Active Directory. È possibile usare questa identità per l'autenticazione ai servizi che supportano l'autenticazione di Azure AD senza dover inserire le credenziali nel codice. 

In questo articolo è illustrato come creare, elencare ed eliminare un'identità gestita assegnata dall'utente tramite Azure PowerShell.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisiti

- Se non si ha familiarità con le identità gestite per le risorse di Azure, vedere la [sezione sulla panoramica](overview.md). **Assicurarsi di conoscere la [differenza tra identità assegnata dal sistema e identità gestita assegnata dall'utente](overview.md#how-does-it-work)**.
- Se non si ha un account Azure, [registrarsi per ottenere un account gratuito](https://azure.microsoft.com/free/) prima di continuare.
- Installare [la versione più recente di Azure PowerShell](/powershell/azure/install-az-ps), se non è già installata.
- Se si esegue PowerShell in locale, è anche necessario: 
    - Eseguire `Connect-AzAccount` per creare una connessione con Azure.
    - Installare la [versione più recente di PowerShellGet](/powershell/gallery/installing-psget#for-systems-with-powershell-50-or-newer-you-can-install-the-latest-powershellget).
    - Eseguire `Install-Module -Name PowerShellGet -AllowPrerelease` per ottenere la versione non definitiva del modulo `PowerShellGet` (potrebbe essere necessario `Exit` uscire dalla sessione corrente di PowerShell dopo aver eseguito questo comando per installare il modulo `Az.ManagedServiceIdentity`).
    - Eseguire `Install-Module -Name Az.ManagedServiceIdentity -AllowPrerelease` per installare la versione non definitiva del modulo `Az.ManagedServiceIdentity` per eseguire le operazioni di identità gestite assegnate dall'utente in questo articolo.

## <a name="create-a-user-assigned-managed-identity"></a>Creare un'identità gestita assegnata dall'utente

Per creare un'identità gestita assegnata dall'utente, all'account deve essere assegnato il ruolo [Collaboratore di identità gestite](/azure/role-based-access-control/built-in-roles#managed-identity-contributor).

Per creare un'identità gestita assegnata dall'utente, usare il comando `New-AzUserAssignedIdentity`. Il parametro `ResourceGroupName` specifica il gruppo di risorse in cui creare l'identità gestita assegnata dall'utente, mentre il parametro `-Name` ne specifica il nome. Sostituire i valori dei parametri `<RESOURCE GROUP>` e `<USER ASSIGNED IDENTITY NAME>` con valori personalizzati:

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

 ```azurepowershell-interactive
New-AzUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-managed-identities"></a>Elencare le identità gestite assegnate dall'utente

Per elencare/leggere un'identità gestita assegnata dall'utente, all'account deve essere assegnato il ruolo [Operatore di identità gestite](/azure/role-based-access-control/built-in-roles#managed-identity-operator) o [Collaboratore di identità gestite](/azure/role-based-access-control/built-in-roles#managed-identity-contributor).

Per elencare le identità gestite assegnate dall'utente, usare il comando [Get-AzUserAssigned].  Il parametro `-ResourceGroupName` specifica il gruppo di risorse in cui è stata creata l'identità gestita assegnata dall'utente. Sostituire `<RESOURCE GROUP>` con il proprio valore:

```azurepowershell-interactive
Get-AzUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP>
```
Nella risposta, le identità gestite assegnate dall'utente hanno il valore `"Microsoft.ManagedIdentity/userAssignedIdentities"` restituito per la chiave, `Type`.

`Type :Microsoft.ManagedIdentity/userAssignedIdentities`

## <a name="delete-a-user-assigned-managed-identity"></a>Eliminare un'identità gestita assegnata dall'utente

Per eliminare un'identità gestita assegnata dall'utente, all'account deve essere assegnato il ruolo [Collaboratore di identità gestite](/azure/role-based-access-control/built-in-roles#managed-identity-contributor).

Per eliminare un'identità gestita assegnata dall'utente, usare il comando `Remove-AzUserAssignedIdentity`.  Il parametro `-ResourceGroupName` specifica il gruppo di risorse in cui è stata creata l'identità assegnata dall'utente, mentre il parametro `-Name` ne specifica il nome. Sostituire i valori dei parametri `<RESOURCE GROUP>` e `<USER ASSIGNED IDENTITY NAME>` con valori personalizzati:

 ```azurepowershell-interactive
Remove-AzUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
> [!NOTE]
> L'eliminazione di un'identità gestita assegnata dall'utente non eliminerà il riferimento proveniente da qualunque risorsa a cui è stato assegnato. Le assegnazioni delle identità devono essere rimosse separatamente.

## <a name="next-steps"></a>Passaggi successivi

Per un elenco completo e altri dettagli sui comandi delle identità gestite di Azure PowerShell per le risorse di Azure, vedere [Az.ManagedServiceIdentity](/powershell/module/az.managedserviceidentity#managed_service_identity).
