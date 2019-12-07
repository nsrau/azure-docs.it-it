---
title: Autorizzare l'accesso ai dati con un'identità gestita
titleSuffix: Azure Storage
description: Informazioni su come usare le identità gestite per le risorse di Azure per autorizzare l'accesso ai dati BLOB e di Accodamento da applicazioni in esecuzione in macchine virtuali di Azure, app per le funzioni, set di scalabilità di macchine virtuali e altri.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: c0b07cc001f65ff369f87954779634b8b49ba4ec
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74892052"
---
# <a name="authorize-access-to-blob-and-queue-data-with-managed-identities-for-azure-resources"></a>Autorizzare l'accesso ai dati BLOB e di Accodamento con le identità gestite per le risorse di Azure

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

## <a name="authenticate-with-the-azure-identity-library"></a>Eseguire l'autenticazione con la libreria di identità di Azure

La libreria client di identità di Azure fornisce il supporto per l'autenticazione del token di Azure Azure AD per [Azure SDK](https://github.com/Azure/azure-sdk). Le versioni più recenti delle librerie client di archiviazione di Azure per .NET, Java, Python e JavaScript si integrano con la libreria di identità di Azure per fornire un modo semplice e sicuro per acquisire un token OAuth 2,0 per l'autorizzazione delle richieste di archiviazione di Azure.

Un vantaggio della libreria client di Azure Identity è che consente di usare lo stesso codice per autenticare se l'applicazione è in esecuzione nell'ambiente di sviluppo o in Azure. La libreria client Azure Identity per .NET autentica un'entità di sicurezza. Quando il codice è in esecuzione in Azure, l'entità di sicurezza è un'identità gestita per le risorse di Azure. Nell'ambiente di sviluppo, l'identità gestita non esiste, pertanto la libreria client autentica l'utente o un'entità servizio a scopo di test.

Dopo l'autenticazione, la libreria client Azure Identity riceve una credenziale token. Questa credenziale del token viene quindi incapsulata nell'oggetto client del servizio creato per eseguire operazioni in archiviazione di Azure. Questa operazione viene gestita automaticamente dalla libreria ottenendo le credenziali del token appropriate.

Per altre informazioni sulla libreria client Azure Identity per .NET, vedere [libreria client di identità di Azure per .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity). Per la documentazione di riferimento per la libreria client di identità di Azure, vedere [spazio dei nomi Azure. Identity](/dotnet/api/azure.identity).

### <a name="assign-role-based-access-control-rbac-roles-for-access-to-data"></a>Assegnare ruoli di controllo degli accessi in base al ruolo per l'accesso ai dati

Quando un Azure AD entità di sicurezza tenta di accedere ai dati del BLOB o della coda, l'entità di sicurezza deve avere le autorizzazioni per la risorsa. Se l'entità di sicurezza è un'identità gestita in Azure o un account utente Azure AD che esegue il codice nell'ambiente di sviluppo, all'entità di sicurezza deve essere assegnato un ruolo di controllo degli accessi in base al ruolo che concede l'accesso ai dati BLOB o della coda in archiviazione di Azure. Per informazioni sull'assegnazione di autorizzazioni tramite RBAC, vedere la sezione assegnare i **ruoli RBAC per i diritti di accesso** in [autorizzare l'accesso a BLOB e code di Azure con Azure Active Directory](../common/storage-auth-aad.md#assign-rbac-roles-for-access-rights).

### <a name="authenticate-the-user-in-the-development-environment"></a>Autenticare l'utente nell'ambiente di sviluppo

Quando il codice è in esecuzione nell'ambiente di sviluppo, l'autenticazione può essere gestita automaticamente oppure è possibile che sia necessario un account di accesso del browser, a seconda degli strumenti che si stanno usando. Ad esempio, Microsoft Visual Studio supporta Single Sign-On (SSO), in modo che l'account utente Active Azure AD venga usato automaticamente per l'autenticazione. Per ulteriori informazioni su SSO, vedere [Single Sign-on to Applications](../../active-directory/manage-apps/what-is-single-sign-on.md).

Altri strumenti di sviluppo potrebbero richiedere l'accesso tramite un Web browser.

### <a name="authenticate-a-service-principal-in-the-development-environment"></a>Autenticare un'entità servizio nell'ambiente di sviluppo

Se l'ambiente di sviluppo non supporta la Single Sign-On o l'accesso tramite un Web browser, è possibile usare un'entità servizio per l'autenticazione dall'ambiente di sviluppo.

#### <a name="create-the-service-principal"></a>Creare l'entità servizio

Per creare un'entità servizio con l'interfaccia della riga di comando di Azure e assegnare un ruolo RBAC, chiamare il comando [AZ ad SP create-for-RBAC](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) . Fornire un ruolo di accesso ai dati di archiviazione di Azure da assegnare alla nuova entità servizio. Fornire inoltre l'ambito per l'assegnazione di ruolo. Per altre informazioni sui ruoli predefiniti forniti per archiviazione di Azure, vedere [ruoli predefiniti per le risorse di Azure](../../role-based-access-control/built-in-roles.md).

Se non si dispone di autorizzazioni sufficienti per assegnare un ruolo all'entità servizio, potrebbe essere necessario richiedere al proprietario o all'amministratore dell'account di eseguire l'assegnazione di ruolo.

L'esempio seguente usa l'interfaccia della riga di comando di Azure per creare una nuova entità servizio e assegnare il ruolo di **lettore dati BLOB di archiviazione** con l'ambito dell'account

```azurecli-interactive
az ad sp create-for-rbac \
    --name <service-principal> \
    --role "Storage Blob Data Reader" \
    --scopes /subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

Il comando `az ad sp create-for-rbac` restituisce un elenco di proprietà dell'entità servizio in formato JSON. Copiare questi valori in modo che sia possibile usarli per creare le variabili di ambiente necessarie nel passaggio successivo.

```json
{
    "appId": "generated-app-ID",
    "displayName": "service-principal-name",
    "name": "http://service-principal-uri",
    "password": "generated-password",
    "tenant": "tenant-ID"
}
```

> [!IMPORTANT]
> Le assegnazioni di ruolo RBAC potrebbero richiedere alcuni minuti per la propagazione.

#### <a name="set-environment-variables"></a>Impostare le variabili di ambiente

La libreria client di identità di Azure legge i valori da tre variabili di ambiente in fase di esecuzione per autenticare l'entità servizio. Nella tabella seguente viene descritto il valore da impostare per ogni variabile di ambiente.

|Variabile di ambiente|Value
|-|-
|`AZURE_CLIENT_ID`|ID app per l'entità servizio
|`AZURE_TENANT_ID`|ID tenant Azure AD dell'entità servizio
|`AZURE_CLIENT_SECRET`|Password generata per l'entità servizio

> [!IMPORTANT]
> Dopo aver impostato le variabili di ambiente, chiudere e riaprire la finestra della console. Se si usa Visual Studio o un altro ambiente di sviluppo, potrebbe essere necessario riavviare l'ambiente di sviluppo per poter registrare le nuove variabili di ambiente.

Per altre informazioni, vedere [creare un'identità per l'app Azure nel portale](../../active-directory/develop/howto-create-service-principal-portal.md).

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

## <a name="net-code-example-create-a-block-blob"></a>Esempio di codice .NET: creare un BLOB in blocchi

Aggiungere le seguenti direttive di `using` al codice per usare le librerie di identità di Azure e client di archiviazione di Azure.

```csharp
using Azure;
using Azure.Identity;
using Azure.Storage.Blobs;
using System;
using System.IO;
using System.Text;
using System.Threading.Tasks;
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
    catch (RequestFailedException e)
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

- [Gestione dei diritti di accesso ai dati di archiviazione con RBAC](storage-auth-aad-rbac.md).
- [Usare Azure ad con le applicazioni di archiviazione](storage-auth-aad-app.md).
- [Eseguire l'interfaccia della riga di comando di Azure o i comandi di PowerShell con Azure ad credenziali per accedere ai dati BLOB o Queue](storage-auth-aad-script.md)
