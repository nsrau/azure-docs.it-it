---
title: Recuperare i token della firma di accesso condiviso nel codice | Azure Key Vault
description: La funzionalità dell'account di archiviazione gestito offre un'integrazione uniforme tra Azure Key Vault e un account di archiviazione di Azure.
ms.topic: conceptual
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/10/2019
ms.openlocfilehash: e429115ce2624685c413ae252229964feee70137
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2020
ms.locfileid: "86232594"
---
# <a name="fetch-shared-access-signature-tokens-in-code"></a>Recuperare i token della firma di accesso condiviso nel codice

È possibile gestire l'account di archiviazione con i token di firma di accesso condiviso archiviati nell'insieme di credenziali delle chiavi. Per altre informazioni, vedere [concedere l'accesso limitato alle risorse di archiviazione di Azure tramite la firma di accesso](../../storage/common/storage-sas-overview.md)condiviso.

Questo articolo fornisce esempi di codice .NET che recupera un token SAS ed esegue operazioni con esso. Per informazioni su come creare e archiviare i token SAS, vedere [gestire le chiavi degli account di archiviazione con Key Vault e l'interfaccia della](overview-storage-keys.md) riga di comando di Azure o [gestire le chiavi dell'account di archiviazione con Key Vault e Azure PowerShell](overview-storage-keys-powershell.md).

## <a name="code-samples"></a>Esempi di codice

In questo esempio, il codice recupera un token SAS dall'insieme di credenziali delle chiavi, lo usa per creare un nuovo account di archiviazione e crea un nuovo client del servizio BLOB.

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

Se il token della firma di accesso condiviso sta per scadere, è possibile recuperare il token di firma di accesso condiviso dall'insieme di credenziali delle chiavi e aggiornare il codice.

```cs
// If your shared access signature token is about to expire,
// get the shared access signature token again from Key Vault and update it.
KeyVaultSecret secret = client.GetSecret(secretName);
var sasToken = secret.Value;
accountSAS.UpdateSASToken(sasToken);
```


## <a name="next-steps"></a>Passaggi successivi
- Informazioni su come [concedere un accesso limitato alle risorse di archiviazione di Azure tramite SAS](../../storage/common/storage-sas-overview.md).
- Informazioni su come [gestire le chiavi degli account di archiviazione con Key Vault e l'interfaccia della riga](overview-storage-keys.md) di comando di Azure o [Azure PowerShell](overview-storage-keys-powershell.md).
- Vedere gli [esempi di chiave dell'account di archiviazione gestita](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)
