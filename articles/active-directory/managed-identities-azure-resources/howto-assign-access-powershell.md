---
title: Assegnare un accesso a identità gestite a una risorsa usando PowerShell-Azure AD
description: Istruzioni dettagliate per l'assegnazione di un'identità gestita in una risorsa, l'accesso a un'altra risorsa tramite PowerShell.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2018
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: b66567275bf2c7454a2d4bb87dcd4c14bb1fb9b4
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90969287"
---
# <a name="assign-a-managed-identity-access-to-a-resource-using-powershell"></a>Assegnare a un'identità gestita l'accesso a una risorsa tramite PowerShell

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Dopo aver configurato una risorsa di Azure con un'identità gestita, è possibile concedere all'identità gestita l'accesso a un'altra risorsa, proprio come per qualsiasi entità di sicurezza. Questo esempio mostra come assegnare a un'identità gestita di una macchina virtuale di Azure l'accesso a un account di archiviazione di Azure tramite PowerShell.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisiti

- Se non si ha familiarità con le identità gestite per le risorse di Azure, vedere la [sezione sulla panoramica](overview.md). **Assicurarsi di conoscere la [differenza tra identità assegnata dal sistema e identità gestita assegnata dall'utente](overview.md#managed-identity-types)**.
- Se non si ha un account Azure, [registrarsi per ottenere un account gratuito](https://azure.microsoft.com/free/) prima di continuare.
- Per eseguire gli script di esempio, sono disponibili due opzioni:
    - Usare il [Azure cloud Shell](../../cloud-shell/overview.md), che è possibile aprire usando il pulsante **prova** nell'angolo superiore destro dei blocchi di codice.
    - Eseguire gli script localmente installando la versione più recente di [Azure PowerShell](/powershell/azure/install-az-ps), quindi accedere ad Azure usando `Connect-AzAccount` . 

## <a name="use-azure-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Usare il controllo degli accessi in base al ruolo di Azure

1. Abilitare l'identità gestita in una risorsa di Azure, [ad esempio una macchina virtuale di Azure](qs-configure-powershell-windows-vm.md).

1. In questo esempio, viene concesso l'accesso alla macchina virtuale di Azure in un account di archiviazione. Prima di tutto viene usato il comando [Get-AzVM](/powershell/module/az.compute/get-azvm) per ottenere l'entità servizio per la macchina virtuale denominata `myVM`, che è stata creata quando è stata abilitata l'identità gestita. Viene quindi usato [New-AzRoleAssignment](/powershell/module/Az.Resources/New-AzRoleAssignment) per concedere alla macchina virtuale l'accesso in **Lettura** a un account di archiviazione denominato `myStorageAcct`:

    ```azurepowershell-interactive
    $spID = (Get-AzVM -ResourceGroupName myRG -Name myVM).identity.principalid
    New-AzRoleAssignment -ObjectId $spID -RoleDefinitionName "Reader" -Scope "/subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/<myStorageAcct>"
    ```

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica delle identità gestite per le risorse di Azure](overview.md)
- Per abilitare l'identità gestita in una macchina virtuale di Azure, vedere [Configurare identità gestite per risorse di Azure in una macchina virtuale tramite PowerShell](qs-configure-powershell-windows-vm.md).
