---
title: Recuperare i token di firma di accesso condiviso nel codice | Azure Key Vault
description: Un account di archiviazione gestito consente un'integrazione diretta tra Azure Key Vault e un account di archiviazione di Azure.
ms.topic: tutorial
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/10/2019
ms.openlocfilehash: 6530434e36f7c9a9a60a9782bcf2dce7ba447dab
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2020
ms.locfileid: "88584914"
---
# <a name="fetch-shared-access-signature-tokens-in-code"></a>Recuperare i token della firma di accesso condiviso nel codice

È possibile gestire l'account di archiviazione con i token di firma di accesso condiviso archiviati nell'insieme di credenziali delle chiavi. Per altre informazioni, vedere [Concedere l'accesso limitato alle risorse di Archiviazione di Azure tramite la firma di accesso condiviso](../../storage/common/storage-sas-overview.md).

Questo articolo fornisce esempi di codice .NET che recupera un token di firma di accesso condiviso ed esegue operazioni con tale token. Per informazioni su come creare e archiviare token di firma di accesso condiviso, vedere [Gestire le chiavi dell'account di archiviazione con Key Vault e l'interfaccia della riga di comando di Azure](overview-storage-keys.md) o [Gestire le chiavi dell'account di archiviazione con Key Vault e Azure PowerShell](overview-storage-keys-powershell.md).

## <a name="code-samples"></a>Esempi di codice

In questo esempio il codice recupera un token di firma di accesso condiviso dall'insieme di credenziali delle chiavi, lo usa per creare un nuovo account di archiviazione e crea un nuovo client del servizio BLOB.

```cs
// The shared access signature is stored as a secret in keyvault. 
// After you get a security token, create a new SecretClient with vault credentials and the key vault URI.
// The format for the key vault URI (kvuri) is https://<YourKeyVaultName>.vault.azure.net

var kv = new SecretClient(new Uri(kvUri), new DefaultAzureCredential());

// Now retrive your storage SAS token from Key Vault using the name of the secret (secretName).

KeyVaultSecret secret = client.GetSecret(secretName);
var sasToken = secret.Value;

// Create new storage credentials using the SAS token.
StorageCredentials accountSAS = new StorageCredentials(sasToken);

// Use these credentials and your storage account name to create a Blob service client.
CloudStorageAccount accountWithSAS = new CloudStorageAccount(accountSAS, "<storage-account>", endpointSuffix: null, useHttps: true);
CloudBlobClient blobClientWithSAS = accountWithSAS.CreateCloudBlobClient();
```

Se il token di firma di accesso condiviso sta per scadere, è possibile recuperare il token di firma di accesso condiviso dall'insieme di credenziali delle chiavi e aggiornare il codice.

```cs
// If your shared access signature token is about to expire,
// get the shared access signature token again from Key Vault and update it.
KeyVaultSecret secret = client.GetSecret(secretName);
var sasToken = secret.Value;
accountSAS.UpdateSASToken(sasToken);
```


## <a name="next-steps"></a>Passaggi successivi
- Vedere altre informazioni su come [Concedere l'accesso limitato alle risorse di Archiviazione di Azure tramite la firma di accesso condiviso](../../storage/common/storage-sas-overview.md).
- Vedere altre informazioni su come [Gestire le chiavi degli account di archiviazione con Key Vault e l'interfaccia della riga di comando di Azure](overview-storage-keys.md) o [Azure PowerShell](overview-storage-keys-powershell.md).
- Vedere [Esempi di chiavi di account di archiviazione gestiti](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)
