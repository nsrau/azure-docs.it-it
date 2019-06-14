---
title: Eseguire i comandi CLI di Azure o PowerShell con credenziali di Azure AD di accedere ai dati di accodamento o blob | Microsoft Docs
description: PowerShell e CLI di Azure supporta l'accesso con credenziali di Azure AD per eseguire comandi sui dati di code e blob di archiviazione di Azure. Un token di accesso viene fornito per la sessione e usato per autorizzare la chiamata delle operazioni. Le autorizzazioni variano a seconda il ruolo RBAC assegnato all'entità di sicurezza di Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/19/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 41ca1c5f413e5e15691f336d203edb918f21dc1a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65147301"
---
# <a name="run-azure-cli-or-powershell-commands-with-azure-ad-credentials-to-access-blob-or-queue-data"></a>Eseguire i comandi CLI di Azure o PowerShell con credenziali di Azure AD di accedere ai dati di accodamento o blob

Archiviazione di Azure fornisce estensioni di comando di Azure e PowerShell che consentono di accedere a ed eseguire i comandi script con le credenziali di Azure Active Directory (Azure AD). Quando si accede al comando di Azure o PowerShell con credenziali di Azure AD, viene restituito un token di accesso di OAuth 2.0. Tale token viene usato automaticamente da PowerShell o CLI per autorizzare le operazioni successive dei dati in archiviazione di accodamento o Blob. Per le operazioni supportate, non è più necessario passare un chiave dell'account o un token di firma di accesso condiviso con il comando.

È possibile assegnare autorizzazioni per i dati di accodamento e blob a un'entità di sicurezza di Azure AD tramite il controllo di accesso basato sui ruoli (RBAC). Per altre informazioni sui ruoli RBAC in archiviazione di Azure, vedere [Gestisci i diritti di accesso ai dati di archiviazione di Azure con RBAC](storage-auth-aad-rbac.md).

## <a name="supported-operations"></a>Operazioni supportate

Le estensioni sono supportate per le operazioni su contenitori e code. Quali operazioni è possibile chiamare dipende dalle autorizzazioni concesse all'entità di sicurezza di Azure AD con cui si accede al comando di Azure o PowerShell. Le autorizzazioni per i contenitori o le code di Archiviazione di Azure vengono assegnate tramite il controllo degli accessi in base al ruolo (RBAC). Ad esempio, se è stata assegnata la **lettore di dati Blob** ruolo, sarà possibile eseguire i comandi script che leggono i dati da un contenitore o una coda. Se è stata assegnata la **collaboratore ai dati Blob** ruolo, sarà possibile eseguire i comandi script che leggeranno, scrivano o eliminare un contenitore o coda o dati che contengono. 

Per informazioni dettagliate sulle autorizzazioni necessarie per ogni operazione di Archiviazione di Azure su un contenitore o una coda, vedere [Permissions for calling REST operations](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-rest-operations) (Autorizzazioni per la chiamata di operazioni REST).  

## <a name="call-cli-commands-using-azure-ad-credentials"></a>Chiamare i comandi dell'interfaccia della riga usando le credenziali di Azure AD

Comando di Azure supporta il `--auth-mode` parametro per le operazioni di dati blob e coda:

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
    
1. Prima di creare il contenitore, assegnare il [collaboratore ai dati Blob di archiviazione](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) ruolo a se stessi. Anche se si è il proprietario dell'account, sono necessarie autorizzazioni esplicite per eseguire operazioni di dati nell'account di archiviazione. Per altre informazioni sull'assegnazione dei ruoli RBAC, vedere [concedere l'accesso a Azure blob e Accodamento dei dati con accessi nel portale di Azure](storage-auth-aad-rbac.md).

    > [!IMPORTANT]
    > Le assegnazioni di ruolo RBAC potrebbero richiedere alcuni minuti per propagarsi.
    
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

1. Prima di creare il contenitore, assegnare il [collaboratore ai dati Blob di archiviazione](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) ruolo a se stessi. Anche se si è il proprietario dell'account, sono necessarie autorizzazioni esplicite per eseguire operazioni di dati nell'account di archiviazione. Per altre informazioni sull'assegnazione dei ruoli RBAC, vedere [concedere l'accesso a Azure blob e Accodamento dei dati con accessi nel portale di Azure](storage-auth-aad-rbac.md).

    > [!IMPORTANT]
    > Le assegnazioni di ruolo RBAC potrebbero richiedere alcuni minuti per propagarsi.

1. Creare un contenitore chiamando [New-AzStorageContainer](/powershell/module/az.storage/new-azstoragecontainer). Poiché questa chiamata viene utilizzato il contesto creato nei passaggi precedenti, viene creato il contenitore usando le credenziali di Azure AD. 

    ```powershell
    $containerName = "sample-container"
    New-AzStorageContainer -Name $containerName -Context $ctx
    ```

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sui ruoli RBAC per archiviazione di Azure, vedere [Gestisci i diritti di accesso ai dati di archiviazione con RBAC](storage-auth-aad-rbac.md).
- Per altre informazioni sull'utilizzo delle identità gestita per le risorse di Azure con archiviazione di Azure, vedere [autenticare l'accesso a BLOB e code con Azure Active Directory e identità gestite per le risorse di Azure](storage-auth-aad-msi.md).
- Per informazioni su come autorizzare l'accesso a contenitori e code all'interno delle applicazioni di archiviazione, vedere [Usare Azure AD con applicazioni di archiviazione](storage-auth-aad-app.md).
