---
title: Usare CLI di Azure per gestire i diritti di accesso di Azure AD per i dati blob e coda con RBAC - archiviazione di Azure
description: Usare il comando di Azure per assegnare l'accesso a contenitori e le code di controllo di accesso basato sui ruoli (RBAC). Archiviazione di Azure supporta i ruoli RBAC predefiniti e personalizzati per l'autenticazione tramite Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/21/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: de8cb99ae5db93c2438a9ea982ad1c6c9324b47f
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58450134"
---
# <a name="grant-access-to-azure-blob-and-queue-data-with-rbac-using-azure-cli"></a>Concedere l'accesso a dati blob e di Accodamento di Azure con RBAC tramite la CLI di Azure

Azure Active Directory (Azure AD) autorizza diritti di accesso a risorse protette tramite il [controllo degli accessi in base al ruolo](../../role-based-access-control/overview.md). Archiviazione di Azure definisce un set di ruoli RBAC predefiniti che includono set di autorizzazioni utilizzate per accedere ai dati blob o di Accodamento comuni. 

Quando viene assegnato un ruolo RBAC a un'entità di sicurezza di Azure AD, Azure concede l'accesso a tali risorse per tale entità di sicurezza. È possibile definire l'ambito dell'accesso a livello di sottoscrizione, gruppo di risorse, account di archiviazione o singolo contenitore o coda. Un'entità di sicurezza di Azure AD può essere un utente, un gruppo, un'entità servizio dell'applicazione, o un [identità per le risorse di Azure gestito](../../active-directory/managed-identities-azure-resources/overview.md).

Questo articolo descrive come usare Azure CLI per elencare i ruoli RBAC predefiniti e assegnarle agli utenti. Per altre informazioni sull'uso della riga di comando di Azure, vedere [interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure).

## <a name="rbac-roles-for-blobs-and-queues"></a>Ruoli Controllo degli accessi in base al ruolo per BLOB e code

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>Determinare l'ambito di risorse 

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="list-available-rbac-roles"></a>Elencare i ruoli RBAC disponibili

Per elencare i ruoli RBAC predefiniti disponibili con CLI di Azure, usare il [elenco di definizioni di ruolo az](/cli/azure/role/definition#az-role-definition-list) comando:

```azurecli-interactive
az role definition list --out table
```

Si noterà i ruoli predefiniti di dati di archiviazione di Azure elencati insieme altri ruoli predefiniti per Azure:

```Example
Storage Blob Data Contributor             Allows for read, write and delete access to Azure Storage blob containers and data
Storage Blob Data Owner                   Allows for full access to Azure Storage blob containers and data, including assigning POSIX access control.
Storage Blob Data Reader                  Allows for read access to Azure Storage blob containers and data
Storage Queue Data Contributor            Allows for read, write, and delete access to Azure Storage queues and queue messages
Storage Queue Data Message Processor      Allows for peek, receive, and delete access to Azure Storage queue messages
Storage Queue Data Message Sender         Allows for sending of Azure Storage queue messages
Storage Queue Data Reader                 Allows for read access to Azure Storage queues and queue messages
```

## <a name="assign-an-rbac-role-to-a-user"></a>Assegnare un ruolo RBAC a un utente

Per assegnare un ruolo RBAC a un utente, usare il [assegnazione di ruolo az creare](/cli/azure/role/assignment#az-role-assignment-create) comando. Il formato del comando può differire a seconda dell'ambito di assegnazione. Gli esempi seguenti illustrano come assegnare un ruolo a un utente a vari ambiti.

### <a name="container-scope"></a>Ambito del contenitore

Per assegnare un ruolo con ambito limitato a un contenitore, specificare una stringa che contiene l'ambito del contenitore per il `--scope` parametro. L'ambito per un contenitore è nel formato:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container-name>
```

L'esempio seguente assegna il **collaboratore ai dati di archiviazione Blob** ruolo a un utente, come ambito un contenitore denominato *esempio-container*. Assicurarsi di sostituire i valori di esempio e i valori segnaposto racchiusi tra parentesi con i propri valori: 

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/sample-container"
```

### <a name="queue-scope"></a>Ambito di coda

Per assegnare un ruolo con ambito limitato a una coda, specificare una stringa che contiene l'ambito di coda per il `--scope` parametro. L'ambito per una coda è nel formato:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/<queue-name>
```

L'esempio seguente assegna il **collaboratore ai dati di archiviazione coda** ruolo a un utente, nell'ambito di una coda denominata *coda di esempio*. Assicurarsi di sostituire i valori di esempio e i valori segnaposto racchiusi tra parentesi con i propri valori: 

```azurecli-interactive
az role assignment create \
    --role "Storage Queue Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/sample-queue"
```

### <a name="storage-account-scope"></a>Ambito di account di archiviazione

Per assegnare un ruolo con ambito limitato all'account di archiviazione, specificare l'ambito della risorsa account di archiviazione per il `--scope` parametro. L'ambito per un account di archiviazione è nel formato:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

Nell'esempio seguente viene illustrato come assegnare i **lettore di dati Blob di archiviazione** ruolo a un utente a livello di account di archiviazione. Assicurarsi di sostituire i valori di esempio con i propri valori: 

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Reader" \
    --assignee <email> \
    --scope "/subscriptions/<subscription-id>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/storagesamples"
```

### <a name="resource-group-scope"></a>Ambito di gruppo di risorse

Per assegnare un ruolo nell'ambito del gruppo di risorse, specificare il nome del gruppo di risorse o l'ID per il `--resource-group` parametro. L'esempio seguente assegna il **lettore dati di archiviazione coda** ruolo a un utente a livello di gruppo di risorse. Assicurarsi di sostituire i valori di esempio e i valori segnaposto racchiusi tra parentesi con i propri valori: 

```azurecli-interactive
az role assignment create \
    --role "Storage Queue Data Reader" \
    --assignee <email> \
    --resource-group sample-resource-group
```

### <a name="subscription-scope"></a>Ambito della sottoscrizione

Per assegnare un ruolo con ambito limitato alla sottoscrizione, specificare l'ambito per la sottoscrizione per il `--scope` parametro. L'ambito per una sottoscrizione è nel formato:

```
/subscriptions/<subscription>
```

Nell'esempio seguente viene illustrato come assegnare i **lettore di dati Blob di archiviazione** ruolo a un utente a livello di account di archiviazione. Assicurarsi di sostituire i valori di esempio con i propri valori: 

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Reader" \
    --assignee <email> \
    --scope "/subscriptions/<subscription-id>"
```

## <a name="next-steps"></a>Passaggi successivi

- [Gestire l'accesso alle risorse di Azure usando il controllo degli accessi in base al ruolo e Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)
- [Concedere l'accesso a dati blob e di Accodamento di Azure con RBAC tramite Azure PowerShell](storage-auth-aad-rbac-powershell.md)
- [Concedere l'accesso a Azure blob e Accodamento dei dati con accessi nel portale di Azure](storage-auth-aad-rbac-portal.md)
