---
title: Gestire gli utenti con l'API Microsoft Graph
titleSuffix: Azure AD B2C
description: Come gestire gli utenti in un tenant B2C di Azure AD chiamando l'API Microsoft Graph e usando un'identità dell'applicazione per automatizzare il processo.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 42596ba5470c6062efba4fd1050c1c9745b76e80
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637337"
---
# <a name="manage-azure-ad-b2c-user-accounts-with-microsoft-graph"></a>Gestire gli account utente di Azure AD B2C con Microsoft GraphManage Azure AD B2C user accounts with Microsoft Graph

Microsoft Graph consente di gestire gli account utente nella directory B2C di Azure AD fornendo metodi di creazione, lettura, aggiornamento ed eliminazione nell'API Microsoft Graph. È possibile eseguire la migrazione di un archivio utente esistente a un tenant B2C di Azure AD ed eseguire altre operazioni di gestione degli account utente chiamando l'API Microsoft Graph.You can migrate an existing user store to an Azure AD B2C tenant and perform other user account management operations by calling the Microsoft Graph API.

Nelle sezioni seguenti vengono presentati gli aspetti chiave della gestione degli utenti B2C di Azure AD con l'API Microsoft Graph.In the sections that follow, the key aspects of Azure AD B2C user management with the Microsoft Graph API are presented. Le operazioni, i tipi e le proprietà dell'API Microsoft Graph qui presentati sono un sottoinsieme di ciò che viene visualizzato nella documentazione di riferimento dell'API Microsoft Graph.

## <a name="register-a-management-application"></a>Registrare un'applicazione di gestioneRegister a management application

Prima che qualsiasi applicazione o script di gestione degli utenti che si scrive possa interagire con le risorse nel tenant B2C di Azure AD, è necessaria una registrazione dell'applicazione che conceda le autorizzazioni a tale scopo.

Seguire i passaggi descritti in questo articolo per creare una registrazione dell'applicazione che può essere utilizzata dall'applicazione di gestione:Follow the steps in this how-to article to create an application registration that your management application can use:

[Gestire Azure AD B2C con Microsoft GraphManage Azure AD B2C with Microsoft Graph](microsoft-graph-get-started.md)

## <a name="user-management-microsoft-graph-operations"></a>Gestione utenti Operazioni di Microsoft Graph

[Nell'API Microsoft Graph](https://docs.microsoft.com/graph/api/resources/user)sono disponibili le seguenti operazioni di gestione degli utenti:

- [Ottenere un elenco di utentiGet a list of users](https://docs.microsoft.com/graph/api/user-list)
- [Creare un utenteCreate a user](https://docs.microsoft.com/graph/api/user-post-users)
- [Ottenere un utenteGet a user](https://docs.microsoft.com/graph/api/user-get)
- [Aggiornare un utente](https://docs.microsoft.com/graph/api/user-update)
- [Eliminare un utente](https://docs.microsoft.com/graph/api/user-delete)

## <a name="user-properties"></a>Proprietà utente

### <a name="display-name-property"></a>Proprietà del nome visualizzato

Il `displayName` nome da visualizzare nella gestione degli utenti del portale di Azure per l'utente e nel token di accesso che Azure AD B2C restituisce all'applicazione. Questa proprietà è obbligatoria.

### <a name="identities-property"></a>Identities (proprietà)

Un account cliente, che può essere un consumatore, un partner o un cittadino, può essere associato a questi tipi di identità:A customer account, which could be a consumer, partner, or citizen, can be associated with these identity types:

- **Identità locale:** il nome utente e la password vengono archiviati localmente nella directory B2C di Azure AD. Spesso ci riferiamo a queste identità come "account locali".
- **Identità federata:** nota anche come account *social o* *aziendali,* l'identità dell'utente è gestita da un provider di identità federato come Facebook, Microsoft, ADFS o Salesforce.

Un utente con un account cliente può accedere con più identità. Ad esempio, nome utente, indirizzo di posta elettronica, ID dipendente, ID governativo e altri. Un singolo account può avere più identità, sia locali che social, con la stessa password.

Nell'API di Microsoft Graph, le identità locali e `identities` federate vengono archiviate nell'attributo user, che è di tipo [objectIdentity][graph-objectIdentity]. La `identities` raccolta rappresenta un set di identità utilizzate per accedere a un account utente. Questa raccolta consente all'utente di accedere all'account utente con una delle identità associate.

| Proprietà   | Type |Descrizione|
|:---------------|:--------|:----------|
|signInType (firmaInType)|string| Specifica i tipi di accesso dell'utente nella directory. Per l'account `emailAddress1` `emailAddress2`locale: `userName` `emailAddress`, , , `emailAddress3`, o qualsiasi altro tipo desiderato. L'account social `federated`deve essere impostato su .|
|autorità di certificazione|string|Specifica l'autorità emittente dell'identità. Per gli account locali (dove **signInType** non `federated`è ), questa proprietà è `contoso.onmicrosoft.com`il nome di dominio predefinito del tenant B2C locale, ad esempio . Per l'identità sociale `federated`(dove **signInType** è ) il valore è il nome dell'autorità emittente, ad esempio`facebook.com`|
|issuerAssignedId|string|Specifica l'identificatore univoco assegnato all'utente dall'autorità emittente. La combinazione di **issuer** e **issuerAssignedId** deve essere univoca all'interno del tenant. Per l'account locale, quando **signInType** è impostato su `emailAddress` o `userName`, rappresenta il nome di accesso per l'utente.<br>Quando signInType è impostato su:When **signInType** is set to: <ul><li>`emailAddress`(o inizia `emailAddress` `emailAddress1`con like ) **issuerAssignedId** deve essere un indirizzo di posta elettronica valido</li><li>`userName`(o qualsiasi altro valore), **issuerAssignedId** deve essere una parte locale valida [di un indirizzo e-mail](https://tools.ietf.org/html/rfc3696#section-3)</li><li>`federated`, **issuerAssignedId** rappresenta l'identificatore univoco dell'account federato</li></ul>|

La proprietà **Identities** seguente, con un'identità dell'account locale con un nome di accesso, un indirizzo di posta elettronica come accesso e con un'identità di social networking. 

 ```JSON
 "identities": [
     {
       "signInType": "userName",
       "issuer": "contoso.onmicrosoft.com",
       "issuerAssignedId": "johnsmith"
     },
     {
       "signInType": "emailAddress",
       "issuer": "contoso.onmicrosoft.com",
       "issuerAssignedId": "jsmith@yahoo.com"
     },
     {
       "signInType": "federated",
       "issuer": "facebook.com",
       "issuerAssignedId": "5eecb0cd"
     }
   ]
 ```

Per le identità federate, a seconda del provider di identità, **issuerAssignedId** è un valore univoco per un determinato utente per ogni applicazione o account di sviluppo. Configurare i criteri B2C di Azure AD con lo stesso ID applicazione precedentemente assegnato dal provider di social networking o da un'altra applicazione all'interno dello stesso account di sviluppo.

### <a name="password-profile-property"></a>Proprietà del profilo password

Per un'identità locale, la proprietà **passwordProfile** è obbligatoria e contiene la password dell'utente. La `forceChangePasswordNextSignIn` proprietà deve `false`essere impostata su .

Per un'identità federata (social), la proprietà **passwordProfile** non è necessaria.

```JSON
"passwordProfile" : {
    "password": "password-value",
    "forceChangePasswordNextSignIn": false
  }
```

### <a name="password-policy-property"></a>Proprietà dei criteri password

I criteri password B2C di Azure AD (per gli account locali) si basano sui criteri di [complessità avanzata delle password](../active-directory/authentication/concept-sspr-policy.md) di Azure Active Directory.The Azure AD B2C password policy (for local accounts) is based on the Azure Active Directory strong password strength policy. I criteri di iscrizione o di accesso e reimpostazione della password B2C di Azure AD richiedono questa complessità forte della password e non scadono le password.

Negli scenari di migrazione degli utenti, se gli account di cui si vuole eseguire la migrazione hanno una complessità della password più debole rispetto alla forza della [password forte](../active-directory/authentication/concept-sspr-policy.md) applicata da Azure AD B2C, è possibile disabilitare il requisito della password complessa. Per modificare i criteri password predefiniti, impostare la proprietà `passwordPolicies` su `DisableStrongPassword`. È ad esempio possibile modificare la richiesta di creazione utente come segue:

```JSON
"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"
```

### <a name="extension-properties"></a>Proprietà di estensione

Ogni applicazione rivolta ai clienti ha requisiti unici per le informazioni da raccogliere. Il tenant B2C di Azure AD include un set predefinito di informazioni archiviate nelle proprietà, ad esempio Nome, Cognome, Città e Codice postale. Con Azure AD B2C è possibile estendere il set di proprietà archiviate in ogni account cliente. Per ulteriori informazioni sulla definizione di attributi personalizzati, vedere [attributi personalizzati (flussi utente)](user-flow-custom-attributes.md) e [attributi personalizzati (criteri personalizzati).](custom-policy-custom-attributes.md)

L'API Microsoft Graph supporta la creazione e l'aggiornamento di un utente con attributi di estensione. Gli attributi di estensione nell'API Graph vengono denominati usando la convenzione `extension_ApplicationObjectID_attributename`. Ad esempio:

```JSON
"extension_831374b3bd5041bfaa54263ec9e050fc_loyaltyNumber": "212342"
```

## <a name="code-sample"></a>Esempio di codice

Questo esempio di codice è un'applicazione console .NET Core che usa Microsoft Graph SDK per interagire con l'API Microsoft Graph.This code sample is a .NET Core console application that uses the [Microsoft Graph SDK](https://docs.microsoft.com/graph/sdks/sdks-overview) to interact with Microsoft Graph API. Il codice illustra come chiamare l'API per gestire gli utenti in un tenant B2C di Azure AD.
È possibile [scaricare l'archivio di esempio](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management/archive/master.zip) (con estensione zip), [esplorare il repository](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management) su GitHub o clonare il repository:You can download the sample archive (.zip), browse the repository on GitHub, or clone the repository:

```cmd
git clone https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management.git
```

Dopo aver ottenuto l'esempio di codice, configurarlo per l'ambiente e quindi compilare il progetto:After you've obtained the code sample, configure it for your environment and then build the project:

1. Aprire il progetto in [Visual Studio](https://visualstudio.microsoft.com) o Visual [Studio Code](https://code.visualstudio.com).
1. Aprire `src/appsettings.json`.
1. Nella `appSettings` sezione sostituire `your-b2c-tenant` con il nome del `Application (client) ID` `Client secret` tenant e con i valori per la registrazione dell'applicazione di gestione (vedere la sezione [Registrare un'applicazione](#register-a-management-application) di gestione di questo articolo).
1. Aprire una finestra della console all'interno del `src` clone locale del repository, passare alla directory, quindi compilare il progetto:
    ```console
    cd src
    dotnet build
    ```
1. Eseguire l'applicazione con il comando `dotnet`:

    ```console
    dotnet bin/Debug/netcoreapp3.0/b2c-ms-graph.dll
    ```

L'applicazione visualizza un elenco di comandi che è possibile eseguire. Ad esempio, ottenere tutti gli utenti, ottenere un singolo utente, eliminare un utente, aggiornare la password di un utente e l'importazione in blocco.

### <a name="code-discussion"></a>Discussione codice

Il codice di esempio usa [Microsoft Graph SDK,](https://docs.microsoft.com/graph/sdks/sdks-overview)progettato per semplificare la creazione di applicazioni di alta qualità, efficienti e resilienti che accedono a Microsoft Graph.

Qualsiasi richiesta all'API Microsoft Graph richiede un token di accesso per l'autenticazione. La soluzione utilizza il pacchetto [Microsoft.Graph.Auth](https://www.nuget.org/packages/Microsoft.Graph.Auth/) NuGet che fornisce un wrapper basato su scenario di autenticazione della libreria di autenticazione Microsoft (MSAL) per l'utilizzo con Microsoft Graph SDK.

Il `RunAsync` metodo nel file _di Program.cs:_

1. Legge le impostazioni dell'applicazione dal file _appsettings.json_
1. Inizializza il provider di autenticazione utilizzando il flusso di concessione delle [credenziali client OAuth 2.0.](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md) Con la concessione del flusso delle credenziali client, l'app è in grado di ottenere un token di accesso per chiamare l'API Microsoft Graph.With the client credentials grant flow, the app is able to get an access token to call the Microsoft Graph API.
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

Il *GraphServiceClient* inizializzato viene quindi utilizzato in _UserService.cs_ per eseguire le operazioni di gestione degli utenti. Ad esempio, ottenere un elenco degli account utente nel tenant:

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

[Effettuare chiamate API utilizzando gli SDK](https://docs.microsoft.com/graph/sdks/create-requests) di Microsoft Graph include informazioni su `$select` come leggere e scrivere informazioni da Microsoft `$filter` Graph, utilizzare per controllare le proprietà restituite, fornire parametri di query personalizzati e utilizzare i parametri e `$orderBy` query.

## <a name="next-steps"></a>Passaggi successivi

Per un indice completo delle operazioni api di Microsoft Graph supportate per le risorse B2C di Azure AD, vedere Operazioni di [Microsoft Graph disponibili per Azure AD B2C.](microsoft-graph-operations.md)

<!-- LINK -->

[graph-objectIdentity]: https://docs.microsoft.com/graph/api/resources/objectidentity
[graph-user]: (https://docs.microsoft.com/graph/api/resources/user)
