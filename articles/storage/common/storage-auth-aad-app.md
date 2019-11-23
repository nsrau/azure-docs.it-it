---
title: Autorizzare l'accesso a BLOB e code con Azure Active Directory da un'applicazione client-archiviazione di Azure
description: Usare Azure Active Directory per eseguire l'autenticazione da un'applicazione client, acquisire un token OAuth 2,0 e autorizzare le richieste all'archivio BLOB di Azure e all'archiviazione code.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: ca6b055b5d3702cea4ca1986ad1c81b59f76cee3
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2019
ms.locfileid: "72299627"
---
# <a name="authorize-access-to-blobs-and-queues-with-azure-active-directory-from-a-client-application"></a>Autorizzare l'accesso a BLOB e code con Azure Active Directory da un'applicazione client

Un vantaggio fondamentale dell'uso di Azure Active Directory (Azure AD) con l'archiviazione BLOB o l'archiviazione di Accodamento di Azure consiste nel fatto che non è più necessario archiviare le credenziali nel codice. È invece possibile richiedere un token di accesso OAuth 2,0 dalla piattaforma di identità Microsoft (in precedenza Azure AD). Azure AD autentica l'entità di sicurezza (un utente, un gruppo o un'entità servizio) che esegue l'applicazione. Se l'autenticazione ha esito positivo, Azure AD restituisce il token di accesso all'applicazione e l'applicazione può usare il token di accesso per autorizzare le richieste all'archivio BLOB di Azure o all'archiviazione code.

Questo articolo illustra come configurare un'applicazione nativa o un'applicazione Web per l'autenticazione con Microsoft Identity Platform 2,0. L'esempio di codice è con .NET, ma altri linguaggi usano un approccio simile. Per ulteriori informazioni sulla piattaforma Microsoft Identity Platform 2,0, vedere [Panoramica di Microsoft Identity Platform (v 2.0)](../../active-directory/develop/v2-overview.md).

Per una panoramica del flusso di concessione del codice di OAuth 2.0, vedere [Autorizzare l'accesso ad applicazioni Web di Azure Active Directory mediante il flusso di concessione di OAuth 2.0](../../active-directory/develop/v2-oauth2-auth-code-flow.md).

## <a name="assign-a-role-to-an-azure-ad-security-principal"></a>Assegnare un ruolo a un'entità di sicurezza Azure AD

Prima di poter autenticare un'entità di sicurezza dall'applicazione di Archiviazione di Azure, configurare le impostazioni di controllo degli accessi in base al ruolo per l'entità di sicurezza. Archiviazione di Azure definisce i ruoli RBAC predefiniti che comprendono le autorizzazioni per contenitori e code. Quando il ruolo di controllo degli accessi in base al ruolo viene assegnato a un'entità di sicurezza, a questa viene concesso l'accesso a tale risorsa. Per altre informazioni, vedere [gestire i diritti di accesso ai dati di Accodamento e BLOB di Azure con RBAC](storage-auth-aad-rbac.md).

## <a name="register-your-application-with-an-azure-ad-tenant"></a>Registrare l'applicazione nel tenant di Azure AD

Il primo passaggio nell'uso di Azure AD per autorizzare l'accesso alle risorse di archiviazione è la registrazione dell'applicazione client con un tenant di Azure AD dal [portale di Azure](https://portal.azure.com). Quando si registra l'applicazione client, è necessario fornire informazioni sull'applicazione Azure AD. Azure AD fornisce quindi un ID client, chiamato anche *ID applicazione*, da usare per associare l'applicazione ad Azure AD in fase di esecuzione. Per altre informazioni sull'ID client, vedere [Oggetti applicazione e oggetti entità servizio in Azure Active Directory](../../active-directory/develop/app-objects-and-service-principals.md).

Per registrare l'applicazione di archiviazione di Azure, seguire i passaggi illustrati in [Guida introduttiva: registrare un'applicazione con la piattaforma di identità Microsoft](../../active-directory/develop/quickstart-configure-app-access-web-apis.md). Nella figura seguente vengono illustrate le impostazioni comuni per la registrazione di un'applicazione Web:

![Screenshot che illustra come registrare l'applicazione di archiviazione con Azure AD](./media/storage-auth-aad-app/app-registration.png)

> [!NOTE]
> Se si registra l'applicazione come applicazione nativa, è possibile specificare qualsiasi URI valido come **URI di reindirizzamento**. Per le applicazioni native, questo valore non deve essere un URL reale. Per le applicazioni Web, l'URI di reindirizzamento deve essere un URI valido perché specifica l'URL a cui vengono forniti i token.

Al termine della registrazione dell'applicazione, verrà visualizzato l'ID applicazione (o ID client) in **Impostazioni**:

![Screenshot che mostra l'ID client](./media/storage-auth-aad-app/app-registration-client-id.png)

Per altre informazioni sulla registrazione di un'applicazione in Azure AD, vedere [Integrazione di applicazioni con Azure Active Directory](../../active-directory/develop/quickstart-v2-register-an-app.md).

## <a name="grant-your-registered-app-permissions-to-azure-storage"></a>Concedere all'app registrata autorizzazioni per Archiviazione di Azure

Successivamente, concedere all'applicazione le autorizzazioni per chiamare le API di archiviazione di Azure. Questo passaggio consente all'applicazione di autorizzare le richieste ad archiviazione di Azure con Azure AD.

1. Nella pagina **Panoramica** dell'applicazione registrata selezionare **Visualizza autorizzazioni**per le API.
1. Nella sezione **autorizzazioni API** selezionare **Aggiungi un'autorizzazione** e scegliere **API Microsoft**.
1. Selezionare **archiviazione di Azure** dall'elenco dei risultati per visualizzare il riquadro **autorizzazioni API richiesta** .
1. In **quali tipi di autorizzazioni sono necessarie per l'applicazione?** , osservare che il tipo di autorizzazione disponibile sono le **autorizzazioni delegate**. Questa opzione è selezionata per impostazione predefinita.
1. Nella sezione **Seleziona autorizzazioni** del riquadro **autorizzazioni API richiesta** Selezionare la casella di controllo accanto a **user_impersonation**, quindi fare clic su **Aggiungi autorizzazioni**.

    ![Screenshot che mostra le autorizzazioni per l'archiviazione](media/storage-auth-aad-app/registered-app-permissions-1.png)

Il riquadro **autorizzazioni API** Mostra ora che l'applicazione Azure ad registrata ha accesso sia al Microsoft Graph che all'archiviazione di Azure. Le autorizzazioni vengono concesse per Microsoft Graph automaticamente alla prima registrazione dell'app con Azure AD.

![Screenshot che mostra le autorizzazioni dell'app register](media/storage-auth-aad-app/registered-app-permissions-2.png)

## <a name="create-a-client-secret"></a>Creare un segreto client

L'applicazione richiede un segreto client per dimostrare la propria identità quando richiede un token. Per aggiungere il segreto client, attenersi alla seguente procedura:

1. Passare alla registrazione dell'app nel portale di Azure.
1. Selezionare l'impostazione **certificati & segreti** .
1. In **segreti client**fare clic su **nuovo segreto client** per creare un nuovo segreto.
1. Specificare una descrizione per il segreto e scegliere l'intervallo di scadenza desiderato.
1. Copiare immediatamente il valore del nuovo segreto in una posizione sicura. Il valore completo viene visualizzato una sola volta.

    ![Screenshot che mostra il segreto client](media/storage-auth-aad-app/client-secret.png)

## <a name="client-libraries-for-token-acquisition"></a>Librerie client per l'acquisizione di token

Dopo aver registrato l'applicazione e concesso le autorizzazioni per accedere ai dati nell'archivio BLOB di Azure o nell'archiviazione di Accodamento, è possibile aggiungere codice all'applicazione per autenticare un'entità di sicurezza e acquisire un token OAuth 2,0. Per autenticare e acquisire il token, è possibile usare una delle librerie di [autenticazione della piattaforma di Microsoft Identity](../../active-directory/develop/reference-v2-libraries.md) o un'altra libreria open source che supporta OpenID Connect 1,0. L'applicazione può quindi usare il token di accesso per autorizzare una richiesta nell'archivio BLOB o nell'archiviazione di Accodamento di Azure.

Per un elenco degli scenari per i quali è supportato l'acquisizione di token, vedere la sezione relativa ai [flussi di autenticazione](/en-us/azure/active-directory/develop/msal-authentication-flows) nel contenuto della [libreria di autenticazione Microsoft](/azure/active-directory/develop/msal-overview).

## <a name="well-known-values-for-authentication-with-azure-ad"></a>Valori ben noti per l'autenticazione con Azure AD

Per autenticare un'entità di sicurezza con Azure AD, è necessario includere alcuni valori ben noti nel codice.

### <a name="azure-ad-authority"></a>Autorità Azure AD

Per il cloud pubblico di Microsoft, l'autorità di base di Azure AD è la seguente, dove *tenant-id* è l'ID tenant di Active Directory (o ID directory):

`https://login.microsoftonline.com/<tenant-id>/`

L'ID tenant identifica il tenant di Azure AD da usare per l'autenticazione. Viene anche definito ID directory. Per recuperare l'ID tenant, passare alla pagina **Panoramica** per la registrazione dell'app nel portale di Azure e copiare il valore da tale posizione.

### <a name="azure-storage-resource-id"></a>ID risorsa di archiviazione di Azure

[!INCLUDE [storage-resource-id-include](../../../includes/storage-resource-id-include.md)]

## <a name="net-code-example-create-a-block-blob"></a>Esempio di codice .NET: creare un BLOB in blocchi

L'esempio di codice mostra come ottenere un token di accesso da Azure AD. Il token di accesso viene usato per l'autenticazione dell'utente specificato e quindi per l'autorizzazione di una richiesta di creazione di un BLOB in blocchi. Per rendere operativo questo esempio, seguire prima di tutto i passaggi forniti nelle sezioni precedenti.

Per richiedere il token, sono necessari i valori seguenti dalla registrazione dell'app:

- Nome del dominio Azure AD. Recuperare questo valore dalla pagina **Panoramica** del Azure Active Directory.
- ID del tenant (o della directory). Recuperare questo valore dalla pagina **Panoramica** della registrazione dell'app.
- ID client (o applicazione). Recuperare questo valore dalla pagina **Panoramica** della registrazione dell'app.
- URI di reindirizzamento del client. Recuperare questo valore dalle impostazioni di **autenticazione** per la registrazione dell'app.
- Valore del segreto client. Recuperare questo valore dal percorso in cui è stato precedentemente copiato.

### <a name="create-a-storage-account-and-container"></a>Creare un account di archiviazione e un contenitore

Per eseguire l'esempio di codice, creare un account di archiviazione nella stessa sottoscrizione del Azure Active Directory. Quindi, creare un contenitore all'interno dell'account di archiviazione. Il codice di esempio creerà un BLOB in blocchi in questo contenitore.

Successivamente, assegnare in modo esplicito il ruolo di **collaboratore dati BLOB di archiviazione** all'account utente con cui verrà eseguito il codice di esempio. Per istruzioni su come assegnare questo ruolo nella portale di Azure, vedere [concedere l'accesso ai dati di Accodamento e BLOB di Azure con RBAC nel portale di Azure](storage-auth-aad-rbac-portal.md).

> [!NOTE]
> Quando si crea un account di archiviazione di Azure, non vengono assegnate automaticamente le autorizzazioni per accedere ai dati tramite Azure AD. È invece necessario assegnare in modo esplicito a se stessi un ruolo di controllo degli accessi in base al ruolo per Archiviazione di Azure. È possibile assegnare questo ruolo a livello di sottoscrizione, gruppo di risorse, account di archiviazione o singolo contenitore o coda.

### <a name="create-a-web-application-that-authorizes-access-to-blob-storage-with-azure-ad"></a>Creare un'applicazione Web che autorizza l'accesso all'archiviazione BLOB con Azure AD

Quando l'applicazione accede ad archiviazione di Azure, questa operazione viene eseguita per conto dell'utente, ovvero si accede alle risorse BLOB o Queue usando le autorizzazioni dell'utente che ha eseguito l'accesso. Per provare questo esempio di codice, è necessaria un'applicazione Web che richiede all'utente di eseguire l'accesso con un'identità Azure AD. È possibile crearne di personalizzati o usare l'applicazione di esempio fornita da Microsoft.

Un'applicazione Web di esempio completa che acquisisce un token e la usa per creare un BLOB in archiviazione di Azure è disponibile in [GitHub](https://aka.ms/aadstorage). Per conoscere gli esempi di codice, è possibile esaminare ed eseguire l'esempio completato. Per istruzioni su come eseguire l'esempio completato, vedere la sezione [visualizzare ed eseguire l'esempio completato](#view-and-run-the-completed-sample).

#### <a name="add-references-and-using-statements"></a>Aggiungere riferimenti e istruzioni using  

Da Visual Studio installare la libreria client di archiviazione di Azure. Scegliere **Gestione pacchetti NuGet** dal menu **Strumenti** e quindi fare clic su **Console di Gestione pacchetti**. Digitare i comandi seguenti nella finestra della console per installare i pacchetti necessari dalla libreria client di archiviazione di Azure per .NET:

```console
Install-Package Microsoft.Azure.Storage.Blob
Install-Package Microsoft.Azure.Storage.Common
```

Aggiungere quindi le istruzioni using seguenti al file HomeController.cs:

```csharp
using Microsoft.Identity.Client; //MSAL library for getting the access token
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;
```

#### <a name="create-a-block-blob"></a>Creare un BLOB in blocchi

Aggiungere il frammento di codice seguente per creare un BLOB in blocchi:

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
> Per autorizzare le operazioni BLOB e Queue con un token OAuth 2,0, è necessario usare HTTPS.

Nell'esempio precedente, la libreria client .NET gestisce l'autorizzazione della richiesta di creazione del blob in blocchi. Anche le librerie client di archiviazione di Azure per altri linguaggi gestiscono l'autorizzazione della richiesta. ma se si chiama un'operazione di Archiviazione di Azure con un token OAuth tramite l'API REST, è necessario autorizzare la richiesta usando il token OAuth.

Per chiamare operazioni del Servizio BLOB e del Servizio di accodamento usando token di accesso OAuth, passare il token di accesso nell'intestazione **Autorizzazione** intestazione tramite lo schema **Bearer** e specificare come versione del servizio la versione 2017-09-11 o successiva, come illustrato nell'esempio seguente:

```https
GET /container/file.txt HTTP/1.1
Host: mystorageaccount.blob.core.windows.net
x-ms-version: 2017-11-09
Authorization: Bearer eyJ0eXAiOnJKV1...Xd6j
```

#### <a name="get-an-oauth-token-from-azure-ad"></a>Ottenere un token OAuth da Azure AD

Successivamente, aggiungere un metodo che richiede un token da Azure AD per conto dell'utente. Questo metodo definisce l'ambito per il quale devono essere concesse le autorizzazioni. Per ulteriori informazioni sulle autorizzazioni e gli ambiti, vedere [autorizzazioni e consenso nell'endpoint della piattaforma di identità Microsoft](../../active-directory/develop/v2-permissions-and-consent.md).

Usare l'ID risorsa per costruire l'ambito per il quale acquisire il token. Nell'esempio viene costruito l'ambito usando l'ID della risorsa insieme all'ambito `user_impersonation` incorporato, che indica che il token viene richiesto per conto dell'utente.

Tenere presente che potrebbe essere necessario presentare all'utente un'interfaccia che consenta all'utente di richiedere il token per conto dell'utente. Quando è necessario il consenso, nell'esempio viene intercettato il **MsalUiRequiredException** e viene chiamato un altro metodo per facilitare la richiesta di consenso:

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

Il consenso è il processo con cui un utente autorizza un'applicazione ad accedere per proprio conto a risorse protette. La piattaforma Microsoft Identity 2,0 supporta il consenso incrementale, ovvero un'entità di sicurezza può richiedere inizialmente un set minimo di autorizzazioni e aggiungere le autorizzazioni nel tempo in base alle esigenze. Quando il codice richiede un token di accesso, specificare l'ambito delle autorizzazioni necessarie per l'app in un determinato momento nel parametro `scope`. Per ulteriori informazioni sul consenso incrementale, vedere la sezione intitolata **consenso incrementale e dinamico** nel [perché eseguire l'aggiornamento a Microsoft Identity Platform (v 2.0)?](../../active-directory/develop/azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent).

Il metodo seguente costruisce le proprietà di autenticazione per richiedere il consenso incrementale:

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

Per eseguire l'applicazione di esempio, clonarla o scaricarla da [GitHub](https://github.com/Azure-Samples/storage-dotnet-azure-ad-msal). Aggiornare quindi l'applicazione come descritto nelle sezioni riportate di seguito.

### <a name="provide-values-in-the-settings-file"></a>Specificare i valori nel file di impostazioni

Aggiornare quindi il file *appSettings. JSON* con i propri valori, come indicato di seguito:

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

### <a name="update-the-storage-account-and-container-name"></a>Aggiornare l'account di archiviazione e il nome del contenitore

Nel file *HomeController.cs* aggiornare l'URI che fa riferimento al BLOB in blocchi per usare il nome dell'account di archiviazione e il contenitore:

```csharp
CloudBlockBlob blob = new CloudBlockBlob(
                      new Uri("https://<storage-account>.blob.core.windows.net/<container>/Blob1.txt"),
                      storageCredentials);
```

### <a name="enable-implicit-grant-flow"></a>Abilita flusso di concessione implicita

Per eseguire l'esempio, potrebbe essere necessario configurare il flusso di concessione implicita per la registrazione dell'app. Seguire questa procedura:

1. Passare alla registrazione dell'app nel portale di Azure.
1. Nella sezione Gestisci selezionare l'impostazione di **autenticazione** .
1. In **Impostazioni avanzate**, nella sezione **concessione implicita** Selezionare le caselle di controllo per abilitare i token di accesso e i token ID, come illustrato nell'immagine seguente:

    ![Screenshot che illustra come abilitare le impostazioni per il flusso di concessione implicito](media/storage-auth-aad-app/enable-implicit-grant-flow.png)

### <a name="update-the-port-used-by-localhost"></a>Aggiornare la porta usata da localhost

Quando si esegue l'esempio, potrebbe essere necessario aggiornare l'URI di reindirizzamento specificato nella registrazione dell'app per usare la porta *localhost* assegnata in fase di esecuzione. Per aggiornare l'URI di reindirizzamento per l'utilizzo della porta assegnata, attenersi alla seguente procedura:

1. Passare alla registrazione dell'app nel portale di Azure.
1. Nella sezione Gestisci selezionare l'impostazione di **autenticazione** .
1. In **URI di reindirizzamento**modificare la porta in modo che corrisponda a quella usata dall'applicazione di esempio, come illustrato nell'immagine seguente:

    ![Screenshot che Mostra gli URI di reindirizzamento per la registrazione dell'app](media/storage-auth-aad-app/redirect-uri.png)

## <a name="next-steps"></a>Passaggi successivi

- Per ulteriori informazioni sulla piattaforma di identità Microsoft, vedere la pagina relativa alla [piattaforma delle identità Microsoft](https://docs.microsoft.com/azure/active-directory/develop/).
- Per altre informazioni sui ruoli RBAC per archiviazione di Azure, vedere [gestire i diritti di accesso ai dati di archiviazione con RBAC](storage-auth-aad-rbac.md).
- Per informazioni sull'uso delle identità gestite per le risorse di Azure con archiviazione di Azure, vedere [autenticare l'accesso a BLOB e code con Azure Active Directory e identità gestite per le risorse di Azure](storage-auth-aad-msi.md).
