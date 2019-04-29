---
title: Autenticare l'accesso a BLOB e code con identità gestite per le risorse di Azure - archiviazione di Azure | Microsoft Docs
description: Archiviazione di accodamento e Blob di Azure supporta l'autenticazione di Azure Active Directory con identità gestite per le risorse di Azure. È possibile usare le identità gestite per le risorse di Azure per autenticare l'accesso a BLOB e code da applicazioni in esecuzione in macchine virtuali, app per le funzioni, set di scalabilità di macchine virtuali di Azure e altro ancora.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/21/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 9209161f9c9e34320b1388e0e1edbd5069e73727
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61438291"
---
# <a name="authenticate-access-to-blobs-and-queues-with-azure-active-directory-and-managed-identities-for-azure-resources"></a>Autenticare l'accesso a BLOB e code con Azure Active Directory e identità gestite per le risorse di Azure

Archiviazione di BLOB e coda di Azure supporta l'autenticazione con [identità gestite di Azure Active Directory per le risorse di Azure](../../active-directory/managed-identities-azure-resources/overview.md). Gestiti le identità per le risorse di Azure possono autorizzare l'accesso a blob e i dati della coda utilizzando le credenziali di Azure AD da applicazioni in esecuzione in macchine virtuali di Azure (VM), App per le funzioni, il set di scalabilità di macchine virtuali e altri servizi. Usando identità gestite per le risorse di Azure con autenticazione di Azure AD, è possibile evitare l'archiviazione delle credenziali con le applicazioni che eseguono nel cloud.  

Questo articolo illustra come autorizzare l'accesso ai dati di accodamento o blob con un'identità gestita da una macchina virtuale di Azure. 

## <a name="enable-managed-identities-on-a-vm"></a>Abilitare le identità gestite su una macchina virtuale

Prima di poter utilizzare le identità gestito per le risorse di Azure per autorizzare l'accesso a BLOB e code dalla macchina virtuale, è necessario abilitare prima di tutto le identità gestito per le risorse di Azure nella macchina virtuale. Per informazioni su come abilitare identità gestite per le risorse di Azure, vedere uno di questi articoli:

- [Portale di Azure](https://docs.microsoft.com/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Interfaccia della riga di comando di Azure](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Modello di Azure Resource Manager](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure SDK](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-an-azure-ad-managed-identity"></a>Concedere le autorizzazioni per un'identità Azure Active Directory gestita

Per autorizzare una richiesta al servizio di accodamento o Blob da un'identità gestita all'interno dell'applicazione di archiviazione di Azure, prima di tutto configurare le impostazioni di controllo degli accessi in base al ruolo di accesso per tale identità gestita. Archiviazione di Azure definisce i ruoli RBAC che includono le autorizzazioni per i dati blob e coda. Quando viene assegnato il ruolo RBAC a un'identità gestita, l'identità gestita viene concesso le autorizzazioni per i dati di accodamento o blob nell'ambito appropriato. 

Per altre informazioni sull'assegnazione dei ruoli RBAC, vedere uno degli articoli seguenti:

- [Concedere l'accesso a Azure blob e Accodamento dei dati con accessi nel portale di Azure](storage-auth-aad-rbac-portal.md)
- [Concedere l'accesso a dati blob e di Accodamento di Azure con RBAC tramite la CLI di Azure](storage-auth-aad-rbac-cli.md)
- [Concedere l'accesso a dati blob e di Accodamento di Azure con RBAC tramite PowerShell](storage-auth-aad-rbac-powershell.md)

## <a name="authorize-with-a-managed-identity-access-token"></a>Autorizzazione con un token di accesso di identità gestita

Per autorizzare le richieste in archiviazione di accodamento e Blob con un'identità gestita, l'applicazione o lo script deve acquisire un token OAuth. Il [autenticazione di App di Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) libreria client per .NET (anteprima) semplifica il processo di acquisizione e il rinnovo di un token dal codice.

La libreria client di autenticazione dell'App gestisce automaticamente l'autenticazione. La libreria Usa le credenziali per gli sviluppatori per l'autenticazione durante lo sviluppo locale. L'utilizzo delle credenziali per lo sviluppatore durante lo sviluppo locale è più sicuro perché non è necessario creare credenziali di Azure AD o condividere le credenziali tra gli sviluppatori. Quando in un secondo momento, la soluzione viene distribuita in Azure, la libreria passa automaticamente a utilizzando le credenziali dell'applicazione.

Per usare la libreria di autenticazione dell'App in un'applicazione di archiviazione di Azure, installare il pacchetto di anteprima più recente dal [Nuget]((https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication)), nonché la versione più recente delle [libreria client di archiviazione di Azure per .NET](https://www.nuget.org/packages/WindowsAzure.Storage/). Aggiungere il codice seguente **usando** istruzioni al codice:

```csharp
using Microsoft.Azure.Services.AppAuthentication;
using Microsoft.WindowsAzure.Storage.Blob;
using Microsoft.WindowsAzure.Storage.Auth;
```

La libreria di autenticazione dell'App fornisce il **AzureServiceTokenProvider** classe. Un'istanza di questa classe può essere passata a un callback che recupera un token e rinnova quindi il token prima della scadenza.

Nell'esempio seguente ottiene un token e lo usa per creare un nuovo blob, quindi Usa lo stesso token per leggere il blob.

```csharp
const string blobName = "https://storagesamples.blob.core.windows.net/sample-container/blob1.txt";

// Get the initial access token and the interval at which to refresh it.
AzureServiceTokenProvider azureServiceTokenProvider = new AzureServiceTokenProvider();
var tokenAndFrequency = TokenRenewerAsync(azureServiceTokenProvider, 
                                            CancellationToken.None).GetAwaiter().GetResult();

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
blob.UploadTextAsync(string.Format("This is a blob named {0}", blob.Name));

// Continue to make requests against Azure Storage. 
// The token is automatically refreshed as needed in the background.
do
{
    // Read blob contents
    Console.WriteLine("Time accessed: {0} Blob Content: {1}", 
                        DateTimeOffset.UtcNow, 
                        blob.DownloadTextAsync().Result);

    // Sleep for ten seconds, then read the contents of the blob again.
    Thread.Sleep(TimeSpan.FromSeconds(10));
} while (true);
```

Il metodo di callback controlla il tempo di scadenza del token e lo rinnova in base alle esigenze:

```csharp
private static async Task<NewTokenAndFrequency> TokenRenewerAsync(Object state, CancellationToken cancellationToken)
{
    // Specify the resource ID for requesting Azure AD tokens for Azure Storage.
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

Per altre informazioni sulla libreria di autenticazione dell'App, vedere [l'autenticazione da servizio a Azure Key Vault usando .NET](../../key-vault/service-to-service-authentication.md). 

Per altre informazioni su come acquisire un token di accesso, vedere [come usare le identità gestito per le risorse di Azure in una macchina virtuale di Azure per acquisire un token di accesso](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md).

> [!NOTE]
> Per autorizzare le richieste rispetto ai dati di accodamento o blob con Azure AD, è necessario usare HTTPS per le richieste.

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sui ruoli RBAC per archiviazione di Azure, vedere [Gestisci i diritti di accesso ai dati di archiviazione con RBAC](storage-auth-aad-rbac.md).
- Per informazioni su come autorizzare l'accesso a contenitori e code all'interno delle applicazioni di archiviazione, vedere [Usare Azure AD con applicazioni di archiviazione](storage-auth-aad-app.md).
- Per informazioni su come eseguire i comandi di PowerShell e CLI di Azure con credenziali di Azure AD, vedere [comandi di esecuzione della riga di comando di Azure o PowerShell con credenziali di Azure AD per accedere ai dati di accodamento o blob](storage-auth-aad-script.md).