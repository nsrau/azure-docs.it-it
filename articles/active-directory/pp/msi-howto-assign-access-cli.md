---
title: "Come assegnare a un'identità del servizio gestito assegnata dall'utente l'accesso a una risorsa di Azure tramite l'interfaccia della riga di comando di Azure"
description: "Istruzioni dettagliate per assegnare a un'identità del servizio gestito assegnata dall'utente in una risorsa l'accesso a un'altra risorsa tramite l'interfaccia della riga di comando di Azure."
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
ms.date: 12/22/2017
ms.author: daveba
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 8cdfd80f436a9ef39426da9d3f2f76ae59677847
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="assign-a-user-assigned-managed-service-identity-msi-access-to-a-resource-using-azure-cli"></a>Assegnare a un'identità del servizio gestito assegnata dall'utente l'accesso a una risorsa tramite l'interfaccia della riga di comando di Azure

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Dopo aver creato un'identità del servizio gestito assegnata dall'utente è possibile concedere all'identità del servizio gestito l'accesso a un'altra risorsa, analogamente a qualsiasi entità di sicurezza. In questo esempio viene illustrato come concedere a un'identità del servizio gestito assegnata dall'utente l'accesso a un account di archiviazione di Azure tramite l'interfaccia della riga di comando di Azure.

## <a name="prerequisites"></a>prerequisiti

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

Per eseguire gli esempi di script dell'interfaccia della riga di comando in questa esercitazione sono disponibili due opzioni:

- Usare [Azure Cloud Shell](~/articles/cloud-shell/overview.md) tramite il portale di Azure o il pulsante "Prova", che si trova nell'angolo in alto a destra di ogni blocco di codice.
- [Installare la versione più recente dell'interfaccia della riga di comando 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.23 o successiva) se si preferisce usare una console dell'interfaccia della riga di comando locale. Accedere quindi ad Azure tramite [az login](/cli/azure/reference-index#az_login). Usare un account associato alla sottoscrizione di Azure in cui si desidera distribuire la macchina virtuale e l'identità del servizio gestito assegnata dall'utente:

   ```azurecli
   az login
   ```

## <a name="use-rbac-to-assign-the-msi-access-to-another-resource"></a>Usare il controllo degli accessi in base al ruolo per assegnare a un'Identità del servizio gestito l'accesso a un'altra risorsa

Per usare un'identità del servizio gestito con una risorsa dell'host, ad esempio una macchina virtuale, è necessario che all'identità del servizio gestito venga prima concesso l'accesso alla risorsa tramite l'assegnazione di ruolo. È possibile farlo prima associando l'identità del servizio gestito all'host o dopo. Per la procedura completa per la creazione e l'associazione a una macchina virtuale, vedere [Configurare un'identità del servizio gestito assegnata dall'utente per una macchina virtuale tramite l'interfaccia della riga di comando di Azure](msi-qs-configure-cli-windows-vm.md).

Nell'esempio seguente all'identità del servizio gestito assegnata dall'utente viene concesso l'accesso a un account di archiviazione.  

1. Per concedere l'accesso a un'identità del servizio gestito, è necessario l'ID client (noto anche come ID app) dell'entità servizio dell'identità del servizio gestito. L'ID client viene fornito tramite [az identity create](/cli/azure/identity#az_identity_create), dopo il provisioning dell'identità del servizio gestito e della relativa entità servizio (illustrato di seguito per riferimento):

   ```azurecli-interactive
   az identity create -g rgPrivate -n msiServiceApp
   ```

   Una risposta con esito positivo contiene l'ID client dell'entità servizio dell'identità del servizio gestito assegnata dall'utente nella proprietà `clientId`:

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

2. Una volta che l'ID client è noto, usare [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) per assegnare all'identità del servizio gestito l'accesso a un'altra risorsa. Assicurarsi di usare l'ID client per il parametro `--assignee` e l'ID sottoscrizione e il nome del gruppo di risorse corrispondenti, come restituiti quando si esegue `az identity create`. In questo esempio all'identità del servizio gestito viene assegnato l'accesso in "Lettura" a un account di archiviazione denominato "myStorageAcct":

   ```azurecli-interactive
   az role assignment create --assignee 9391e5b1-dada-4a8z-834a-43ad44f296bl --role Reader --scope /subscriptions/90z696ff-5efa-4909-a64d-f1b616f423ll/resourcegroups/rgPrivate/providers/Microsoft.Storage/storageAccounts/myStorageAcct
   ```

   Una risposta con esito positivo sarà simile all'output seguente:

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
- Per abilitare un'identità del servizio gestito assegnata dall'utente in una macchina virtuale di Azure, vedere [Configurare un'identità del servizio gestito assegnata dall'utente per una macchina virtuale tramite l'interfaccia della riga di comando di Azure](msi-qs-configure-cli-windows-vm.md).

Usare la sezione dei commenti seguente per fornire commenti e suggerimenti utili per migliorare e organizzare i contenuti disponibili.

