---
title: Eseguire comandi di PowerShell con Azure AD credenziali per accedere ai dati di BLOB o di Accodamento
titleSuffix: Azure Storage
description: PowerShell supporta l'accesso con le credenziali Azure AD per eseguire i comandi nei BLOB di archiviazione di Azure e accodare i dati. Un token di accesso viene fornito per la sessione e usato per autorizzare la chiamata delle operazioni. Le autorizzazioni dipendono dal ruolo RBAC assegnato alla Azure AD entità di sicurezza.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/30/2019
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 80ca5b63a91da31a5b226a589e15fb202eabd4ad
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84805769"
---
# <a name="run-powershell-commands-with-azure-ad-credentials-to-access-blob-or-queue-data"></a>Eseguire comandi di PowerShell con Azure AD credenziali per accedere ai dati di BLOB o di Accodamento

Archiviazione di Azure fornisce estensioni per PowerShell che consentono di accedere ed eseguire comandi di script con le credenziali di Azure Active Directory (Azure AD). Quando si accede a PowerShell con Azure AD credenziali, viene restituito un token di accesso OAuth 2,0. Il token viene usato automaticamente da PowerShell per autorizzare le operazioni sui dati successive sull'archiviazione BLOB o di Accodamento. Per le operazioni supportate, non è più necessario passare un chiave dell'account o un token di firma di accesso condiviso con il comando.

È possibile assegnare autorizzazioni per i dati BLOB e di Accodamento a un Azure AD entità di sicurezza tramite il controllo degli accessi in base al ruolo (RBAC). Per altre informazioni sui ruoli RBAC in archiviazione di Azure, vedere [gestire i diritti di accesso ai dati di archiviazione di Azure con RBAC](storage-auth-aad-rbac.md).

## <a name="supported-operations"></a>Operazioni supportate

Le estensioni di archiviazione di Azure sono supportate per le operazioni sui dati BLOB e di Accodamento. Le operazioni che è possibile chiamare dipendono dalle autorizzazioni concesse all'entità di sicurezza Azure AD con cui si accede a PowerShell. Le autorizzazioni per i contenitori o le code di archiviazione di Azure vengono assegnate tramite RBAC. Se ad esempio è stato assegnato il ruolo **lettore dati BLOB** , è possibile eseguire i comandi di scripting per leggere i dati da un contenitore o da una coda. Se è stato assegnato il ruolo di **collaboratore dati BLOB** , è possibile eseguire i comandi di scripting per la lettura, la scrittura o l'eliminazione di un contenitore o di una coda o dei dati in essi contenuti.

Per informazioni dettagliate sulle autorizzazioni necessarie per ogni operazione di archiviazione di Azure in un contenitore o in una coda, vedere [chiamare le operazioni di archiviazione con token OAuth](/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens).  

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

1. Prima di creare il contenitore, assegnare il ruolo [Collaboratore ai dati dei BLOB di archiviazione](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) a se stessi. Anche se si è il proprietario dell'account, sono necessarie autorizzazioni esplicite per eseguire operazioni sui dati nell'account di archiviazione. Per altre informazioni sull'assegnazione di ruoli RBAC, vedere [concedere l'accesso ai dati di Accodamento e BLOB di Azure con RBAC nel portale di Azure](storage-auth-aad-rbac.md).

    > [!IMPORTANT]
    > Le assegnazioni di ruolo RBAC potrebbero richiedere alcuni minuti per la propagazione.

1. Creare un contenitore chiamando [New-AzStorageContainer](/powershell/module/az.storage/new-azstoragecontainer). Poiché questa chiamata usa il contesto creato nei passaggi precedenti, il contenitore viene creato usando le credenziali Azure AD.

    ```powershell
    $containerName = "sample-container"
    New-AzStorageContainer -Name $containerName -Context $ctx
    ```

## <a name="next-steps"></a>Passaggi successivi

- [Usare PowerShell per assegnare un ruolo RBAC per l'accesso ai dati BLOB e di Accodamento](storage-auth-aad-rbac-powershell.md)
- [Autorizzare l'accesso ai dati BLOB e di Accodamento con le identità gestite per le risorse di Azure](storage-auth-aad-msi.md)
