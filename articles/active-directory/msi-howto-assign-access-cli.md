---
title: "Come assegnare all'Identità del servizio gestito l'accesso a una risorsa di Azure tramite l'interfaccia della riga di comando di Azure"
description: "Istruzioni dettagliate per assegnare a un'Identità del servizio gestito in una risorsa l'accesso a un'altra risorsa, tramite l'interfaccia della riga di comando di Azure."
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
ms.date: 09/25/2017
ms.author: daveba
ms.openlocfilehash: 90a7ec3059b6905e4aa660f538c299f3e8dedaae
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2018
---
# <a name="assign-a-managed-service-identity-msi-access-to-a-resource-using-azure-cli"></a>Assegnare a un'Identità del servizio gestito, ovvero MSI, l'accesso a una risorsa tramite l'interfaccia della riga di comando di Azure

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

Dopo aver configurato una risorsa di Azure con un'Identità del servizio gestito, è possibile concedere a un'Identità del servizio gestito l'accesso a un'altra risorsa, analogamente a qualsiasi entità di sicurezza. In questo esempio viene illustrato come concedere a un'identità del servizio gestito della macchina virtuale di Azure o a un set di scalabilità di macchine virtuali di Azure l'accesso a un account di archiviazione di Azure mediante l'interfaccia della riga di comando di Azure.

## <a name="prerequisites"></a>prerequisiti

[!INCLUDE [msi-qs-configure-prereqs](../../includes/active-directory-msi-qs-configure-prereqs.md)]

Per eseguire gli esempi di script dell'interfaccia della riga di comando, sono disponibili tre opzioni:

- Usare [Azure Cloud Shell](../cloud-shell/overview.md) dal portale di Azure (vedere la sezione successiva).
- Usare Azure Cloud Shell incorporato tramite il pulsante "Prova", che si trova nell'angolo in alto a destra di ogni blocco di codice.
- [Installare la versione più recente dell'interfaccia della riga di comando 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 o successiva) se si preferisce usare una console dell'interfaccia della riga di comando locale. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="use-rbac-to-assign-the-msi-access-to-another-resource"></a>Usare il controllo degli accessi in base al ruolo per assegnare a un'Identità del servizio gestito l'accesso a un'altra risorsa

Dopo aver abilitato l'identità del servizio gestito in una risorsa di Azure, ad esempio una [macchina virtuale di Azure](msi-qs-configure-cli-windows-vm.md) o un [set di scalabilità di macchine virtuali di Azure](msi-qs-configure-cli-windows-vmss.md): 

1. Se si usa l'interfaccia della riga di comando di Azure in una console locale, accedere prima di tutto ad Azure tramite [az login](/cli/azure/#az_login). Usare un account associato alla sottoscrizione di Azure in cui si vuole distribuire la macchina virtuale o il set di scalabilità di macchine virtuali:

   ```azurecli-interactive
   az login
   ```

2. In questo esempio viene concesso alla macchina virtuale di Azure l'accesso a un account di archiviazione. Prima di tutto usare il comando [az resource list](/cli/azure/resource/#az_resource_list) per ottenere l'entità servizio per la macchina virtuale denominata "myVM":

   ```azurecli-interactive
   spID=$(az resource list -n myVM --query [*].identity.principalId --out tsv)
   ```
   Per un set di scalabilità di macchine virtuali di Azure il comando è uguale. In questo caso, tuttavia, si ottiene l'entità servizio per il set di scalabilità di macchine virtuali denominato "DevTestVMSS":
   
   ```azurecli-interactive
   spID=$(az resource list -n DevTestVMSS --query [*].identity.principalId --out tsv)
   ```

3. Dopo aver ottenuto l'ID dell'entità servizio, usare il comando [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) per concedere alla macchina virtuale o al set di scalabilità di macchine virtuali l'accesso "Lettura" a un account di archiviazione denominato "myStorageAcct":

   ```azurecli-interactive
   az role assignment create --assignee $spID --role 'Reader' --scope /subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/myStorageAcct
   ```

## <a name="troubleshooting"></a>risoluzione dei problemi

Se l'Identità del servizio gestito per la risorsa non viene visualizzata nell'elenco delle identità disponibili, verificare che sia stata abilitata correttamente. In questo caso, è possibile tornare alla macchina virtuale di Azure o al set di scalabilità di macchine virtuali di Azure nel [portale di Azure](https://portal.azure.com) e quindi eseguire le operazioni seguenti:

- Si esamina la pagina "Configurazione" e si verifica che l'Identità del servizio gestito sia attivata = "Sì".
- Nella pagina "Estensioni" assicurarsi che l'identità del servizio gestito sia stata distribuita correttamente (la pagina **Estensioni** non è disponibile per un set di scalabilità di macchine virtuali di Azure).

Se una delle due opzioni è errata, è necessario ridistribuire l'Identità del servizio gestito nella risorsa o risolvere il problema di distribuzione.

## <a name="related-content"></a>Contenuti correlati

- Per una panoramica dell'Identità di servizio gestito, vedere [Panoramica dell'Identità di servizio gestito](msi-overview.md).
- Per abilitare l'identità del servizio gestito in una macchina virtuale di Azure, vedere [Configurare un'identità del servizio gestito della macchina virtuale di Azure mediante l'interfaccia della riga di comando di Azure](msi-qs-configure-cli-windows-vm.md).
- Per abilitare l'identità del servizio gestito in un set di scalabilità di macchine virtuali di Azure, vedere [Configurare un'identità del servizio gestito in un set di scalabilità di macchine virtuali di Azure tramite il portale di Azure](msi-qs-configure-portal-windows-vmss.md)

Usare la sezione dei commenti seguente per fornire commenti e suggerimenti utili per migliorare e organizzare i contenuti disponibili.

