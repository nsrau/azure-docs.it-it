---
title: Gestire gli utenti con l'API Microsoft Graph
titleSuffix: Azure AD B2C
description: Come gestire gli utenti in un tenant di Azure AD B2C chiamando l'API Microsoft Graph e usando un'identità dell'applicazione per automatizzare il processo.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: b53405d199072211304e21b681de646c4e41243c
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77585629"
---
# <a name="manage-azure-ad-b2c-user-accounts-with-microsoft-graph"></a>Gestire gli account utente Azure AD B2C con Microsoft Graph

Microsoft Graph consente di gestire gli account utente nella directory Azure AD B2C fornendo metodi di creazione, lettura, aggiornamento ed eliminazione nell'API Microsoft Graph. È possibile eseguire la migrazione di un archivio utente esistente a un tenant di Azure AD B2C ed eseguire altre operazioni di gestione degli account utente chiamando l'API Microsoft Graph.

Nelle sezioni seguenti vengono presentati gli aspetti chiave di Azure AD B2C la gestione degli utenti con l'API Microsoft Graph. Le operazioni, i tipi e le proprietà dell'API Microsoft Graph presentati di seguito sono un subset di che viene visualizzato nella documentazione di riferimento per l'API Microsoft Graph.

## <a name="register-a-management-application"></a>Registrare un'applicazione di gestione

Prima che qualsiasi applicazione di gestione utente o script scritto possa interagire con le risorse nel tenant di Azure AD B2C, è necessaria la registrazione di un'applicazione che conceda le autorizzazioni necessarie.

Per creare una registrazione dell'applicazione utilizzabile dall'applicazione di gestione, attenersi alla procedura descritta in questo articolo:

[Gestire Azure AD B2C con Microsoft Graph](microsoft-graph-get-started.md)

## <a name="user-management-microsoft-graph-operations"></a>Operazioni di Microsoft Graph gestione utenti

Le seguenti operazioni di gestione degli utenti sono disponibili nell' [API Microsoft Graph](https://docs.microsoft.com/graph/api/resources/user):

- [Ottenere un elenco di utenti](https://docs.microsoft.com/graph/api/user-list)
- [Creare un utente](https://docs.microsoft.com/graph/api/user-post-users)
- [Ottenere un utente](https://docs.microsoft.com/graph/api/user-get)
- [Aggiornare un utente](https://docs.microsoft.com/graph/api/user-update)
- [Eliminare un utente](https://docs.microsoft.com/graph/api/user-delete)

## <a name="user-properties"></a>Proprietà utente

### <a name="display-name-property"></a>Proprietà nome visualizzato

Il `displayName` è il nome da visualizzare in portale di Azure Gestione utenti per l'utente e nel token di accesso Azure AD B2C torna all'applicazione. Questa proprietà è obbligatoria.

### <a name="identities-property"></a>Proprietà identitys

Un account cliente, che può essere un consumatore, un partner o un cittadino, può essere associato a questi tipi di identità:

- Identità **locale** : il nome utente e la password vengono archiviati localmente nella directory Azure ad B2C. Spesso si fa riferimento a queste identità come "account locali".
- Identità **federata** , nota anche come account *aziendali* o *sociali* , l'identità dell'utente è gestita da un provider di identità FEDERATO come Facebook, Microsoft, ADFS o Salesforce.

Un utente con un account cliente può accedere con più identità. Ad esempio nome utente, indirizzo di posta elettronica, ID dipendente, ID pubblico e altro. Un singolo account può avere più identità, sia locali che sociali, con la stessa password.

Nell'API Microsoft Graph, le identità locali e federative vengono archiviate nell'attributo `identities` dell'utente, che è di tipo [objectIdentity][graph-objectIdentity]. La raccolta di `identities` rappresenta un set di identità usate per accedere a un account utente. Questa raccolta consente all'utente di accedere all'account utente con qualsiasi identità associata.

| Proprietà   | Type |Descrizione|
|:---------------|:--------|:----------|
|signInType|string| Specifica i tipi di accesso utente nella directory. Per l'account locale: `emailAddress`, `emailAddress1`, `emailAddress2`, `emailAddress3`, `userName`o qualsiasi altro tipo. L'account di social networking deve essere impostato su `federated`.|
|autorità di certificazione|string|Specifica l'emittente dell'identità. Per gli account locali (dove **signInType** non è `federated`), questa proprietà è il nome di dominio predefinito del tenant B2C locale, ad esempio `contoso.onmicrosoft.com`. Per l'identità sociale (dove **signInType** è `federated`), il valore è il nome dell'autorità emittente, ad esempio `facebook.com`|
|issuerAssignedId|string|Specifica l'identificatore univoco assegnato all'utente dall'emittente. La combinazione di **Issuer** e **issuerAssignedId** deve essere univoca all'interno del tenant. Per l'account locale, quando **signInType** è impostato su `emailAddress` o `userName`, rappresenta il nome di accesso per l'utente.<br>Quando **signInType** è impostato su: <ul><li>`emailAddress` (o inizia con `emailAddress` come `emailAddress1`) **issuerAssignedId** deve essere un indirizzo di posta elettronica valido</li><li>`userName` (o qualsiasi altro valore), **issuerAssignedId** deve essere una [parte locale valida di un indirizzo di posta elettronica](https://tools.ietf.org/html/rfc3696#section-3)</li><li>`federated`, **issuerAssignedId** rappresenta l'identificatore univoco dell'account federato</li></ul>|

Per le identità federate, a seconda del provider di identità, **issuerAssignedId** è un valore univoco per un determinato utente per ogni applicazione o account di sviluppo. Configurare i criteri di Azure AD B2C con lo stesso ID applicazione assegnato in precedenza dal provider di social networking o da un'altra applicazione all'interno dello stesso account di sviluppo.

### <a name="password-profile-property"></a>Proprietà profilo password

Per un'identità locale, la proprietà **passwordProfile** è obbligatoria e contiene la password dell'utente. La proprietà `forceChangePasswordNextSignIn` deve essere impostata su `false`.

Per un'identità federata (social), la proprietà **passwordProfile** non è obbligatoria.

```JSON
"passwordProfile" : {
    "password": "password-value",
    "forceChangePasswordNextSignIn": false
  }
```

### <a name="password-policy-property"></a>Proprietà dei criteri password

I criteri di Azure AD B2C password (per gli account locali) si basano sui criteri di Azure Active Directory [complessità delle password complesse](../active-directory/authentication/concept-sspr-policy.md) . I criteri di registrazione o accesso e reimpostazione della password di Azure AD B2C richiedono questo livello di complessità della password complessa e non scadono le password.

Negli scenari di migrazione degli utenti, se gli account di cui si vuole eseguire la migrazione hanno un livello di complessità della password più debole rispetto al livello di [attendibilità delle password complesse](../active-directory/authentication/concept-sspr-policy.md) applicato da Azure ad B2C, è possibile disabilitare il requisito per la password complessa. Per modificare i criteri password predefiniti, impostare la proprietà `passwordPolicies` su `DisableStrongPassword`. È ad esempio possibile modificare la richiesta di creazione utente come segue:

```JSON
"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"
```

### <a name="extension-properties"></a>Proprietà estensione

Ogni applicazione rivolte ai clienti presenta requisiti specifici per la raccolta delle informazioni. Il tenant di Azure AD B2C dispone di un set predefinito di informazioni archiviate in proprietà, ad esempio nome, cognome, città e codice postale. Con Azure AD B2C è possibile estendere il set di proprietà archiviate in ogni account del cliente. Per altre informazioni sulla definizione di attributi personalizzati, vedere [attributi personalizzati (flussi utente)](user-flow-custom-attributes.md) e [attributi personalizzati (criteri personalizzati)](custom-policy-custom-attributes.md).

Microsoft Graph API supporta la creazione e l'aggiornamento di un utente con attributi di estensione. Gli attributi di estensione nell'API Graph vengono denominati usando la convenzione `extension_ApplicationObjectID_attributename`. Ad esempio:

```JSON
"extension_831374b3bd5041bfaa54263ec9e050fc_loyaltyNumber": "212342"
```

## <a name="code-sample"></a>Esempio di codice

Questo esempio di codice è un'applicazione console .NET Core che usa l' [SDK Microsoft Graph](https://docs.microsoft.com/graph/sdks/sdks-overview) per interagire con Microsoft Graph API. Il codice illustra come chiamare l'API per gestire gli utenti a livello di codice in un tenant Azure AD B2C.
È possibile [scaricare l'archivio di esempio](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management/archive/master.zip) (*. zip), [esplorare il repository](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management) in GitHub o clonare il repository:

```cmd
git clone https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management.git
```

Dopo aver ottenuto l'esempio di codice, configurarlo per l'ambiente e quindi compilare il progetto:

1. Aprire il progetto in [Visual Studio](https://visualstudio.microsoft.com) o in [Visual Studio Code](https://code.visualstudio.com).
1. Aprire `src/appsettings.json`.
1. Nella sezione `appSettings` sostituire `your-b2c-tenant` con il nome del tenant e `Application (client) ID` e `Client secret` con i valori per la registrazione dell'applicazione di gestione (vedere la sezione [registrare un'applicazione di gestione](#register-a-management-application) di questo articolo).
1. Aprire una finestra della console all'interno del clone locale del repository, passare alla directory `src`, quindi compilare il progetto:
    ```console
    cd src
    dotnet build
    ```
1. Eseguire l'applicazione con il comando `dotnet`:

```console
dotnet bin/Debug/netcoreapp3.0/b2c-ms-graph.dll
```

L'applicazione visualizza un elenco di comandi che è possibile eseguire. Ad esempio, ottenere tutti gli utenti, ottenere un singolo utente, eliminare un utente, aggiornare la password di un utente ed eseguire l'importazione bulk.

### <a name="code-discussion"></a>Discussione sul codice

Il codice di esempio usa il [Microsoft Graph SDK](https://docs.microsoft.com/graph/sdks/sdks-overview), progettato per semplificare la creazione di applicazioni di alta qualità, efficienti e resilienti che accedono a Microsoft Graph. Non è quindi necessario creare una diretta per tutte le API Microsoft Graph.

Qualsiasi richiesta all'API Microsoft Graph richiede un token di accesso per l'autenticazione. La soluzione USA il pacchetto NuGet [Microsoft. Graph. auth](https://www.nuget.org/packages/Microsoft.Graph.Auth/) che fornisce un wrapper basato sullo scenario di autenticazione di Microsoft Authentication Library (MSAL) per l'uso con il Microsoft Graph SDK.

Il `RunAsync` metodo nel file _Program.cs_ :

1. Legge le impostazioni dell'applicazione dal file _appSettings. JSON_
1. Inizializza il provider di autenticazione utilizzando il flusso di [concessione delle credenziali client OAuth 2,0](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md) . Con il flusso di concessione delle credenziali client, l'app è in grado di ottenere un token di accesso per chiamare l'API Microsoft Graph.
1. Configura il client del servizio Microsoft Graph con il provider di autenticazione:

    ```csharp
    // Read application settings from appsettings.json (tenant ID, app ID, client secret, etc.)
    AppSettings config = AppSettingsFile.ReadFromJsonFile();

    // Initialize the client credential auth provider
    IConfidentialClientApplication confidentialClientApplication = ConfidentialClientApplicationBuilder
        .Create(config.AppId)
        .WithTenantId(config.TenantId)
        .WithClientSecret(config.ClientSecret)
        .Build();
    ClientCredentialProvider authProvider = new ClientCredentialProvider(confidentialClientApplication);

    // Set up the Microsoft Graph service client with client credentials
    GraphServiceClient graphClient = new GraphServiceClient(authProvider);
    ```

Il *GraphServiceClient* inizializzato viene quindi usato in _UserService.cs_ per eseguire le operazioni di gestione degli utenti. Ad esempio, ottenere un elenco degli account utente nel tenant:

```csharp
public static async Task ListUsers(GraphServiceClient graphClient)
{
    Console.WriteLine("Getting list of users...");

    // Get all users (one page)
    var result = await graphClient.Users
        .Request()
        .Select(e => new
        {
            e.DisplayName,
            e.Id,
            e.Identities
        })
        .GetAsync();

    foreach (var user in result.CurrentPage)
    {
        Console.WriteLine(JsonConvert.SerializeObject(user));
    }
}
```

[Eseguire chiamate API usando gli SDK di Microsoft Graph](https://docs.microsoft.com/graph/sdks/create-requests) include informazioni su come leggere e scrivere informazioni da Microsoft Graph, utilizzare `$select` per controllare le proprietà restituite, specificare parametri di query personalizzati e utilizzare i parametri di query `$filter` e `$orderBy`.

## <a name="next-steps"></a>Passaggi successivi

Per un indice completo delle operazioni dell'API Microsoft Graph supportate per le risorse Azure AD B2C, vedere [Microsoft Graph operazioni disponibili per Azure ad B2C](microsoft-graph-operations.md).

<!-- LINK -->

[graph-objectIdentity]: https://docs.microsoft.com/graph/api/resources/objectidentity
[graph-user]: (https://docs.microsoft.com/graph/api/resources/user)
