---
title: Account di archiviazione gestita di Azure Key Vault - Versione PowerShell
description: La funzionalità dell'account di archiviazione gestito offre un'integrazione senza soluzione di continuità, tra L'insieme di credenziali delle chiavi di Azure e un account di archiviazione di Azure.The managed storage account feature provides a seamless integration, between Azure Key Vault and an Azure storage account.
ms.topic: conceptual
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/10/2019
ms.openlocfilehash: 7307741e56c7fc912f60d0496979243eb4be77a4
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431267"
---
# <a name="fetch-shared-access-signature-tokens-in-code"></a>Recuperare i token della firma di accesso condiviso nel codice

È possibile gestire l'account di archiviazione con i token di firma di [accesso condiviso](../../storage/common/storage-dotnet-shared-access-signature-part-1.md) nell'insieme di credenziali delle chiavi. In questo articolo vengono forniti esempi di codice in linguaggio C, che recupera un token di firma di accesso condiviso ed esegue operazioni con tale token.  Per informazioni su come creare e archiviare i token di firma di accesso condiviso, vedere [Gestire le chiavi dell'account di archiviazione con Key Vault e l'interfaccia della riga di comando](overview-storage-keys.md) di Azure o Gestire le chiavi dell'account di archiviazione con Key Vault e Azure [PowerShell.](overview-storage-keys-powershell.md)

## <a name="code-samples"></a>Esempi di codice

In questo esempio, il codice recupera un token di firma di accesso condiviso dall'insieme di credenziali delle chiavi, lo usa per creare un nuovo account di archiviazione e crea un nuovo client del servizio BLOB.  

```cs
// After you get a security token, create KeyVaultClient with vault credentials.
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(securityToken));

// Get a shared access signature token for your storage from Key Vault.
// The format for SecretUri is https://<YourKeyVaultName>.vault.azure.net/secrets/<ExamplePassword>
var sasToken = await kv.GetSecretAsync("SecretUri");

// Create new storage credentials by using the shared access signature token.
var accountSasCredential = new StorageCredentials(sasToken.Value);

// Use the storage credentials and the Blob storage endpoint to create a new Blob service client.
var accountWithSas = new CloudStorageAccount(accountSasCredential, new Uri ("https://myaccount.blob.core.windows.net/"), null, null, null);

var blobClientWithSas = accountWithSas.CreateCloudBlobClient();
```

Se il token di firma di accesso condiviso sta per scadere, è possibile recuperare il token di firma di accesso condiviso dall'insieme di credenziali delle chiavi e aggiornare il codice.

```cs
// If your shared access signature token is about to expire,
// get the shared access signature token again from Key Vault and update it.
sasToken = await kv.GetSecretAsync("SecretUri");
accountSasCredential.UpdateSASToken(sasToken);
```


## <a name="next-steps"></a>Passaggi successivi
- Informazioni su come [gestire le chiavi dell'account di archiviazione con Key Vault e l'interfaccia della riga di comando di Azure](overview-storage-keys.md) o Azure [PowerShell.](overview-storage-keys-powershell.md)
- Vedere [Esempi di chiavi dell'account di archiviazione gestitaSee Managed storage account key samples](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)
- [Azure Key Vault PowerShell reference](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault) (Documentazione su PowerShell per Azure Key Vault)
