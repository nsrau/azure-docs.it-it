---
title: Autorizzare l'accesso ai dati con un'identità gestitaAuthorize access to data with a managed identity
titleSuffix: Azure Storage
description: Informazioni su come usare le identità gestite per le risorse di Azure per autorizzare l'accesso ai dati BLOB e coda da applicazioni in esecuzione in macchine virtuali di Azure, app per le funzioni, set di scalabilità di macchine virtuali e altri.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: f3bac0d47a53da1ec4d1fa08b5f0933f5f65dc56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255340"
---
# <a name="authorize-access-to-blob-and-queue-data-with-managed-identities-for-azure-resources"></a>Autorizzare l'accesso ai dati BLOB e queue con identità gestite per le risorse di AzureAuthorize access to blob and queue data with managed identities for Azure resources

Archiviazione di BLOB e coda di Azure supporta l'autenticazione con [identità gestite di Azure Active Directory per le risorse di Azure](../../active-directory/managed-identities-azure-resources/overview.md). Le identità gestite per le risorse di Azure possono autorizzare l'accesso ai dati BLOB e queue usando le credenziali di Azure AD da applicazioni in esecuzione in macchine virtuali di Azure, app per le funzioni, set di scalabilità di macchine virtuali e altri servizi. Usando le identità gestite per le risorse di Azure con l'autenticazione di Azure AD, è possibile evitare di archiviare le credenziali con le applicazioni eseguite nel cloud.  

Questo articolo illustra come autorizzare l'accesso ai dati BLOB o della coda da una macchina virtuale di Azure usando le identità gestite per le risorse di Azure.This article shows how to authorize access to blob or queue data from an Azure VM using managed identities for Azure Resources. Viene inoltre descritto come testare il codice nell'ambiente di sviluppo.

## <a name="enable-managed-identities-on-a-vm"></a>Abilitare le identità gestite su una macchina virtuale

Prima di poter usare le identità gestite per le risorse di Azure per autorizzare l'accesso a BLOB e code dalla macchina virtuale, è necessario abilitare le identità gestite per le risorse di Azure nella macchina virtuale. Per informazioni su come abilitare identità gestite per le risorse di Azure, vedere uno di questi articoli:

- [Portale di Azure](https://docs.microsoft.com/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Interfaccia della riga di comando di AzureAzure](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Modello di Azure Resource ManagerAzure Resource Manager template](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Librerie client di Azure Resource ManagerAzure Resource Manager client libraries](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

Per altre informazioni sulle identità gestite, vedere Identità gestite per le risorse di Azure.For more information about managed [identities, see Managed identities for Azure resources.](../../active-directory/managed-identities-azure-resources/overview.md)

## <a name="authenticate-with-the-azure-identity-library"></a>Eseguire l'autenticazione con la libreria di identità di AzureAuthenticate with the Azure Identity library

La libreria client di Azure Identity fornisce il supporto dell'autenticazione token di Azure AD per [Azure SDK.](https://github.com/Azure/azure-sdk) Le versioni più recenti delle librerie client di Archiviazione di Azure per .NET, Java, Python e JavaScript si integrano con la libreria di identità di Azure per fornire un mezzo semplice e sicuro per acquisire un token OAuth 2.0 per l'autorizzazione delle richieste di Archiviazione di Azure.The latest versions of the Azure Storage client libraries for .NET, Java, Python, and JavaScript integrate with the Azure Identity library to provide a simple and secure means to acquire an OAuth 2.0 token for authorization of Azure Storage requests.

Un vantaggio della libreria client di identità di Azure è che consente di usare lo stesso codice per autenticare se l'applicazione è in esecuzione nell'ambiente di sviluppo o in Azure.An advantage of the Azure Identity client library is that it enables you to use the same code to authenticate whether your application is running in the development environment or in Azure. La libreria client di Azure Identity per .NET autentica un'entità di sicurezza. Quando il codice è in esecuzione in Azure, l'entità di sicurezza è un'identità gestita per le risorse di Azure.When your code is running in Azure, the security principal is a managed identity for Azure resources. Nell'ambiente di sviluppo, l'identità gestita non esiste, pertanto la libreria client autentica l'utente o un'entità servizio a scopo di test.

Dopo l'autenticazione, la libreria client di Azure Identity ottiene una credenziale token. Questa credenziale token viene quindi incapsulata nell'oggetto client del servizio creato per eseguire operazioni su Archiviazione di Azure.This token credential is then encapsulated in the service client object that you create to perform operations against Azure Storage. La libreria gestisce questo per voi senza soluzione di continuità ottenendo le credenziali di token appropriato.

Per altre informazioni sulla libreria client di Azure Identity per .NET, vedere [Libreria client di Identità di Azure per .NET.For](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity)more information about the Azure Identity client library for .NET, see Azure Identity client library for .NET . Per la documentazione di riferimento per la libreria client di Azure Identity, vedere Spazio dei nomi Azure.Identity.For reference documentation for the Azure Identity client library, see [Azure.Identity Namespace.](/dotnet/api/azure.identity)

### <a name="assign-role-based-access-control-rbac-roles-for-access-to-data"></a>Assegnare ruoli di controllo degli accessi in base al ruolo per l'accesso ai datiAssign role-based access control (RBAC) roles for access to data

Quando un'entità di sicurezza di Azure AD tenta di accedere ai dati BLOB o coda, tale entità di sicurezza deve disporre delle autorizzazioni per la risorsa. Se l'entità di sicurezza è un'identità gestita in Azure o un account utente di Azure AD che esegue il codice nell'ambiente di sviluppo, all'entità di sicurezza deve essere assegnato un ruolo Controllo degli accessi in base al ruolo che conceda l'accesso ai dati BLOB o della coda in Archiviazione di Azure.Whether the security principal is a managed identity in Azure or an Azure AD user account running code in the development environment, the security principal must be assigned an RBAC role that grants access to blob or queue data in Azure Storage. Per informazioni sull'assegnazione di autorizzazioni tramite controllo degli accessi in base al ruolo, vedere la sezione **relativa all'assegnazione** dei ruoli RBAC per i diritti di accesso in [Autorizzare l'accesso ai BLOB e alle code di Azure tramite Azure Active Directory.](../common/storage-auth-aad.md#assign-rbac-roles-for-access-rights)

### <a name="authenticate-the-user-in-the-development-environment"></a>Autenticare l'utente nell'ambiente di sviluppoAuthenticate the user in the development environment

Quando il codice è in esecuzione nell'ambiente di sviluppo, l'autenticazione può essere gestita automaticamente o potrebbe richiedere un account di accesso del browser, a seconda degli strumenti in uso. Ad esempio, Microsoft Visual Studio supporta Single Sign-On (SSO), in modo che l'account utente di Azure AD attivo venga usato automaticamente per l'autenticazione. Per ulteriori informazioni su SSO, vedere [Single Sign-On to applications](../../active-directory/manage-apps/what-is-single-sign-on.md).

Altri strumenti di sviluppo potrebbero richiedere l'accesso tramite un browser web.

### <a name="authenticate-a-service-principal-in-the-development-environment"></a>Autenticare un'entità servizio nell'ambiente di sviluppoAuthenticate a service principal in the development environment

Se l'ambiente di sviluppo non supporta l'accesso Single Sign-On o l'accesso tramite un Web browser, è possibile usare un'entità servizio per l'autenticazione dall'ambiente di sviluppo.

#### <a name="create-the-service-principal"></a>Creare l'entità servizio

Per creare un'entità servizio con l'interfaccia della riga di comando di Azure e assegnare un ruolo Controllo degli accessi in base al ruolo, chiamare il comando [az ad sp create-for-rbac.](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) Fornire un ruolo di accesso ai dati di Archiviazione di Azure da assegnare alla nuova entità servizio. Inoltre, fornire l'ambito per l'assegnazione di ruolo. Per altre informazioni sui ruoli predefiniti forniti per Archiviazione di Azure, vedere Ruoli predefiniti per le risorse di Azure.For more information about the built-in roles provided for Azure Storage, see [Built-in roles for Azure resources.](../../role-based-access-control/built-in-roles.md)

Se non si dispone di autorizzazioni sufficienti per assegnare un ruolo all'entità servizio, potrebbe essere necessario chiedere al proprietario o all'amministratore dell'account di eseguire l'assegnazione del ruolo.

L'esempio seguente usa l'interfaccia della riga di comando di Azure per creare una nuova entità servizio e assegnarle il ruolo **Lettore dati BLOB** di archiviazione con ambito account

```azurecli-interactive
az ad sp create-for-rbac \
    --name <service-principal> \
    --role "Storage Blob Data Reader" \
    --scopes /subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

Il `az ad sp create-for-rbac` comando restituisce un elenco di proprietà dell'entità servizio in formato JSON. Copiare questi valori in modo che sia possibile utilizzarli per creare le variabili di ambiente necessarie nel passaggio successivo.

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
> La propagazione delle assegnazioni di ruolo RBAC può richiedere alcuni minuti.

#### <a name="set-environment-variables"></a>Impostare le variabili di ambiente

La libreria client di Azure Identity legge i valori di tre variabili di ambiente in fase di esecuzione per autenticare l'entità servizio. Nella tabella seguente viene descritto il valore da impostare per ogni variabile di ambiente.

|Variabile di ambiente|valore
|-|-
|`AZURE_CLIENT_ID`|ID dell'app per l'entità servizio
|`AZURE_TENANT_ID`|ID tenant di Azure AD dell'entità servizioThe service principal's Azure AD tenant ID
|`AZURE_CLIENT_SECRET`|Password generata per l'entità servizio

> [!IMPORTANT]
> Dopo aver impostato le variabili di ambiente, chiudere e riaprire la finestra della console. Se si utilizza Visual Studio o un altro ambiente di sviluppo, potrebbe essere necessario riavviare l'ambiente di sviluppo per registrare le nuove variabili di ambiente.

Per altre informazioni, vedere [Creare un'identità per l'app Azure nel portale.](../../active-directory/develop/howto-create-service-principal-portal.md)

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

## <a name="net-code-example-create-a-block-blob"></a>Esempio di codice .NET: creare un BLOB in blocchi

Aggiungere le `using` direttive seguenti al codice per usare le librerie client di Archiviazione di Azure.Add the following directives to your code to use the Azure Identity and Azure Storage client libraries.

```csharp
using Azure;
using Azure.Identity;
using Azure.Storage.Blobs;
using System;
using System.IO;
using System.Text;
using System.Threading.Tasks;
```

Per ottenere una credenziale token che il codice può usare per autorizzare le richieste ad Archiviazione di Azure, creare un'istanza della classe [DefaultAzureCredential.To](/dotnet/api/azure.identity.defaultazurecredential) get a token credential that your code can use to authorize requests to Azure Storage, create an instance of the DefaultAzureCredential class. L'esempio di codice seguente mostra come ottenere le credenziali del token autenticato e usarlo per creare un oggetto client del servizio, quindi usare il client del servizio per caricare un nuovo BLOB:The following code example shows how to get the authenticated token credential and use it to create a service client object, then use the service client to upload a new blob:

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
> Per autorizzare le richieste per i dati BLOB o coda con Azure AD, è necessario usare HTTPS per tali richieste.

## <a name="next-steps"></a>Passaggi successivi

- Gestire i diritti di [accesso ai dati di archiviazione con il controllo degli accessi in base al ruolo](storage-auth-aad-rbac.md).
- [Usare Azure AD con le applicazioni di archiviazione.](storage-auth-aad-app.md)
- [Eseguire comandi dell'interfaccia della riga di comando o di PowerShell](authorize-active-directory-powershell.md)di Azure con credenziali di Azure AD per accedere ai dati BLOB o della coda.
