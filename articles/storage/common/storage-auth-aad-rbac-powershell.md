---
title: Usare PowerShell per assegnare un ruolo RBAC per l'accesso ai dati
titleSuffix: Azure Storage
description: Informazioni su come usare PowerShell per assegnare autorizzazioni a un'entità di sicurezza Azure Active Directory con il controllo degli accessi in base al ruolo (RBAC). Archiviazione di Azure supporta ruoli RBAC predefiniti e personalizzati per l'autenticazione tramite Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 57d30803f20d17ee31c3d42d9a26e04c1b0832b6
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74892018"
---
# <a name="use-powershell-to-assign-an-rbac-role-for-access-to-blob-and-queue-data"></a>Usare PowerShell per assegnare un ruolo RBAC per l'accesso ai dati BLOB e di Accodamento

Azure Active Directory (Azure AD) autorizza diritti di accesso a risorse protette tramite il [controllo degli accessi in base al ruolo](../../role-based-access-control/overview.md). Archiviazione di Azure definisce un set di ruoli di controllo degli accessi in base al ruolo predefiniti che includono un set comune di autorizzazioni usate per accedere a contenitori o code.

Quando un ruolo RBAC viene assegnato a un'entità di sicurezza Azure AD, Azure concede l'accesso a tali risorse per l'entità di sicurezza. È possibile definire l'ambito dell'accesso a livello di sottoscrizione, gruppo di risorse, account di archiviazione o singolo contenitore o coda. Un Azure AD entità di sicurezza può essere un utente, un gruppo, un'entità servizio dell'applicazione o un' [identità gestita per le risorse di Azure](../../active-directory/managed-identities-azure-resources/overview.md).

Questo articolo descrive come usare Azure PowerShell per elencare i ruoli RBAC predefiniti e assegnarli agli utenti. Per ulteriori informazioni sull'utilizzo di Azure PowerShell, vedere [Panoramica di Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="rbac-roles-for-blobs-and-queues"></a>Ruoli Controllo degli accessi in base al ruolo per BLOB e code

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>Determinare l'ambito della risorsa

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="list-available-rbac-roles"></a>Elencare i ruoli RBAC disponibili

Per elencare i ruoli RBAC predefiniti disponibili con Azure PowerShell, usare il comando [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) :

```powershell
Get-AzRoleDefinition | FT Name, Description
```

Verranno visualizzati i ruoli di dati di archiviazione di Azure predefiniti elencati insieme ad altri ruoli predefiniti per Azure:

```Example
Storage Blob Data Contributor             Allows for read, write and delete access to Azure Storage blob containers and data
Storage Blob Data Owner                   Allows for full access to Azure Storage blob containers and data, including assigning POSIX access control.
Storage Blob Data Reader                  Allows for read access to Azure Storage blob containers and data
Storage Queue Data Contributor            Allows for read, write, and delete access to Azure Storage queues and queue messages
Storage Queue Data Message Processor      Allows for peek, receive, and delete access to Azure Storage queue messages
Storage Queue Data Message Sender         Allows for sending of Azure Storage queue messages
Storage Queue Data Reader                 Allows for read access to Azure Storage queues and queue messages
```

## <a name="assign-an-rbac-role-to-a-security-principal"></a>Assegnare un ruolo RBAC a un'entità di sicurezza

Per assegnare un ruolo RBAC a un'entità di sicurezza, usare il comando [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) . Il formato del comando può variare in base all'ambito dell'assegnazione. Gli esempi seguenti illustrano come assegnare un ruolo a un utente in vari ambiti, ma è possibile usare lo stesso comando per assegnare un ruolo a un'entità di sicurezza.

### <a name="container-scope"></a>Ambito del contenitore

Per assegnare un ruolo con ambito a un contenitore, specificare una stringa contenente l'ambito del contenitore per il parametro `--scope`. L'ambito di un contenitore è nel formato seguente:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container-name>
```

L'esempio seguente assegna il ruolo di **collaboratore dati BLOB di archiviazione** a un utente, con ambito a un contenitore denominato *Sample-Container*. Assicurarsi di sostituire i valori di esempio e i valori segnaposto tra parentesi con valori personalizzati: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/sample-container"
```

### <a name="queue-scope"></a>Ambito della coda

Per assegnare un ruolo con ambito a una coda, specificare una stringa contenente l'ambito della coda per il parametro `--scope`. L'ambito di una coda è nel formato seguente:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/<queue-name>
```

Nell'esempio seguente il ruolo di **collaboratore dei dati della coda di archiviazione** viene assegnato a un utente, con ambito a una coda denominata *Sample-Queue*. Assicurarsi di sostituire i valori di esempio e i valori segnaposto tra parentesi con valori personalizzati: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Queue Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/sample-queue"
```

### <a name="storage-account-scope"></a>Ambito dell'account di archiviazione

Per assegnare un ruolo nell'ambito dell'account di archiviazione, specificare l'ambito della risorsa dell'account di archiviazione per il parametro `--scope`. L'ambito di un account di archiviazione è nel formato seguente:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

Nell'esempio seguente viene illustrato come definire l'ambito del ruolo **lettore dati BLOB di archiviazione** per un utente a livello di account di archiviazione. Assicurarsi di sostituire i valori di esempio con i propri valori: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Reader" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

### <a name="resource-group-scope"></a>Ambito del gruppo di risorse

Per assegnare un ruolo con ambito al gruppo di risorse, specificare il nome o l'ID del gruppo di risorse per il parametro `--resource-group`. L'esempio seguente assegna il ruolo di **lettore dati della coda di archiviazione** a un utente a livello del gruppo di risorse. Assicurarsi di sostituire i valori di esempio e i segnaposto tra parentesi con valori personalizzati: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Queue Data Reader" `
    -ResourceGroupName "sample-resource-group"
```

### <a name="subscription-scope"></a>Ambito della sottoscrizione

Per assegnare un ruolo con ambito alla sottoscrizione, specificare l'ambito per la sottoscrizione per il parametro `--scope`. L'ambito di una sottoscrizione è nel formato seguente:

```
/subscriptions/<subscription>
```

Nell'esempio seguente viene illustrato come assegnare il ruolo **lettore dati BLOB di archiviazione** a un utente a livello di account di archiviazione. Assicurarsi di sostituire i valori di esempio con i propri valori: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Reader" `
    -Scope  "/subscriptions/<subscription>"
```

## <a name="next-steps"></a>Passaggi successivi

- [Gestire l'accesso alle risorse di Azure usando il controllo degli accessi in base al ruolo e Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)
- [Concedere l'accesso ai dati di code e BLOB di Azure con il controllo degli accessi in base al ruolo tramite l'interfaccia della riga di comando di Azure](storage-auth-aad-rbac-cli.md)
- [Concedere l'accesso ai dati di code e BLOB di Azure con il controllo degli accessi in base al ruolo nel portale di Azure](storage-auth-aad-rbac-portal.md)
