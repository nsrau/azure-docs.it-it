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
ms.openlocfilehash: adc8b84f0f22e85de88c4bd80c10a2a35d7b490a
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2018
ms.locfileid: "49114601"
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
      
<a name="step-by-step-instructions"></a>Istruzioni dettagliate
-------------------------

1. Ottenere l'ID risorsa dell'account di Archiviazione di Azure che si vuole gestire.
    a. Dopo aver creato un account di archiviazione, eseguire il comando seguente per ottenere l'ID risorsa dell'account di archiviazione da gestire
    ```
    az storage account show -n storageaccountname (Copy ID out of the result of this command)
    ```
2. Ottenere l'ID applicazione dell'entità servizio di Azure Key Vault 
    ```
    az ad sp show --id cfa8b339-82a2-471a-a3c9-0fc0be7a4093
    ```
3. Assegnare il ruolo "Operatore chiave di archiviazione" all'identità di Azure Key Vault
    ```
    az role assignment create --role "Storage Account Key Operator Service Role"  --assignee-object-id hhjkh --scope idofthestorageaccount
    ```
4. Creare un account di archiviazione gestito da Key Vault.     <br /><br />
   Il comando che segue fa in modo che Key Vault rigeneri periodicamente le chiavi di accesso dell'archiviazione, con un periodo di rigenerazione. Il periodo di rigenerazione impostato di seguito è di 90 giorni. Dopo 90 giorni, Key Vault rigenera 'key1' e passa l'impostazione di chiave attiva da 'key2' a 'key1'.
   ### <a name="key-regeneration"></a>Rigenerazione delle chiavi
    ```
    az keyvault storage add --vault-name <YourVaultName> -n <StorageAccountName> --active-key-name key2 --auto-generate-key --regeneration-period P90D --resource-id <Resource-id-of-storage-account>
    ```
    Nel caso in cui l'utente non abbia creato l'account di archiviazione e non abbia le autorizzazioni per tale account, la procedura seguente consente di impostare le autorizzazioni per l'account, in modo da garantire la possibilità di gestire tutte le autorizzazioni di archiviazione in Key Vault.
    [!NOTE] Nel caso in cui l'utente non abbia le autorizzazioni per l'account di archiviazione, prima si ottiene l'ID oggetto dell'utente

    ```
    az ad user show --upn-or-object-id "developer@contoso.com"

    az keyvault set-policy --name <YourVaultName> --object-id <ObjectId> --storage-permissions backup delete list regeneratekey recover purge restore set setsas update
    ```

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
