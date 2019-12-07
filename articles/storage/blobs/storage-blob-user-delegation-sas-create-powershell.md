---
title: Usare PowerShell per creare una firma di accesso condiviso di delega utente per un contenitore o un BLOB
titleSuffix: Azure Storage
description: Informazioni su come creare una firma di accesso condiviso dell'utente (anteprima) con Azure Active Directory credenziali usando PowerShell.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: blobs
ms.openlocfilehash: 5f4947921a77f2bc94d1810c9b1d1951431d3d71
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74892516"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-powershell-preview"></a>Creare una firma di accesso condiviso di delega utente per un contenitore o un BLOB con PowerShell (anteprima)

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

Questo articolo illustra come usare le credenziali Azure Active Directory (Azure AD) per creare una firma di accesso condiviso di delega utente per un contenitore o un BLOB con Azure PowerShell (anteprima).

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="install-the-preview-module"></a>Installare il modulo di anteprima

Per usare PowerShell per creare una firma di accesso condiviso di delega utente, è necessario prima installare il modulo AZ. Storage 1.3.1-Preview. Per installare il modulo, seguire questa procedura:

1. Disinstallare eventuali installazioni precedenti di Azure PowerShell:

    - Rimuovere da Windows eventuali installazioni precedenti di Azure PowerShell usando l'impostazione **App e funzionalità** in **Impostazioni**.
    - Rimuovere tutti i moduli di **Azure** da `%Program Files%\WindowsPowerShell\Modules`.

1. Assicurarsi di avere la versione più recente di PowerShellGet installata. Aprire una finestra di Windows PowerShell ed eseguire i seguenti comandi per installare la versione più recente:

    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```

1. Chiudere e riaprire la finestra di PowerShell dopo l'installazione di PowerShellGet.

1. Installare la versione più recente di Azure PowerShell:

    ```powershell
    Install-Module Az –Repository PSGallery –AllowClobber
    ```

1. Installare un modulo di anteprima di archiviazione di Azure che supporta la firma di accesso condiviso dell'utente:

    ```powershell
    Install-Module Az.Storage `
        –Repository PSGallery `
        -RequiredVersion 1.3.1-preview `
        –AllowPrerelease `
        –AllowClobber `
        –Force
    ```

1. Chiudere e riaprire la finestra di PowerShell.

Poiché PowerShell carica il modulo AZ. storage più recente per impostazione predefinita, potrebbe essere necessario caricare in modo esplicito il modulo 1.3.1-Preview quando si avvia la console. Per caricare in modo esplicito il modulo di anteprima, eseguire il comando [Import-Module](/powershell/module/microsoft.powershell.core/import-module) :

```powershell
Import-Module Az.Storage -RequiredVersion 1.3.1
```

Per ulteriori informazioni sull'installazione di Azure PowerShell, vedere [Install Azure PowerShell with PowerShellGet](/powershell/azure/install-az-ps).

## <a name="sign-in-to-azure-powershell-with-azure-ad"></a>Accedere a Azure PowerShell con Azure AD

Chiamare il comando [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) per accedere con l'account Azure ad:

```powershell
Connect-AzAccount
```

Per altre informazioni sull'accesso con PowerShell, vedere [accedere con Azure PowerShell](/powershell/azure/authenticate-azureps).

## <a name="assign-permissions-with-rbac"></a>Assegnare autorizzazioni con RBAC

Per creare una firma di accesso condiviso di delega utente da Azure PowerShell, è necessario assegnare all'account Azure AD usato per accedere a PowerShell un ruolo che includa l'azione **Microsoft. storage/storageAccounts/blobServices/generateUserDelegationKey** . Questa autorizzazione consente a tale account Azure AD di richiedere la *chiave di delega dell'utente*. La chiave di delega utente viene usata per firmare la firma di accesso condiviso della delega utente. Il ruolo che fornisce l'azione **Microsoft. storage/storageAccounts/blobServices/generateUserDelegationKey** deve essere assegnato a livello dell'account di archiviazione, del gruppo di risorse o della sottoscrizione. Per ulteriori informazioni sulle autorizzazioni RBAC per la creazione di una firma di accesso condiviso di delega utente, vedere la sezione **assegnare autorizzazioni con RBAC** in creare una firma di accesso condiviso di [delega utente](/rest/api/storageservices/create-user-delegation-sas).

Se non si dispone di autorizzazioni sufficienti per assegnare i ruoli di controllo degli accessi in base al ruolo a un Azure AD entità di sicurezza, potrebbe essere necessario richiedere al proprietario o all'amministratore dell'account di assegnare le autorizzazioni necessarie.

Nell'esempio seguente viene assegnato il ruolo di **collaboratore dati BLOB di archiviazione** , che include l'azione **Microsoft. storage/storageAccounts/blobServices/generateUserDelegationKey** . L'ambito del ruolo è a livello dell'account di archiviazione.

È necessario ricordare di sostituire i valori segnaposto tra parentesi uncinate con i valori personalizzati:

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

Per altre informazioni sui ruoli predefiniti che includono l'azione **Microsoft. storage/storageAccounts/blobServices/generateUserDelegationKey** , vedere [ruoli predefiniti per le risorse di Azure](../../role-based-access-control/built-in-roles.md).

## <a name="use-azure-ad-credentials-to-secure-a-sas"></a>Usare le credenziali Azure AD per proteggere una firma di accesso condiviso

Quando si crea una firma di accesso condiviso di delega utente con Azure PowerShell, viene creata in modo implicito la chiave di delega utente utilizzata per firmare la firma di accesso condiviso. L'ora di inizio e l'ora di scadenza specificate per la firma di accesso condiviso vengono usate anche come ora di inizio e ora di scadenza per la chiave di delega utente. 

Poiché l'intervallo massimo di validità della chiave di delega utente è di 7 giorni dalla data di inizio, è necessario specificare un'ora di scadenza per la firma di accesso condiviso entro 7 giorni dall'ora di inizio. La firma di accesso condiviso non è valida dopo la scadenza della chiave di delega dell'utente. Pertanto, una firma di accesso condiviso con una scadenza superiore a 7 giorni sarà ancora valida solo per 7 giorni.

Per creare una firma di accesso condiviso di delega utente per un contenitore o un BLOB con Azure PowerShell, creare prima di tutto un nuovo oggetto di contesto di archiviazione di Azure, specificando il parametro `-UseConnectedAccount`. Il parametro `-UseConnectedAccount` specifica che il comando crea l'oggetto di contesto nell'account Azure AD con cui è stato effettuato l'accesso.

È necessario ricordare di sostituire i valori segnaposto tra parentesi uncinate con i valori personalizzati:

```powershell
$ctx = New-AzStorageContext -StorageAccountName <storage-account> -UseConnectedAccount
```

### <a name="create-a-user-delegation-sas-for-a-container"></a>Creare una firma di accesso condiviso di delega utente per un contenitore

Per restituire un token di firma di accesso condiviso di delega utente per un contenitore, chiamare il comando [New-AzStorageContainerSASToken](/powershell/module/az.storage/new-azstoragecontainersastoken) , passando l'oggetto di contesto di archiviazione di Azure creato in precedenza.

Nell'esempio seguente viene restituito un token SAS di delega utente per un contenitore. Ricordarsi di sostituire i valori segnaposto tra parentesi con valori personalizzati:

```powershell
New-AzStorageContainerSASToken -Context $ctx `
    -Name <container> `
    -Permission racwdl `
    -ExpiryTime <date-time>
```

Il token SAS della delega utente restituito sarà simile al seguente:

```
?sv=2018-11-09&sr=c&sig=<sig>&skoid=<skoid>&sktid=<sktid>&skt=2019-08-05T22%3A24%3A36Z&ske=2019-08-07T07%3A
00%3A00Z&sks=b&skv=2018-11-09&se=2019-08-07T07%3A00%3A00Z&sp=rwdl
```

### <a name="create-a-user-delegation-sas-for-a-blob"></a>Creare una firma di accesso condiviso di delega utente per un BLOB

Per restituire un token di firma di accesso condiviso della delega utente per un BLOB, chiamare il comando [New-AzStorageBlobSASToken](/powershell/module/az.storage/new-azstorageblobsastoken) , passando l'oggetto di contesto di archiviazione di Azure creato in precedenza.

La sintassi seguente restituisce una firma di accesso condiviso di delega utente per un BLOB. Nell'esempio viene specificato il parametro `-FullUri`, che restituisce l'URI del BLOB con il token di firma di accesso condiviso accodato. Ricordarsi di sostituire i valori segnaposto tra parentesi con valori personalizzati:

```powershell
New-AzStorageBlobSASToken -Context $ctx `
    -Container <container> `
    -Blob <blob> `
    -Permission racwd `
    -ExpiryTime <date-time>
    -FullUri
```

L'URI SAS della delega utente restituito sarà simile al seguente:

```
https://storagesamples.blob.core.windows.net/sample-container/blob1.txt?sv=2018-11-09&sr=b&sig=<sig>&skoid=<skoid>&sktid=<sktid>&skt=2019-08-06T21%3A16%3A54Z&ske=2019-08-07T07%3A00%3A00Z&sks=b&skv=2018-11-09&se=2019-08-07T07%3A00%3A00Z&sp=racwd
```

> [!NOTE]
> Una firma di accesso condiviso di delega utente non supporta la definizione di autorizzazioni con criteri di accesso archiviati.

## <a name="revoke-a-user-delegation-sas"></a>Revocare una firma di accesso condiviso di delega utente

Per revocare una firma di accesso condiviso di delega utente da Azure PowerShell, chiamare il comando **Revoke-AzStorageAccountUserDelegationKeys** . Questo comando revoca tutte le chiavi di delega utente associate all'account di archiviazione specificato. Eventuali firme di accesso condiviso associate a tali chiavi vengono invalidate.

È necessario ricordare di sostituire i valori segnaposto tra parentesi uncinate con i valori personalizzati:

```powershell
Revoke-AzStorageAccountUserDelegationKeys -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
```

> [!IMPORTANT]
> Sia la chiave di delega utente che le assegnazioni di ruolo RBAC vengono memorizzate nella cache da archiviazione di Azure, pertanto potrebbe verificarsi un ritardo tra il momento in cui si avvia il processo di revoca e quando una firma di accesso condiviso dell'utente esistente diventa non valida.

## <a name="next-steps"></a>Passaggi successivi

- [Creare una firma di accesso condiviso per la delega utente (API REST)](/rest/api/storageservices/create-user-delegation-sas)
- [Operazione di ottenimento della chiave di delega utente](/rest/api/storageservices/get-user-delegation-key)
