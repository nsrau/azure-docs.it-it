---
title: Usare l'interfaccia della riga di comando di Azure per creare una firma di accesso condiviso per un contenitore o un BLOB
titleSuffix: Azure Storage
description: Informazioni su come creare una firma di accesso condiviso di delega utente (anteprima) con Azure Active Directory credenziali usando l'interfaccia della riga di comando di Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: blobs
ms.openlocfilehash: 85f49799472c92770cc8a503a5a1be0b496387f7
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74892550"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-the-azure-cli-preview"></a>Creare una firma di accesso condiviso di delega utente per un contenitore o BLOB con l'interfaccia della riga di comando di Azure (anteprima)

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

Questo articolo illustra come usare le credenziali Azure Active Directory (Azure AD) per creare una firma di accesso condiviso di delega utente per un contenitore o un BLOB con l'interfaccia della riga di comando di Azure (anteprima).

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="install-the-latest-version-of-the-azure-cli"></a>Installare la versione più recente dell'interfaccia della riga di comando di Azure

Per usare l'interfaccia della riga di comando di Azure per proteggere una firma di accesso condiviso con Azure AD credenziali, assicurarsi prima di installare la versione più recente dell'interfaccia della riga di comando di Azure. Per altre informazioni sull'installazione dell'interfaccia della riga di comando di Azure, vedere [Install the Azure CLI](/cli/azure/install-azure-cli).

## <a name="sign-in-with-azure-ad-credentials"></a>Accedi con credenziali di Azure AD

Accedere all'interfaccia della riga di comando di Azure con le credenziali Azure AD. Per altre informazioni, vedere [Accedere tramite l'interfaccia della riga di comando di Azure](/cli/azure/authenticate-azure-cli).

## <a name="assign-permissions-with-rbac"></a>Assegnare autorizzazioni con RBAC

Per creare una firma di accesso condiviso di delega utente da Azure PowerShell, è necessario assegnare all'account Azure AD usato per accedere all'interfaccia della riga di comando di Azure un ruolo che includa l'azione **Microsoft. storage/storageAccounts/blobServices/generateUserDelegationKey** . Questa autorizzazione consente a tale account Azure AD di richiedere la *chiave di delega dell'utente*. La chiave di delega utente viene usata per firmare la firma di accesso condiviso della delega utente. Il ruolo che fornisce l'azione **Microsoft. storage/storageAccounts/blobServices/generateUserDelegationKey** deve essere assegnato a livello dell'account di archiviazione, del gruppo di risorse o della sottoscrizione.

Se non si dispone di autorizzazioni sufficienti per assegnare i ruoli di controllo degli accessi in base al ruolo a un Azure AD entità di sicurezza, potrebbe essere necessario richiedere al proprietario o all'amministratore dell'account di assegnare le autorizzazioni necessarie.

Nell'esempio seguente viene assegnato il ruolo di **collaboratore dati BLOB di archiviazione** , che include l'azione **Microsoft. storage/storageAccounts/blobServices/generateUserDelegationKey** . L'ambito del ruolo è a livello dell'account di archiviazione.

È necessario ricordare di sostituire i valori segnaposto tra parentesi uncinate con i valori personalizzati:

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

Per altre informazioni sui ruoli predefiniti che includono l'azione **Microsoft. storage/storageAccounts/blobServices/generateUserDelegationKey** , vedere [ruoli predefiniti per le risorse di Azure](../../role-based-access-control/built-in-roles.md).

## <a name="use-azure-ad-credentials-to-secure-a-sas"></a>Usare le credenziali Azure AD per proteggere una firma di accesso condiviso

Quando si crea una firma di accesso condiviso dell'utente con l'interfaccia della riga di comando di Azure, la chiave di delega utente usata per firmare la firma di accesso condiviso viene creata in modo implicito. L'ora di inizio e l'ora di scadenza specificate per la firma di accesso condiviso vengono usate anche come ora di inizio e ora di scadenza per la chiave di delega utente.

Poiché l'intervallo massimo di validità della chiave di delega utente è di 7 giorni dalla data di inizio, è necessario specificare un'ora di scadenza per la firma di accesso condiviso entro 7 giorni dall'ora di inizio. La firma di accesso condiviso non è valida dopo la scadenza della chiave di delega dell'utente. Pertanto, una firma di accesso condiviso con una scadenza superiore a 7 giorni sarà ancora valida solo per 7 giorni.

Quando si crea una firma di accesso condiviso di delega utente, è necessario il `--auth-mode login` e il `--as-user parameters`. Specificare l' *account di accesso* per il parametro `--auth-mode` in modo che le richieste effettuate all'archiviazione di Azure siano autorizzate con le credenziali Azure ad. Specificare il parametro `--as-user` per indicare che la firma di accesso condiviso restituita deve essere una firma di accesso condiviso dell'utente.

### <a name="create-a-user-delegation-sas-for-a-container"></a>Creare una firma di accesso condiviso di delega utente per un contenitore

Per creare una firma di accesso condiviso di delega utente per un contenitore con l'interfaccia della riga di comando di Azure, chiamare il comando [AZ Storage container generate-SAS](/cli/azure/storage/container#az-storage-container-generate-sas) .

Le autorizzazioni supportate per una firma di accesso condiviso di delega utente in un contenitore includono Aggiungi, crea, Elimina, elenca, lettura e scrittura. È possibile specificare le autorizzazioni singolarmente o combinate. Per altre informazioni su queste autorizzazioni, vedere [creare una](/rest/api/storageservices/create-user-delegation-sas)firma di accesso condiviso di delega utente.

Nell'esempio seguente viene restituito un token SAS di delega utente per un contenitore. Ricordarsi di sostituire i valori segnaposto tra parentesi con valori personalizzati:

```azurecli-interactive
az storage container generate-sas \
    --account-name <storage-account> \
    --name <container> \
    --permissions acdlrw \
    --expiry <date-time> \
    --auth-mode login \
    --as-user
```

Il token SAS della delega utente restituito sarà simile al seguente:

```
se=2019-07-27&sp=r&sv=2018-11-09&sr=c&skoid=<skoid>&sktid=<sktid>&skt=2019-07-26T18%3A01%3A22Z&ske=2019-07-27T00%3A00%3A00Z&sks=b&skv=2018-11-09&sig=<signature>
```

### <a name="create-a-user-delegation-sas-for-a-blob"></a>Creare una firma di accesso condiviso di delega utente per un BLOB

Per creare una firma di accesso condiviso di delega utente per un BLOB con l'interfaccia della riga di comando di Azure, chiamare il comando [AZ storage BLOB generate-SAS](/cli/azure/storage/blob#az-storage-blob-generate-sas) .

Le autorizzazioni supportate per la firma di accesso condiviso di una delega utente in un BLOB includono Aggiungi, crea, Elimina, lettura e scrittura. È possibile specificare le autorizzazioni singolarmente o combinate. Per altre informazioni su queste autorizzazioni, vedere [creare una](/rest/api/storageservices/create-user-delegation-sas)firma di accesso condiviso di delega utente.

La sintassi seguente restituisce una firma di accesso condiviso di delega utente per un BLOB. Nell'esempio viene specificato il parametro `--full-uri`, che restituisce l'URI del BLOB con il token di firma di accesso condiviso accodato. Ricordarsi di sostituire i valori segnaposto tra parentesi con valori personalizzati:

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

L'URI SAS della delega utente restituito sarà simile al seguente:

```
https://storagesamples.blob.core.windows.net/sample-container/blob1.txt?se=2019-08-03&sp=rw&sv=2018-11-09&sr=b&skoid=<skoid>&sktid=<sktid>&skt=2019-08-02T2
2%3A32%3A01Z&ske=2019-08-03T00%3A00%3A00Z&sks=b&skv=2018-11-09&sig=<signature>
```

> [!NOTE]
> Una firma di accesso condiviso di delega utente non supporta la definizione di autorizzazioni con criteri di accesso archiviati.

## <a name="revoke-a-user-delegation-sas"></a>Revocare una firma di accesso condiviso di delega utente

Per revocare una firma di accesso condiviso di delega utente dall'interfaccia della riga di comando di Azure, chiamare il comando [AZ storage account Revoke-Delegation-Keys](/cli/azure/storage/account#az-storage-account-revoke-delegation-keys) . Questo comando revoca tutte le chiavi di delega utente associate all'account di archiviazione specificato. Eventuali firme di accesso condiviso associate a tali chiavi vengono invalidate.

È necessario ricordare di sostituire i valori segnaposto tra parentesi uncinate con i valori personalizzati:

```azurecli-interactive
az storage account revoke-delegation-keys \
    --name <storage-account> \
    --resource-group <resource-group>
```

> [!IMPORTANT]
> Sia la chiave di delega utente che le assegnazioni di ruolo RBAC vengono memorizzate nella cache da archiviazione di Azure, pertanto potrebbe verificarsi un ritardo tra il momento in cui si avvia il processo di revoca e quando una firma di accesso condiviso dell'utente esistente diventa non valida.

## <a name="next-steps"></a>Passaggi successivi

- [Creare una firma di accesso condiviso per la delega utente (API REST)](/rest/api/storageservices/create-user-delegation-sas)
- [Operazione di ottenimento della chiave di delega utente](/rest/api/storageservices/get-user-delegation-key)
