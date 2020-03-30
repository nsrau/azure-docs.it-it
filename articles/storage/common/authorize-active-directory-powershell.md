---
title: Eseguire comandi di PowerShell con credenziali di Azure AD per accedere ai dati BLOB o codaRun PowerShell commands with Azure AD credentials to access blob or queue data
titleSuffix: Azure Storage
description: PowerShell supporta l'accesso con le credenziali di Azure AD per eseguire comandi nel BLOB di Archiviazione di Azure e nei dati delle code. Un token di accesso viene fornito per la sessione e usato per autorizzare la chiamata delle operazioni. Le autorizzazioni dipendono dal ruolo RBAC assegnato all'entità di sicurezza di Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/30/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 373b939ac63f31ccaf6a9f01fac92920e19074ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75553449"
---
# <a name="run-powershell-commands-with-azure-ad-credentials-to-access-blob-or-queue-data"></a>Eseguire comandi di PowerShell con credenziali di Azure AD per accedere ai dati BLOB o codaRun PowerShell commands with Azure AD credentials to access blob or queue data

Archiviazione di Azure offre estensioni per PowerShell che consentono di accedere ed eseguire comandi di scripting con credenziali di Azure Active Directory (Azure AD). Quando si accede a PowerShell con le credenziali di Azure AD, viene restituito un token di accesso OAuth 2.0.When you sign in to PowerShell with Azure AD credentials, an OAuth 2.0 access token is returned. Tale token viene usato automaticamente da PowerShell per autorizzare le operazioni di dati successive sull'archiviazione BLOB o coda. Per le operazioni supportate, non è più necessario passare un chiave dell'account o un token di firma di accesso condiviso con il comando.

È possibile assegnare autorizzazioni ai dati BLOB e coda a un'entità di sicurezza di Azure AD tramite il controllo degli accessi in base al ruolo. Per altre informazioni sui ruoli RBAC in Archiviazione di Azure, vedere Gestire i diritti di accesso ai dati di Archiviazione di [Azure con rbac.](storage-auth-aad-rbac.md)

## <a name="supported-operations"></a>Operazioni supportate

Le estensioni di Archiviazione di Azure sono supportate per le operazioni sui dati BLOB e coda. Which operations you may call depends on the permissions granted to the Azure AD security principal with which you sign in to PowerShell. Le autorizzazioni per i contenitori o le code di Archiviazione di Azure vengono assegnate tramite controllo degli accessi in base al ruolo. Ad esempio, se è stato assegnato il ruolo **Lettore dati BLOB,** è possibile eseguire comandi di script che leggono i dati da un contenitore o da una coda. Se è stato assegnato il ruolo **Collaboratore dati BLOB,** è possibile eseguire comandi di script che leggono, scrivono o eliminano un contenitore o una coda o i dati in essi contenuti.

Per informazioni dettagliate sulle autorizzazioni necessarie per ogni operazione di Archiviazione di Azure in un contenitore o in una coda, vedere [Operazioni di archiviazione delle chiamate con token OAuth.](/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens)  

## <a name="call-powershell-commands-using-azure-ad-credentials"></a>Chiamare i comandi di PowerShell usando le credenziali di Azure ADCall PowerShell commands using Azure AD credentials

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Per usare Azure PowerShell per accedere ed eseguire operazioni successive in Archiviazione di Azure usando le credenziali `-UseConnectedAccount` di Azure AD, creare un contesto di archiviazione per fare riferimento all'account di archiviazione e includere il parametro.

L'esempio seguente mostra come creare un contenitore in un nuovo account di archiviazione da Azure PowerShell usando le credenziali di Azure AD. È necessario ricordare di sostituire i valori segnaposto tra parentesi uncinate con i valori personalizzati:

1. Accedere all'account Azure con il comando [Connect-AzAccount:](/powershell/module/az.accounts/connect-azaccount)

    ```powershell
    Connect-AzAccount
    ```

    Per altre informazioni sull'accesso ad Azure con PowerShell, vedere Accedere con Azure PowerShell.For more information about signing into Azure with PowerShell, see [Sign in with Azure PowerShell.](/powershell/azure/authenticate-azureps)

1. Creare un gruppo di risorse di Azure chiamando [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). 

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

1. Ottenere il contesto dell'account di archiviazione che specifica il nuovo account di archiviazione chiamando [New-AzStorageContext](/powershell/module/az.storage/new-azstoragecontext). Quando si agisce su un account di archiviazione, è possibile fare riferimento al contesto anziché passare ripetutamente le credenziali. Includere `-UseConnectedAccount` il parametro per chiamare eventuali operazioni sui dati successive usando le credenziali di Azure AD:Include the parameter to call any subsequent data operations using your Azure AD credentials:

    ```powershell
    $ctx = New-AzStorageContext -StorageAccountName "<storage-account>" -UseConnectedAccount
    ```

1. Prima di creare il contenitore, assegnare il ruolo [Collaboratore dati BLOB di archiviazione](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) a se stessi. Anche se si è il proprietario dell'account, sono necessarie autorizzazioni esplicite per eseguire operazioni sui dati sull'account di archiviazione. Per altre informazioni sull'assegnazione di ruoli RBAC, vedere [Concedere l'accesso ai dati BLOB e coda](storage-auth-aad-rbac.md)di Azure con il controllo degli accessi in base al ruolo nel portale di Azure.For more information about assigning RBAC roles, see Grant access to Azure blob and queue data with RBAC in the Azure portal.

    > [!IMPORTANT]
    > La propagazione delle assegnazioni di ruolo RBAC può richiedere alcuni minuti.

1. Creare un contenitore chiamando [New-AzStorageContainer](/powershell/module/az.storage/new-azstoragecontainer). Poiché questa chiamata usa il contesto creato nei passaggi precedenti, il contenitore viene creato usando le credenziali di Azure AD.

    ```powershell
    $containerName = "sample-container"
    New-AzStorageContainer -Name $containerName -Context $ctx
    ```

## <a name="next-steps"></a>Passaggi successivi

- [Usare PowerShell per assegnare un ruolo RBAC per l'accesso ai dati BLOB e codaUse PowerShell to assign an RBAC role for access to blob and queue data](storage-auth-aad-rbac-powershell.md)
- [Autorizzare l'accesso ai dati BLOB e queue con identità gestite per le risorse di AzureAuthorize access to blob and queue data with managed identities for Azure resources](storage-auth-aad-msi.md)
