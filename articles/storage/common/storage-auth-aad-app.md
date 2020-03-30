---
title: Acquisire un token da Azure AD per autorizzare le richieste da un'applicazione clientAcquire a token from Azure AD for authorizing requests from a client application
titleSuffix: Azure Storage
description: Usare Azure Active Directory per eseguire l'autenticazione dall'interno di un'applicazione client, acquisire un token OAuth 2.0 e autorizzare le richieste all'archiviazione BLOB di Azure e all'archiviazione delle code.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: d3ee211298598d78f423d88fd4df1c58ed4bfa29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268483"
---
# <a name="acquire-a-token-from-azure-ad-for-authorizing-requests-from-a-client-application"></a>Acquisire un token da Azure AD per autorizzare le richieste da un'applicazione clientAcquire a token from Azure AD for authorizing requests from a client application

Un vantaggio chiave dell'uso di Azure Active Directory (Azure AD) con l'archiviazione BLOB di Azure o l'archiviazione delle code è che le credenziali non devono più essere archiviate nel codice. È invece possibile richiedere un token di accesso OAuth 2.0 dalla piattaforma di identità Microsoft (in precedenza Azure AD). Azure AD autentica l'entità di sicurezza (un utente, un gruppo o un'entità servizio) che esegue l'applicazione. Se l'autenticazione ha esito positivo, Azure AD restituisce il token di accesso all'applicazione e l'applicazione può quindi usare il token di accesso per autorizzare le richieste all'archiviazione BLOB di Azure o all'archiviazione della coda.

In questo articolo viene illustrato come configurare l'applicazione nativa o l'applicazione Web per l'autenticazione con Microsoft Identity Platform 2.0.This article shows how to configure your native application or web application for authentication with Microsoft identity platform 2.0. L'esempio di codice è con .NET, ma altri linguaggi usano un approccio simile. Per ulteriori informazioni sulla piattaforma di identità Microsoft 2.0, vedere Panoramica della piattaforma di [identità Microsoft (v2.0).](../../active-directory/develop/v2-overview.md)

Per una panoramica del flusso di concessione del codice di OAuth 2.0, vedere [Autorizzare l'accesso ad applicazioni Web di Azure Active Directory mediante il flusso di concessione di OAuth 2.0](../../active-directory/develop/v2-oauth2-auth-code-flow.md).

## <a name="assign-a-role-to-an-azure-ad-security-principal"></a>Assegnare un ruolo a un'entità di sicurezza di Azure ADAssign a role to an Azure AD security principal

Prima di poter autenticare un'entità di sicurezza dall'applicazione di Archiviazione di Azure, configurare le impostazioni di controllo degli accessi in base al ruolo per l'entità di sicurezza. Archiviazione di Azure definisce i ruoli RBAC predefiniti che includono le autorizzazioni per contenitori e code. Quando il ruolo di controllo degli accessi in base al ruolo viene assegnato a un'entità di sicurezza, a questa viene concesso l'accesso a tale risorsa. Per altre informazioni, vedere Gestire i diritti di accesso ai dati BLOB e coda di [Azure con il controllo degli accessi in base](storage-auth-aad-rbac.md)al ruolo.

## <a name="register-your-application-with-an-azure-ad-tenant"></a>Registrare l'applicazione nel tenant di Azure AD

Il primo passaggio nell'uso di Azure AD per autorizzare l'accesso alle risorse di archiviazione consiste nella registrazione dell'applicazione client con un tenant di Azure AD dal portale di Azure.The first step in using Azure AD to authorize access to storage resources is registering your client application with an Azure AD tenant from the [Azure portal](https://portal.azure.com). Quando si registra l'applicazione client, si forniscono informazioni sull'applicazione ad Azure AD. Azure AD fornisce quindi un ID client, chiamato anche *ID applicazione*, da usare per associare l'applicazione ad Azure AD in fase di esecuzione. Per altre informazioni sull'ID client, vedere [Oggetti applicazione e oggetti entità servizio in Azure Active Directory](../../active-directory/develop/app-objects-and-service-principals.md).

Per registrare l'applicazione Archiviazione di Azure, seguire i passaggi illustrati in [Guida introduttiva: Registrare un'applicazione con la piattaforma di identità Microsoft](../../active-directory/develop/quickstart-configure-app-access-web-apis.md). L'immagine seguente mostra le impostazioni comuni per la registrazione di un'applicazione Web:

![Screenshot che mostra come registrare l'applicazione di archiviazione con Azure AD](./media/storage-auth-aad-app/app-registration.png)

> [!NOTE]
> Se si registra l'applicazione come applicazione nativa, è possibile specificare qualsiasi URI valido come **URI di reindirizzamento**. Per le applicazioni native, questo valore non deve essere un URL reale. Per le applicazioni Web, l'URI di reindirizzamento deve essere un URI valido, perché specifica l'URL in cui vengono forniti i token.

Al termine della registrazione dell'applicazione, verrà visualizzato l'ID applicazione (o ID client) in **Impostazioni**:

![Schermata che mostra l'ID client](./media/storage-auth-aad-app/app-registration-client-id.png)

Per altre informazioni sulla registrazione di un'applicazione in Azure AD, vedere [Integrazione di applicazioni con Azure Active Directory](../../active-directory/develop/quickstart-v2-register-an-app.md).

## <a name="grant-your-registered-app-permissions-to-azure-storage"></a>Concedere all'app registrata autorizzazioni per Archiviazione di Azure

Successivamente, concedere all'applicazione le autorizzazioni per chiamare le API di Archiviazione di Azure.Next, grant your application permissions to call Azure Storage APIs. Questo passaggio consente all'applicazione di autorizzare le richieste ad Archiviazione di Azure con Azure AD.

1. Nella pagina **Panoramica** dell'applicazione registrata selezionare **Visualizza autorizzazioni API**.
1. Nella sezione **Autorizzazioni API** selezionare **Aggiungi un'autorizzazione** e scegliere **API Microsoft**.
1. Selezionare **Archiviazione di Azure** nell'elenco dei risultati per visualizzare il riquadro Autorizzazioni API **richiesta.**
1. In **Tipo di autorizzazioni richiesta dall'applicazione,** osservare che il tipo di autorizzazione disponibile è Autorizzazioni **delegate**. Questa opzione è selezionata per impostazione predefinita.
1. Nella sezione **Selezione autorizzazioni** del riquadro Richiedi **autorizzazioni API** selezionare la casella di controllo accanto a **user_impersonation**, quindi fare clic su **Aggiungi autorizzazioni**.

    ![Schermata che mostra le autorizzazioni per l'archiviazione](media/storage-auth-aad-app/registered-app-permissions-1.png)

Il riquadro Autorizzazioni API ora mostra che l'applicazione Azure AD registrata ha accesso sia a Microsoft Graph che ad Archiviazione di Azure.The **API permissions** pane now shows that your registered Azure AD application has access to both Microsoft Graph and the Azure Storage. Le autorizzazioni vengono concesse automaticamente a Microsoft Graph quando si registra l'app per la prima volta con Azure AD.

![Schermata che mostra le autorizzazioni per registrare le app](media/storage-auth-aad-app/registered-app-permissions-2.png)

## <a name="create-a-client-secret"></a>Creare un segreto client

L'applicazione necessita di un segreto client per dimostrare la propria identità quando si richiede un token. Per aggiungere il segreto client, attenersi alla seguente procedura:

1. Passare alla registrazione dell'app nel portale di Azure.Navigate to your app registration in the Azure portal.
1. Selezionare l'impostazione **Certificati & segreti.**
1. In **Segreti client**fare clic su Nuovo **segreto client** per creare un nuovo segreto.
1. Fornire una descrizione per il segreto e scegliere l'intervallo di scadenza desiderato.
1. Copiare immediatamente il valore del nuovo segreto in un luogo sicuro. Il valore completo viene visualizzato all'utente una sola volta.

    ![Schermata che mostra il segreto client](media/storage-auth-aad-app/client-secret.png)

## <a name="client-libraries-for-token-acquisition"></a>Librerie client per l'acquisizione di token

Dopo aver registrato l'applicazione e concesso le autorizzazioni per accedere ai dati nell'archiviazione BLOB di Azure o nell'archiviazione delle code, è possibile aggiungere codice all'applicazione per autenticare un'entità di sicurezza e acquisire un token OAuth 2.0.Once you have registered your application and granted it permissions to access data in Azure Blob storage or Queue storage, you can add code to your application to authenticate a security principal and acquire an OAuth 2.0 token. Per autenticare e acquisire il token, è possibile utilizzare una delle librerie di autenticazione della piattaforma di [identità Microsoft](../../active-directory/develop/reference-v2-libraries.md) o un'altra libreria open source che supporta OpenID Connect 1.0. L'applicazione può quindi usare il token di accesso per autorizzare una richiesta sull'archiviazione BLOB di Azure o sull'archiviazione delle code.

Per un elenco degli scenari per i quali è supportata l'acquisizione di token, vedere la sezione [relativa ai flussi](/en-us/azure/active-directory/develop/msal-authentication-flows) di autenticazione del [contenuto della libreria di autenticazione Microsoft](/azure/active-directory/develop/msal-overview).

## <a name="well-known-values-for-authentication-with-azure-ad"></a>Valori ben noti per l'autenticazione con Azure AD

Per autenticare un'entità di sicurezza con Azure AD, è necessario includere alcuni valori ben noti nel codice.

### <a name="azure-ad-authority"></a>Autorità Azure AD

Per il cloud pubblico di Microsoft, l'autorità di base di Azure AD è la seguente, dove *tenant-id* è l'ID tenant di Active Directory (o ID directory):

`https://login.microsoftonline.com/<tenant-id>/`

L'ID tenant identifica il tenant di Azure AD da usare per l'autenticazione. Viene anche definito ID di directory. Per recuperare l'ID tenant, passare alla pagina **Panoramica** per la registrazione dell'app nel portale di Azure e copiare il valore da tale posizione.

### <a name="azure-storage-resource-id"></a>ID risorsa di Archiviazione di AzureAzure Storage resource ID

[!INCLUDE [storage-resource-id-include](../../../includes/storage-resource-id-include.md)]

## <a name="net-code-example-create-a-block-blob"></a>Esempio di codice .NET: creare un BLOB in blocchi

L'esempio di codice mostra come ottenere un token di accesso da Azure AD. Il token di accesso viene usato per l'autenticazione dell'utente specificato e quindi per l'autorizzazione di una richiesta di creazione di un BLOB in blocchi. Per rendere operativo questo esempio, seguire prima di tutto i passaggi forniti nelle sezioni precedenti.

Per richiedere il token, sono necessari i seguenti valori dalla registrazione dell'app:

- Nome del dominio di Azure AD. Recuperare questo valore dalla pagina **Panoramica** di Azure Active Directory.Retrieve this value from the Overview page of your Azure Active Directory.
- ID tenant (o directory). Recupera questo valore dalla pagina **Panoramica** della registrazione dell'app.
- ID del client (o dell'applicazione). Recupera questo valore dalla pagina **Panoramica** della registrazione dell'app.
- URI di reindirizzamento client. Recupera questo valore dalle impostazioni **di autenticazione** per la registrazione dell'app.
- Valore del segreto client. Recuperare questo valore dalla posizione in cui è stato copiato in precedenza.

### <a name="create-a-storage-account-and-container"></a>Creare un account di archiviazione e un contenitore

Per eseguire l'esempio di codice, creare un account di archiviazione all'interno della stessa sottoscrizione di Azure Active Directory.To run the code sample, create a storage account within the same subscription as your Azure Active Directory. Creare quindi un contenitore all'interno dell'account di archiviazione. Il codice di esempio creerà un BLOB in blocchi in questo contenitore.

Assegnare quindi in modo esplicito il ruolo **Collaboratore dati BLOB di archiviazione** all'account utente con cui verrà eseguito il codice di esempio. Per istruzioni su come assegnare questo ruolo nel portale di Azure, vedere [Concedere l'accesso ai dati BLOB e coda](storage-auth-aad-rbac-portal.md)di Azure con il controllo degli accessi in base al ruolo nel portale di Azure.For instructions on how to assign this role in the Azure portal, see Grant access to Azure blob and queue data with RBAC in the Azure portal.

> [!NOTE]
> Quando si crea un account di Archiviazione di Azure, non vengono assegnate automaticamente le autorizzazioni per accedere ai dati tramite Azure AD. È invece necessario assegnare in modo esplicito a se stessi un ruolo di controllo degli accessi in base al ruolo per Archiviazione di Azure. È possibile assegnare questo ruolo a livello di sottoscrizione, gruppo di risorse, account di archiviazione o singolo contenitore o coda.

### <a name="create-a-web-application-that-authorizes-access-to-blob-storage-with-azure-ad"></a>Creare un'applicazione Web che autorizza l'accesso all'archiviazione BLOB con Azure ADCreate a web application that authorizes access to Blob storage with Azure AD

Quando l'applicazione accede ad Archiviazione di Azure, esegue questa operazione per conto dell'utente, vale a dire che l'accesso alle risorse BLOB o coda avviene tramite le autorizzazioni dell'utente connesso. Per provare questo esempio di codice, è necessaria un'applicazione Web che richiede all'utente di accedere usando un'identità di Azure AD. È possibile crearne uno personalizzato o utilizzare l'applicazione di esempio fornita da Microsoft.You can create your own, or use the sample application provided by Microsoft.

Un'applicazione Web di esempio completata che acquisisce un token e lo usa per creare un BLOB in Archiviazione di Azure è disponibile in [GitHub.](https://aka.ms/aadstorage) La revisione e l'esecuzione dell'esempio completato possono essere utili per comprendere gli esempi di codice. Per istruzioni su come eseguire l'esempio completato, vedere la sezione vista [ed eseguire l'esempio completato](#view-and-run-the-completed-sample).

#### <a name="add-references-and-using-statements"></a>Aggiungere riferimenti e istruzioni using  

Da Visual Studio installare la libreria client di Archiviazione di Azure.From Visual Studio, install the Azure Storage client library. Dal menu **Strumenti** , selezionare **NuGet Gestione pacchetti**, quindi Console di gestione **pacchetti**. Digitare i comandi seguenti nella finestra della console per installare i pacchetti necessari dalla libreria client di Archiviazione di Azure per .NET:

```console
Install-Package Microsoft.Azure.Storage.Blob
Install-Package Microsoft.Azure.Storage.Common
```

Successivamente, aggiungere le seguenti istruzioni using al file HomeController.cs:

```csharp
using Microsoft.Identity.Client; //MSAL library for getting the access token
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;
```

#### <a name="create-a-block-blob"></a>Creare un BLOB in blocchi

Aggiungere il frammento di codice seguente per creare un BLOB in blocchi:Add the following code snippet to create a block blob:

```csharp
private static async Task<string> CreateBlob(string accessToken)
{
    // Create a blob on behalf of the user
    TokenCredential tokenCredential = new TokenCredential(accessToken);
    StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

    // Replace the URL below with your storage account URL
    CloudBlockBlob blob =
        new CloudBlockBlob(
            new Uri("https://<storage-account>.blob.core.windows.net/<container>/Blob1.txt"),
            storageCredentials);
    await blob.UploadTextAsync("Blob created by Azure AD authenticated user.");
    return "Blob successfully created";
}
```

> [!NOTE]
> Per autorizzare le operazioni BLOB e queue con un token OAuth 2.0, è necessario usare HTTPS.

Nell'esempio precedente, la libreria client .NET gestisce l'autorizzazione della richiesta di creazione del blob in blocchi. Le librerie client di Archiviazione di Azure per altre lingue gestiscono anche l'autorizzazione della richiesta. ma se si chiama un'operazione di Archiviazione di Azure con un token OAuth tramite l'API REST, è necessario autorizzare la richiesta usando il token OAuth.

Per chiamare operazioni del Servizio BLOB e del Servizio di accodamento usando token di accesso OAuth, passare il token di accesso nell'intestazione **Autorizzazione** intestazione tramite lo schema **Bearer** e specificare come versione del servizio la versione 2017-09-11 o successiva, come illustrato nell'esempio seguente:

```https
GET /container/file.txt HTTP/1.1
Host: mystorageaccount.blob.core.windows.net
x-ms-version: 2017-11-09
Authorization: Bearer eyJ0eXAiOnJKV1...Xd6j
```

#### <a name="get-an-oauth-token-from-azure-ad"></a>Ottenere un token OAuth da Azure AD

Successivamente, aggiungere un metodo che richiede un token da Azure AD per conto dell'utente. Questo metodo definisce l'ambito per il quale devono essere concesse le autorizzazioni. Per ulteriori informazioni sulle autorizzazioni e sugli ambiti, vedere [Autorizzazioni e consenso nell'endpoint della piattaforma di identità Microsoft.](../../active-directory/develop/v2-permissions-and-consent.md)

Usare l'ID risorsa per costruire l'ambito per il quale acquisire il token. L'esempio costruisce l'ambito utilizzando l'ID di `user_impersonation` risorsa con l'ambito predefinito, che indica che il token viene richiesto per conto dell'utente.

Tieni presente che potrebbe essere necessario presentare all'utente un'interfaccia che consenta all'utente di acconsentire a richiedere il token per loro conto. Quando è necessario il consenso, l'esempio intercetta **l'eccezione MsalUiRequiredException** e chiama un altro metodo per facilitare la richiesta di consenso:

```csharp
public async Task<IActionResult> Blob()
{
    var scopes = new string[] { "https://storage.azure.com/user_impersonation" };
    try
    {
        var accessToken =
            await _tokenAcquisition.GetAccessTokenOnBehalfOfUser(HttpContext, scopes);
        ViewData["Message"] = await CreateBlob(accessToken);
        return View();
    }
    catch (MsalUiRequiredException ex)
    {
        AuthenticationProperties properties =
            BuildAuthenticationPropertiesForIncrementalConsent(scopes, ex);
        return Challenge(properties);
    }
}
```

Il consenso è il processo con cui un utente autorizza un'applicazione ad accedere per proprio conto a risorse protette. La piattaforma di identità Microsoft 2.0 supporta il consenso incrementale, il che significa che un'entità di sicurezza può richiedere inizialmente un set minimo di autorizzazioni e aggiungere autorizzazioni nel tempo in base alle esigenze. Quando il codice richiede un token di accesso, specifica l'ambito delle `scope` autorizzazioni necessarie all'app in base al parametro. Per ulteriori informazioni sul consenso incrementale, vedere la sezione intitolata **Consenso incrementale e dinamico** in [Perché eseguire l'aggiornamento a Microsoft Identity Platform (v2.0)?](../../active-directory/azuread-dev/azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent).

Il metodo seguente costruisce le proprietà di autenticazione per la richiesta di consenso incrementale:The following method constructs the authentication properties for requesting incremental consent:

```csharp
private AuthenticationProperties BuildAuthenticationPropertiesForIncrementalConsent(string[] scopes,
                                                                                    MsalUiRequiredException ex)
{
    AuthenticationProperties properties = new AuthenticationProperties();

    // Set the scopes, including the scopes that ADAL.NET or MSAL.NET need for the Token cache.
    string[] additionalBuildInScopes = new string[] { "openid", "offline_access", "profile" };
    properties.SetParameter<ICollection<string>>(OpenIdConnectParameterNames.Scope,
                                                 scopes.Union(additionalBuildInScopes).ToList());

    // Attempt to set the login_hint so that the logged-in user is not presented
    // with an account selection dialog.
    string loginHint = HttpContext.User.GetLoginHint();
    if (!string.IsNullOrWhiteSpace(loginHint))
    {
        properties.SetParameter<string>(OpenIdConnectParameterNames.LoginHint, loginHint);

        string domainHint = HttpContext.User.GetDomainHint();
        properties.SetParameter<string>(OpenIdConnectParameterNames.DomainHint, domainHint);
    }

    // Specify any additional claims that are required (for instance, MFA).
    if (!string.IsNullOrEmpty(ex.Claims))
    {
        properties.Items.Add("claims", ex.Claims);
    }

    return properties;
}
```

## <a name="view-and-run-the-completed-sample"></a>Visualizzare ed eseguire l'esempio completato

Per eseguire l'applicazione di esempio, clonarla o scaricarla da [GitHub](https://github.com/Azure-Samples/storage-dotnet-azure-ad-msal). Aggiornare quindi l'applicazione come descritto nelle sezioni seguenti.

### <a name="provide-values-in-the-settings-file"></a>Fornire valori nel file di impostazioni

Successivamente, aggiornare il file appsettings.json con i propri valori, come indicato di seguito:Next, update the *appsettings.json* file with your own values, as follows:

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "<azure-ad-domain-name>.onmicrosoft.com",
    "TenantId": "<tenant-id>",
    "ClientId": "<client-id>",
    "CallbackPath": "/signin-oidc",
    "SignedOutCallbackPath ": "/signout-callback-oidc",

    // To call an API
    "ClientSecret": "<client-secret>"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

### <a name="update-the-storage-account-and-container-name"></a>Aggiornare il nome dell'account di archiviazione e del contenitoreUpdate the storage account and container name

Nel file *HomeController.cs* aggiornare l'URI che fa riferimento al BLOB in blocchi per usare il nome dell'account di archiviazione e del contenitore:In the HomeController.cs file, update the URI that references the block blob to use the name of your storage account and container:

```csharp
CloudBlockBlob blob = new CloudBlockBlob(
                      new Uri("https://<storage-account>.blob.core.windows.net/<container>/Blob1.txt"),
                      storageCredentials);
```

### <a name="enable-implicit-grant-flow"></a>Abilitare il flusso di concessione implicitaEnable implicit grant flow

Per eseguire l'esempio, potrebbe essere necessario configurare il flusso di concessione implicita per la registrazione dell'app. A tale scopo, seguire questa procedura:

1. Passare alla registrazione dell'app nel portale di Azure.Navigate to your app registration in the Azure portal.
1. Nella sezione Gestisci selezionare l'impostazione **Autenticazione.In** the Manage section, select the Authentication setting.
1. In **Impostazioni avanzate**, nella sezione **Concessione implicita** selezionare le caselle di controllo per abilitare i token di accesso e i token ID, come illustrato nell'immagine seguente:

    ![Screenshot che mostra come abilitare le impostazioni per il flusso di concessione implicita](media/storage-auth-aad-app/enable-implicit-grant-flow.png)

### <a name="update-the-port-used-by-localhost"></a>Aggiornare la porta utilizzata da localhostUpdate the port used by localhost

Quando si esegue l'esempio, potrebbe essere necessario aggiornare l'URI di reindirizzamento specificato nella registrazione dell'app per usare la porta *localhost* assegnata in fase di esecuzione. Per aggiornare l'URI di reindirizzamento per utilizzare la porta assegnata, attenersi alla seguente procedura:

1. Passare alla registrazione dell'app nel portale di Azure.Navigate to your app registration in the Azure portal.
1. Nella sezione Gestisci selezionare l'impostazione **Autenticazione.In** the Manage section, select the Authentication setting.
1. In **Reindirizza URI**modificare la porta in modo che corrisponda a quella utilizzata dall'applicazione di esempio, come illustrato nell'immagine seguente:

    ![Screenshot che mostra gli URI di reindirizzamento per la registrazione dell'app](media/storage-auth-aad-app/redirect-uri.png)

## <a name="next-steps"></a>Passaggi successivi

- Per ulteriori informazioni sulla piattaforma di identità Microsoft, vedere [Piattaforma di identità Microsoft](https://docs.microsoft.com/azure/active-directory/develop/).
- Per altre informazioni sui ruoli RBAC per l'archiviazione di Azure, vedere Gestire i diritti di [accesso ai dati di archiviazione con il controllo degli accessi in base](storage-auth-aad-rbac.md)al ruolo.
- Per informazioni sull'uso delle identità gestite per le risorse di Azure con Archiviazione di Azure, vedere Autenticare l'accesso a BLOB e code con Azure Active Directory e le identità gestite per le risorse di Azure.To learn about using managed identities for Azure resources with Azure Storage, see [Authenticate access to blobs and queues with Azure Active Directory and managed identities for Azure Resources.](storage-auth-aad-msi.md)
