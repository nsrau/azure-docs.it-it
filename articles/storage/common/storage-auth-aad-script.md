---
title: Eseguire l'interfaccia della riga di comando di Azure o i comandi di PowerShell con Azure AD credenziali per accedere ai dati di BLOB o Microsoft Docs
description: L'interfaccia della riga di comando di Azure e PowerShell supportano l'accesso con Azure AD credenziali per eseguire i comandi nei BLOB di archiviazione di Azure e le code. Un token di accesso viene fornito per la sessione e usato per autorizzare la chiamata delle operazioni. Le autorizzazioni dipendono dal ruolo RBAC assegnato alla Azure AD entità di sicurezza.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 07/03/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 80ab896e1393d6c68b22a61d1b96acd507aa6994
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/16/2019
ms.locfileid: "68249902"
---
# <a name="run-azure-cli-or-powershell-commands-with-azure-ad-credentials-to-access-blob-or-queue-data"></a>Eseguire l'interfaccia della riga di comando di Azure o i comandi di PowerShell con Azure AD credenziali per accedere ai dati BLOB o

Archiviazione di Azure fornisce estensioni per l'interfaccia della riga di comando di Azure e PowerShell che consentono di accedere ed eseguire comandi di script con credenziali Azure Active Directory (Azure AD). Quando si accede all'interfaccia della riga di comando di Azure o a PowerShell con Azure AD credenziali, viene restituito un token di accesso OAuth 2,0. Il token viene usato automaticamente dall'interfaccia della riga di comando o da PowerShell per autorizzare le operazioni sui dati successive sull'archiviazione BLOB o di Accodamento. Per le operazioni supportate, non è più necessario passare un chiave dell'account o un token di firma di accesso condiviso con il comando.

È possibile assegnare autorizzazioni per i dati BLOB e di Accodamento a un Azure AD entità di sicurezza tramite il controllo degli accessi in base al ruolo (RBAC). Per altre informazioni sui ruoli RBAC in archiviazione di Azure, vedere [gestire i diritti di accesso ai dati di archiviazione di Azure con RBAC](storage-auth-aad-rbac.md).

## <a name="supported-operations"></a>Operazioni supportate

Le estensioni sono supportate per le operazioni su contenitori e code. Le operazioni che è possibile chiamare dipendono dalle autorizzazioni concesse all'entità di sicurezza Azure AD con cui si accede all'interfaccia della riga di comando di Azure o a PowerShell. Le autorizzazioni per i contenitori o le code di Archiviazione di Azure vengono assegnate tramite il controllo degli accessi in base al ruolo (RBAC). Se ad esempio viene assegnato il ruolo **lettore dati BLOB** , è possibile eseguire i comandi di scripting per la lettura di dati da un contenitore o una coda. Se viene assegnato il ruolo di **collaboratore dati BLOB** , è possibile eseguire i comandi di scripting per la lettura, la scrittura o l'eliminazione di un contenitore o di una coda o dei dati in essi contenuti. 

Per informazioni dettagliate sulle autorizzazioni necessarie per ogni operazione di archiviazione di Azure in un contenitore o in una coda, vedere [chiamare le operazioni di archiviazione con token OAuth](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#call-storage-operations-with-oauth-tokens).  

## <a name="call-cli-commands-using-azure-ad-credentials"></a>Chiamare i comandi dell'interfaccia della riga di comando usando credenziali Azure AD

L'interfaccia della riga `--auth-mode` di comando di Azure supporta il parametro per le operazioni di dati BLOB e Queue

- Impostare il `--auth-mode` parametro su `login` per accedere usando un'entità di sicurezza Azure ad.
- Impostare il parametro `--auth-mode` sul valore legacy `key` per tentare di eseguire una query per ottenere una chiave dell'account se non vengono forniti parametri di autenticazione per l'account. 

L'esempio seguente illustra come creare un contenitore in un nuovo account di archiviazione dall'interfaccia della riga di comando di Azure usando le credenziali Azure AD. Ricordarsi di sostituire i valori segnaposto tra parentesi angolari con valori personalizzati: 

1. Assicurarsi di aver installato l'interfaccia della riga di comando di Azure versione 2.0.46 o successiva. Eseguire `az --version` per controllare la versione installata.

1. Eseguire `az login` e autenticare nella finestra del browser: 

    ```azurecli
    az login
    ```

1. Specificare la sottoscrizione desiderata. Creare un gruppo di risorse usando [az group create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create). Creare un account di archiviazione all'interno di tale gruppo di risorse usando [AZ storage account create](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create):

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

1. Prima di creare il contenitore, assegnare il ruolo di [collaboratore dati BLOB di archiviazione](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) a se stessi. Anche se si è il proprietario dell'account, sono necessarie autorizzazioni esplicite per eseguire operazioni sui dati nell'account di archiviazione. Per altre informazioni sull'assegnazione di ruoli RBAC, vedere [concedere l'accesso ai dati di Accodamento e BLOB di Azure con RBAC nel portale di Azure](storage-auth-aad-rbac.md).

    > [!IMPORTANT]
    > Le assegnazioni di ruolo RBAC potrebbero richiedere alcuni minuti per la propagazione.

1. Chiamare il comando [AZ Storage container create](https://docs.microsoft.com/cli/azure/storage/container?view=azure-cli-latest#az-storage-container-create) con il `--auth-mode` parametro impostato su `login` per creare il contenitore usando le credenziali Azure ad:

    ```azurecli
    az storage container create \ 
        --account-name <storage-account> \ 
        --name sample-container \
        --auth-mode login
    ```

La variabile di ambiente associata al parametro `--auth-mode` è `AZURE_STORAGE_AUTH_MODE`. È possibile specificare il valore appropriato nella variabile di ambiente per evitare di includerlo a ogni chiamata a un'operazione sui dati di archiviazione di Azure.

## <a name="call-powershell-commands-using-azure-ad-credentials"></a>Chiamare i comandi di PowerShell usando credenziali Azure AD

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Per usare Azure PowerShell per accedere ed eseguire operazioni successive sull'archiviazione di Azure usando Azure ad credenziali, creare un contesto di archiviazione per fare riferimento all'account di archiviazione e `-UseConnectedAccount` includere il parametro.

L'esempio seguente illustra come creare un contenitore in un nuovo account di archiviazione da Azure PowerShell usando le credenziali Azure AD. Ricordarsi di sostituire i valori segnaposto tra parentesi angolari con valori personalizzati:

1. Accedere alla sottoscrizione di Azure con il `Connect-AzAccount` comando e seguire le istruzioni visualizzate per immettere le credenziali Azure ad: 

    ```powershell
    Connect-AzAccount
    ```

1. Per creare un gruppo di risorse di Azure, chiamare [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). 

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

1. Ottenere il contesto dell'account di archiviazione che specifica il nuovo account di archiviazione chiamando [New-AzStorageContext](/powershell/module/az.storage/new-azstoragecontext). Quando si agisce su un account di archiviazione, è possibile fare riferimento al contesto anziché passare ripetutamente le credenziali. Includere il `-UseConnectedAccount` parametro per chiamare qualsiasi operazione di dati successiva usando le credenziali Azure ad:

    ```powershell
    $ctx = New-AzStorageContext -StorageAccountName "<storage-account>" -UseConnectedAccount
    ```

1. Prima di creare il contenitore, assegnare il ruolo di [collaboratore dati BLOB di archiviazione](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) a se stessi. Anche se si è il proprietario dell'account, sono necessarie autorizzazioni esplicite per eseguire operazioni sui dati nell'account di archiviazione. Per altre informazioni sull'assegnazione di ruoli RBAC, vedere [concedere l'accesso ai dati di Accodamento e BLOB di Azure con RBAC nel portale di Azure](storage-auth-aad-rbac.md).

    > [!IMPORTANT]
    > Le assegnazioni di ruolo RBAC potrebbero richiedere alcuni minuti per la propagazione.

1. Creare un contenitore chiamando [New-AzStorageContainer](/powershell/module/az.storage/new-azstoragecontainer). Poiché questa chiamata usa il contesto creato nei passaggi precedenti, il contenitore viene creato usando le credenziali Azure AD. 

    ```powershell
    $containerName = "sample-container"
    New-AzStorageContainer -Name $containerName -Context $ctx
    ```

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sui ruoli RBAC per archiviazione di Azure, vedere [gestire i diritti di accesso ai dati di archiviazione con RBAC](storage-auth-aad-rbac.md).
- Per informazioni sull'uso delle identità gestite per le risorse di Azure con archiviazione di Azure, vedere [autenticare l'accesso a BLOB e code con Azure Active Directory e identità gestite per le risorse di Azure](storage-auth-aad-msi.md).
- Per informazioni su come autorizzare l'accesso a contenitori e code all'interno delle applicazioni di archiviazione, vedere [Usare Azure AD con applicazioni di archiviazione](storage-auth-aad-app.md).
