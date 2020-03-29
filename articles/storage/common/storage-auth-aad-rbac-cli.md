---
title: Usare l'interfaccia della riga di comando di Azure per assegnare un ruolo Controllo degli accessi in base al ruolo per l'accesso ai datiUse Azure CLI to assign
titleSuffix: Azure Storage
description: Informazioni su come usare l'interfaccia della riga di comando di Azure per assegnare autorizzazioni a un'entità di sicurezza di Azure Active Directory con controllo degli accessi in base al ruolo. Archiviazione di Azure supporta ruoli RBAC predefiniti e personalizzati per l'autenticazione tramite Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: abe35f3193e2d7ff9a949ca7cd330cb58da2b78c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74891969"
---
# <a name="use-azure-cli-to-assign-an-rbac-role-for-access-to-blob-and-queue-data"></a>Usare l'interfaccia della riga di comando di Azure per assegnare un ruolo Controllo degli accessi al controllo degli accessi ai dati BLOB e codaUse Azure CLI to assign an RBAC role for access to blob and queue data

Azure Active Directory (Azure AD) autorizza diritti di accesso a risorse protette tramite il [controllo degli accessi in base al ruolo](../../role-based-access-control/overview.md). Archiviazione di Azure definisce un set di ruoli RBAC predefiniti che includono set comuni di autorizzazioni usate per accedere ai dati BLOB o di coda.

Quando un ruolo Controllo degli accessi in base al ruolo viene assegnato a un'entità di sicurezza di Azure AD, Azure concede l'accesso a tali risorse per tale entità di sicurezza. È possibile definire l'ambito dell'accesso a livello di sottoscrizione, gruppo di risorse, account di archiviazione o singolo contenitore o coda. Un'entità di sicurezza di Azure AD può essere un utente, un gruppo, un'entità servizio dell'applicazione o [un'identità gestita per](../../active-directory/managed-identities-azure-resources/overview.md)le risorse di Azure.An Azure AD security principal may be a user, a group, an application service principal, or a managed identity for Azure resources.

Questo articolo descrive come usare l'interfaccia della riga di comando di Azure per elencare i ruoli RBAC predefiniti e assegnarli agli utenti. Per altre informazioni sull'uso dell'interfaccia della riga di comando di Azure, vedere Interfaccia della riga di comando di Azure.For more information about using Azure CLI, see [Azure Command-Line Interface (CLI)](https://docs.microsoft.com/cli/azure).

## <a name="rbac-roles-for-blobs-and-queues"></a>Ruoli Controllo degli accessi in base al ruolo per BLOB e code

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>Determinare l'ambito delle risorse

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="list-available-rbac-roles"></a>Elencare i ruoli RBAC disponibili

Per elencare i ruoli RBAC predefiniti disponibili con l'interfaccia della riga di comando di Azure, usare il comando az role definition list:To list available built-in RBAC roles with Azure CLI, use the [az role definition list command:](/cli/azure/role/definition#az-role-definition-list)

```azurecli-interactive
az role definition list --out table
```

Verranno elencati i ruoli dati di Archiviazione di Azure incorporati, insieme ad altri ruoli predefiniti per Azure:You'll see the built-in Azure Storage data roles listed, together with other built-in roles for Azure:

```Example
Storage Blob Data Contributor             Allows for read, write and delete access to Azure Storage blob containers and data
Storage Blob Data Owner                   Allows for full access to Azure Storage blob containers and data, including assigning POSIX access control.
Storage Blob Data Reader                  Allows for read access to Azure Storage blob containers and data
Storage Queue Data Contributor            Allows for read, write, and delete access to Azure Storage queues and queue messages
Storage Queue Data Message Processor      Allows for peek, receive, and delete access to Azure Storage queue messages
Storage Queue Data Message Sender         Allows for sending of Azure Storage queue messages
Storage Queue Data Reader                 Allows for read access to Azure Storage queues and queue messages
```

## <a name="assign-an-rbac-role-to-a-security-principal"></a>Assegnare un ruolo Controllo degli accessi in base al ruolo a un'entità di sicurezzaAssign an RBAC role to a security principal

Per assegnare un ruolo Controllo degli accessi in base al ruolo a un'entità di sicurezza, usare il comando [az role assignment create.](/cli/azure/role/assignment#az-role-assignment-create) Il formato del comando può variare in base all'ambito dell'assegnazione. Negli esempi seguenti viene illustrato come assegnare un ruolo a un utente in vari ambiti, ma è possibile utilizzare lo stesso comando per assegnare un ruolo a qualsiasi entità di sicurezza.

### <a name="container-scope"></a>Ambito del contenitore

Per assegnare un ruolo con ambito a un contenitore, `--scope` specificare una stringa contenente l'ambito del contenitore per il parametro. L'ambito per un contenitore è nel formato seguente:The scope for a container is in the form:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container>
```

Nell'esempio seguente viene assegnato il ruolo **Collaboratore dati BLOB di archiviazione** a un utente, con ambito al livello del contenitore. Assicurarsi di sostituire i valori di esempio e i valori segnaposto tra parentesi quadre con valori personalizzati:

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container>"
```

### <a name="queue-scope"></a>Ambito della coda

Per assegnare un ruolo con ambito a una coda, specificare una stringa contenente l'ambito della coda per il `--scope` parametro. L'ambito di una coda è nel formato:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/<queue>
```

Nell'esempio seguente viene assegnato il ruolo **Collaboratore dati coda** di archiviazione a un utente, con ambito al livello della coda. Assicurarsi di sostituire i valori di esempio e i valori segnaposto tra parentesi quadre con valori personalizzati:

```azurecli-interactive
az role assignment create \
    --role "Storage Queue Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/<queue>"
```

### <a name="storage-account-scope"></a>Ambito dell'account di archiviazioneStorage account scope

Per assegnare un ruolo con ambito all'account di archiviazione, `--scope` specificare l'ambito della risorsa dell'account di archiviazione per il parametro. L'ambito per un account di archiviazione è nel formato:The scope for a storage account is in the form:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

Nell'esempio seguente viene illustrato come assegnare il ruolo **Lettore dati BLOB** di archiviazione a un utente a livello dell'account di archiviazione. Assicurarsi di sostituire i valori di esempio con i propri valori:

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Reader" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

### <a name="resource-group-scope"></a>Ambito del gruppo di risorse

Per assegnare un ruolo con ambito al gruppo di risorse, specificare il nome o l'ID del gruppo di risorse per il `--resource-group` parametro. Nell'esempio seguente viene assegnato il ruolo **Lettore dati coda** di archiviazione a un utente a livello del gruppo di risorse. Assicurarsi di sostituire i valori di esempio e i valori segnaposto tra parentesi quadre con valori personalizzati:

```azurecli-interactive
az role assignment create \
    --role "Storage Queue Data Reader" \
    --assignee <email> \
    --resource-group <resource-group>
```

### <a name="subscription-scope"></a>Ambito della sottoscrizione

Per assegnare un ruolo con ambito alla sottoscrizione, `--scope` specificare l'ambito per la sottoscrizione per il parametro. L'ambito di una sottoscrizione è nel formato seguente:The scope for a subscription is in the form:

```
/subscriptions/<subscription>
```

Nell'esempio seguente viene illustrato come assegnare il ruolo **Lettore dati BLOB** di archiviazione a un utente a livello dell'account di archiviazione. Assicurarsi di sostituire i valori di esempio con valori personalizzati: 

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Reader" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>"
```

## <a name="next-steps"></a>Passaggi successivi

- [Gestire l'accesso alle risorse di Azure usando il controllo degli accessi in base al ruolo e Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)
- [Concedere l'accesso ai dati BLOB e coda di Azure con RBAC usando Azure PowerShellGrant access to Azure blob and queue data with RBAC using Azure PowerShell](storage-auth-aad-rbac-powershell.md)
- [Concedere l'accesso ai dati di code e BLOB di Azure con il controllo degli accessi in base al ruolo nel portale di Azure](storage-auth-aad-rbac-portal.md)
