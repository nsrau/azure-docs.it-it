---
title: Usare il API Graph in Azure Active Directory B2C
description: Come gestire gli utenti in un tenant di Azure AD B2C chiamando il Azure AD API Graph e usando un'identità dell'applicazione per automatizzare il processo.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: c4c8f123eb8c32362219f21dc70d137f2cc9b4b1
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74078829"
---
# <a name="azure-ad-b2c-use-the-azure-ad-graph-api"></a>Azure AD B2C: usare l'API Graph di Azure AD

I tenant di Azure Active Directory B2C (Azure AD B2C) possono avere migliaia o milioni di utenti. il che significa che molte attività comuni di gestione dei tenant devono essere eseguite a livello di programmazione, ad esempio la gestione degli utenti.

Potrebbe essere necessario eseguire la migrazione di un archivio utenti esistente in un tenant di B2C. Potrebbe essere necessario ospitare la registrazione degli utenti nella propria pagina e creare gli account utente nella directory Azure AD B2C in background. Questi tipi di attività richiedono la possibilità di creare, leggere, aggiornare ed eliminare gli account utente. È possibile eseguire tali attività usando il API Graph Azure AD.

Per i tenant B2C, esistono due modalità principali di comunicazione con i API Graph:

* Per le attività **interattive**, Esegui una sola volta, è necessario fungere da account amministratore nel tenant B2C quando si eseguono le attività. Questa modalità richiede che un amministratore acceda con le credenziali prima di effettuare chiamate all'API Graph.
* Per le attività **automatiche**continue, è necessario utilizzare un tipo di account del servizio fornito con i privilegi necessari per eseguire le attività di gestione. In Azure AD è possibile ottenere questo risultato registrando un'applicazione ed effettuando l'autenticazione in Azure AD. A questo scopo usare un *ID applicazione* che usa la [concessione delle credenziali client OAuth 2.0](../active-directory/develop/service-to-service.md). In questo caso l'applicazione agisce autonomamente, non come utente, per chiamare l'API Graph.

In questo articolo si apprenderà come eseguire il caso di utilizzo automatico. Verrà compilato un `B2CGraphClient` .NET 4.5 che esegue operazioni di creazione, lettura, aggiornamento ed eliminazione (CRUD, Create, Read, Update, Delete) di utenti. Il client avrà un'interfaccia della riga di comando di Windows che consente di richiamare diversi metodi. Tuttavia, il codice viene scritto per comportarsi in modo automatico e non interattivo.

>[!IMPORTANT]
> Per gestire gli utenti in una directory Azure AD B2C, è **necessario** utilizzare il [Azure ad API Graph](../active-directory/develop/active-directory-graph-api-quickstart.md) . Il API Graph Azure AD è diverso dall'API Microsoft Graph. Per altre informazioni, vedere il post di Blog di MSDN [Microsoft Graph o Azure AD Graph](https://blogs.msdn.microsoft.com/aadgraphteam/2016/07/08/microsoft-graph-or-azure-ad-graph/).

## <a name="prerequisites"></a>prerequisiti

Prima di poter creare applicazioni o utenti, è necessario un tenant di Azure AD B2C. Se non ne è già presente uno, [creare un tenant Azure Active Directory B2C](tutorial-create-tenant.md).

## <a name="register-an-application"></a>Registrare un'applicazione

Quando si dispone di un tenant di Azure AD B2C, è necessario registrare l'applicazione di gestione utilizzando l' [portale di Azure](https://portal.azure.com). È anche necessario concedergli le autorizzazioni necessarie per l'esecuzione di attività di gestione per conto dell'applicazione di gestione o dello script automatizzato.

### <a name="register-application-in-azure-active-directory"></a>Registrare l'applicazione in Azure Active Directory

Per usare la Azure AD API Graph con il tenant B2C, è necessario registrare un'applicazione usando il flusso di lavoro di registrazione dell'applicazione Azure Active Directory.

[!INCLUDE [active-directory-b2c-appreg-mgmt](../../includes/active-directory-b2c-appreg-mgmt.md)]

### <a name="assign-api-access-permissions"></a>Assegnare le autorizzazioni di accesso all'API

[!INCLUDE [active-directory-b2c-permissions-directory](../../includes/active-directory-b2c-permissions-directory.md)]

### <a name="create-client-secret"></a>Crea segreto client

[!INCLUDE [active-directory-b2c-client-secret](../../includes/active-directory-b2c-client-secret.md)]

A questo punto si dispone di un'applicazione che dispone dell'autorizzazione per *creare*, *leggere*e *aggiornare* gli utenti nel tenant del Azure ad B2C. Passare alla sezione successiva per aggiungere le autorizzazioni per l' *eliminazione* e l' *aggiornamento della password* dell'utente.

## <a name="add-user-delete-and-password-update-permissions"></a>Aggiungere le autorizzazioni di eliminazione utente e aggiornamento password

L'autorizzazione per la *lettura e la scrittura dei dati della directory* concessa in precedenza **non include la** possibilità di eliminare gli utenti o di aggiornarne le password.

Se si desidera consentire all'applicazione di eliminare gli utenti o aggiornare le password, è necessario concedergli il ruolo di *amministratore utente* .

1. Accedere al [portale di Azure](https://portal.azure.com) e passare alla directory che contiene il tenant del Azure ad B2C.
1. Selezionare **Azure ad B2C** nel menu a sinistra. In alternativa, selezionare **tutti i servizi** e quindi cercare e selezionare **Azure ad B2C**.
1. In **Gestisci**selezionare **ruoli e amministratori**.
1. Selezionare il ruolo di **amministratore utente** .
1. Selezionare **Aggiungi assegnazione**.
1. Nella casella di testo **Seleziona** immettere il nome dell'applicazione registrata in precedenza, ad esempio *managementapp1*. Selezionare l'applicazione quando viene visualizzata nei risultati della ricerca.
1. Selezionare **Aggiungi**. Potrebbero essere necessari alcuni minuti per la propagazione completa delle autorizzazioni.

L'applicazione Azure AD B2C dispone ora delle autorizzazioni aggiuntive necessarie per eliminare gli utenti o aggiornare le password nel tenant B2C.

## <a name="get-the-sample-code"></a>Scaricare il codice di esempio

L'esempio di codice è un'applicazione console .NET che usa la [Active Directory Authentication Library (adal)](../active-directory/develop/active-directory-authentication-libraries.md) per interagire con Azure ad API Graph. Il codice illustra come chiamare l'API per gestire gli utenti a livello di codice in un tenant Azure AD B2C.

È possibile [scaricare l'archivio di esempio](https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet/archive/master.zip) (\*. zip) o clonare il repository GitHub:

```cmd
git clone https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet.git
```

Dopo aver ottenuto l'esempio di codice, configurarlo per l'ambiente e quindi compilare il progetto:

1. Aprire la soluzione `B2CGraphClient\B2CGraphClient.sln` in Visual Studio.
1. Nel progetto **B2CGraphClient** aprire il file *app. config* .
1. Sostituire la sezione `<appSettings>` con il codice XML seguente. Sostituire quindi `{your-b2c-tenant}` con il nome del tenant e `{Application ID}` e `{Client secret}` con i valori registrati in precedenza.

    ```xml
    <appSettings>
        <add key="b2c:Tenant" value="{your-b2c-tenant}.onmicrosoft.com" />
        <add key="b2c:ClientId" value="{Application ID}" />
        <add key="b2c:ClientSecret" value="{Client secret}" />
    </appSettings>
    ```

1. Compilare la soluzione. Fare clic con il pulsante destro del mouse sulla soluzione **B2CGraphClient** nella Esplora soluzioni, quindi scegliere **Ricompila soluzione**.

Se la compilazione ha esito positivo, è possibile trovare l'applicazione console `B2C.exe` in `B2CGraphClient\bin\Debug`.

## <a name="review-the-sample-code"></a>Esaminare il codice di esempio

Per usare B2CGraphClient, aprire un prompt dei comandi (`cmd.exe`) e passare alla directory `Debug` del progetto. Eseguire quindi il comando `B2C Help`.

```cmd
cd B2CGraphClient\bin\Debug
B2C Help
```

Il comando `B2C Help` Visualizza una breve descrizione dei sottocomandi disponibili. Ogni volta che si richiama uno dei sottocomandi, `B2CGraphClient` invia una richiesta al Azure AD API Graph.

Nelle sezioni seguenti viene illustrato il modo in cui il codice dell'applicazione effettua chiamate al API Graph Azure AD.

### <a name="get-an-access-token"></a>Ottenere un token di accesso

Qualsiasi richiesta al API Graph di Azure AD richiede un token di accesso per l'autenticazione. `B2CGraphClient` usa il Active Directory Authentication Library Open Source (ADAL) per semplificare il recupero dei token di accesso. ADAL rende più semplice l'acquisizione di token fornendo un'API helper e occupando alcuni dettagli importanti, come la memorizzazione nella cache dei token di accesso. Tuttavia, non è necessario usare ADAL per ottenere i token. È possibile ottenere i token tramite la creazione manuale di richieste HTTP.

> [!NOTE]
> Per ottenere i token di accesso che possono essere usati con il Azure AD API Graph, è necessario usare ADAL v2 o versione successiva. Non è possibile usare ADAL V1.

Quando `B2CGraphClient` viene eseguito, viene creata un'istanza della classe `B2CGraphClient`. Il costruttore per questa classe imposta l'impalcatura di autenticazione ADAL:

```csharp
public B2CGraphClient(string clientId, string clientSecret, string tenant)
{
    // The client_id, client_secret, and tenant are provided in Program.cs, which pulls the values from App.config
    this.clientId = clientId;
    this.clientSecret = clientSecret;
    this.tenant = tenant;

    // The AuthenticationContext is ADAL's primary class, in which you indicate the tenant to use.
    this.authContext = new AuthenticationContext("https://login.microsoftonline.com/" + tenant);

    // The ClientCredential is where you pass in your client_id and client_secret, which are
    // provided to Azure AD in order to receive an access_token by using the app's identity.
    this.credential = new ClientCredential(clientId, clientSecret);
}
```

Usare il comando `B2C Get-User` come esempio.

Quando `B2C Get-User` viene richiamato senza argomenti aggiuntivi, l'applicazione chiama il metodo `B2CGraphClient.GetAllUsers()`. `GetAllUsers()` chiama quindi `B2CGraphClient.SendGraphGetRequest()`, che invia una richiesta HTTP GET al Azure AD API Graph. Prima che `B2CGraphClient.SendGraphGetRequest()` invii la richiesta GET, ottiene prima di tutto un token di accesso usando ADAL:

```csharp
public async Task<string> SendGraphGetRequest(string api, string query)
{
    // First, use ADAL to acquire a token by using the app's identity (the credential)
    // The first parameter is the resource we want an access_token for; in this case, the Graph API.
    AuthenticationResult result = authContext.AcquireToken("https://graph.windows.net", credential);
    ...
```

È possibile ottenere un token di accesso per l'API Graph chiamando il metodo `AuthenticationContext.AcquireToken()` di ADAL. ADAL restituisce quindi un valore `access_token` che rappresenta l'identità dell'applicazione.

### <a name="read-users"></a>Leggere gli utenti

Quando si desidera ottenere un elenco di utenti o ottenere un determinato utente dal Azure AD API Graph, è possibile inviare una richiesta HTTP `GET` all'endpoint `/users`. Una richiesta per tutti gli utenti in un tenant ha un aspetto analogo al seguente:

```HTTP
GET https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

Per visualizzare questa richiesta, eseguire:

 ```cmd
 B2C Get-User
 ```

Esistono due aspetti importanti da notare:

* Il token di accesso ottenuto tramite ADAL viene aggiunto all'intestazione `Authorization` usando lo schema di `Bearer`.
* Per i tenant B2C, è necessario usare il parametro della query `api-version=1.6`.

Entrambi questi dettagli vengono gestiti con il metodo `B2CGraphClient.SendGraphGetRequest()` :

```csharp
public async Task<string> SendGraphGetRequest(string api, string query)
{
    ...

    // For B2C user management, be sure to use the 1.6 Graph API version.
    HttpClient http = new HttpClient();
    string url = "https://graph.windows.net/" + tenant + api + "?" + "api-version=1.6";
    if (!string.IsNullOrEmpty(query))
    {
        url += "&" + query;
    }

    // Append the access token for the Graph API to the Authorization header of the request by using the Bearer scheme.
    HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get, url);
    request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
    HttpResponseMessage response = await http.SendAsync(request);

    ...
```

### <a name="create-consumer-user-accounts"></a>Creare account utente consumer

Quando si creano account utente nel tenant B2C, è possibile inviare una richiesta HTTP `POST` all'endpoint `/users`. La richiesta `POST` HTTP seguente mostra un esempio di utente da creare nel tenant.

La maggior parte delle proprietà nella richiesta seguente è necessaria per creare utenti consumer. I commenti `//` sono stati inclusi a scopo illustrativo, non includerli in una richiesta effettiva.

```HTTP
POST https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 338

{
    // All of these properties are required to create consumer users.

    "accountEnabled": true,
    "signInNames": [                           // controls which identifier the user uses to sign in to the account
        {
            "type": "emailAddress",            // can be 'emailAddress' or 'userName'
            "value": "consumer@fabrikam.com"
        }
    ],
    "creationType": "LocalAccount",            // always set to 'LocalAccount'
    "displayName": "Consumer User",            // a value that can be used for displaying to the end user
    "mailNickname": "cuser",                   // an email alias for the user
    "passwordProfile": {
        "password": "P@ssword!",
        "forceChangePasswordNextLogin": false  // always set to false
    },
    "passwordPolicies": "DisablePasswordExpiration"
}
```

Per visualizzare la richiesta, eseguire uno dei comandi seguenti:

```cmd
B2C Create-User ..\..\..\usertemplate-email.json
B2C Create-User ..\..\..\usertemplate-username.json
```

Il comando `Create-User` accetta come parametro di input un file JSON che contiene una rappresentazione JSON di un oggetto utente. Nell'esempio di codice sono presenti due file JSON di esempio: `usertemplate-email.json` e `usertemplate-username.json`. È possibile modificare questi file per adeguarli alle proprie esigenze. Oltre ai campi obbligatori indicati in precedenza, nei file sono inclusi diversi campi facoltativi.

Per ulteriori informazioni sui campi obbligatori e facoltativi, vedere informazioni di [riferimento sui tipi di entità e complessi | Riferimento API Graph](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference).

È possibile vedere come viene costruita la richiesta POST in `B2CGraphClient.SendGraphPostRequest()`:

* Collega un token di accesso all'intestazione `Authorization` della richiesta.
* Imposta `api-version=1.6`.
* Include l'oggetto utente JSON nel corpo della richiesta.

> [!NOTE]
> Se gli account di cui si vuole eseguire la migrazione da un archivio utenti esistente hanno un livello di complessità della password inferiore rispetto al livello [di attendibilità delle password complesse applicato da Azure ad B2C](active-directory-b2c-reference-password-complexity.md), è possibile disabilitare il requisito per la password complessa usando il valore `DisableStrongPassword` nella proprietà `passwordPolicies`. È ad esempio possibile modificare la richiesta di creazione utente precedente nel modo seguente: `"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"`.

### <a name="update-consumer-user-accounts"></a>Aggiornare gli account utente consumer

Quando si aggiornano gli oggetti utente, il processo è simile a quello usato per creare oggetti utente, ma usa il metodo HTTP `PATCH`:

```HTTP
PATCH https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 37

{
    "displayName": "Joe Consumer"    // this request updates only the user's displayName
}
```

Provare ad aggiornare un utente modificando alcuni valori nei file JSON e quindi usare il `B2CGraphClient` per eseguire uno di questi comandi:

```cmd
B2C Update-User <user-object-id> ..\..\..\usertemplate-email.json
B2C Update-User <user-object-id> ..\..\..\usertemplate-username.json
```

Esaminare il metodo `B2CGraphClient.SendGraphPatchRequest()` per informazioni dettagliate su come inviare la richiesta.

### <a name="search-users"></a>Cercare gli utenti

È possibile cercare gli utenti nel tenant B2C nei modi seguenti:

* Fare riferimento all' **ID oggetto**dell'utente.
* Fare riferimento al identificatore di accesso, ovvero la proprietà `signInNames`.
* Fare riferimento a uno dei parametri OData validi, ad esempio given, cognome, displayName e così via.

Eseguire uno dei comandi seguenti per cercare un utente:

```cmd
B2C Get-User <user-object-id>
B2C Get-User <filter-query-expression>
```

Ad esempio:

```cmd
B2C Get-User 2bcf1067-90b6-4253-9991-7f16449c2d91
B2C Get-User $filter=signInNames/any(x:x/value%20eq%20%27consumer@fabrikam.com%27)
B2C get-user $filter=givenName%20eq%20%27John%27
B2C get-user $filter=surname%20eq%20%27Doe%27
B2C get-user $filter=displayName%20eq%20%27John%20Doe%27
```

### <a name="delete-users"></a>Eliminare gli utenti

Per eliminare gli utenti, usare il metodo HTTP `DELETE` e costruire l'URL con l'ID oggetto dell'utente:

```HTTP
DELETE https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

Per visualizzare un esempio, immettere questo comando e visualizzare la richiesta di eliminazione stampata nella console:

```cmd
B2C Delete-User <object-id-of-user>
```

Esaminare il metodo `B2CGraphClient.SendGraphDeleteRequest()` per informazioni dettagliate su come inviare la richiesta.

È possibile eseguire molte altre azioni con l'API Graph di Azure AD, oltre alla gestione degli utenti. Le [informazioni di riferimento sull'API Graph di Azure AD](/previous-versions/azure/ad/graph/api/api-catalog) illustrano i dettagli di ogni azione, insieme a richieste di esempio.

## <a name="use-custom-attributes"></a>Usare gli attributi personalizzati

La maggior parte delle applicazioni consumer deve archiviare alcune informazioni del profilo utente personalizzate. Un modo per eseguire questa operazione consiste nel definire un attributo personalizzato nel tenant di B2C. È quindi possibile trattare tale attributo nello stesso modo in cui si tratta di qualsiasi altra proprietà di un oggetto utente. È possibile aggiornare l'attributo, eliminarlo, eseguire una query in base all'attributo, inviarlo come attestazione in un token di accesso e così via.

Per definire un attributo personalizzato nel tenant di B2C, vedere le [informazioni di riferimento sugli attributi personalizzati di B2C](active-directory-b2c-reference-custom-attr.md).

È possibile visualizzare gli attributi personalizzati definiti nel tenant B2C usando i comandi di `B2CGraphClient` seguenti:

```cmd
B2C Get-B2C-Application
B2C Get-Extension-Attribute <object-id-in-the-output-of-the-above-command>
```

L'output rivela i dettagli di ogni attributo personalizzato. Ad esempio:

```json
{
      "odata.type": "Microsoft.DirectoryServices.ExtensionProperty",
      "objectType": "ExtensionProperty",
      "objectId": "cec6391b-204d-42fe-8f7c-89c2b1964fca",
      "deletionTimestamp": null,
      "appDisplayName": "",
      "name": "extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number",
      "dataType": "Integer",
      "isSyncedFromOnPremises": false,
      "targetObjects": [
        "User"
      ]
}
```

È possibile usare il nome completo, ad esempio `extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number`, come proprietà per gli oggetti utente. Aggiornare il file JSON con la nuova proprietà e un valore per la proprietà, quindi eseguire:

```cmd
B2C Update-User <object-id-of-user> <path-to-json-file>
```

## <a name="next-steps"></a>Passaggi successivi

Se si usa `B2CGraphClient`, si ha a disposizione un'applicazione di servizio che può gestire gli utenti del tenant di B2C a livello di codice. `B2CGraphClient` usa la propria identità di applicazione per l'autenticazione all'API Graph di Azure AD. Acquisisce anche i token usando un segreto client.

Quando si incorpora questa funzionalità nella propria applicazione, ricordare alcuni punti chiave per le applicazioni B2C:

* Concedere all'applicazione le autorizzazioni necessarie nel tenant.
* Attualmente è necessario usare ADAL (non MSAL) per ottenere i token di accesso. È anche possibile inviare direttamente messaggi di protocollo, senza usare una raccolta.
* Quando si chiama l'API Graph, usare `api-version=1.6`.
* Quando si creano e aggiornano utenti consumer, sono necessarie alcune proprietà, come illustrato in precedenza.
