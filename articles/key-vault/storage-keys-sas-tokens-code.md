---
title: Account di archiviazione gestita di Azure Key Vault - Versione PowerShell
description: La funzionalità dell'account di archiviazione gestito offre un'integrazione uniforme tra Azure Key Vault e un account di archiviazione di Azure.
ms.topic: conceptual
ms.service: key-vault
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/10/2019
ms.openlocfilehash: 75561c643c2b8881e4b390be885b77f2fff4971d
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/29/2019
ms.locfileid: "71673460"
---
# <a name="fetch-shared-access-signature-tokens-in-code"></a>Recuperare i token della firma di accesso condiviso nel codice

È possibile gestire l'account di archiviazione con i [token di firma di accesso condiviso](../storage/common/storage-dotnet-shared-access-signature-part-1.md) nell'insieme di credenziali delle chiavi. Questo articolo fornisce esempi di C# codice che recupera un token SAS ed esegue operazioni con esso.  Per informazioni su come creare e archiviare i token SAS, vedere [gestire le chiavi degli account di archiviazione con Key Vault e l'interfaccia della](key-vault-ovw-storage-keys.md) riga di comando di Azure o [gestire le chiavi dell'account di archiviazione con Key Vault e Azure PowerShell](key-vault-overview-storage-keys-powershell.md).

## <a name="code-samples"></a>Esempi di codice

In questo esempio, il codice recupera un token SAS dall'insieme di credenziali delle chiavi, lo usa per creare un nuovo account di archiviazione e crea un nuovo client del servizio BLOB.  

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

Se il token della firma di accesso condiviso sta per scadere, è possibile recuperare il token di firma di accesso condiviso dall'insieme di credenziali delle chiavi e aggiornare il codice.

```cs
// If your shared access signature token is about to expire,
// get the shared access signature token again from Key Vault and update it.
sasToken = await kv.GetSecretAsync("SecretUri");
accountSasCredential.UpdateSASToken(sasToken);
```


## <a name="next-steps"></a>Passaggi successivi
- Informazioni su come [gestire le chiavi degli account di archiviazione con Key Vault e l'interfaccia della riga](key-vault-ovw-storage-keys.md) di comando di Azure o [Azure PowerShell](key-vault-overview-storage-keys-powershell.md).
- Vedere gli [esempi di chiave dell'account di archiviazione gestita](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)
- [Informazioni su chiavi, segreti e certificati](about-keys-secrets-and-certificates.md)
- [Azure Key Vault PowerShell reference](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault) (Documentazione su PowerShell per Azure Key Vault)
