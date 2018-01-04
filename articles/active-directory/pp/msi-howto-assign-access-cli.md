---
title: Come assegnare un accesso MSI utente assegnato a una risorsa di Azure mediante Azure CLI
description: Procedura dettagliata le istruzioni per l'assegnazione di un file MSI assegnati dall'utente in una risorsa, l'accesso a un'altra risorsa, tramite l'interfaccia CLI di Azure.
services: active-directory
documentationcenter: 
author: bryanLa
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/22/2017
ms.author: bryanla
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 732fc1981bdf95e7548ea0ebe0ca8ece00f483ce
ms.sourcegitcommit: a648f9d7a502bfbab4cd89c9e25aa03d1a0c412b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/22/2017
---
# <a name="assign-a-user-assigned-managed-service-identity-msi-access-to-a-resource-using-azure-cli"></a>Assegnare un accesso di identità di servizio gestiti (MSI) utente assegnato a una risorsa, utilizzando l'interfaccia CLI di Azure

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Dopo aver creato un file MSI assegnati dall'utente, è possibile assegnare l'accesso a file MSI in un'altra risorsa, analogamente a qualsiasi entità di sicurezza. In questo esempio viene illustrato come concedere un accesso MSI utente assegnato a un account di archiviazione di Azure, mediante Azure CLI.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

Per eseguire gli esempi di script CLI in questa esercitazione, sono disponibili due opzioni:

- Utilizzare [Azure Cloud Shell](~/articles/cloud-shell/overview.md) dal portale di Azure o tramite il pulsante "Provalo", si trova nell'angolo superiore destro di ogni blocco di codice.
- [Installare la versione più recente di CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.23 o versione successiva) se si preferisce utilizzare una console locale di CLI. Accedere quindi a Azure tramite [accesso az](/cli/azure/#login). Utilizzare un account che viene associato alla sottoscrizione di Azure in cui si desidera distribuire il file MSI assegnati dall'utente e macchina virtuale:

   ```azurecli
   az login
   ```

## <a name="use-rbac-to-assign-the-msi-access-to-another-resource"></a>Usare il controllo degli accessi in base al ruolo per assegnare a un'Identità del servizio gestito l'accesso a un'altra risorsa

Per usare un file MSI con una risorsa dell'host (ad esempio, una macchina virtuale), per l'accesso di accesso o una risorsa, il file MSI devono innanzitutto essere concessi accesso alle risorse tramite l'assegnazione di ruolo. È possibile farlo prima di associare il file MSI, con l'host, o dopo. Per la procedura sulla creazione e l'associazione a una macchina virtuale, vedere [configurare un file MSI assegnati dall'utente per una macchina virtuale con Azure CLI](msi-qs-configure-cli-windows-vm.md).

Nell'esempio seguente, un file MSI assegnati dall'utente di accedere a un account di archiviazione.  

1. Per concedere l'accesso a file MSI, è necessario l'ID client (noto anche come ID app) dell'entità servizio del file MSI. Il client ID viene fornito dal [creare identità az](/cli/azure/identity#az_identity_create), durante il provisioning di file MSI e il relativo dell'entità servizio (illustrato di seguito per riferimento):

   ```azurecli-interactive
   az identity create -g rgPrivate -n msiServiceApp
   ```

   Una risposta corretta contiene l'ID client dell'entità, nel servizio del MSI assegnati dall'utente di `clientId` proprietà:

   ```json
   {
        "clientId": "9391e5b1-dada-4a8z-834a-43ad44f296bl",
        "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/90z696ff-5efa-4909-a64d-f1b616f423ll/resourcegroups/rgPrivate/providers/Microsoft.ManagedIdentity/userAssignedIdentities/msiServiceApp/credentials?tid=733a8f0p-ec41-4e69-8adz-971fc4b533bl&oid=z4baa4fc-fce4-4202-8250-5fb359abblll&aid=9391e5b1-dada-4a8z-834a-43ad44f296bl",
        "id": "/subscriptions/90z696ff-5efa-4909-a64d-f1b616f423ll/resourcegroups/rgPrivate/providers/Microsoft.ManagedIdentity/userAssignedIdentities/msiServiceApp",
        "location": "westcentralus",
        "name": "msiServiceApp",
        "principalId": "z4baa4fc-fce4-4202-8250-5fb359abblll",
        "resourceGroup": "rgPrivate",
        "tags": {},
        "tenantId": "733a8f0p-ec41-4e69-8adz-971fc4b533bl",
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities"
   }
   ```

2. Una volta che è nota l'ID client, utilizzare [di creazione dell'assegnazione di ruolo az](/cli/azure/role/assignment#az_role_assignment_create) per assegnare l'accesso a file MSI in un'altra risorsa. Assicurarsi di usare l'ID client per il `--assignee` parametro e il gruppo di risorse e l'ID sottoscrizione corrispondente, name, come restituito quando si esegue `az identity create`. Qui il file MSI è assegnato l'accesso "Lettura" a un account di archiviazione denominato "myStorageAcct":

   ```azurecli-interactive
   az role assignment create --assignee 9391e5b1-dada-4a8z-834a-43ad44f296bl --role Reader --scope /subscriptions/90z696ff-5efa-4909-a64d-f1b616f423ll/resourcegroups/rgPrivate/providers/Microsoft.Storage/storageAccounts/myStorageAcct
   ```

   Una risposta corretta sarà simile al seguente output:

   ```json
   {
        "id": "/subscriptions/90z696ff-5efa-4909-a64d-f1b616f423ll/resourcegroups/rgPrivate/providers/Microsoft.Storage/storageAccounts/myStorageAcct/providers/Microsoft.Authorization/roleAssignments/f4766864-9493-43c6-91d7-abd131c3c017",
        "name": "f4766864-9493-43c6-91d7-abd131c3c017",
        "properties": {
            "additionalProperties": {
            "createdBy": null,
            "createdOn": "2017-12-21T20:49:37.5590544Z",
            "updatedBy": "pd78b21f-17a4-41az-b7db-9aadec3376bl",
            "updatedOn": "2017-12-21T20:49:37.5590544Z"
            },
        "principalId": "z4baa4fc-fce4-4202-8250-5fb359abblll",
        "roleDefinitionId": "/subscriptions/90z696ff-5efa-4909-a64d-f1b616f423ll/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
        "scope": "/subscriptions/90z696ff-5efa-4909-a64d-f1b616f423ll/resourcegroups/rgPrivate/providers/Microsoft.Storage/storageAccounts/myStorageAcct"
        },
        "resourceGroup": "rgPrivate",
        "type": "Microsoft.Authorization/roleAssignments"
   }
   ```

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica dell'Identità di servizio gestito, vedere [Panoramica dell'Identità di servizio gestito](msi-overview.md).
- Per abilitare un MSI assegnati dall'utente in una macchina virtuale di Azure, vedere [configurare un utente assegnato gestiti servizio identità (MSI) per una macchina virtuale con Azure CLI](msi-qs-configure-cli-windows-vm.md).

Usare la sezione dei commenti seguente per fornire commenti e suggerimenti utili per migliorare e organizzare i contenuti disponibili.

