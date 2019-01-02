---
title: Come assegnare a un'identità gestita l'accesso a una risorsa di Azure tramite PowerShell
description: Istruzioni dettagliate su come assegnare a un'identità gestita in una risorsa l'accesso a un'altra risorsa tramite PowerShell.
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
ms.date: 12/06/2018
ms.author: daveba
ms.openlocfilehash: 7ed61ead475acb81da4434c880954e801492351b
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2018
ms.locfileid: "53081558"
---
# <a name="assign-a-managed-identity-access-to-a-resource-using-powershell"></a>Assegnare a un'identità gestita l'accesso a una risorsa tramite PowerShell

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Dopo aver configurato una risorsa di Azure con un'identità gestita, è possibile concedere all'identità gestita l'accesso a un'altra risorsa, proprio come per qualsiasi entità di sicurezza. Questo esempio mostra come assegnare a un'identità gestita di una macchina virtuale di Azure l'accesso a un account di archiviazione di Azure tramite PowerShell.

## <a name="prerequisites"></a>Prerequisiti

- Se non si ha familiarità con le identità gestite per le risorse di Azure, vedere la [sezione sulla panoramica](overview.md). **Assicurarsi di conoscere la [differenza tra identità assegnata dal sistema e identità gestita assegnata dall'utente](overview.md#how-does-it-work)**.
- Se non si ha un account Azure, [registrarsi per ottenere un account gratuito](https://azure.microsoft.com/free/) prima di continuare.
- Installare [la versione più recente di Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM), se non è già installata.

## <a name="use-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Usare il controllo degli accessi in base al ruolo per assegnare a un'identità gestita l'accesso a un'altra risorsa

Dopo aver abilitato l'identità gestita in una risorsa di Azure, [ad esempio una macchina virtuale di Azure](qs-configure-powershell-windows-vm.md):

1. Accedere ad Azure tramite il cmdlet `Connect-AzureRmAccount`. Usare un account associato alla sottoscrizione di Azure in cui è stata configurata l'identità gestita:

   ```powershell
   Connect-AzureRmAccount
   ```
2. In questo esempio, viene concesso l'accesso alla macchina virtuale di Azure in un account di archiviazione. Prima di tutto viene usato il comando [Get-AzureRMVM](/powershell/module/azurerm.compute/get-azurermvm) per ottenere l'entità servizio per la macchina virtuale denominata `myVM`, che è stata creata quando è stata abilitata l'identità gestita. Viene quindi usato [New-AzureRmRoleAssignment](/powershell/module/AzureRM.Resources/New-AzureRmRoleAssignment) per concedere alla macchina virtuale l'accesso in **Lettura** a un account di archiviazione denominato `myStorageAcct`:

    ```powershell
    $spID = (Get-AzureRMVM -ResourceGroupName myRG -Name myVM).identity.principalid
    New-AzureRmRoleAssignment -ObjectId $spID -RoleDefinitionName "Reader" -Scope "/subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/<myStorageAcct>"
    ```

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica delle identità gestite per le risorse di Azure](overview.md)
- Per abilitare l'identità gestita in una macchina virtuale di Azure, vedere [Configurare identità gestite per risorse di Azure in una macchina virtuale tramite PowerShell](qs-configure-powershell-windows-vm.md).
