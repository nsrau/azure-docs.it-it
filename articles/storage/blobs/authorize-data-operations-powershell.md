---
title: Eseguire comandi di PowerShell con Azure AD credenziali per accedere ai dati BLOB
titleSuffix: Azure Storage
description: PowerShell supporta l'accesso con Azure AD credenziali per eseguire comandi sui dati BLOB in archiviazione di Azure. Un token di accesso viene fornito per la sessione e usato per autorizzare la chiamata delle operazioni. Le autorizzazioni dipendono dal ruolo di Azure assegnato alla Azure AD entità di sicurezza.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 09/14/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: blobs
ms.openlocfilehash: cca0b197bdef04ffca9b71a7f394d3359023e2b7
ms.sourcegitcommit: 295db318df10f20ae4aa71b5b03f7fb6cba15fc3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2020
ms.locfileid: "94637429"
---
# <a name="run-powershell-commands-with-azure-ad-credentials-to-access-blob-data"></a>Eseguire comandi di PowerShell con Azure AD credenziali per accedere ai dati BLOB

Archiviazione di Azure fornisce estensioni per PowerShell che consentono di accedere ed eseguire comandi di script con le credenziali di Azure Active Directory (Azure AD). Quando si accede a PowerShell con Azure AD credenziali, viene restituito un token di accesso OAuth 2,0. Il token viene usato automaticamente da PowerShell per autorizzare le operazioni sui dati successive nell'archivio BLOB. Per le operazioni supportate, non è più necessario passare un chiave dell'account o un token di firma di accesso condiviso con il comando.

È possibile assegnare autorizzazioni ai dati BLOB a un'entità di sicurezza Azure AD tramite il controllo degli accessi in base al ruolo di Azure (RBAC di Azure). Per altre informazioni sui ruoli di Azure in archiviazione di Azure, vedere [gestire i diritti di accesso ai dati di archiviazione di Azure con RBAC di Azure](../common/storage-auth-aad-rbac.md).

## <a name="supported-operations"></a>Operazioni supportate

Le estensioni di archiviazione di Azure sono supportate per le operazioni sui dati BLOB. Le operazioni che è possibile chiamare dipendono dalle autorizzazioni concesse all'entità di sicurezza Azure AD con cui si accede a PowerShell. Le autorizzazioni per i contenitori di archiviazione di Azure vengono assegnate tramite RBAC di Azure. Se ad esempio è stato assegnato il ruolo **lettore dati BLOB** , è possibile eseguire i comandi di scripting per leggere i dati da un contenitore. Se è stato assegnato il ruolo di **collaboratore dati BLOB** , è possibile eseguire i comandi di scripting per la lettura, la scrittura o l'eliminazione di un contenitore o dei dati in essi contenuti.

Per informazioni dettagliate sulle autorizzazioni necessarie per ogni operazione di archiviazione di Azure in un contenitore, vedere [chiamare le operazioni di archiviazione con token OAuth](/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens).  

## <a name="call-powershell-commands-using-azure-ad-credentials"></a>Chiamare i comandi di PowerShell usando credenziali Azure AD

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Per usare Azure PowerShell per accedere ed eseguire operazioni successive sull'archiviazione di Azure usando Azure AD credenziali, creare un contesto di archiviazione per fare riferimento all'account di archiviazione e includere il `-UseConnectedAccount` parametro.

L'esempio seguente illustra come creare un contenitore in un nuovo account di archiviazione da Azure PowerShell usando le credenziali Azure AD. È necessario ricordare di sostituire i valori segnaposto tra parentesi uncinate con i valori personalizzati:

1. Accedere al proprio account Azure con il comando [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) :

    ```powershell
    Connect-AzAccount
    ```

    Per altre informazioni sull'accesso ad Azure con PowerShell, vedere [accedere con Azure PowerShell](/powershell/azure/authenticate-azureps).

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

1. Prima di creare il contenitore, assegnare il ruolo [Collaboratore ai dati dei BLOB di archiviazione](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) a se stessi. Anche se si è il proprietario dell'account, sono necessarie autorizzazioni esplicite per eseguire operazioni sui dati nell'account di archiviazione. Per altre informazioni sull'assegnazione di ruoli di Azure, vedere [usare la portale di Azure per assegnare un ruolo di Azure per l'accesso ai dati BLOB e di Accodamento](../common/storage-auth-aad-rbac.md).

    > [!IMPORTANT]
    > La propagazione delle assegnazioni dei ruoli può richiedere alcuni minuti.

1. Creare un contenitore chiamando [New-AzStorageContainer](/powershell/module/az.storage/new-azstoragecontainer). Poiché questa chiamata usa il contesto creato nei passaggi precedenti, il contenitore viene creato usando le credenziali Azure AD.

    ```powershell
    $containerName = "sample-container"
    New-AzStorageContainer -Name $containerName -Context $ctx
    ```

## <a name="next-steps"></a>Passaggi successivi

- [Usare PowerShell per assegnare un ruolo di Azure per l'accesso ai dati BLOB e di Accodamento](../common/storage-auth-aad-rbac-powershell.md)
- [Autorizzare l'accesso ai dati BLOB e di Accodamento con le identità gestite per le risorse di Azure](../common/storage-auth-aad-msi.md)
