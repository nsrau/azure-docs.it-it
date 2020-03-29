---
title: Usare l'interfaccia della riga di comando di Azure per creare una sAS di delega utente per un contenitore o un BLOBUse Azure CLI to create a user delegation SAS for a container or blob
titleSuffix: Azure Storage
description: Informazioni su come creare una sAS di delega utente con le credenziali di Azure Active Directory usando l'interfaccia della riga di comando di Azure.Learn how to create a user delegation SAS with Azure Active Directory credentials by using Azure CLI.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: blobs
ms.openlocfilehash: e1a81b25042501a166cee122279d21e3702cd419
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75371990"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-the-azure-cli"></a>Creare una chiave di accesso client di delega utente per un contenitore o un BLOB con l'interfaccia della riga di comando di AzureCreate a user delegation SAS for a container or blob with the Azure CLI

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

Questo articolo illustra come usare le credenziali di Azure Active Directory (Azure AD) per creare una provider di servizi di accesso condiviso di delega utente per un contenitore o un BLOB con l'interfaccia della riga di comando di Azure.This article shows how to use Azure Active Directory (Azure AD) credentials to create a user delegation SAS for a container or blob with the Azure CLI.

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="install-the-latest-version-of-the-azure-cli"></a>Installare la versione più recente dell'interfaccia della riga di comando di Azure

To use the Azure CLI to secure a SAS with Azure AD credentials, first make sure that you have installed the latest version of Azure CLI. Per altre informazioni sull'installazione dell'interfaccia della riga di comando di Azure, vedere [Installare l'interfaccia della riga di comando](/cli/azure/install-azure-cli)di Azure.For more information about installing the Azure CLI, see Install the Azure CLI .

Per creare una sAS di delega utente usando l'interfaccia della riga di comando di Azure, assicurarsi di aver installato la versione 2.0.78 o successiva. Per verificare la versione `az --version` installata, utilizzare il comando .

## <a name="sign-in-with-azure-ad-credentials"></a>Accedere con le credenziali di Azure ADSign in with Azure AD credentials

Accedere all'interfaccia della riga di comando di Azure con le credenziali di Azure AD. Per altre informazioni, vedere [Accedere tramite l'interfaccia della riga di comando di Azure](/cli/azure/authenticate-azure-cli).

## <a name="assign-permissions-with-rbac"></a>Assegnare autorizzazioni con controllo degli accessi in base al ruoloAssign permissions

Per creare una firma di accesso figlio di sola utente da Azure PowerShell, all'account azure AD usato per accedere all'interfaccia della riga di comando di Azure deve essere assegnato un ruolo che include l'azione **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey.To** create a user delegation SAS from Azure PowerShell, the Azure AD account used to sign into Azure CLI must be assigned a role that includes the Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey action. Questa autorizzazione consente all'account di Azure AD di richiedere la chiave di *delega dell'utente.* La chiave di delega utente viene utilizzata per firmare la firma di accesso livello di delega utente. Il ruolo che fornisce l'azione **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey** deve essere assegnato a livello dell'account di archiviazione, del gruppo di risorse o della sottoscrizione.

Se non si dispone di autorizzazioni sufficienti per assegnare ruoli RBAC a un'entità di sicurezza di Azure AD, potrebbe essere necessario chiedere al proprietario o all'amministratore dell'account di assegnare le autorizzazioni necessarie.

Nell'esempio seguente viene assegnato il ruolo **Collaboratore dati BLOB di archiviazione,** che include l'azione **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey.** L'ambito del ruolo è a livello di account di archiviazione.

È necessario ricordare di sostituire i valori segnaposto tra parentesi uncinate con i valori personalizzati:

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

Per altre informazioni sui ruoli predefiniti che includono l'azione **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey,** vedere Ruoli predefiniti per le risorse di Azure.For more information about the built-in roles that include the Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey action, see [Built-in roles for Azure resources.](../../role-based-access-control/built-in-roles.md)

## <a name="use-azure-ad-credentials-to-secure-a-sas"></a>Usare le credenziali di Azure AD per proteggere una provider di servizi di accesso condivisoUse Azure AD credentials to secure a SAS

Quando si crea una firma di accesso sas di delega utente con l'interfaccia della riga di comando di Azure, la chiave di delega utente usata per firmare la firma di accesso base viene creata automaticamente. L'ora di inizio e l'ora di scadenza specificate per la chiamata di accesso locale vengono utilizzate anche come ora di inizio e ora di scadenza per la chiave di delega utente.

Poiché l'intervallo massimo di validità della chiave di delega utente è di 7 giorni dalla data di inizio, è necessario specificare un'ora di scadenza per la firma di accesso locale entro 7 giorni dall'ora di inizio. La firma di accesso locale non è valida dopo la scadenza della chiave di delega utente, pertanto una firma di accesso locale con un tempo di scadenza superiore a 7 giorni sarà comunque valida solo per 7 giorni.

Quando si crea una sAS di delega utente, `--auth-mode login` sono necessari. `--as-user parameters` Specificare *l'account di accesso* per il `--auth-mode` parametro in modo che le richieste effettuate ad Archiviazione di Azure siano autorizzate con le credenziali di Azure AD. Specificare `--as-user` il parametro per indicare che la sAS restituita deve essere una sAS di delega utente.

### <a name="create-a-user-delegation-sas-for-a-container"></a>Creare una chiave di accesso client di delega utente per un contenitoreCreate a user delegation SAS for a container

Per creare una delega utente SAS per un contenitore con l'interfaccia della riga di comando di Azure, chiamare il comando [generate-sas del contenitore di archiviazione az.](/cli/azure/storage/container#az-storage-container-generate-sas)

Le autorizzazioni supportate per una combinazione di accesso sAS di delega utente in un contenitore includono Aggiunta, Creazione, Eliminazione, Elenco, Lettura e Scrittura.Supported permissions for a user delegation SAS on a container include Add, Create, Delete, List, Read, and Write. Le autorizzazioni possono essere specificate in modo scenoso o combinato. Per ulteriori informazioni su queste autorizzazioni, vedere [Creare una sAS](/rest/api/storageservices/create-user-delegation-sas)di delega utente .

Nell'esempio seguente viene restituito un token di firma di accesso condiviso di delega utente per un contenitore. Ricordarsi di sostituire i valori segnaposto tra parentesi quadre con valori personalizzati:

```azurecli-interactive
az storage container generate-sas \
    --account-name <storage-account> \
    --name <container> \
    --permissions acdlrw \
    --expiry <date-time> \
    --auth-mode login \
    --as-user
```

Il token di firma di accesso condiviso della delega utente restituito sarà simile al:The user delegation SAS token returned will be similar to:

```
se=2019-07-27&sp=r&sv=2018-11-09&sr=c&skoid=<skoid>&sktid=<sktid>&skt=2019-07-26T18%3A01%3A22Z&ske=2019-07-27T00%3A00%3A00Z&sks=b&skv=2018-11-09&sig=<signature>
```

### <a name="create-a-user-delegation-sas-for-a-blob"></a>Creare una sAS di delega utente per un BLOBCreate a user delegation SAS for a blob

Per creare una richiesta di accesso sAS di delega utente per un BLOB con l'interfaccia della riga di comando di Azure, chiamare il comando [generate-sas del BLOB di archiviazione az.](/cli/azure/storage/blob#az-storage-blob-generate-sas)

Le autorizzazioni supportate per una combinazione di accesso sAS di delega utente in un BLOB includono Add, Create, Delete, Read e Write.Supported permissions for a user delegation SAS on a blob include Add, Create, Delete, Read, and Write. Le autorizzazioni possono essere specificate in modo scenoso o combinato. Per ulteriori informazioni su queste autorizzazioni, vedere [Creare una sAS](/rest/api/storageservices/create-user-delegation-sas)di delega utente .

La sintassi seguente restituisce una sAS di delega utente per un BLOB. L'esempio specifica `--full-uri` il parametro, che restituisce l'URI del BLOB con il token di firma di accesso condiviso aggiunto. Ricordarsi di sostituire i valori segnaposto tra parentesi quadre con valori personalizzati:

```azurecli-interactive
az storage blob generate-sas \
    --account-name <storage-account> \
    --container-name <container> \
    --name <blob> \
    --permissions acdrw \
    --expiry <date-time> \
    --auth-mode login \
    --as-user
    --full-uri
```

L'URI di accesso condiviso della delega utente restituito sarà simile al:

```
https://storagesamples.blob.core.windows.net/sample-container/blob1.txt?se=2019-08-03&sp=rw&sv=2018-11-09&sr=b&skoid=<skoid>&sktid=<sktid>&skt=2019-08-02T2
2%3A32%3A01Z&ske=2019-08-03T00%3A00%3A00Z&sks=b&skv=2018-11-09&sig=<signature>
```

> [!NOTE]
> Una classe di accesso sAS di delega utente non supporta la definizione di autorizzazioni con criteri di accesso archiviati.

## <a name="revoke-a-user-delegation-sas"></a>Revocare una licenza di accesso sAS di delega utenteRevoke a user delegation SAS

Per revocare una chiamata di accesso sAS di delega utente dall'interfaccia della riga di comando di Azure, chiamare il comando [az storage account revoke-delegation-keys.](/cli/azure/storage/account#az-storage-account-revoke-delegation-keys) Questo comando revoca tutte le chiavi di delega utente associate all'account di archiviazione specificato. Tutte le firme di accesso condiviso associate a tali chiavi vengono invalidate.

È necessario ricordare di sostituire i valori segnaposto tra parentesi uncinate con i valori personalizzati:

```azurecli-interactive
az storage account revoke-delegation-keys \
    --name <storage-account> \
    --resource-group <resource-group>
```

> [!IMPORTANT]
> Sia la chiave di delega utente che le assegnazioni di ruolo RBAC vengono memorizzate nella cache da Archiviazione di Azure, pertanto potrebbe verificarsi un ritardo tra l'avvio del processo di revoca e il momento in cui una firma di accesso client di delega utente esistente non è più valida.

## <a name="next-steps"></a>Passaggi successivi

- [Creare una sAS (GH) di delega utenteCreate a user delegation SAS (REST API)](/rest/api/storageservices/create-user-delegation-sas)
- [Operazione Get User Delegation Key](/rest/api/storageservices/get-user-delegation-key)
