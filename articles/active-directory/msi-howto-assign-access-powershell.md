---
title: "Come assegnare all'Identità del servizio gestito l'accesso a una risorsa di Azure tramite PowerShell"
description: "Istruzioni dettagliate per assegnare a un'Identità del servizio gestito in una risorsa l'accesso a un'altra risorsa, tramite PowerShell."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/14/2017
ms.author: daveba
ms.openlocfilehash: bdc6268bd56598b917f51f39e604f858cfe81868
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2018
---
# <a name="assign-a-managed-service-identity-msi-access-to-a-resource-using-powershell"></a>Assegnare a un'Identità del servizio gestito, ovvero MSI, l'accesso a una risorsa tramite PowerShell

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

Dopo aver configurato una risorsa di Azure con un'Identità del servizio gestito, è possibile concedere a un'Identità del servizio gestito l'accesso a un'altra risorsa, analogamente a qualsiasi entità di sicurezza. In questo esempio viene illustrato come concedere a un'Identità del servizio gestito della macchina virtuale di Azure l'accesso a un account di archiviazione di Azure, mediante PowerShell.

## <a name="prerequisites"></a>prerequisiti

[!INCLUDE [msi-qs-configure-prereqs](../../includes/active-directory-msi-qs-configure-prereqs.md)]

Installare anche [Azure PowerShell, versione 4.3.1](https://www.powershellgallery.com/packages/AzureRM/4.3.1), se non è stato già installato.

## <a name="use-rbac-to-assign-the-msi-access-to-another-resource"></a>Usare il controllo degli accessi in base al ruolo per assegnare a un'Identità del servizio gestito l'accesso a un'altra risorsa

Dopo aver abilitato l'Identità del servizio gestito in una risorsa di Azure, [ad esempio in una macchina virtuale di Azure](msi-qs-configure-powershell-windows-vm.md):

1. Accedere ad Azure tramite il cmdlet `Login-AzureRmAccount`. Usare un account associato alla sottoscrizione di Azure in cui si desidera configurare l'Identità del servizio gestito:

   ```powershell
   Login-AzureRmAccount
   ```
2. In questo esempio, viene concesso l'accesso alla macchina virtuale di Azure in un account di archiviazione. Prima di tutto viene usato il comando [Get-AzureRMVM](/powershell/module/azurerm.compute/get-azurermvm) per ottenere l'entità servizio per la macchina virtuale denominata "myVM", che è stata creata quando è stata abilitata l'Identità del servizio gestito. Quindi, si usa [New-AzureRmRoleAssignment](/powershell/module/AzureRM.Resources/New-AzureRmRoleAssignment) per assegnare alla macchina virtuale l'accesso in "Lettura" a un account di archiviazione denominato "myStorageAcct":

    ```powershell
    $spID = (Get-AzureRMVM -ResourceGroupName myRG -Name myVM).identity.principalid
    New-AzureRmRoleAssignment -ObjectId $spID -RoleDefinitionName "Reader" -Scope "/subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/<myStorageAcct>"
    ```

## <a name="troubleshooting"></a>risoluzione dei problemi

Se l'Identità del servizio gestito per la risorsa non viene visualizzata nell'elenco delle identità disponibili, verificare che sia stata abilitata correttamente. In questo caso, si torna alla macchina virtuale di Azure nel [portale di Azure](https://portal.azure.com) e:

- Si esamina la pagina "Configurazione" e si verifica che l'Identità del servizio gestito sia attivata = "Sì".
- Si esamina la pagina "Estensioni" e si verifica che l'estensione dell'Identità del servizio gestito sia stata distribuita correttamente.

Se una delle due opzioni è errata, è necessario ridistribuire l'Identità del servizio gestito nella risorsa o risolvere il problema di distribuzione.

## <a name="related-content"></a>Contenuti correlati

- Per una panoramica dell'Identità di servizio gestito, vedere [Panoramica dell'Identità di servizio gestito](msi-overview.md).
- Per abilitare l'Identità del servizio gestito in una macchina virtuale di Azure, vedere [Configurare un'Identità del servizio gestito della macchina virtuale di Azure mediante PowerShell](msi-qs-configure-powershell-windows-vm.md).

Usare la sezione dei commenti seguente per fornire commenti e suggerimenti utili per migliorare e organizzare i contenuti disponibili.

