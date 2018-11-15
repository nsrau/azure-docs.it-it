---
ms.assetid: ''
title: Chiavi dell'account di archiviazione Key Vault
description: Le chiavi dell'account di archiviazione forniscono un'integrazione diretta tra Azure Key Vault e l'accesso basato sulla chiave dell'account di Archiviazione di Azure.
ms.topic: conceptual
services: key-vault
ms.service: key-vault
author: bryanla
ms.author: bryanla
manager: mbaldwin
ms.date: 10/03/2018
ms.openlocfilehash: e62c599d82f488bf1fc30ce503c271084c5ae59d
ms.sourcegitcommit: d372d75558fc7be78b1a4b42b4245f40f213018c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/09/2018
ms.locfileid: "51300224"
---
# <a name="azure-key-vault-storage-account-keys"></a>Chiavi dell'account di archiviazione Key Vault

> [!NOTE]
> [Archiviazione di Azure ora supporta l'autorizzazione di AAD](https://docs.microsoft.com/azure/storage/common/storage-auth-aad). È consigliabile usare Azure Active Directory per l'autenticazione e l'autorizzazione per l'archiviazione, perché in questo modo gli utenti non devono preoccuparsi di ruotare le chiavi dell'account di archiviazione.

- Azure Key Vault gestisce le chiavi di un account di archiviazione di Azure.
    - Internamente Key Vault consente di elencare (sincronizzazione) le chiavi con un account di archiviazione di Azure.    
    - Key Vault rigenera (ruota) le chiavi periodicamente.
    - I valori di chiave non vengono mai restituiti in risposta al chiamante.
    - Key Vault gestisce le chiavi sia degli account di archiviazione che degli account di archiviazione classici.

<a name="prerequisites"></a>Prerequisiti
--------------
1. [Interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) Installare l'interfaccia della riga di comando di Azure   
2. [Creare un account di archiviazione](https://azure.microsoft.com/services/storage/)
    - Seguire i passaggi in questo [documento](https://docs.microsoft.com/azure/storage/) per creare un account di archiviazione  
    - **Linee guida sulla denominazione** I nomi degli account di archiviazione devono avere una lunghezza compresa tra 3 e 24 caratteri e possono contenere solo numeri e lettere minuscole.        
      
<a name="step-by-step-instructions-on-how-to-use-key-vault-to-manage-storage-account-keys"></a>Istruzioni dettagliate su come usare Key Vault per gestire le chiavi dell'account di archiviazione
--------------------------------------------------------------------------------
Nelle istruzioni seguenti si assegna Key Vault come servizio per avere autorizzazioni di operatore per l'account di archiviazione

> [!NOTE]
> Si noti che dopo aver configurato le chiavi dell'account di archiviazione gestita di Azure Key Vault, tali chiavi possono essere modificate **SOLO** tramite Key Vault. Le chiavi dell'account di archiviazione gestita consentono a Key Vault di gestire la rotazione della chiave dell'account di archiviazione.

1. Dopo aver creato un account di archiviazione, eseguire il comando seguente per ottenere l'ID risorsa dell'account di archiviazione da gestire

    ```
    az storage account show -n storageaccountname (Copy ID field out of the result of this command)
    ```
    
2. Ottenere l'ID applicazione dell'entità servizio di Azure Key Vault 

    ```
    az ad sp show --id cfa8b339-82a2-471a-a3c9-0fc0be7a4093
    ```
    
3. Assegnare il ruolo "Operatore chiave di archiviazione" all'identità di Azure Key Vault

    ```
    az role assignment create --role "Storage Account Key Operator Service Role"  --assignee-object-id <ApplicationIdOfKeyVault> --scope <IdOfStorageAccount>
    ```
    
4. Creare un account di archiviazione gestito da Key Vault.     <br /><br />
   Il periodo di rigenerazione impostato di seguito è di 90 giorni. Dopo 90 giorni, Key Vault rigenera 'key1' e passa l'impostazione di chiave attiva da 'key2' a 'key1'.
   
    ```
    az keyvault storage add --vault-name <YourVaultName> -n <StorageAccountName> --active-key-name key2 --auto-regenerate-key --regeneration-period P90D --resource-id <Resource-id-of-storage-account>
    ```
    Nel caso in cui l'utente non abbia creato l'account di archiviazione e non abbia le autorizzazioni per tale account, la procedura seguente consente di impostare le autorizzazioni per l'account, in modo da garantire la possibilità di gestire tutte le autorizzazioni di archiviazione in Key Vault.
 > [!NOTE] 
    Nel caso in cui l'utente non abbia le autorizzazioni per l'account di archiviazione, prima si ottiene l'ID oggetto dell'utente

    ```
    az ad user show --upn-or-object-id "developer@contoso.com"

    az keyvault set-policy --name <YourVaultName> --object-id <ObjectId> --storage-permissions backup delete list regeneratekey recover     purge restore set setsas update
    ```
### <a name="relavant-azure-cli-cmdlets"></a>Cmdlet dell'interfaccia della riga di comando di Azure pertinenti
- [Cmdlet di archiviazione dell'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/keyvault/storage?view=azure-cli-latest)

### <a name="relevant-powershell-cmdlets"></a>Cmdlet PowerShell pertinenti

- [Get-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.keyvault/get-azurekeyvaultmanagedstorageaccount)
- [Add-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Add-AzureKeyVaultManagedStorageAccount)
- [Get-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Get-AzureKeyVaultManagedStorageSasDefinition)
- [Update-AzureKeyVaultManagedStorageAccountKey](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Update-AzureKeyVaultManagedStorageAccountKey)
- [Remove-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.keyvault/remove-azurekeyvaultmanagedstorageaccount)
- [Remove-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Remove-AzureKeyVaultManagedStorageSasDefinition)
- [Set-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Set-AzureKeyVaultManagedStorageSasDefinition)

## <a name="see-also"></a>Vedere anche 

- [Informazioni su chiavi, segreti e certificati](https://docs.microsoft.com/rest/api/keyvault/)
- [Blog del team di Key Vault](https://blogs.technet.microsoft.com/kv/)
