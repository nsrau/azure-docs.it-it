---
title: Autorizzare l'accesso a BLOB e code con Azure Active Directory e identità gestite per le risorse di Azure-archiviazione di Azure
description: Archiviazione BLOB e code di Azure supporta l'autorizzazione dell'accesso alle risorse con Azure Active Directory e identità gestite per le risorse di Azure. È possibile usare le identità gestite per le risorse di Azure per autorizzare l'accesso a BLOB e code da applicazioni in esecuzione in macchine virtuali di Azure, app per le funzioni, set di scalabilità di macchine virtuali e altro ancora.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 07/18/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 0f1c66126a1aa9a6ebf6f78ac6fb1ba37ba41829
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68985419"
---
# <a name="authorize-access-to-blobs-and-queues-with-azure-active-directory-and-managed-identities-for-azure-resources"></a>Autorizzare l'accesso a BLOB e code con Azure Active Directory e identità gestite per le risorse di Azure

Archiviazione di BLOB e coda di Azure supporta l'autenticazione con [identità gestite di Azure Active Directory per le risorse di Azure](../../active-directory/managed-identities-azure-resources/overview.md). Le identità gestite per le risorse di Azure possono autorizzare l'accesso ai dati BLOB e di accodamento tramite Azure AD credenziali da applicazioni in esecuzione in macchine virtuali di Azure, app per le funzioni, set di scalabilità di macchine virtuali e altri servizi. Usando le identità gestite per le risorse di Azure con l'autenticazione di Azure AD, è possibile evitare di archiviare le credenziali con le applicazioni eseguite nel cloud.  

Questo articolo illustra come autorizzare l'accesso ai dati di BLOB o di Accodamento con un'identità gestita da una macchina virtuale di Azure.

## <a name="enable-managed-identities-on-a-vm"></a>Abilitare le identità gestite su una macchina virtuale

Prima di poter usare le identità gestite per le risorse di Azure per autorizzare l'accesso a BLOB e code dalla VM, è necessario prima di tutto abilitare le identità gestite per le risorse di Azure nella macchina virtuale. Per informazioni su come abilitare identità gestite per le risorse di Azure, vedere uno di questi articoli:

- [Portale di Azure](https://docs.microsoft.com/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Interfaccia della riga di comando di Azure](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Modello di Azure Resource Manager](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Librerie client di Azure Resource Manager](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-an-azure-ad-managed-identity"></a>Concedere le autorizzazioni a un'identità gestita di Azure AD

Per autorizzare una richiesta al BLOB o Servizio di accodamento da un'identità gestita nell'applicazione di archiviazione di Azure, configurare prima le impostazioni di controllo degli accessi in base al ruolo (RBAC) per tale identità gestita. Archiviazione di Azure definisce i ruoli RBAC che includono le autorizzazioni per i dati di BLOB e di Accodamento. Quando il ruolo controllo degli accessi in base al ruolo viene assegnato a un'identità gestita, all'identità gestita vengono concesse le autorizzazioni per i dati BLOB o di Accodamento nell'ambito appropriato.

Per ulteriori informazioni sull'assegnazione di ruoli RBAC, vedere uno degli articoli seguenti:

- [Concedere l'accesso ai dati di code e BLOB di Azure con il controllo degli accessi in base al ruolo nel portale di Azure](storage-auth-aad-rbac-portal.md)
- [Concedere l'accesso ai dati di code e BLOB di Azure con il controllo degli accessi in base al ruolo tramite l'interfaccia della riga di comando di Azure](storage-auth-aad-rbac-cli.md)
- [Concedere l'accesso ai dati di code e BLOB di Azure con il controllo degli accessi in base al ruolo tramite PowerShell](storage-auth-aad-rbac-powershell.md)

## <a name="azure-storage-resource-id"></a>ID risorsa di archiviazione di Azure

[!INCLUDE [storage-resource-id-include](../../../includes/storage-resource-id-include.md)]

## <a name="net-code-example-create-a-block-blob"></a>Esempio di codice .NET: Creare un BLOB in blocchi

L'esempio di codice Mostra come ottenere un token OAuth 2,0 da Azure AD e usarlo per autorizzare una richiesta di creazione di un BLOB in blocchi. Per usare questo esempio, eseguire prima i passaggi descritti nelle sezioni precedenti.

[!INCLUDE [storage-app-auth-lib-include](../../../includes/storage-app-auth-lib-include.md)]

### <a name="add-the-callback-method"></a>Aggiungere il metodo di callback

Il metodo di callback controlla l'ora di scadenza del token e ne rinnova le informazioni in base alle esigenze:

```csharp
private static async Task<NewTokenAndFrequency> TokenRenewerAsync(Object state, CancellationToken cancellationToken)
{
    // Specify the resource ID for requesting Azure AD tokens for Azure Storage.
    // Note that you can also specify the root URI for your storage account as the resource ID.
    const string StorageResource = "https://storage.azure.com/";  

    // Use the same token provider to request a new token.
    var authResult = await ((AzureServiceTokenProvider)state).GetAuthenticationResultAsync(StorageResource);

    // Renew the token 5 minutes before it expires.
    var next = (authResult.ExpiresOn - DateTimeOffset.UtcNow) - TimeSpan.FromMinutes(5);
    if (next.Ticks < 0)
    {
        next = default(TimeSpan);
        Console.WriteLine("Renewing token...");
    }

    // Return the new token and the next refresh time.
    return new NewTokenAndFrequency(authResult.AccessToken, next);
}
```

### <a name="get-a-token-and-create-a-block-blob"></a>Ottenere un token e creare un BLOB in blocchi

La libreria di autenticazione app fornisce la classe **AzureServiceTokenProvider** . Un'istanza di questa classe può essere passata a un callback che ottiene un token e quindi rinnova il token prima della scadenza.

L'esempio seguente ottiene un token e lo usa per creare un nuovo BLOB, quindi usa lo stesso token per leggere il BLOB.

```csharp
const string blobName = "https://storagesamples.blob.core.windows.net/sample-container/blob1.txt";

// Get the initial access token and the interval at which to refresh it.
AzureServiceTokenProvider azureServiceTokenProvider = new AzureServiceTokenProvider();
var tokenAndFrequency = await TokenRenewerAsync(azureServiceTokenProvider,CancellationToken.None);

// Create storage credentials using the initial token, and connect the callback function
// to renew the token just before it expires
TokenCredential tokenCredential = new TokenCredential(tokenAndFrequency.Token,
                                                        TokenRenewerAsync,
                                                        azureServiceTokenProvider,
                                                        tokenAndFrequency.Frequency.Value);

StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

// Create a blob using the storage credentials.
CloudBlockBlob blob = new CloudBlockBlob(new Uri(blobName),
                                            storageCredentials);

// Upload text to the blob.
await blob.UploadTextAsync(string.Format("This is a blob named {0}", blob.Name));

// Continue to make requests against Azure Storage.
// The token is automatically refreshed as needed in the background.
do
{
    // Read blob contents
    Console.WriteLine("Time accessed: {0} Blob Content: {1}",
                        DateTimeOffset.UtcNow,
                        await blob.DownloadTextAsync());

    // Sleep for ten seconds, then read the contents of the blob again.
    Thread.Sleep(TimeSpan.FromSeconds(10));
} while (true);
```

Per altre informazioni sulla libreria di autenticazione delle app, vedere [autenticazione da servizio a servizio per Azure Key Vault usando .NET](../../key-vault/service-to-service-authentication.md).

Per altre informazioni su come acquisire un token di accesso, vedere [come usare le identità gestite per le risorse di Azure in una macchina virtuale di Azure per acquisire un token di accesso](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md).

> [!NOTE]
> Per autorizzare le richieste sui dati BLOB o della coda con Azure AD, è necessario usare HTTPS per tali richieste.

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sui ruoli RBAC per archiviazione di Azure, vedere [gestire i diritti di accesso ai dati di archiviazione con RBAC](storage-auth-aad-rbac.md).
- Per informazioni su come autorizzare l'accesso a contenitori e code all'interno delle applicazioni di archiviazione, vedere [Usare Azure AD con applicazioni di archiviazione](storage-auth-aad-app.md).
- Per informazioni su come eseguire l'interfaccia della riga di comando di Azure e i comandi di PowerShell con Azure AD credenziali, vedere [eseguire l'interfaccia della riga di comando di Azure o i comandi di PowerShell con Azure ad credenziali per accedere ai dati](storage-auth-aad-script.md)
