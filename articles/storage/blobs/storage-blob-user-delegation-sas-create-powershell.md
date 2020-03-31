---
title: Usare PowerShell per creare una classe di accesso client di delega utente per un contenitore o un BLOBUse PowerShell to create a user delegation SAS for a container or blob
titleSuffix: Azure Storage
description: Informazioni su come creare una sAS di delega utente con le credenziali di Azure Active Directory tramite PowerShell.Learn how to create a user delegation SAS with Azure Active Directory credentials by using PowerShell.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/18/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: blobs
ms.openlocfilehash: 5250a27e6c5fcf012207f1edb95ad46c0aabfe63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79536174"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-powershell"></a>Creare una chiave di accesso client di delega utente per un contenitore o un BLOB con PowerShellCreate a user delegation SAS for a container or blob with PowerShell

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

Questo articolo illustra come usare le credenziali di Azure Active Directory (Azure AD) per creare una provider di servizi di accesso condiviso di delega utente per un contenitore o un BLOB con Azure PowerShell.This article shows how to use Azure Active Directory (Azure AD) credentials to create a user delegation SAS for a container or blob with Azure PowerShell.

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="install-the-powershell-module"></a>Installa il modulo PowerShell

Per creare una delega utente SAS con PowerShell, installare la versione 1.10.0 o successiva del modulo Az.Storage. Attenersi alla seguente procedura per installare la versione più recente del modulo:

1. Disinstallare eventuali installazioni precedenti di Azure PowerShell:

    - Rimuovere da Windows eventuali installazioni precedenti di Azure PowerShell usando l'impostazione **App e funzionalità** in **Impostazioni**.
    - Rimuovere **Azure** tutti i `%Program Files%\WindowsPowerShell\Modules`moduli di Azure da .

1. Assicurarsi di avere la versione più recente di PowerShellGet installata. Aprire una finestra di Windows PowerShell ed eseguire i seguenti comandi per installare la versione più recente:

    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```

1. Chiudere e riaprire la finestra di PowerShell dopo l'installazione di PowerShellGet.

1. Installare la versione più recente di Azure PowerShell:

    ```powershell
    Install-Module Az –Repository PSGallery –AllowClobber
    ```

1. Assicurarsi di aver installato Azure PowerShell versione 3.2.0 o successiva. Eseguire il comando seguente per installare la versione più recente del modulo PowerShell di Archiviazione di Azure:

    ```powershell
    Install-Module -Name Az.Storage -Repository PSGallery -Force
    ```

1. Chiudere e riaprire la finestra di PowerShell.

Per verificare quale versione del modulo Az.Storage è installata, eseguire il comando seguente:

```powershell
Get-Module -ListAvailable -Name Az.Storage -Refresh
```

Per altre informazioni sull'installazione di Azure PowerShell, vedere Installare Azure PowerShell con PowerShellGet.For more information about installing Azure [PowerShell, see Install Azure PowerShell with PowerShellGet.](/powershell/azure/install-az-ps)

## <a name="sign-in-to-azure-powershell-with-azure-ad"></a>Accedere ad Azure PowerShell con Azure ADSign in to Azure PowerShell with Azure AD

Chiamare il comando Connect-AzAccount per accedere con l'account Azure AD:Call the [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) command to sign in with your Azure AD account:

```powershell
Connect-AzAccount
```

Per altre informazioni sull'accesso con PowerShell, vedere Accedere con Azure PowerShell.For more information about signing in with PowerShell, see [Sign in with Azure PowerShell.](/powershell/azure/authenticate-azureps)

## <a name="assign-permissions-with-rbac"></a>Assegnare autorizzazioni con controllo degli accessi in base al ruoloAssign permissions

Per creare una firma di accesso sAS di delega utente da Azure PowerShell, all'account Azure AD usato per accedere a PowerShell deve essere assegnato un ruolo che include l'azione **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey.To** create a user delegation SAS from Azure PowerShell, the Azure AD account used to sign into PowerShell must be assigned a role that includes the Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey action. Questa autorizzazione consente all'account di Azure AD di richiedere la chiave di *delega dell'utente.* La chiave di delega utente viene utilizzata per firmare la firma di accesso livello di delega utente. Il ruolo che fornisce l'azione **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey** deve essere assegnato a livello dell'account di archiviazione, del gruppo di risorse o della sottoscrizione. Per ulteriori informazioni sulle autorizzazioni RBAC per la creazione di una sAS di delega utente, vedere la sezione **Assegnare autorizzazioni con controllo degli accessi in** base al ruolo in [Creare una sAS](/rest/api/storageservices/create-user-delegation-sas)di delega utente .

Se non si dispone di autorizzazioni sufficienti per assegnare ruoli RBAC a un'entità di sicurezza di Azure AD, potrebbe essere necessario chiedere al proprietario o all'amministratore dell'account di assegnare le autorizzazioni necessarie.

Nell'esempio seguente viene assegnato il ruolo **Collaboratore dati BLOB di archiviazione,** che include l'azione **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey.** L'ambito del ruolo è a livello di account di archiviazione.

È necessario ricordare di sostituire i valori segnaposto tra parentesi uncinate con i valori personalizzati:

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

Per altre informazioni sui ruoli predefiniti che includono l'azione **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey,** vedere Ruoli predefiniti per le risorse di Azure.For more information about the built-in roles that include the Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey action, see [Built-in roles for Azure resources.](../../role-based-access-control/built-in-roles.md)

## <a name="use-azure-ad-credentials-to-secure-a-sas"></a>Usare le credenziali di Azure AD per proteggere una provider di servizi di accesso condivisoUse Azure AD credentials to secure a SAS

Quando si crea una firma di accesso utente con Azure PowerShell, la chiave di delega utente usata per firmare la firma di accesso base viene creata automaticamente. L'ora di inizio e l'ora di scadenza specificate per la chiamata di accesso locale vengono utilizzate anche come ora di inizio e ora di scadenza per la chiave di delega utente. 

Poiché l'intervallo massimo di validità della chiave di delega utente è di 7 giorni dalla data di inizio, è necessario specificare un'ora di scadenza per la firma di accesso locale entro 7 giorni dall'ora di inizio. La firma di accesso locale non è valida dopo la scadenza della chiave di delega utente, pertanto una firma di accesso locale con un tempo di scadenza superiore a 7 giorni sarà comunque valida solo per 7 giorni.

Per creare una delega utente di accesso condiviso per un contenitore o un BLOB `-UseConnectedAccount` con Azure PowerShell, creare innanzitutto un nuovo oggetto di contesto di Archiviazione di Azure, specificando il parametro. Il `-UseConnectedAccount` parametro specifica che il comando crea l'oggetto di contesto nell'account Azure AD con cui è stato eseguito l'accesso.

È necessario ricordare di sostituire i valori segnaposto tra parentesi uncinate con i valori personalizzati:

```powershell
$ctx = New-AzStorageContext -StorageAccountName <storage-account> -UseConnectedAccount
```

### <a name="create-a-user-delegation-sas-for-a-container"></a>Creare una chiave di accesso client di delega utente per un contenitoreCreate a user delegation SAS for a container

Per restituire un token di firma di accesso condiviso di delega utente per un contenitore, chiamare il comando [New-AzStorageContainerSASToken,](/powershell/module/az.storage/new-azstoragecontainersastoken) passando l'oggetto di contesto di Archiviazione di Azure creato in precedenza.

Nell'esempio seguente viene restituito un token di firma di accesso condiviso di delega utente per un contenitore. Ricordarsi di sostituire i valori segnaposto tra parentesi quadre con valori personalizzati:

```powershell
New-AzStorageContainerSASToken -Context $ctx `
    -Name <container> `
    -Permission racwdl `
    -ExpiryTime <date-time>
```

Il token di firma di accesso condiviso della delega utente restituito sarà simile al:The user delegation SAS token returned will be similar to:

```output
?sv=2018-11-09&sr=c&sig=<sig>&skoid=<skoid>&sktid=<sktid>&skt=2019-08-05T22%3A24%3A36Z&ske=2019-08-07T07%3A
00%3A00Z&sks=b&skv=2018-11-09&se=2019-08-07T07%3A00%3A00Z&sp=rwdl
```

### <a name="create-a-user-delegation-sas-for-a-blob"></a>Creare una sAS di delega utente per un BLOBCreate a user delegation SAS for a blob

Per restituire un token di firma di accesso condiviso di delega utente per un BLOB, chiamare il comando [New-AzStorageBlobSASToken,](/powershell/module/az.storage/new-azstorageblobsastoken) passando l'oggetto di contesto di Archiviazione di Azure creato in precedenza.

La sintassi seguente restituisce una sAS di delega utente per un BLOB. L'esempio specifica `-FullUri` il parametro, che restituisce l'URI del BLOB con il token di firma di accesso condiviso aggiunto. Ricordarsi di sostituire i valori segnaposto tra parentesi quadre con valori personalizzati:

```powershell
New-AzStorageBlobSASToken -Context $ctx `
    -Container <container> `
    -Blob <blob> `
    -Permission racwd `
    -ExpiryTime <date-time>
    -FullUri
```

L'URI di accesso condiviso della delega utente restituito sarà simile al:

```output
https://storagesamples.blob.core.windows.net/sample-container/blob1.txt?sv=2018-11-09&sr=b&sig=<sig>&skoid=<skoid>&sktid=<sktid>&skt=2019-08-06T21%3A16%3A54Z&ske=2019-08-07T07%3A00%3A00Z&sks=b&skv=2018-11-09&se=2019-08-07T07%3A00%3A00Z&sp=racwd
```

> [!NOTE]
> Una classe di accesso sAS di delega utente non supporta la definizione di autorizzazioni con criteri di accesso archiviati.

## <a name="revoke-a-user-delegation-sas"></a>Revocare una licenza di accesso sAS di delega utenteRevoke a user delegation SAS

Per revocare una licenza di accesso sAS di delega utente da Azure PowerShell, chiamare il comando Revoke-AzStorageAccountUserDelegationKeys.To revoke a user delegation SAS from Azure PowerShell, call the **Revoke-AzStorageAccountUserDelegationKeys command.** Questo comando revoca tutte le chiavi di delega utente associate all'account di archiviazione specificato. Tutte le firme di accesso condiviso associate a tali chiavi vengono invalidate.

È necessario ricordare di sostituire i valori segnaposto tra parentesi uncinate con i valori personalizzati:

```powershell
Revoke-AzStorageAccountUserDelegationKeys -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
```

> [!IMPORTANT]
> Sia la chiave di delega utente che le assegnazioni di ruolo RBAC vengono memorizzate nella cache da Archiviazione di Azure, pertanto potrebbe verificarsi un ritardo tra l'avvio del processo di revoca e il momento in cui una firma di accesso client di delega utente esistente non è più valida.

## <a name="next-steps"></a>Passaggi successivi

- [Creare una sAS (GH) di delega utenteCreate a user delegation SAS (REST API)](/rest/api/storageservices/create-user-delegation-sas)
- [Operazione Get User Delegation Key](/rest/api/storageservices/get-user-delegation-key)
