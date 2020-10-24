---
title: Acquisire un token da Azure AD per autorizzare le richieste da un'applicazione client
titleSuffix: Azure Storage
description: Usare Azure Active Directory per eseguire l'autenticazione da un'applicazione client, acquisire un token OAuth 2,0 e autorizzare le richieste all'archivio BLOB di Azure e all'archiviazione code.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 09/21/2020
ms.author: tamram
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: 20ebfc571d72b79b61a61fb633feb63c4cc58fdd
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92488810"
---
# <a name="acquire-a-token-from-azure-ad-for-authorizing-requests-from-a-client-application"></a>Acquisire un token da Azure AD per autorizzare le richieste da un'applicazione client

Un vantaggio fondamentale dell'uso di Azure Active Directory (Azure AD) con l'archiviazione BLOB o l'archiviazione di Accodamento di Azure consiste nel fatto che non è più necessario archiviare le credenziali nel codice. È invece possibile richiedere un token di accesso OAuth 2,0 dalla piattaforma di identità Microsoft. Azure AD autentica l'entità di sicurezza (un utente, un gruppo o un'entità servizio) che esegue l'applicazione. Se l'autenticazione ha esito positivo, Azure AD restituisce il token di accesso all'applicazione e l'applicazione può usare il token di accesso per autorizzare le richieste all'archivio BLOB di Azure o all'archiviazione code.

Questo articolo illustra come configurare un'applicazione nativa o un'applicazione Web per l'autenticazione con la piattaforma di identità Microsoft usando un'applicazione di esempio disponibile per il download. L'applicazione di esempio include .NET, ma altri linguaggi utilizzano un approccio simile. Per ulteriori informazioni sulla piattaforma Microsoft Identity, vedere [Panoramica di Microsoft Identity Platform](../../active-directory/develop/v2-overview.md).

Per una panoramica del flusso di concessione del codice di OAuth 2.0, vedere [Autorizzare l'accesso ad applicazioni Web di Azure Active Directory mediante il flusso di concessione di OAuth 2.0](../../active-directory/develop/v2-oauth2-auth-code-flow.md).

## <a name="about-the-sample-application"></a>Informazioni sull'applicazione di esempio

L'applicazione di esempio offre un'esperienza end-to-end che Mostra come configurare un'applicazione Web per l'autenticazione con Azure AD in un ambiente di sviluppo locale. Per visualizzare ed eseguire l'applicazione di esempio, clonarla o scaricarla da [GitHub](https://github.com/Azure-Samples/storage-dotnet-azure-ad-msal). Seguire quindi la procedura descritta nell'articolo per configurare una registrazione di app di Azure e aggiornare l'applicazione per l'ambiente.

## <a name="assign-a-role-to-an-azure-ad-security-principal"></a>Assegnare un ruolo a un'entità di sicurezza Azure AD

Per autenticare un'entità di sicurezza dall'applicazione di archiviazione di Azure, configurare prima le impostazioni di controllo degli accessi in base al ruolo di Azure (RBAC) per tale entità di sicurezza. Archiviazione di Azure definisce ruoli predefiniti che includono le autorizzazioni per contenitori e code. Quando il ruolo Azure viene assegnato a un'entità di sicurezza, all'entità di sicurezza viene concesso l'accesso a tale risorsa. Per altre informazioni, vedere [gestire i diritti di accesso ai dati di Accodamento e BLOB di Azure con RBAC di Azure](storage-auth-aad-rbac.md).

## <a name="register-your-application-with-an-azure-ad-tenant"></a>Registrare l'applicazione nel tenant di Azure AD

Il primo passaggio nell'uso di Azure AD per autorizzare l'accesso alle risorse di archiviazione è la registrazione dell'applicazione client con un tenant di Azure AD dal [portale di Azure](https://portal.azure.com). Quando si registra l'applicazione client, è necessario fornire informazioni sull'applicazione Azure AD. Azure AD fornisce quindi un ID client, chiamato anche *ID applicazione*, da usare per associare l'applicazione ad Azure AD in fase di esecuzione. Per altre informazioni sull'ID client, vedere [Oggetti applicazione e oggetti entità servizio in Azure Active Directory](../../active-directory/develop/app-objects-and-service-principals.md). Per registrare l'applicazione di archiviazione di Azure, seguire i passaggi illustrati in [Guida introduttiva: registrare un'applicazione con la piattaforma di identità Microsoft](../../active-directory/develop/quickstart-configure-app-access-web-apis.md). 

Nell'immagine seguente vengono illustrate le impostazioni comuni per la registrazione di un'applicazione Web. Si noti che in questo esempio l'URI di reindirizzamento è impostato su `http://localhost:5000/signin-oidc` per il test dell'applicazione di esempio nell'ambiente di sviluppo. È possibile modificare questa impostazione in un secondo momento in base all'impostazione di **autenticazione** per l'applicazione registrata nel portale di Azure:

:::image type="content" source="media/storage-auth-aad-app/app-registration.png" alt-text="Screenshot che illustra come registrare l'applicazione di archiviazione con Azure AD":::

> [!NOTE]
> Se si registra l'applicazione come applicazione nativa, è possibile specificare qualsiasi URI valido per l' **URI di reindirizzamento**. Per le applicazioni native, questo valore non deve essere un URL reale. Per le applicazioni Web, l'URI di reindirizzamento deve essere un URI valido perché specifica l'URL a cui vengono forniti i token.

Al termine della registrazione dell'applicazione, verrà visualizzato l'ID applicazione (o ID client) in **Impostazioni**:

:::image type="content" source="media/storage-auth-aad-app/app-registration-client-id.png" alt-text="Screenshot che illustra come registrare l'applicazione di archiviazione con Azure AD":::

Per altre informazioni sulla registrazione di un'applicazione in Azure AD, vedere [Integrazione di applicazioni con Azure Active Directory](../../active-directory/develop/quickstart-v2-register-an-app.md).

### <a name="grant-your-registered-app-permissions-to-azure-storage"></a>Concedere all'app registrata autorizzazioni per Archiviazione di Azure

Successivamente, concedere all'applicazione le autorizzazioni per chiamare le API di archiviazione di Azure. Questo passaggio consente all'applicazione di autorizzare le richieste ad archiviazione di Azure con Azure AD.

1. Nella pagina **autorizzazioni API** per l'applicazione registrata selezionare **Aggiungi un'autorizzazione**.
1. Nella scheda **Microsoft Apis (API Microsoft** ) selezionare **archiviazione di Azure**.
1. Nel riquadro **autorizzazioni API richiesta** , in **quali tipi di autorizzazioni sono richieste dall'applicazione?**, osservare che il tipo di autorizzazione disponibile è **autorizzazioni delegate**. Questa opzione è selezionata per impostazione predefinita.
1. In **autorizzazioni**selezionare la casella di controllo accanto a **user_impersonation**, quindi selezionare il pulsante **Aggiungi autorizzazioni** .

    :::image type="content" source="media/storage-auth-aad-app/registered-app-permissions-1.png" alt-text="Screenshot che illustra come registrare l'applicazione di archiviazione con Azure AD":::

1. Successivamente, concedere il consenso dell'amministratore per queste autorizzazioni facendo clic su **concedi il consenso dell'amministratore per la directory predefinita**.

Il riquadro **autorizzazioni API** Mostra ora che l'applicazione Azure ad registrata ha accesso alle api di Microsoft Graph e di archiviazione di Azure e che il consenso è concesso per la directory predefinita. Le autorizzazioni vengono automaticamente concesse a Microsoft Graph quando si registra l'app per la prima volta in Azure AD.

:::image type="content" source="media/storage-auth-aad-app/registered-app-permissions-2.png" alt-text="Screenshot che illustra come registrare l'applicazione di archiviazione con Azure AD":::

### <a name="create-a-client-secret"></a>Creare un segreto client

L'applicazione richiede un segreto client per dimostrare la propria identità quando richiede un token. Per aggiungere il segreto client, attenersi alla seguente procedura:

1. Passare alla registrazione dell'app nel portale di Azure.
1. Selezionare l'impostazione **certificati & segreti** .
1. In **segreti client**fare clic su **nuovo segreto client** per creare un nuovo segreto.
1. Specificare una descrizione per il segreto e scegliere l'intervallo di scadenza desiderato.
1. Copiare immediatamente il valore del nuovo segreto in una posizione sicura. Il valore completo viene visualizzato una sola volta.

    ![Screenshot che mostra il segreto client](media/storage-auth-aad-app/client-secret.png)

### <a name="enable-implicit-grant-flow"></a>Abilita flusso di concessione implicita

Configurare quindi il flusso di concessione implicita per l'applicazione. Seguire questa procedura:

1. Passare alla registrazione dell'app nel portale di Azure.
1. Nella sezione **Gestisci** selezionare l'impostazione di **autenticazione** .
1. Nella sezione **Grant implicite** selezionare la casella di controllo per abilitare i token ID, come illustrato nell'immagine seguente:

    :::image type="content" source="media/storage-auth-aad-app/enable-implicit-grant-flow.png" alt-text="Screenshot che illustra come registrare l'applicazione di archiviazione con Azure AD":::

## <a name="client-libraries-for-token-acquisition"></a>Librerie client per l'acquisizione di token

Dopo aver registrato l'applicazione e concesso le autorizzazioni per accedere ai dati nell'archivio BLOB di Azure o nell'archiviazione di Accodamento, è possibile aggiungere codice all'applicazione per autenticare un'entità di sicurezza e acquisire un token OAuth 2,0. Per autenticare e acquisire il token, è possibile usare una delle librerie di [autenticazione della piattaforma di Microsoft Identity](../../active-directory/develop/reference-v2-libraries.md) o un'altra libreria open source che supporta OpenID Connect 1,0. L'applicazione può quindi usare il token di accesso per autorizzare una richiesta nell'archivio BLOB o nell'archiviazione di Accodamento di Azure.

Per un elenco degli scenari per i quali è supportato l'acquisizione di token, vedere la sezione relativa ai [flussi di autenticazione](/en-us/azure/active-directory/develop/msal-authentication-flows) nella documentazione di [Microsoft Authentication Library (MSAL)](/azure/active-directory/develop/msal-overview) .

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

Successivamente, assegnare in modo esplicito il ruolo di **collaboratore dati BLOB di archiviazione** all'account utente con cui verrà eseguito il codice di esempio. Per istruzioni su come assegnare questo ruolo nella portale di Azure, vedere [usare i portale di Azure per assegnare un ruolo di Azure per l'accesso ai dati di Accodamento e BLOB](storage-auth-aad-rbac-portal.md).

> [!NOTE]
> Quando si crea un account di archiviazione di Azure, non vengono assegnate automaticamente le autorizzazioni per accedere ai dati tramite Azure AD. È necessario assegnare in modo esplicito a se stessi un ruolo di Azure per archiviazione di Azure. È possibile assegnare questo ruolo a livello di sottoscrizione, gruppo di risorse, account di archiviazione o singolo contenitore o coda.

### <a name="create-a-web-application-that-authorizes-access-to-blob-storage-with-azure-ad"></a>Creare un'applicazione Web che autorizza l'accesso all'archiviazione BLOB con Azure AD

Quando l'applicazione accede ad archiviazione di Azure, questa operazione viene eseguita per conto dell'utente, ovvero si accede alle risorse BLOB o Queue usando le autorizzazioni dell'utente che ha eseguito l'accesso. Per provare questo esempio di codice, è necessaria un'applicazione Web che richiede all'utente di eseguire l'accesso con un'identità Azure AD. È possibile crearne di personalizzati o usare l'applicazione di esempio fornita da Microsoft.

Un'applicazione Web di esempio completa che acquisisce un token e la usa per creare un BLOB in archiviazione di Azure è disponibile in [GitHub](https://aka.ms/aadstorage). Per conoscere gli esempi di codice, è possibile esaminare ed eseguire l'esempio completato. Per istruzioni su come eseguire l'esempio completato, vedere la sezione [visualizzare ed eseguire l'esempio completato](#view-and-run-the-completed-sample).

#### <a name="add-references-and-using-statements"></a>Aggiungere riferimenti e istruzioni using  

Da Visual Studio installare la libreria client di archiviazione di Azure. Dal menu **strumenti** selezionare **Gestione pacchetti NuGet**e quindi Console di **Gestione pacchetti**. Digitare i comandi seguenti nella finestra della console per installare i pacchetti necessari dalla libreria client di archiviazione di Azure per .NET:

# <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

```console
Install-Package Azure.Storage.Blobs
Install-Package Microsoft.Identity.Web -Version 0.4.0-preview
```

Aggiungere quindi le istruzioni using seguenti al file HomeController.cs:

```csharp
using Microsoft.Identity.Web; //MSAL library for getting the access token
using Azure.Storage.Blobs;
```

# <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnet11)

```console
Install-Package Microsoft.Azure.Storage.Blob
Install-Package Microsoft.Identity.Web -Version 0.4.0-preview //or a later version
```

Aggiungere quindi le istruzioni using seguenti al file HomeController.cs:

```csharp
using Microsoft.Identity.Client; //MSAL library for getting the access token
using Microsoft.Azure.Storage.Auth;
using Microsoft.Azure.Storage.Blob;
```

---

#### <a name="create-a-block-blob"></a>Creare un BLOB in blocchi

Aggiungere il frammento di codice seguente per creare un BLOB in blocchi. Ricordare di sostituire i valori tra parentesi angolari con valori personalizzati:

# <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

```csharp
private static async Task<string> CreateBlob(TokenAcquisitionTokenCredential tokenCredential)
{
    Uri blobUri = new Uri("https://<storage-account>.blob.core.windows.net/<container>/Blob1.txt");
    BlobClient blobClient = new BlobClient(blobUri, tokenCredential);

    string blobContents = "Blob created by Azure AD authenticated user.";
    byte[] byteArray = Encoding.ASCII.GetBytes(blobContents);

    using (MemoryStream stream = new MemoryStream(byteArray))
    {
        await blobClient.UploadAsync(stream);
    }
    return "Blob successfully created";
}
```

# <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnet11)

```csharp
private static async Task<string> CreateBlob(string accessToken)
{
    // Create a blob on behalf of the user.
    TokenCredential tokenCredential = new TokenCredential(accessToken);
    StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

    // Replace the URL below with the URL to your blob.
    Uri blobUri = new Uri("https://<storage-account>.blob.core.windows.net/<container>/Blob1.txt");
    CloudBlockBlob blob = new CloudBlockBlob(blobUri, storageCredentials);
    await blob.UploadTextAsync("Blob created by Azure AD authenticated user.");
    return "Blob successfully created";
}
```

---

> [!NOTE]
> Per autorizzare le operazioni BLOB e Queue con un token OAuth 2,0, è necessario usare HTTPS.

Nell'esempio precedente, la libreria client .NET gestisce l'autorizzazione della richiesta di creazione del blob in blocchi. Anche le librerie client di archiviazione di Azure per altri linguaggi gestiscono l'autorizzazione della richiesta. Tuttavia, se si chiama un'operazione di archiviazione di Azure con un token OAuth usando l'API REST, è necessario creare l'intestazione di **autorizzazione** usando il token OAuth.

Per chiamare operazioni del Servizio BLOB e del Servizio di accodamento usando token di accesso OAuth, passare il token di accesso nell'intestazione **Autorizzazione** intestazione tramite lo schema **Bearer** e specificare come versione del servizio la versione 2017-09-11 o successiva, come illustrato nell'esempio seguente:

```https
GET /container/file.txt HTTP/1.1
Host: mystorageaccount.blob.core.windows.net
x-ms-version: 2017-11-09
Authorization: Bearer eyJ0eXAiOnJKV1...Xd6j
```

#### <a name="get-an-access-token-from-azure-ad"></a>Ottenere un token di accesso da Azure AD

Successivamente, aggiungere un metodo che richiede un token da Azure AD per conto dell'utente. Questo metodo definisce l'ambito per il quale devono essere concesse le autorizzazioni. Per ulteriori informazioni sulle autorizzazioni e gli ambiti, vedere [autorizzazioni e consenso nell'endpoint della piattaforma di identità Microsoft](../../active-directory/develop/v2-permissions-and-consent.md).

Usare l'ID risorsa per costruire l'ambito per il quale acquisire il token. Nell'esempio viene costruito l'ambito usando l'ID risorsa insieme all' `user_impersonation` ambito incorporato, che indica che il token viene richiesto per conto dell'utente.

Tenere presente che potrebbe essere necessario presentare all'utente un'interfaccia che consenta all'utente di accettare la richiesta del token per conto dell'utente:

```csharp
[AuthorizeForScopes(Scopes = new string[] { "https://storage.azure.com/user_impersonation" })]
public async Task<IActionResult> Blob()
{
    string message = await CreateBlob(new TokenAcquisitionTokenCredential(_tokenAcquisition));
    ViewData["Message"] = message;
    return View();
}
```

Il consenso è il processo con cui un utente autorizza un'applicazione ad accedere per proprio conto a risorse protette. La piattaforma Microsoft Identity supporta il consenso incrementale, ovvero un'entità di sicurezza può richiedere inizialmente un set minimo di autorizzazioni e aggiungere le autorizzazioni nel tempo in base alle esigenze. Quando il codice richiede un token di accesso, specificare l'ambito delle autorizzazioni necessarie per l'app. Per ulteriori informazioni sul consenso incrementale, vedere il [consenso incrementale e dinamico](../../active-directory/azuread-dev/azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent).

## <a name="view-and-run-the-completed-sample"></a>Visualizzare ed eseguire l'esempio completato

Per eseguire l'applicazione di esempio, clonarla o scaricarla da [GitHub](https://github.com/Azure-Samples/storage-dotnet-azure-ad-msal). Aggiornare quindi l'applicazione come descritto nelle sezioni riportate di seguito.

### <a name="provide-values-in-the-settings-file"></a>Specificare i valori nel file di impostazioni

Aggiornare il *appsettings.jsnel* file con i propri valori, come indicato di seguito:

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "<azure-ad-domain-name>.onmicrosoft.com",
    "TenantId": "<tenant-id>",
    "ClientId": "<client-id>",
    "ClientSecret": "<client-secret>"
    "ClientCertificates": [
    ],
    "CallbackPath": "/signin-oidc"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  },
  "AllowedHosts": "*"
}
```

### <a name="update-the-storage-account-and-container-name"></a>Aggiornare l'account di archiviazione e il nome del contenitore

Nel file *HomeController.cs* aggiornare l'URI che fa riferimento al BLOB in blocchi per usare il nome dell'account di archiviazione e del contenitore, sostituendo i valori tra parentesi angolari con valori personalizzati:

```html
https://<storage-account>.blob.core.windows.net/<container>/Blob1.txt
```

## <a name="next-steps"></a>Passaggi successivi

- [Microsoft Identity Platform](/azure/active-directory/develop/)
- [Gestire i diritti di accesso ai dati di archiviazione con RBAC di Azure](storage-auth-aad-rbac.md)
- [Autenticare l'accesso a BLOB e code con Azure Active Directory e identità gestite per le risorse di Azure](storage-auth-aad-msi.md)
