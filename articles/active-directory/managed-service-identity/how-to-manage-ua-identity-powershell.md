---
title: Come creare, elencare o eliminare un'identità assegnata dall'utente (identità del servizio gestita) tramite Azure PowerShell
description: Istruzioni dettagliate su come creare, elencare ed eliminare un'identità del servizio gestita assegnata dall'utente usando Azure PowerShell.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/16/2018
ms.author: daveba
ms.openlocfilehash: cc9b1b002c882a847d0ba2359caf4a193ea8d648
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/10/2018
ms.locfileid: "33930791"
---
# <a name="create-list-or-delete-a-user-assigned-identity-using-azure-powershell"></a>Creare, elencare ed eliminare un'identità del servizio gestita assegnata dall'utente usando Azure PowerShell

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

L'identità del servizio gestito offre servizi di Azure con un'identità gestita in Azure Active Directory. È possibile usare questa identità per l'autenticazione ai servizi che supportano l'autenticazione di Azure AD senza dover inserire le credenziali nel codice. 

In questo articolo è illustrato come creare, elencare ed eliminare un'identità assegnata dall'utente tramite Azure PowerShell.

## <a name="prerequisites"></a>prerequisiti

- Se non si ha familiarità con l'identità del servizio gestita, vedere la [sezione sulla panoramica](overview.md). **Assicurarsi di conoscere la [differenza tra identità assegnata dal sistema e identità assegnata dall'utente](overview.md#how-does-it-work)**.
- Se non si ha un account Azure, [registrarsi per ottenere un account gratuito](https://azure.microsoft.com/free/) prima di continuare.
- Installare [la versione più recente di Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM), se non è già installata.
- Se si sceglie di installare e usare PowerShell in locale, per questa esercitazione è necessario il modulo Azure PowerShell 5.7.0 o versione successiva. Eseguire ` Get-Module -ListAvailable AzureRM` per trovare la versione. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-azurerm-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Login-AzureRmAccount` per creare una connessione con Azure.

## <a name="create-a-user-assigned-identity"></a>Creare un'identità assegnata dall'utente

Per creare un'identità assegnata dall'utente usare il comando [New-AzureRmUserAssignedIdentity](/powershell/module/azurerm.managedserviceidentity/new-azurermuserassignedidentity). Il parametro `ResourceGroupName` specifica il gruppo di risorse in cui creare l'identità assegnata dall'utente, mentre il parametro `-Name` ne specifica il nome. Sostituire i valori dei parametri `<RESOURCE GROUP>` e `<USER ASSIGNED IDENTITY NAME>` con valori personalizzati:

> [!IMPORTANT]
> Per creare identità assegnate dall'utente occorre usare solo caratteri alfanumerici e trattino (da 0 a 9, da "a" a "z", da "A" a "Z" e "-"). Inoltre, il nome può avere al massimo 24 caratteri perché l'assegnazione alla macchina virtuale/set di scalabilità di macchine virtuali funzioni correttamente. Ricontrollare in seguito per aggiornamenti. Per altre informazioni, vedere [Domande frequenti e problemi noti](known-issues.md)

 ```azurepowershell-interactive
New-AzureRmUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-identities"></a>Elencare le identità assegnate dall'utente

Per elencare le identità assegnate dall'utente, usare il comando [Get-AzureRmUserAssigned](/powershell/module/azurerm.managedserviceidentity/get-azurermuserassignedidentity).  Il parametro `-ResourceGroupName` specifica il gruppo di risorse in cui è stata creata l'identità assegnata dall'utente.  Sostituire `<RESOURCE GROUP>` con il proprio valore:

```azurepowershell-interactive
Get-AzureRmUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP>
```
Nella risposta, le identità dell'utente hanno il valore `"Microsoft.ManagedIdentity/userAssignedIdentities"` restituito per la chiave, `Type`.

`Type :Microsoft.ManagedIdentity/userAssignedIdentities`

## <a name="delete-a-user-assigned-identity"></a>Eliminare un'identità assegnata dall'utente

Per eliminare un'identità assegnata dall'utente usare il comando [Remove-AzureRmUserAssignedIdentity](/powershell/module/azurerm.managedserviceidentity/remove-azurermuserassignedidentity).  Il parametro `-ResourceGroupName` specifica il gruppo di risorse in cui viene creata l'identità assegnata dall'utente, mentre il parametro `-Name` ne specifica il nome.  Sostituire i valori dei parametri `<RESOURCE GROUP>` e `<USER ASSIGNED IDENTITY NAME>` con valori personalizzati:

 ```azurecli-interactive
Remove-AzurRmUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
> [!NOTE]
> L'eliminazione di un'identità assegnata dall'utente non rimuoverà il riferimento, da qualsiasi risorsa a cui è stato assegnato. Le assegnazioni delle identità devono essere rimosse separatamente.

## <a name="related-content"></a>Contenuti correlati

Per un elenco completo e altri dettagli sui comandi dell'identità del servizio gestita di Azure PowerShell, vedere [AzureRM.ManagedServiceIdentity](/powershell/module/azurerm.managedserviceidentity#managed_service_identity).


 
