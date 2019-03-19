---
title: Eseguire i comandi CLI di Azure o PowerShell in un'identità di Azure AD per accedere ad archiviazione di Azure | Microsoft Docs
description: L'interfaccia della riga di comando di Azure e PowerShell supportano l'accesso con un'identità di Azure AD per eseguire comandi sui contenitori e le code di Archiviazione di Azure e i relativi dati. Un token di accesso viene fornito per la sessione e usato per autorizzare la chiamata delle operazioni. Le autorizzazioni variano in base al ruolo assegnato all'identità di Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/06/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: f8fd3cdcf73749d787fc6f1c2222946961091f80
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57849840"
---
# <a name="use-an-azure-ad-identity-to-access-azure-storage-with-cli-or-powershell"></a>Usare un'identità di Azure AD per accedere ad archiviazione di Azure con PowerShell o CLI

Archiviazione di Azure fornisce estensioni di comando di Azure e PowerShell che consentono di accedere ed eseguire i comandi script con un'identità di Azure Active Directory (Azure AD). L'identità di Azure AD può essere un utente, un gruppo o un'entità servizio dell'applicazione oppure può essere un'[identità gestita per le risorse di Azure](../../active-directory/managed-identities-azure-resources/overview.md). È possibile assegnare autorizzazioni per accedere alle risorse di archiviazione per l'identità di Azure AD tramite il controllo degli accessi in base al ruolo (RBAC). Per altre informazioni sui ruoli RBAC in Archiviazione di Azure, vedere [Gestire i diritti di accesso a dati di Archiviazione di Azure con il controllo degli accessi in base al ruolo (anteprima)](storage-auth-aad-rbac.md).

Quando si accede all'interfaccia della riga di comando di Azure o a PowerShell con un'identità di Azure AD, viene restituito un token di accesso per l'accesso ad Archiviazione di Azure con questa identità. Tale token viene quindi usato automaticamente dall'interfaccia della riga di comando o da PowerShell per autorizzare le operazioni su Archiviazione di Azure. Per le operazioni supportate, non è più necessario passare un chiave dell'account o un token di firma di accesso condiviso con il comando.

## <a name="supported-operations"></a>Operazioni supportate

Le estensioni sono supportate per le operazioni su contenitori e code. Le operazioni che è possibile chiamare dipendono dalle autorizzazioni concesse all'identità di Azure AD usata per accedere all'interfaccia della riga di comando di Azure o a PowerShell. Le autorizzazioni per i contenitori o le code di Archiviazione di Azure vengono assegnate tramite il controllo degli accessi in base al ruolo (RBAC). Ad esempio, se all'identità viene assegnato un ruolo di Lettore dei dati, è possibile eseguire i comandi di scripting che leggono i dati da un contenitore o una coda. Se all'identità viene assegnato un ruolo di Collaboratore ai dati, è possibile eseguire i comandi di scripting per la lettura, scrittura o eliminazione di un contenitore, di una coda o dei dati in essi contenuti. 

Per informazioni dettagliate sulle autorizzazioni necessarie per ogni operazione di Archiviazione di Azure su un contenitore o una coda, vedere [Permissions for calling REST operations](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-rest-operations) (Autorizzazioni per la chiamata di operazioni REST).  

## <a name="call-cli-commands-using-azure-ad-credentials"></a>Chiamare i comandi dell'interfaccia della riga usando le credenziali di Azure AD

Comando di Azure supporta il `--auth-mode` parametro per le operazioni di dati in archiviazione di Azure:

- Impostare il `--auth-mode` parametro per `login` accedere usando un'entità di sicurezza di Azure AD.
- Impostare il parametro `--auth-mode` sul valore legacy `key` per tentare di eseguire una query per ottenere una chiave dell'account se non vengono forniti parametri di autenticazione per l'account. 

Nell'esempio seguente viene illustrato come creare un contenitore in un nuovo account di archiviazione della riga di comando di Azure usando le credenziali di Azure AD. Ricordare di sostituire i valori segnaposto in parentesi acute con i propri valori: 

1. Verificare di aver installato la versione della riga di comando di Azure 2.0.46 o versione successiva. Eseguire `az --version` per controllare la versione installata.

1. Eseguire `az login` ed eseguire l'autenticazione nella finestra del browser: 

    ```azurecli
    az login
    ```
    
1. Specificare la sottoscrizione desiderata. Creare un gruppo di risorse usando [az group create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create). Creare un account di archiviazione all'interno di tale gruppo di risorse usando [az storage account creare](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create): 

    ```azurecli
    az account set --subscription <subscription-id>

    az group create \
        --name sample-resource-group-cli \
        --location eastus

    az storage account create \
        --name <storage-account> \
        --resource-group sample-resource-group-cli \
        --location eastus \
        --sku Standard_LRS \
        --encryption-services blob
    ```
    
1. Prima di creare il contenitore, assegnare il [collaboratore ai dati di archiviazione Blob (anteprima)](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor-preview) ruolo a se stessi. Anche se si è il proprietario dell'account, sono necessarie autorizzazioni esplicite per eseguire operazioni di dati nell'account di archiviazione. Per altre informazioni sull'assegnazione dei ruoli RBAC, vedere [concedere l'accesso a contenitori di Azure e code con accessi nel portale di Azure (anteprima)](storage-auth-aad-rbac.md).

    > [!IMPORTANT]
    > Durante l'anteprima del supporto di Azure AD per code e BLOB, le assegnazioni di ruolo RBAC possono richiedere fino a 5 minuti per propagarsi.
    
1. Chiamare il [creare il contenitore di archiviazione di az](https://docs.microsoft.com/cli/azure/storage/container?view=azure-cli-latest#az-storage-container-create) con il `--auth-mode` parametro impostato su `login` per creare il contenitore usando le credenziali di Azure AD:

    ```azurecli
    az storage container create \ 
        --account-name <storage-account> \ 
        --name sample-container \
        --auth-mode login
    ```

La variabile di ambiente associata al parametro `--auth-mode` è `AZURE_STORAGE_AUTH_MODE`. È possibile specificare il valore appropriato nella variabile di ambiente per evitare di includerlo in ogni chiamata a un'operazione di dati di archiviazione di Azure.

## <a name="call-powershell-commands-using-azure-ad-credentials"></a>Chiamare i comandi di PowerShell utilizzando le credenziali di Azure AD

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Per usare Azure PowerShell per accedere ed eseguire le operazioni successive da archiviazione di Azure tramite credenziali di Azure AD, creare un contesto di archiviazione per fare riferimento all'account di archiviazione e l'inclusione di `-UseConnectedAccount` parametro.

Nell'esempio seguente viene illustrato come creare un contenitore in un nuovo account di archiviazione da Azure PowerShell usando le credenziali di Azure AD. Ricordare di sostituire i valori segnaposto in parentesi acute con i propri valori:

1. Accedi alla sottoscrizione di Azure con il `Connect-AzAccount` comando e seguire sullo schermo le direzioni di immettere le credenziali di Azure AD: 

    ```powershell
    Connect-AzAccount
    ```
    
1. Creare un gruppo di risorse di Azure tramite una chiamata [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). 

    ```powershell
    $resourceGroup = "sample-resource-group-ps"
    $location = "eastus"
    New-AzResourceGroup -Name $resourceGroup -Location $location
    ```

1. Creare un account di archiviazione chiamando [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount).

    ```powershell
    $storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
      -Name "<storage-account>" `
      -SkuName Standard_LRS `
      -Location $location `
    ```

1. Ottenere il contesto di account di archiviazione che specifica il nuovo account di archiviazione chiamando [New-AzStorageContext](/powershell/module/az.storage/new-azstoragecontext). Quando si usa un account di archiviazione, è possibile fare riferimento al contesto anziché passare ripetutamente le credenziali. Includere il `-UseConnectedAccount` parametro chiamare qualsiasi operazione successiva dei dati usando le credenziali di Azure AD:

    ```powershell
    $ctx = New-AzStorageContext -StorageAccountName "<storage-account>" -UseConnectedAccount
    ```

1. Prima di creare il contenitore, assegnare il [collaboratore ai dati di archiviazione Blob (anteprima)](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor-preview) ruolo a se stessi. Anche se si è il proprietario dell'account, sono necessarie autorizzazioni esplicite per eseguire operazioni di dati nell'account di archiviazione. Per altre informazioni sull'assegnazione dei ruoli RBAC, vedere [concedere l'accesso a contenitori di Azure e code con accessi nel portale di Azure (anteprima)](storage-auth-aad-rbac.md).

    > [!IMPORTANT]
    > Durante l'anteprima del supporto di Azure AD per code e BLOB, le assegnazioni di ruolo RBAC possono richiedere fino a 5 minuti per propagarsi.

1. Creare un contenitore chiamando [New-AzStorageContainer](/powershell/module/az.storage/new-azstoragecontainer). Poiché questa chiamata viene utilizzato il contesto creato nei passaggi precedenti, viene creato il contenitore usando le credenziali di Azure AD. 

    ```powershell
    $containerName = "sample-container"
    New-AzStorageContainer -Name $containerName -Context $ctx
    ```

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sui ruoli RBAC per Archiviazione di Azure, vedere [Manage access rights to storage data with RBAC (Preview)](storage-auth-aad-rbac.md) (Gestire i diritti di accesso ai dati di archiviazione con il controllo degli accessi in base al ruolo - anteprima).
- Per altre informazioni sull'uso delle identità gestite per le risorse di Azure con Archiviazione di Azure, vedere [Authenticate access to blobs and queues with Azure managed identities for Azure Resources (Preview)](storage-auth-aad-msi.md) (Autenticare l'accesso a BLOB e code con le identità gestite di Azure per le risorse di Azure - anteprima).
- Per informazioni su come autorizzare l'accesso a contenitori e code all'interno delle applicazioni di archiviazione, vedere [Usare Azure AD con applicazioni di archiviazione](storage-auth-aad-app.md).