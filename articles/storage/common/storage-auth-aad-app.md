---
title: Eseguire l'autenticazione con Azure Active Directory per accedere ai dati blob e code dall'applicazione client
description: Usare Azure Active Directory per eseguire l'autenticazione da un'applicazione client, acquisire un token OAuth 2.0 e autorizzare le richieste di archiviazione Blob di Azure e archiviazione code.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 06/05/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 30ebfec88182684f8e852808e978a51854389898
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67073459"
---
# <a name="authenticate-with-azure-active-directory-from-an-application-for-access-to-blobs-and-queues"></a>Eseguire l'autenticazione con Azure Active Directory da un'applicazione per l'accesso a BLOB e code

Dei principali vantaggi dell'uso di Azure Active Directory (Azure AD) con archiviazione Blob di Azure o archiviazione di accodamento è che le credenziali non è più necessario da archiviare nel codice. In alternativa, è possibile richiedere un token di accesso di OAuth 2.0 da piattaforma delle identità Microsoft (in precedenza Azure AD). Azure AD autentica l'entità di sicurezza (utenti, gruppo o entità servizio) che esegue l'applicazione. Se l'autenticazione riesce, Azure AD restituisce il token di accesso all'applicazione e l'applicazione può quindi usare il token di accesso per autorizzare le richieste di archiviazione Blob di Azure o archiviazione di Accodamento.

Questo articolo illustra come configurare l'applicazione nativa o applicazione web per l'autenticazione con la piattaforma delle identità Microsoft 2.0. L'esempio di codice è con .NET, ma altri linguaggi usano un approccio simile. Per altre informazioni sulla piattaforma delle identità di Microsoft 2.0, vedere [Panoramica di Microsoft identity platform (v2.0)](../../active-directory/develop/v2-overview.md).

Per una panoramica del flusso di concessione del codice di OAuth 2.0, vedere [Autorizzare l'accesso ad applicazioni Web di Azure Active Directory mediante il flusso di concessione di OAuth 2.0](../../active-directory/develop/v2-oauth2-auth-code-flow.md).

## <a name="assign-a-role-to-an-azure-ad-security-principal"></a>Assegnare un ruolo a un'entità di sicurezza di Azure AD

Prima di poter autenticare un'entità di sicurezza dall'applicazione di Archiviazione di Azure, configurare le impostazioni di controllo degli accessi in base al ruolo per l'entità di sicurezza. Archiviazione di Azure definisce i ruoli RBAC predefiniti che includono le autorizzazioni per i contenitori e le code. Quando il ruolo di controllo degli accessi in base al ruolo viene assegnato a un'entità di sicurezza, a questa viene concesso l'accesso a tale risorsa. Per altre informazioni, vedere [Gestisci i diritti di accesso ai dati di accodamento e Blob di Azure con RBAC](storage-auth-aad-rbac.md).

## <a name="register-your-application-with-an-azure-ad-tenant"></a>Registrare l'applicazione nel tenant di Azure AD

Il primo passaggio nell'uso di Azure AD per autorizzare l'accesso alle risorse di archiviazione è registrando l'applicazione client con un tenant di Azure AD dal [portale di Azure](https://portal.azure.com). Quando si registra l'applicazione client, si forniscono informazioni sull'applicazione ad Azure AD. Azure AD fornisce quindi un ID client, chiamato anche *ID applicazione*, da usare per associare l'applicazione ad Azure AD in fase di esecuzione. Per altre informazioni sull'ID client, vedere [Oggetti applicazione e oggetti entità servizio in Azure Active Directory](../../active-directory/develop/app-objects-and-service-principals.md).

Per registrare l'applicazione di archiviazione di Azure, seguire i passaggi illustrati [Guida introduttiva: Registrare un'applicazione con la piattaforma delle identità Microsoft](../../active-directory/develop/quickstart-configure-app-access-web-apis.md). L'immagine seguente mostra le impostazioni comuni per la registrazione di un'applicazione web:

![Screenshot che mostra come registrare l'applicazione di archiviazione di Azure AD](./media/storage-auth-aad-app/app-registration.png)

> [!NOTE]
> Se si registra l'applicazione come applicazione nativa, è possibile specificare qualsiasi URI valido come **URI di reindirizzamento**. Per le applicazioni native, questo valore non deve essere un URL reale. Per le applicazioni web, l'URI di reindirizzamento deve essere un URI valido, in quanto specifica l'URL a cui vengono forniti i token.

Al termine della registrazione dell'applicazione, verrà visualizzato l'ID applicazione (o ID client) in **Impostazioni**:

![Screenshot che mostra l'ID client](./media/storage-auth-aad-app/app-registration-client-id.png)

Per altre informazioni sulla registrazione di un'applicazione in Azure AD, vedere [Integrazione di applicazioni con Azure Active Directory](../../active-directory/develop/quickstart-v2-register-an-app.md).

## <a name="grant-your-registered-app-permissions-to-azure-storage"></a>Concedere all'app registrata autorizzazioni per Archiviazione di Azure

Successivamente, concedere autorizzazioni per l'applicazione per chiamare le API di archiviazione di Azure. Questo passaggio consente all'applicazione autorizzare le richieste ad archiviazione di Azure con Azure AD.

1. Nel **Overview** pagina per l'applicazione registrata, seleziona **visualizzare le autorizzazioni API**.
1. Nel **le autorizzazioni API** sezione, selezionare **aggiungere un'autorizzazione** e scegliere **API Usa la mia organizzazione**.
1. Sotto il **API Usa la mia organizzazione** sezione, cercare "Azure Storage" e selezionare **archiviazione di Azure** dall'elenco dei risultati per visualizzare il **le autorizzazioni richieste API** riquadro.

    ![Screenshot che mostra autorizzazioni per l'archiviazione](media/storage-auth-aad-app/registered-app-permissions-1.png)

1. Sotto **quale tipo di autorizzazioni per l'applicazione richiede?** , osservare che il tipo di autorizzazione disponibili **autorizzazioni delegate**. Questa opzione è selezionata per impostazione predefinita.
1. Nel **selezionare le autorizzazioni** sezione del **le autorizzazioni API Request** riquadro, selezionare la casella di controllo accanto a **user_impersonation**, quindi fare clic su **Aggiungi le autorizzazioni**.
1. Il **le autorizzazioni API** riquadro Mostra ora che l'applicazione Azure AD ha accesso a Microsoft Graph e l'archiviazione di Azure. Le autorizzazioni vengono concesse automaticamente a Microsoft Graph quando prima di tutto registrare l'app con Azure AD.

    ![Screenshot di registrare le autorizzazioni dell'app](media/storage-auth-aad-app/registered-app-permissions-2.png)

## <a name="create-a-client-secret"></a>Creare un segreto client

L'applicazione richiede un segreto client per dimostrare la propria identità quando viene richiesto un token. Per aggiungere il segreto client, seguire questa procedura:

1. Passare alla registrazione dell'app nel portale di Azure.
1. Selezionare il **certificati e i segreti** impostazione.
1. Sotto **i segreti Client**, fare clic su **nuovo segreto client** per creare un nuovo master secret.
1. Fornire una descrizione per la chiave privata e scegliere l'intervallo di scadenza desiderato.
1. Copiare immediatamente il valore del segreto nuovo in una posizione sicura. Il valore completo viene visualizzato all'utente una sola volta.

    ![Screenshot che mostra privata del client](media/storage-auth-aad-app/client-secret.png)

## <a name="client-libraries-for-token-acquisition"></a>Librerie client per l'acquisizione dei token

Dopo aver registrato l'applicazione e viene concesso le autorizzazioni per accedere ai dati nell'archivio Blob di Azure o archiviazione di accodamento, è possibile aggiungere codice all'applicazione per autenticare un'entità di sicurezza e acquisire un token OAuth 2.0. Per eseguire l'autenticazione e acquisire il token, è possibile utilizzare uno dei due le [librerie di autenticazione di Microsoft identity platform](../../active-directory/develop/reference-v2-libraries.md) o un'altra libreria open source che supporta OpenID Connect 1.0. L'applicazione può quindi usare il token di accesso per autorizzare una richiesta in archiviazione Blob di Azure o archiviazione di Accodamento.

Per un elenco di scenari per cui l'acquisizione di token è supportato, vedere la [scenari](https://aka.ms/msal-net-scenarios) sezione il [Microsoft Authentication Library (MSAL) per .NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) repository GitHub.

## <a name="net-code-example-create-a-block-blob"></a>Esempio di codice .NET: Creare un BLOB in blocchi

L'esempio di codice mostra come ottenere un token di accesso da Azure AD. Il token di accesso viene usato per l'autenticazione dell'utente specificato e quindi per l'autorizzazione di una richiesta di creazione di un BLOB in blocchi. Per rendere operativo questo esempio, seguire prima di tutto i passaggi forniti nelle sezioni precedenti.

Per richiedere il token, sono necessari i valori seguenti dalla registrazione dell'app:

- Il nome di dominio Azure AD. Recuperare il valore predefinito di **Panoramica** pagina di Azure Active Directory.
- L'ID tenant (o directory). Recuperare il valore predefinito di **Panoramica** pagina della registrazione dell'app.
- L'ID client (o applicazione). Recuperare il valore predefinito di **Panoramica** pagina della registrazione dell'app.
- L'URI di reindirizzamento client. Recuperare il valore predefinito di **autenticazione** le impostazioni per la registrazione dell'app.
- Il valore del segreto client. Recuperare questo valore dal percorso in cui è stato precedentemente copiato.

### <a name="well-known-values-for-authentication-with-azure-ad"></a>Valori ben noti per l'autenticazione con Azure AD

Per autenticare un'entità di sicurezza con Azure AD, è necessario includere alcuni valori ben noti nel codice.

#### <a name="azure-ad-authority"></a>Autorità Azure AD

Per il cloud pubblico di Microsoft, l'autorità di base di Azure AD è la seguente, dove *tenant-id* è l'ID tenant di Active Directory (o ID directory):

`https://login.microsoftonline.com/<tenant-id>/`

L'ID tenant identifica il tenant di Azure AD da usare per l'autenticazione. Si è detta anche l'ID directory. Per recuperare l'ID tenant, passare al **Panoramica** pagina per la registrazione dell'app nel portale di Azure e copiare il valore da tale posizione.

#### <a name="storage-resource-id"></a>ID risorsa di archiviazione

Usare l'ID di risorsa di archiviazione di Azure per acquisire un token per autorizzare le richieste ad archiviazione di Azure:

`https://storage.azure.com/`

### <a name="create-a-storage-account-and-container"></a>Creare un account di archiviazione e un contenitore

Per eseguire l'esempio di codice, creare un account di archiviazione nella stessa sottoscrizione di Azure Active Directory. Creare quindi un contenitore nell'account di archiviazione. Il codice di esempio verrà creato un blob in blocchi in questo contenitore.

Quindi assegnare in modo esplicito il **collaboratore ai dati Blob di archiviazione** ruolo all'account utente con cui si eseguirà il codice di esempio. Per istruzioni su come assegnare questo ruolo nel portale di Azure, vedere [concedere l'accesso a Azure blob e Accodamento dei dati con accessi nel portale di Azure](storage-auth-aad-rbac-portal.md).

> [!NOTE]
> Quando si crea un account di archiviazione di Azure, non vengono assegnati automaticamente le autorizzazioni per accedere ai dati tramite Azure AD. È invece necessario assegnare in modo esplicito a se stessi un ruolo di controllo degli accessi in base al ruolo per Archiviazione di Azure. È possibile assegnare questo ruolo a livello di sottoscrizione, gruppo di risorse, account di archiviazione o singolo contenitore o coda.

### <a name="create-a-web-application-that-authorizes-access-to-blob-storage-with-azure-ad"></a>Creare un'applicazione web che autorizza l'accesso all'archiviazione Blob con Azure AD

Quando l'applicazione accede all'archiviazione di Azure, così via per conto dell'utente, vale a dire che le risorse di accodamento o blob sono accessibili tramite le autorizzazioni dell'utente connesso. Per provare questo esempio di codice, è necessaria un'applicazione web che richiede all'utente di accedere usando un'identità Azure AD. È possibile creare il proprio o usare l'applicazione di esempio fornita da Microsoft.

Un'applicazione web di esempio completo che acquisisce un token e lo usa per creare un blob in archiviazione di Azure è disponibile nel [GitHub](http://aka.ms/aadstorage). Revisione ed esecuzione di esempio completato può essere utile per comprendere gli esempi di codice. Per istruzioni su come eseguire l'esempio completato, vedere la sezione intitolata [Vista ed eseguire l'esempio completato](#view-and-run-the-completed-sample).

#### <a name="add-references-and-using-statements"></a>Aggiungere riferimenti e istruzioni using  

Da Visual Studio, installare la libreria client di archiviazione di Azure. Scegliere **Gestione pacchetti NuGet** dal menu **Strumenti** e quindi fare clic su **Console di Gestione pacchetti**. Digitare i comandi seguenti nella finestra della console per installare i pacchetti necessari da Azure Storage client library per .NET:

```console
Install-Package Microsoft.Azure.Storage.Blob
Install-Package Microsoft.Azure.Storage.Common
```

Successivamente, aggiungere quanto segue usando istruzioni nel file HomeController.cs:

```csharp
using Microsoft.Identity.Client; //MSAL library for getting the access token
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;
```

#### <a name="create-a-block-blob"></a>Creare un BLOB in blocchi

Aggiungere il frammento di codice seguente per creare un blob in blocchi:

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
> Per autorizzare le operazioni di accodamento e blob con un token OAuth 2.0, è necessario usare HTTPS.

Nell'esempio precedente, la libreria client .NET gestisce l'autorizzazione della richiesta di creazione del blob in blocchi. Librerie client di archiviazione Azure per altri linguaggi di gestiscono anche l'autorizzazione della richiesta per l'utente. ma se si chiama un'operazione di Archiviazione di Azure con un token OAuth tramite l'API REST, è necessario autorizzare la richiesta usando il token OAuth.

Per chiamare operazioni del Servizio BLOB e del Servizio di accodamento usando token di accesso OAuth, passare il token di accesso nell'intestazione **Autorizzazione** intestazione tramite lo schema **Bearer** e specificare come versione del servizio la versione 2017-09-11 o successiva, come illustrato nell'esempio seguente:

```https
GET /container/file.txt HTTP/1.1
Host: mystorageaccount.blob.core.windows.net
x-ms-version: 2017-11-09
Authorization: Bearer eyJ0eXAiOnJKV1...Xd6j
```

#### <a name="get-an-oauth-token-from-azure-ad"></a>Ottenere un token OAuth da Azure AD

Aggiungere quindi un metodo per richiedere un token da Azure AD. È il token che richiesta per conto dell'utente e si userà il metodo GetTokenOnBehalfOfUser.

Tenere presente che se sono registrati di recente, e si sta richiedendo un token per il `storage.azure.com` risorsa, è necessario presentare all'utente un'interfaccia utente in cui l'utente può fornire il consenso per un'azione di questo tipo per loro conto. Per facilitare che è necessario intercettare la `MsalUiRequiredException` e aggiungere la funzionalità per richiedere il consenso dell'utente, come illustrato nell'esempio seguente:

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
        AuthenticationProperties properties = BuildAuthenticationPropertiesForIncrementalConsent(scopes, ex);
        return Challenge(properties);
    }
}
```

Il consenso è il processo con cui un utente autorizza un'applicazione ad accedere per proprio conto a risorse protette. La piattaforma delle identità Microsoft 2.0 supporta consenso incrementale, il che significa che un'entità di sicurezza possa richiedere inizialmente un set minimo di autorizzazioni e aggiungere le autorizzazioni nel corso del tempo in base alle esigenze. Quando il codice richiede un token di accesso, specificare l'ambito delle autorizzazioni necessarie per l'app in qualsiasi momento nel `scope` parametro. Per altre informazioni sul consenso incrementale, vedere la sezione intitolata **consenso incrementale e dinamico** nelle [motivo per cui aggiornare alla piattaforma delle identità di Microsoft (v2.0)?](../../active-directory/develop/azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent).

Il metodo seguente costruisce le proprietà di autenticazione per richiedere il consenso incrementale:

```csharp
private AuthenticationProperties BuildAuthenticationPropertiesForIncrementalConsent(string[] scopes, MsalUiRequiredException ex)
{
    AuthenticationProperties properties = new AuthenticationProperties();

    // Set the scopes, including the scopes that ADAL.NET / MSAL.NET need for the Token cache.
    string[] additionalBuildInScopes = new string[] { "openid", "offline_access", "profile" };
    properties.SetParameter<ICollection<string>>(OpenIdConnectParameterNames.Scope, scopes.Union(additionalBuildInScopes).ToList());

    // Attempt to set the login_hint so that the logged-in user is not presented with an account selection dialog.
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

Per eseguire l'applicazione di esempio, prima di tutto clonare o scaricarlo dal [GitHub](http://aka.ms/aadstorage). Quindi aggiornare l'applicazione come descritto nelle sezioni seguenti.

### <a name="provide-values-in-the-settings-file"></a>Specificare i valori nel file di impostazioni

A questo punto, aggiornare il *appSettings. JSON* file con valori personalizzati, come segue:

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

### <a name="update-the-storage-account-and-container-name"></a>Aggiornare il nome dell'account e contenitore di archiviazione

Nel *HomeController.cs* file, aggiornare l'URI che fa riferimento al blob in blocchi per usare il nome dell'account di archiviazione e contenitore:

```csharp
CloudBlockBlob blob = new CloudBlockBlob(
                      new Uri("https://<storage-account>.blob.core.windows.net/<container>/Blob1.txt"),
                      storageCredentials);
```

### <a name="enable-implicit-grant-flow"></a>Abilitare il flusso di concessione implicita

Per eseguire l'esempio, si potrebbe essere necessario configurare il flusso di concessione implicita per la registrazione dell'app. A tale scopo, seguire questa procedura:

1. Passare alla registrazione dell'app nel portale di Azure.
1. Nella sezione Gestisci selezionare il **autenticazione** impostazione.
1. Sotto **impostazioni avanzate**, nella **concessione implicita** , selezionare le caselle di controllo per abilitare i token di accesso e i token ID, come illustrato nell'immagine seguente:

    ![Screenshot che illustra come abilitare le impostazioni per il flusso di concessione implicita](media/storage-auth-aad-app/enable-implicit-grant-flow.png)

### <a name="update-the-port-used-by-localhost"></a>Aggiornare la porta usata da localhost

Quando si esegue l'esempio, è probabile che è necessario aggiornare l'URI di reindirizzamento specificato nella registrazione dell'app per usare la *localhost* porta assegnata in fase di esecuzione. Per aggiornare l'URI da utilizzare la porta assegnata di reindirizzamento, seguire questa procedura:

1. Passare alla registrazione dell'app nel portale di Azure.
1. Nella sezione Gestisci selezionare il **autenticazione** impostazione.
1. Sotto **URI di reindirizzamento**, modificare la porta affinché corrisponda a quello usato dall'applicazione di esempio, come illustrato nell'immagine seguente:

    ![Screenshot che mostra URI di reindirizzamento per la registrazione dell'app](media/storage-auth-aad-app/redirect-uri.png)

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni su piattaforma delle identità Microsoft, vedere [piattaforma delle identità Microsoft](https://docs.microsoft.com/azure/active-directory/develop/).
- Per altre informazioni sui ruoli RBAC per archiviazione di Azure, vedere [Gestisci i diritti di accesso ai dati di archiviazione con RBAC](storage-auth-aad-rbac.md).
- Per altre informazioni sull'utilizzo delle identità gestita per le risorse di Azure con archiviazione di Azure, vedere [autenticare l'accesso a BLOB e code con Azure Active Directory e identità gestite per le risorse di Azure](storage-auth-aad-msi.md).
