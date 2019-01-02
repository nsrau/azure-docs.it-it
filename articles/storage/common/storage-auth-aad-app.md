---
title: Eseguire l'autenticazione con Azure Active Directory per accedere ai dati BLOB e della coda dalle applicazioni (anteprima) | Microsoft Docs
description: Usare Azure Active Directory per eseguire l'autenticazione dall'interno di un'applicazione e quindi autorizzare le richieste ai BLOB e alle code di Azure (anteprima).
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 11/21/2018
ms.author: tamram
ms.component: common
ms.openlocfilehash: b817723120f07de9159e47c1259a68eb95b9c2e3
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2018
ms.locfileid: "53140779"
---
# <a name="authenticate-with-azure-active-directory-from-an-application-for-access-to-blobs-and-queues-preview"></a>Eseguire l'autenticazione con Azure Active Directory da un'applicazione per l'accesso ai BLOB e alle code (anteprima)

Un vantaggio fondamentale dell'uso di Azure Active Directory (Azure AD) con Archiviazione di Azure è che le credenziali non devono più essere archiviate nel codice. È invece possibile richiedere un token di accesso OAuth 2.0 da Azure AD. Azure AD gestisce l'autenticazione dell'entità di sicurezza (un utente, un gruppo o un'entità servizio) che esegue l'applicazione. Se l'autenticazione riesce, Azure AD restituisce il token di accesso all'applicazione e questa può quindi usare il token di accesso per autorizzare le richieste ad Archiviazione di Azure.

Questo articolo mostra come configurare l'applicazione per l'autenticazione con Azure AD. L'esempio di codice è con .NET, ma altri linguaggi usano un approccio simile.

Prima di poter autenticare un'entità di sicurezza dall'applicazione di Archiviazione di Azure, configurare le impostazioni di controllo degli accessi in base al ruolo per l'entità di sicurezza. Archiviazione di Azure definisce ruoli di controllo degli accessi in base al ruolo che includono autorizzazioni per contenitori e code. Quando il ruolo di controllo degli accessi in base al ruolo viene assegnato a un'entità di sicurezza, a questa viene concesso l'accesso a tale risorsa. Per altre informazioni, vedere [Gestire i diritti di accesso a dati di archiviazione con il controllo degli accessi in base al ruolo (anteprima)](storage-auth-aad-rbac.md).

Per una panoramica del flusso di concessione del codice di OAuth 2.0, vedere [Autorizzare l'accesso ad applicazioni Web di Azure Active Directory mediante il flusso di concessione di OAuth 2.0](../../active-directory/develop/v1-protocols-oauth-code.md).

[!INCLUDE [storage-auth-aad-note-include](../../../includes/storage-auth-aad-note-include.md)]

## <a name="assign-an-rbac-role-to-an-azure-ad-security-principal"></a>Assegnare un ruolo Controllo degli accessi in base al ruolo a un'entità di sicurezza di Azure AD

Prima di poter autenticare un'entità di sicurezza dall'applicazione di Archiviazione di Azure, configurare le impostazioni di controllo degli accessi in base al ruolo per l'entità di sicurezza. Archiviazione di Azure definisce ruoli di controllo degli accessi in base al ruolo che includono autorizzazioni per contenitori e code. Quando il ruolo di controllo degli accessi in base al ruolo viene assegnato a un'entità di sicurezza, a questa viene concesso l'accesso a tale risorsa. Per altre informazioni, vedere [Gestire i diritti di accesso a dati di code e BLOB di Azure con il controllo degli accessi in base al ruolo (anteprima)](storage-auth-aad-rbac.md).

## <a name="register-your-application-with-an-azure-ad-tenant"></a>Registrare l'applicazione nel tenant di Azure AD

Il primo passaggio per l'uso di Azure AD per autorizzare l'accesso a risorse di archiviazione consiste nella registrazione dell'applicazione client in un tenant di Azure AD. La registrazione dell'applicazione permette di chiamare [Active Directory Authentication Library](../../active-directory/active-directory-authentication-libraries.md) dal codice. ADAL offre un'API per eseguire l'autenticazione con Azure AD dall'applicazione. La registrazione dell'applicazione permette anche di autorizzare chiamate dall'applicazione ad API di Archiviazione di Azure con un token di accesso.

Quando si registra l'applicazione, si danno informazioni sull'applicazione ad Azure AD. Azure AD fornisce quindi un ID client, chiamato anche *ID applicazione*, da usare per associare l'applicazione ad Azure AD in fase di esecuzione. Per altre informazioni sull'ID client, vedere [Oggetti applicazione e oggetti entità servizio in Azure Active Directory](../../active-directory/develop/app-objects-and-service-principals.md).

Per registrare l'applicazione di Archiviazione di Azure, seguire i passaggi descritti nella sezione [Aggiunta di un'applicazione](../../active-directory/develop/quickstart-v1-add-azure-ad-app.md) in [Integrazione di applicazioni con Azure Active Directory](../../active-directory/active-directory-integrating-applications.md). Se si registra l'applicazione come applicazione nativa, è possibile specificare qualsiasi URI valido come **URI di reindirizzamento**. Il valore non deve essere un endpoint reale.

![Screenshot che mostra come registrare l'applicazione di archiviazione in Azure AD](./media/storage-auth-aad-app/app-registration.png)

Al termine della registrazione dell'applicazione, verrà visualizzato l'ID applicazione (o ID client) in **Impostazioni**:

![Screenshot che mostra l'ID client](./media/storage-auth-aad-app/app-registration-client-id.png)

Per altre informazioni sulla registrazione di un'applicazione in Azure AD, vedere [Integrazione di applicazioni con Azure Active Directory](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md). 

## <a name="grant-your-registered-app-permissions-to-azure-storage"></a>Concedere all'app registrata autorizzazioni per Archiviazione di Azure

È quindi necessario concedere all'applicazione le autorizzazioni necessarie per chiamare API di Archiviazione di Azure. Questo passaggio permette all'applicazione di autorizzare le chiamate ad Archiviazione di Azure con Azure AD.

1. Nel riquadro di spostamento sinistro del portale di Azure scegliere **Tutti i servizi** e quindi individuare **Registrazioni per l'app**.
2. Cercare il nome dell'applicazione registrata creata nel passaggio precedente.
3. Selezionare l'app registrata e fare clic su **Impostazioni**. Nella sezione **Accesso all'API** selezionare **Autorizzazioni necessarie**.
4. Nel pannello **Autorizzazioni necessarie** fare clic sul pulsante **Aggiungi**.
5. In **Selezionare un'API**, cercare "Archiviazione di Azure" e selezionare **Archiviazione di Azure** nell'elenco dei risultati.

    ![Screenshot che mostra le autorizzazioni per l'archiviazione](media/storage-auth-aad-app/registered-app-permissions-1.png)

6. In **Selezionare le autorizzazioni** selezionare la casella accanto a **Access Azure Storage** (Accesso ad Archiviazione di Azure) e quindi fare clic su **Seleziona**.
7. Fare clic su **Done**.

La finestra **Autorizzazioni necessarie** mostra ora che l'applicazione di Azure AD ha accesso sia ad Azure Active Directory sia ad Archiviazione di Azure. Le autorizzazioni vengono automaticamente concesse ad Azure AD quando si registra l'app per la prima volta in Azure AD.

![Screenshot che mostra le autorizzazioni dell'app registrata](media/storage-auth-aad-app/registered-app-permissions-2.png)

## <a name="net-code-example-create-a-block-blob"></a>Esempio di codice .NET: Creare un BLOB in blocchi

L'esempio di codice mostra come ottenere un token di accesso da Azure AD. Il token di accesso viene usato per l'autenticazione dell'utente specificato e quindi per l'autorizzazione di una richiesta di creazione di un BLOB in blocchi. Per rendere operativo questo esempio, seguire prima di tutto i passaggi forniti nelle sezioni precedenti.

> [!NOTE]
> In quanto proprietario dell'account di archiviazione di Azure, non si ricevono automaticamente le autorizzazioni per l'accesso ai dati. È invece necessario assegnare in modo esplicito a se stessi un ruolo di controllo degli accessi in base al ruolo per Archiviazione di Azure. È possibile assegnare questo ruolo a livello di sottoscrizione, gruppo di risorse, account di archiviazione o singolo contenitore o coda. 
>
> Ad esempio, per eseguire il codice di esempio in un account di archiviazione in cui si è proprietario e usando la propria identità utente, è necessario assegnare a se stessi il ruolo di controllo degli accessi in base al ruolo Collaboratore ai dati del BLOB di archiviazione. In caso contrario, la chiamata per creare il BLOB non riuscirà e restituirà il codice di stato HTTP 403 (operazione non consentita). Per altre informazioni, vedere [Gestire i diritti di accesso a dati di archiviazione con il controllo degli accessi in base al ruolo (anteprima)](storage-auth-aad-rbac.md).

### <a name="well-known-values-for-authentication-with-azure-ad"></a>Valori ben noti per l'autenticazione con Azure AD

Per autenticare un'entità di sicurezza con Azure AD, è necessario includere alcuni valori ben noti nel codice.

#### <a name="azure-ad-authority"></a>Autorità Azure AD

Per il cloud pubblico di Microsoft, l'autorità di base di Azure AD è la seguente, dove *tenant-id* è l'ID tenant di Active Directory (o ID directory):

`https://login.microsoftonline.com/<tenant-id>/`

L'ID tenant identifica il tenant di Azure AD da usare per l'autenticazione. Per recuperare l'ID tenant, seguire i passaggi descritti in **Ottenere l'ID tenant per Azure Active Directory**.

#### <a name="storage-resource-id"></a>ID risorsa di archiviazione

Usare l'ID risorsa di archiviazione di Azure per acquisire un token per l'autenticazione delle richieste ad Azure AD:

`https://storage.azure.com/`

### <a name="get-the-tenant-id-for-your-azure-active-directory"></a>Ottenere l'ID tenant per Azure Active Directory

Per ottenere l'ID tenant, seguire questa procedura:

1. Nel portale di Azure selezionare Active Directory.
2. Fare clic su **Proprietà**.
3. Copiare il valore GUID specificato per **ID directory**. Questo valore viene chiamato anche ID tenant.

![Screenshot che mostra come copiare l'ID tenant](./media/storage-auth-aad-app/aad-tenant-id.png)

### <a name="add-references-and-using-statements"></a>Aggiungere riferimenti e istruzioni using  

In Visual Studio installare la versione di anteprima della libreria client di Archiviazione di Azure. Scegliere **Gestione pacchetti NuGet** dal menu **Strumenti** e quindi fare clic su **Console di Gestione pacchetti**. Digitare il comando seguente nella console per installare la versione più recente della libreria client per .NET:

```
Install-Package WindowsAzure.Storage
```

Installare anche la versione più recente di ADAL:

```
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
```

Aggiungere quindi le istruzioni using seguenti al codice:

```dotnet
using System.Globalization;
using Microsoft.IdentityModel.Clients.ActiveDirectory; //ADAL client library for getting the access token
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;
```

### <a name="get-an-oauth-token-from-azure-ad"></a>Ottenere un token OAuth da Azure AD

Aggiungere quindi un metodo per richiedere un token da Azure AD. Per richiedere il token, chiamare il metodo [AuthenticationContext.AcquireTokenAsync](https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.clients.activedirectory.authenticationcontext.acquiretokenasync). Assicurarsi di avere annotato i valori seguenti nei passaggi eseguiti in precedenza:

- ID Tenant (directory)
- ID Client (applicazione)
- URI di reindirizzamento del client

```dotnet
static string GetUserOAuthToken()
{
    const string ResourceId = "https://storage.azure.com/";
    const string AuthInstance = "https://login.microsoftonline.com/{0}/";
    const string TenantId = "<tenant-id>"; // Tenant or directory ID

    // Construct the authority string from the Azure AD OAuth endpoint and the tenant ID. 
    string authority = string.Format(CultureInfo.InvariantCulture, AuthInstance, TenantId);
    AuthenticationContext authContext = new AuthenticationContext(authority);

    // Acquire an access token from Azure AD. 
    AuthenticationResult result = authContext.AcquireTokenAsync(ResourceId, 
                                                                "<client-id>", 
                                                                new Uri(@"<client-redirect-uri>"), 
                                                                new PlatformParameters(PromptBehavior.Auto)).Result;

    return result.AccessToken;
}
```

### <a name="create-the-block-blob"></a>Creare il BLOB in blocchi

Infine, usare il token di accesso per creare nuove credenziali di archiviazione, da usare per creare il BLOB:

```dotnet
// Get the access token.
string accessToken = GetUserOAuthToken();

// Use the access token to create the storage credentials.
TokenCredential tokenCredential = new TokenCredential(accessToken);
StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

// Create a block blob using those credentials
CloudBlockBlob blob = new CloudBlockBlob(new Uri("https://storagesamples.blob.core.windows.net/sample-container/Blob1.txt"), storageCredentials);

blob.UploadTextAsync("Blob created by Azure AD authenticated user.");
```

> [!NOTE]
> L'integrazione di Azure AD con Archiviazione di Azure richiede l'uso di HTTPS per le operazioni di Archiviazione di Azure.

Nell'esempio precedente, la libreria client .NET gestisce l'autorizzazione della richiesta di creazione del blob in blocchi. Anche altre librerie client di archiviazione possono gestire l'autorizzazione della richiesta, ma se si chiama un'operazione di Archiviazione di Azure con un token OAuth tramite l'API REST, è necessario autorizzare la richiesta usando il token OAuth.   

Per chiamare operazioni del Servizio BLOB e del Servizio di accodamento usando token di accesso OAuth, passare il token di accesso nell'intestazione **Autorizzazione** intestazione tramite lo schema **Bearer** e specificare come versione del servizio la versione 2017-09-11 o successiva, come illustrato nell'esempio seguente:

```
GET /container/file.txt HTTP/1.1
Host: mystorageaccount.blob.core.windows.net
x-ms-version: 2017-11-09
Authorization: Bearer eyJ0eXAiOnJKV1...Xd6j
```

Per altre informazioni sull'autorizzazione di operazioni di Archiviazione di Azure da REST, vedere [Authenticate with Azure Active Directory (Preview)](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory) (Eseguire l'autenticazione con Azure Active Directory (anteprima)).

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sui ruoli RBAC per Archiviazione di Azure, vedere [Manage access rights to storage data with RBAC (Preview)](storage-auth-aad-rbac.md) (Gestire i diritti di accesso ai dati di archiviazione con il controllo degli accessi in base al ruolo - anteprima).
- Per altre informazioni sull'uso delle identità gestite per le risorse di Azure con Archiviazione di Azure, vedere [Authenticate access to blobs and queues with Azure managed identities for Azure Resources (Preview)](storage-auth-aad-msi.md) (Autenticare l'accesso a BLOB e code con le identità gestite di Azure per le risorse di Azure - anteprima).
- Per informazioni su come accedere all'interfaccia della riga di comando di Azure e a PowerShell con un'identità di Azure AD, vedere [Usare un'identità di Azure AD per accedere ad Archiviazione di Azure con l'interfaccia della riga di comando o PowerShell (anteprima)](storage-auth-aad-script.md).
- Per altre informazioni sull'integrazione di Azure AD per BLOB e code di Azure, vedere il post di blog del team di Archiviazione di Azure sull'[annuncio della versione di anteprima di Autenticazione di Azure AD per Archiviazione di Azure](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/).



