---
title: Assegnare un accesso a identità gestite a una risorsa tramite l'interfaccia della riga di comando di Azure-Azure AD
description: Istruzioni dettagliate per l'assegnazione di un'identità gestita in una risorsa, l'accesso a un'altra risorsa tramite l'interfaccia della riga di comando di Azure.
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
ms.date: 12/06/2017
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurecli
ms.openlocfilehash: 13c69dda1e300bcff95b6a017fdeb308a6bbf3a4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90969249"
---
# <a name="assign-a-managed-identity-access-to-a-resource-using-azure-cli"></a>Assegnare a un'identità gestita l'accesso a una risorsa tramite l'interfaccia della riga di comando di Azure

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Dopo aver configurato una risorsa di Azure con un'identità gestita, è possibile concedere all'identità gestita l'accesso a un'altra risorsa, proprio come per qualsiasi entità di sicurezza. In questo esempio viene illustrato come concedere a un'identità gestita della macchina virtuale di Azure o di un set di scalabilità di macchine virtuali di Azure l'accesso a un account di archiviazione di Azure mediante l'interfaccia della riga di comando di Azure.

## <a name="prerequisites"></a>Prerequisiti

- Se non si ha familiarità con le identità gestite per le risorse di Azure, vedere la [sezione sulla panoramica](overview.md). **Assicurarsi di conoscere la [differenza tra identità assegnata dal sistema e identità gestita assegnata dall'utente](overview.md#managed-identity-types)**.
- Se non si ha un account Azure, [registrarsi per ottenere un account gratuito](https://azure.microsoft.com/free/) prima di continuare.
- Per eseguire gli script di esempio, sono disponibili due opzioni:
    - Usare [Azure Cloud Shell](../../cloud-shell/overview.md), che è possibile aprire con il pulsante **Prova** nell'angolo in alto a destra dei blocchi di codice.
    - Eseguire gli script in locale installando l'ultima versione dell'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli), quindi accedere ad Azure usando [az login](/cli/azure/reference-index#az-login). Usare un account associato alla sottoscrizione di Azure in cui creare le risorse.

## <a name="use-azure-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Usare il controllo degli accessi in base al ruolo di Azure

Dopo aver abilitato l'identità gestita in una risorsa di Azure, ad esempio una [macchina virtuale di Azure](qs-configure-cli-windows-vm.md) o un [set di scalabilità di macchine virtuali di Azure](qs-configure-cli-windows-vmss.md): 

1. In questo esempio viene concesso alla macchina virtuale di Azure l'accesso a un account di archiviazione. Prima di tutto usare il comando [az resource list](/cli/azure/resource/#az-resource-list) per ottenere l'entità servizio per la macchina virtuale denominata myVM:

   ```azurecli-interactive
   spID=$(az resource list -n myVM --query [*].identity.principalId --out tsv)
   ```
   Per un set di scalabilità di macchine virtuali di Azure il comando è uguale. In questo caso, tuttavia, si ottiene l'entità servizio per il set di scalabilità di macchine virtuali denominato "DevTestVMSS":
   
   ```azurecli-interactive
   spID=$(az resource list -n DevTestVMSS --query [*].identity.principalId --out tsv)
   ```

1. Dopo aver ottenuto l'ID dell'entità servizio, usare il comando [az role assignment create](/cli/azure/role/assignment#az-role-assignment-create) per concedere alla macchina virtuale o al set di scalabilità di macchine virtuali l'accesso "Lettura" a un account di archiviazione denominato "myStorageAcct":

   ```azurecli-interactive
   az role assignment create --assignee $spID --role 'Reader' --scope /subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/myStorageAcct
   ```

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica delle identità gestite per le risorse di Azure](overview.md)
- Per abilitare l'identità gestita in una macchina virtuale di Azure, vedere [Configurare le identità gestite per le risorse di Azure in una macchina virtuale di Azure tramite l'interfaccia della riga di comando di Azure](qs-configure-cli-windows-vm.md).
- Per abilitare l'identità gestita in un set di scalabilità di macchine virtuali di Azure, vedere [Configurare identità gestite per le risorse di Azure in un set di scalabilità di macchine virtuali tramite l'interfaccia della riga di comando di Azure](qs-configure-cli-windows-vmss.md).
