---
title: Autorizzare l'accesso a BLOB e code con Azure Active Directory e identità gestite per le risorse di Azure-archiviazione di Azure
description: Archiviazione BLOB e code di Azure supporta l'autorizzazione dell'accesso alle risorse con Azure Active Directory e identità gestite per le risorse di Azure. È possibile usare le identità gestite per le risorse di Azure per autorizzare l'accesso a BLOB e code da applicazioni in esecuzione in macchine virtuali di Azure, app per le funzioni, set di scalabilità di macchine virtuali e altro ancora.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 10/17/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 833aa7dcce5c429b3005a378e93e2177df1eb0d4
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/18/2019
ms.locfileid: "72595188"
---
# <a name="authorize-access-to-blobs-and-queues-with-azure-active-directory-and-managed-identities-for-azure-resources"></a>Autorizzare l'accesso a BLOB e code con Azure Active Directory e identità gestite per le risorse di Azure

Archiviazione di BLOB e coda di Azure supporta l'autenticazione con [identità gestite di Azure Active Directory per le risorse di Azure](../../active-directory/managed-identities-azure-resources/overview.md). Le identità gestite per le risorse di Azure possono autorizzare l'accesso ai dati BLOB e di accodamento tramite Azure AD credenziali da applicazioni in esecuzione in macchine virtuali di Azure, app per le funzioni, set di scalabilità di macchine virtuali e altri servizi. Usando le identità gestite per le risorse di Azure con l'autenticazione di Azure AD, è possibile evitare di archiviare le credenziali con le applicazioni eseguite nel cloud.  

Questo articolo illustra come autorizzare l'accesso ai dati di BLOB o di Accodamento da una macchina virtuale di Azure usando identità gestite per le risorse di Azure. Viene inoltre descritto come testare il codice nell'ambiente di sviluppo.

## <a name="enable-managed-identities-on-a-vm"></a>Abilitare le identità gestite su una macchina virtuale

Prima di poter usare le identità gestite per le risorse di Azure per autorizzare l'accesso a BLOB e code dalla VM, è necessario prima di tutto abilitare le identità gestite per le risorse di Azure nella macchina virtuale. Per informazioni su come abilitare identità gestite per le risorse di Azure, vedere uno di questi articoli:

- [Azure portal](https://docs.microsoft.com/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [interfaccia della riga di comando di Azure](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Modello di Azure Resource Manager](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Librerie client di Azure Resource Manager](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

Per altre informazioni sulle identità gestite, vedere [identità gestite per le risorse di Azure](../../active-directory/managed-identities-azure-resources/overview.md).

## <a name="authenticate-with-the-azure-identity-library-preview"></a>Eseguire l'autenticazione con la libreria di identità di Azure (anteprima)

La libreria client di identità di Azure per .NET (anteprima) autentica un'entità di sicurezza. Quando il codice è in esecuzione in Azure, l'entità di sicurezza è un'identità gestita per le risorse di Azure.

Quando il codice è in esecuzione nell'ambiente di sviluppo, l'autenticazione può essere gestita automaticamente oppure è possibile che sia necessario un account di accesso del browser, a seconda degli strumenti che si stanno usando. Microsoft Visual Studio supporta Single Sign-On (SSO), in modo che l'account utente Active Azure AD venga usato automaticamente per l'autenticazione. Per ulteriori informazioni su SSO, vedere [Single Sign-on to Applications](../../active-directory/manage-apps/what-is-single-sign-on.md).

Altri strumenti di sviluppo potrebbero richiedere l'accesso tramite un Web browser. È anche possibile usare un'entità servizio per l'autenticazione dall'ambiente di sviluppo. Per altre informazioni, vedere [creare un'identità per l'app Azure nel portale](../../active-directory/develop/howto-create-service-principal-portal.md).

Dopo l'autenticazione, la libreria client Azure Identity riceve una credenziale token. Questa credenziale del token viene quindi incapsulata nell'oggetto client del servizio creato per eseguire operazioni in archiviazione di Azure. Questa operazione viene gestita automaticamente dalla libreria ottenendo le credenziali del token appropriate.

Per altre informazioni sulla libreria client di Azure Identity, vedere [libreria client di identità di Azure per .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity).

## <a name="assign-rbac-roles-for-access-to-data"></a>Assegnare i ruoli RBAC per l'accesso ai dati

Quando un Azure AD entità di sicurezza tenta di accedere ai dati del BLOB o della coda, l'entità di sicurezza deve avere le autorizzazioni per la risorsa. Se l'entità di sicurezza è un'identità gestita in Azure o un account utente Azure AD che esegue il codice nell'ambiente di sviluppo, all'entità di sicurezza deve essere assegnato un ruolo di controllo degli accessi in base al ruolo che concede l'accesso ai dati BLOB o della coda in archiviazione di Azure. Per informazioni sull'assegnazione di autorizzazioni tramite RBAC, vedere la sezione assegnare i **ruoli RBAC per i diritti di accesso** in [autorizzare l'accesso a BLOB e code di Azure con Azure Active Directory](../common/storage-auth-aad.md#assign-rbac-roles-for-access-rights).

## <a name="install-the-preview-packages"></a>Installare i pacchetti di anteprima

Gli esempi in questo articolo usano la versione di anteprima più recente della libreria client di archiviazione di Azure per l'archiviazione BLOB. Per installare il pacchetto di anteprima, eseguire il comando seguente dalla console di gestione pacchetti NuGet:

```powershell
Install-Package Azure.Storage.Blobs -IncludePrerelease
```

Gli esempi in questo articolo usano anche la versione di anteprima più recente della [libreria client di identità di Azure per .NET](https://www.nuget.org/packages/Azure.Identity/) per l'autenticazione con le credenziali Azure ad. Per installare il pacchetto di anteprima, eseguire il comando seguente dalla console di gestione pacchetti NuGet:

```powershell
Install-Package Azure.Identity -IncludePrerelease
```

## <a name="net-code-example-create-a-block-blob"></a>Esempio di codice .NET: creare un BLOB in blocchi

Aggiungere le seguenti direttive di `using` al codice per usare le versioni di anteprima delle librerie di identità di Azure e client di archiviazione di Azure.

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Azure.Identity;
using Azure.Storage;
using Azure.Storage.Sas;
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
```

Per ottenere una credenziale token che il codice può usare per autorizzare le richieste ad archiviazione di Azure, creare un'istanza della classe [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) . Nell'esempio di codice seguente viene illustrato come ottenere le credenziali del token autenticato e come utilizzarle per creare un oggetto client del servizio, quindi utilizzare il client del servizio per caricare un nuovo BLOB:

```csharp
async static Task CreateBlockBlobAsync(string accountName, string containerName, string blobName)
{
    // Construct the blob container endpoint from the arguments.
    string containerEndpoint = string.Format("https://{0}.blob.core.windows.net/{1}",
                                                accountName,
                                                containerName);

    // Get a credential and create a client object for the blob container.
    BlobContainerClient containerClient = new BlobContainerClient(new Uri(containerEndpoint),
                                                                    new DefaultAzureCredential());

    try
    {
        // Create the container if it does not exist.
        await containerClient.CreateIfNotExistsAsync();

        // Upload text to a new block blob.
        string blobContents = "This is a block blob.";
        byte[] byteArray = Encoding.ASCII.GetBytes(blobContents);

        using (MemoryStream stream = new MemoryStream(byteArray))
        {
            await containerClient.UploadBlobAsync(blobName, stream);
        }
    }
    catch (StorageRequestFailedException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

> [!NOTE]
> Per autorizzare le richieste sui dati BLOB o della coda con Azure AD, è necessario usare HTTPS per tali richieste.

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sui ruoli RBAC per archiviazione di Azure, vedere [gestire i diritti di accesso ai dati di archiviazione con RBAC](storage-auth-aad-rbac.md).
- Per informazioni su come autorizzare l'accesso a contenitori e code all'interno delle applicazioni di archiviazione, vedere [Usare Azure AD con applicazioni di archiviazione](storage-auth-aad-app.md).
- Per informazioni su come eseguire l'interfaccia della riga di comando di Azure e i comandi di PowerShell con Azure AD credenziali, vedere [eseguire l'interfaccia della riga di comando di Azure o i comandi di PowerShell con Azure ad credenziali per accedere ai dati](storage-auth-aad-script.md)
