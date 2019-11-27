---
title: Assegnare un accesso a identità gestite a una risorsa tramite l'interfaccia della riga di comando di Azure-Azure AD
description: Istruzioni dettagliate su come assegnare a un'identità gestita in una risorsa l'accesso a un'altra risorsa tramite l'interfaccia della riga di comando di Azure.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2017
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: b241ac223fd1eb9df2b0a914726d8f37df5f4d88
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74547365"
---
# <a name="assign-a-managed-identity-access-to-a-resource-using-azure-cli"></a>Assegnare a un'identità gestita l'accesso a una risorsa tramite l'interfaccia della riga di comando di Azure

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Dopo aver configurato una risorsa di Azure con un'identità gestita, è possibile concedere all'identità gestita l'accesso a un'altra risorsa, proprio come per qualsiasi entità di sicurezza. In questo esempio viene illustrato come concedere a un'identità gestita della macchina virtuale di Azure o di un set di scalabilità di macchine virtuali di Azure l'accesso a un account di archiviazione di Azure mediante l'interfaccia della riga di comando di Azure.

## <a name="prerequisites"></a>Prerequisiti

- Se non si ha familiarità con le identità gestite per le risorse di Azure, vedere la [sezione sulla panoramica](overview.md). **Assicurarsi di conoscere la [differenza tra identità assegnata dal sistema e identità gestita assegnata dall'utente](overview.md#how-does-the-managed-identities-for-azure-resources-work)** .
- Se non si ha un account Azure, [registrarsi per ottenere un account gratuito](https://azure.microsoft.com/free/) prima di continuare.
- Per eseguire gli esempi di script dell'interfaccia della riga di comando, sono disponibili tre opzioni:
    - Usare [Azure Cloud Shell](../../cloud-shell/overview.md) dal portale di Azure (vedere la sezione successiva).
    - Usare Azure Cloud Shell incorporato tramite il pulsante "Prova", che si trova nell'angolo in alto a destra di ogni blocco di codice.
    - [Installare la versione più recente dell'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) se si preferisce usare una console dell'interfaccia della riga di comando locale. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="use-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Usare il controllo degli accessi in base al ruolo per assegnare a un'identità gestita l'accesso a un'altra risorsa

Dopo aver abilitato l'identità gestita in una risorsa di Azure, ad esempio una [macchina virtuale di Azure](qs-configure-cli-windows-vm.md) o un [set di scalabilità di macchine virtuali di Azure](qs-configure-cli-windows-vmss.md): 

1. Se si usa l'interfaccia della riga di comando di Azure in una console locale, accedere prima di tutto ad Azure tramite [az login](/cli/azure/reference-index#az-login). Usare un account associato alla sottoscrizione di Azure in cui si vuole distribuire la macchina virtuale o il set di scalabilità di macchine virtuali:

   ```azurecli-interactive
   az login
   ```

2. In questo esempio viene concesso alla macchina virtuale di Azure l'accesso a un account di archiviazione. Prima di tutto usare il comando [az resource list](/cli/azure/resource/#az-resource-list) per ottenere l'entità servizio per la macchina virtuale denominata myVM:

   ```azurecli-interactive
   spID=$(az resource list -n myVM --query [*].identity.principalId --out tsv)
   ```
   Per un set di scalabilità di macchine virtuali di Azure il comando è uguale. In questo caso, tuttavia, si ottiene l'entità servizio per il set di scalabilità di macchine virtuali denominato "DevTestVMSS":
   
   ```azurecli-interactive
   spID=$(az resource list -n DevTestVMSS --query [*].identity.principalId --out tsv)
   ```

3. Dopo aver ottenuto l'ID dell'entità servizio, usare il comando [az role assignment create](/cli/azure/role/assignment#az-role-assignment-create) per concedere alla macchina virtuale o al set di scalabilità di macchine virtuali l'accesso "Lettura" a un account di archiviazione denominato "myStorageAcct":

   ```azurecli-interactive
   az role assignment create --assignee $spID --role 'Reader' --scope /subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/myStorageAcct
   ```

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica delle identità gestite per le risorse di Azure](overview.md)
- Per abilitare l'identità gestita in una macchina virtuale di Azure, vedere [Configurare le identità gestite per le risorse di Azure in una macchina virtuale di Azure tramite l'interfaccia della riga di comando di Azure](qs-configure-cli-windows-vm.md).
- Per abilitare l'identità gestita in un set di scalabilità di macchine virtuali di Azure, vedere [Configurare identità gestite per le risorse di Azure in un set di scalabilità di macchine virtuali tramite l'interfaccia della riga di comando di Azure](qs-configure-cli-windows-vmss.md).
