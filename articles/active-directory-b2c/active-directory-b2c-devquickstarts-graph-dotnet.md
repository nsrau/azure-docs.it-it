---
title: Utilizzo dell'API Graph - Azure AD B2C | Microsoft Docs
description: "Come chiamare l'API Graph per un tenant di B2C usando l'identità di un'applicazione per automatizzare il processo."
services: active-directory-b2c
documentationcenter: .net
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: f9904516-d9f7-43b1-ae4f-e4d9eb1c67a0
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/07/2017
ms.author: parakhj
ms.openlocfilehash: cce30360145fe430141ceaf2efc91a6b87938601
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/29/2017
---
# <a name="azure-ad-b2c-use-the-azure-ad-graph-api"></a>Azure AD B2C: usare l'API Graph di Azure AD

>[!NOTE]
> Per gestire gli utenti in una directory di Azure AD B2C è richiesto l'uso dell'[API Graph di Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-operations-overview?f=255&MSPPError=-2147217396). Si tratta di un'API diversa dall'API Microsoft Graph. Fare clic [qui](https://blogs.msdn.microsoft.com/aadgraphteam/2016/07/08/microsoft-graph-or-azure-ad-graph/) per altre informazioni.

I tenant di Azure Active Directory (Azure AD) B2C tendono ad avere dimensioni molto grandi, il che significa che molte attività comuni di gestione dei tenant devono essere eseguite a livello di programmazione, ad esempio la gestione degli utenti. Potrebbe essere necessario eseguire la migrazione di un archivio utenti esistente in un tenant di B2C. Potrebbe essere necessario ospitare la registrazione degli utenti nella propria pagina e creare gli account utente nella directory Azure AD B2C in background. Questi tipi di attività richiedono la possibilità di creare, leggere, aggiornare ed eliminare gli account utente. È possibile eseguire queste attività usando l'API Graph di Azure AD.

Per i tenant di B2C esistono essenzialmente due modalità di comunicazione con l'API Graph.

* Per le attività interattive da eseguire una sola volta, è consigliabile eseguire le attività con un account amministratore nel tenant di B2C. Questa modalità richiede che un amministratore acceda con le credenziali prima di effettuare chiamate all'API Graph.
* Per le attività automatizzate e continue è consigliabile usare un account del servizio a cui assegnare i privilegi necessari per eseguire le attività di gestione. In Azure AD è possibile ottenere questo risultato registrando un'applicazione ed effettuando l'autenticazione in Azure AD. A questo scopo usare un **ID applicazione** che usa la [concessione delle credenziali client OAuth 2.0](../active-directory/develop/active-directory-authentication-scenarios.md#daemon-or-server-application-to-web-api). In questo caso l'applicazione agisce autonomamente, non come utente, per chiamare l'API Graph.

In questo articolo verrà illustrato come eseguire il caso di uso automatizzato. A scopo dimostrativo, verrà compilato un `B2CGraphClient` .NET 4.5 che esegue operazioni di creazione, lettura, aggiornamento ed eliminazione (CRUD, Create, Read, Update, Delete) di utenti. Il client avrà un'interfaccia della riga di comando di Windows che consente di richiamare diversi metodi. Tuttavia, il codice viene scritto affinché si comporti in modo automatico e non interattivo.

## <a name="get-an-azure-ad-b2c-tenant"></a>Ottenere un tenant di Azure AD B2C
Prima di creare applicazioni o utenti oppure di interagire con Azure AD, saranno necessari un tenant di Azure AD B2C e un account amministratore globale in tale tenant. Se non è già disponibile un tenant vedere l' [introduzione ad Azure AD B2C](active-directory-b2c-get-started.md).

## <a name="register-your-application-in-your-tenant"></a>Registrare l'applicazione nel tenant
Dopo aver creato un tenant B2C, è necessario registrare l'applicazione tramite il [Portale di Azure](https://portal.azure.com).

> [!IMPORTANT]
> Per usare l'API Graph con il tenant B2C, sarà necessario registrare un'applicazione dedicata usando il menu generico *Registrazioni per l'app* nel portale di Azure, **NON** il menu *Applicazioni* di Azure AD B2C. Non è possibile usare nuovamente le applicazioni B2C esistenti che sono state registrate nel menu *Applicazioni* di Azure AD B2C.

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Scegliere il tenant di Azure AD B2C selezionando l'account nell'angolo superiore destro della pagina.
3. Nel riquadro di spostamento a sinistra scegliere **More Services** (Altri servizi), fare clic su **Registrazioni per l'app** e quindi su **Aggiungi**.
4. Seguire le istruzioni e creare una nuova applicazione. 
    1. Selezionare **App Web/API** come Tipo di applicazione.    
    2. Fornire **qualsiasi URI di reindirizzamento** (ad esempio https://B2CGraphAPI) in quanto non è rilevante per questo esempio.  
5. L'applicazione verrà mostrata nell'elenco delle applicazioni, fare clic su di essa per ottenere l'**ID applicazione** (noto anche come ID Client). Copiarlo in quanto sarà necessario in una sezione successiva.
6. Nel menu Impostazioni fare clic su **Chiavi** e aggiungere una nuova chiave (nota anche come segreto client). Copiare anche questa per usarla in una sezione successiva.

## <a name="configure-create-read-and-update-permissions-for-your-application"></a>Configurare le autorizzazioni creare, leggere e aggiornare per l'applicazione
A questo punto è necessario configurare l'applicazione per ottenere tutte le autorizzazioni necessarie per creare, leggere, aggiornare ed eliminare gli utenti.

1. Continuando nel menu Registrazioni dell'app del portale di Azure, selezionare l'applicazione.
2. Nel menu Impostazioni fare clic su **Autorizzazioni necessarie**.
3. Nel menu Autorizzazioni necessarie fare clic su **Windows Azure Active Directory**.
4. Nel menu Abilita accesso selezionare l'autorizzazione **Leggi e scrivi i dati della directory** da **Autorizzazioni dell'applicazione** e fare clic su **Salva**.
5. Infine, nel menu Autorizzazioni necessarie fare clic sul pulsante **Concedi autorizzazioni**.

A questo punto è disponibile un'applicazione con le autorizzazioni per creare, leggere e aggiornare gli utenti dal tenant B2C.

> [!NOTE]
> Il completamento dell'operazione di concessione di autorizzazioni può richiedere alcuni minuti.
> 
> 

## <a name="configure-delete-permissions-for-your-application"></a>Configurare le autorizzazioni di eliminazione per l'applicazione
L'autorizzazione *Legge e scrive i dati della directory* attualmente **NON** include la possibilità di eseguire le eliminazioni, ad esempio l'eliminazione degli utenti. Se si desidera assegnare all'applicazione la possibilità di eliminare gli utenti, è necessario eseguire questi passaggi aggiuntivi che coinvolgono PowerShell. In caso contrario, è possibile passare alla sezione successiva.

Prima di tutto. se non è già installato, installare il [modulo Azure AD PowerShell v1 (MSOnline)](https://docs.microsoft.com/en-us/powershell/azure/active-directory/install-msonlinev1?view=azureadps-1.0):

```powershell
Install-Module MSOnline
```

Dopo aver installato il modulo di PowerShell, connettersi al tenant di Azure AD B2C.

> [!IMPORTANT]
> È necessario usare un account di amministratore tenant B2C che sia **locale** in relazione al tenant B2C. Questi account hanno un aspetto simile a questo: myusername@myb2ctenant.onmicrosoft.com.

```powershell
Connect-MsolService
```

A questo punto si userà l'**ID applicazione** nello script seguente per assegnare all'applicazione il ruolo di amministratore account utente che consentirà di eliminare gli utenti. Questi ruoli hanno identificatori noti, pertanto è sufficiente eseguire l'input dell'**ID applicazione** nello script seguente.

```powershell
$applicationId = "<YOUR_APPLICATION_ID>"
$sp = Get-MsolServicePrincipal -AppPrincipalId $applicationId
Add-MsolRoleMember -RoleObjectId fe930be7-5e62-47db-91af-98c3a49a38b1 -RoleMemberObjectId $sp.ObjectId -RoleMemberType servicePrincipal
```

Ora l'applicazione dispone anche delle autorizzazioni per eliminare gli utenti dal tenant B2C.

## <a name="download-configure-and-build-the-sample-code"></a>Scaricare, configurare e compilare il codice di esempio
Scaricare prima di tutto il codice di esempio ed eseguirlo. Esaminare quindi il codice.  È possibile [scaricare il codice di esempio come file ZIP](https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet/archive/master.zip). È anche possibile clonarlo nella directory desiderata:

```
git clone https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet.git
```

Aprire la soluzione Visual Studio `B2CGraphClient\B2CGraphClient.sln` in Visual Studio. Nel progetto `B2CGraphClient`, aprire il file `App.config`. Sostituire le tre impostazioni dell'app con valori personalizzati:

```
<appSettings>
    <add key="b2c:Tenant" value="{Your Tenant Name}" />
    <add key="b2c:ClientId" value="{The ApplicationID from above}" />
    <add key="b2c:ClientSecret" value="{The Key from above}" />
</appSettings>
```

[!INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

Fare quindi clic con il pulsante destro del mouse sulla soluzione `B2CGraphClient` e ricompilare l'esempio. Se l'operazione riesce, sarà disponibile un file eseguibile `B2C.exe` in `B2CGraphClient\bin\Debug`.

## <a name="build-user-crud-operations-by-using-the-graph-api"></a>Creare operazioni CRUD utente usando l'API Graph
Per usare B2CGraphClient aprire un prompt dei comandi `cmd` di Windows e passare alla directory `Debug`. Eseguire quindi il comando `B2C Help` .

```
> cd B2CGraphClient\bin\Debug
> B2C Help
```

Verrà visualizzata una breve descrizione di ogni comando. Ogni volta che si richiama uno di questi comandi, `B2CGraphClient` invia una richiesta all'API Graph di Azure AD.

### <a name="get-an-access-token"></a>Ottenere un token di accesso
Qualsiasi richiesta per l'API Graph necessita di un token di accesso per l'autenticazione. `B2CGraphClient` usa Active Directory Authentication Library (ADAL) open source per semplificare l'acquisizione di token di accesso. ADAL semplifica l'acquisizione del token fornendo un'API semplice e avendo cura di alcuni dettagli importanti, come la memorizzazione nella cache dei token di accesso. Non è tuttavia necessario usare ADAL per ottenere i token. È anche possibile ottenere i token creando richieste HTTP.

> [!NOTE]
> Questo esempio di codice usa ADAL v2 per comunicare con l'API Graph.  Per ottenere token di accesso utilizzabili con l'API Graph di Azure AD, è necessario usare ADAL versione 2 o 3.
> 
> 

Quando si esegue `B2CGraphClient`, si crea un'istanza della classe `B2CGraphClient`. Il costruttore per questa classe imposta uno scaffolding di autenticazione di ADAL:

```C#
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

Verrà usato come esempio il comando `B2C Get-User` . Quando si richiama `B2C Get-User` senza input aggiuntivi, l’interfaccia della riga di comando chiama il metodo `B2CGraphClient.GetAllUsers(...)`. Questo metodo chiama `B2CGraphClient.SendGraphGetRequest(...)`, che invia una richiesta HTTP GET all'API Graph. Prima di inviare la richiesta GET, `B2CGraphClient.SendGraphGetRequest(...)` ottiene un token di accesso usando ADAL:

```C#
public async Task<string> SendGraphGetRequest(string api, string query)
{
    // First, use ADAL to acquire a token by using the app's identity (the credential)
    // The first parameter is the resource we want an access_token for; in this case, the Graph API.
    AuthenticationResult result = authContext.AcquireToken("https://graph.windows.net", credential);

    ...

```

È possibile ottenere un token di accesso per l'API Graph chiamando il metodo `AuthenticationContext.AcquireToken(...)` di ADAL. ADAL restituisce quindi un valore `access_token` che rappresenta l'identità dell'applicazione.

### <a name="read-users"></a>Leggere gli utenti
Quando si vuole ottenere un elenco di utenti oppure un utente specifico dall'API Graph, è possibile inviare una richiesta HTTP `GET` all'endpoint `/users`. Una richiesta per tutti gli utenti in un tenant ha un aspetto analogo al seguente:

```
GET https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

Per visualizzare questa richiesta, eseguire:

 ```
 > B2C Get-User
 ```

Esistono due aspetti importanti da notare:

* Il token di accesso acquisito tramite ADAL viene aggiunto all'intestazione `Authorization` usando lo schema `Bearer`.
* Per i tenant B2C, è necessario usare il parametro della query `api-version=1.6`.

Entrambi questi dettagli vengono gestiti con il metodo `B2CGraphClient.SendGraphGetRequest(...)` :

```C#
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
Quando si creano account utente nel tenant di B2C, è possibile inviare una richiesta HTTP `POST` all'endpoint `/users`:

```
POST https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 338

{
    // All of these properties are required to create consumer users.

    "accountEnabled": true,
    "signInNames": [                            // controls which identifier the user uses to sign in to the account
        {
            "type": "emailAddress",             // can be 'emailAddress' or 'userName'
            "value": "joeconsumer@gmail.com"
        }
    ],
    "creationType": "LocalAccount",            // always set to 'LocalAccount'
    "displayName": "Joe Consumer",                // a value that can be used for displaying to the end user
    "mailNickname": "joec",                        // an email alias for the user
    "passwordProfile": {
        "password": "P@ssword!",
        "forceChangePasswordNextLogin": false   // always set to false
    },
    "passwordPolicies": "DisablePasswordExpiration"
}
```

La maggior parte delle proprietà di questa richiesta è necessaria per creare utenti consumer. Per altre informazioni, fare clic [qui](https://msdn.microsoft.com/library/azure/ad/graph/api/users-operations#CreateLocalAccountUser). I commenti `//` sono stati inclusi a scopo illustrativo. Non includerli in una richiesta effettiva.

Per visualizzare la richiesta, eseguire uno dei comandi seguenti:

```
> B2C Create-User ..\..\..\usertemplate-email.json
> B2C Create-User ..\..\..\usertemplate-username.json
```

Il comando `Create-User` accetta un file con estensione json come parametro di input. Il file include una rappresentazione JSON di un oggetto utente. Nel codice di esempio sono presenti due file con estensione json: `usertemplate-email.json` e `usertemplate-username.json`. È possibile modificare questi file per adeguarli alle proprie esigenze. Oltre ai campi obbligatori precedenti, questi file includono alcuni campi facoltativi che possono essere usati. Per informazioni dettagliate sui campi facoltativi, vedere le [informazioni di riferimento sull'API Graph di Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity).

Per informazioni sul modo in cui viene costruita la richiesta POST, vedere `B2CGraphClient.SendGraphPostRequest(...)`.

* Collega un token di accesso all'intestazione `Authorization` della richiesta.
* Imposta `api-version=1.6`.
* Include l'oggetto utente JSON nel corpo della richiesta.

> [!NOTE]
> Se gli account di cui si vuole eseguire la migrazione da un archivio utenti esistente hanno un livello di complessità della password inferiore al [livello avanzato di complessità della password applicato da Azure AD B2C](https://msdn.microsoft.com/library/azure/jj943764.aspx), è possibile disabilitare il requisito per la password complessa usando il valore `DisableStrongPassword` nella proprietà `passwordPolicies`. È ad esempio possibile modificare la richiesta di creazione utente indicata in precedenza come segue: `"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"`.
> 
> 

### <a name="update-consumer-user-accounts"></a>Aggiornare gli account utente consumer
Quando si aggiornano gli oggetti utente, il processo è simile a quello per la creazione di oggetti utente, ma questo processo usa il metodo HTTP `PATCH`:

```
PATCH https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 37

{
    "displayName": "Joe Consumer",                // this request updates only the user's displayName
}
```

Provare ad aggiornare un utente aggiornando i file JSON con nuovi dati. È quindi possibile usare `B2CGraphClient` per eseguire uno di questi comandi:

```
> B2C Update-User <user-object-id> ..\..\..\usertemplate-email.json
> B2C Update-User <user-object-id> ..\..\..\usertemplate-username.json
```

Esaminare il metodo `B2CGraphClient.SendGraphPatchRequest(...)` per informazioni dettagliate su come inviare la richiesta.

### <a name="search-users"></a>Cercare gli utenti
È possibile cercare gli utenti nel tenant di B2C in due modi: con l'ID oggetto dell'utente oppure con l'identificatore di accesso dell'utente, ovvero la proprietà `signInNames`.

Eseguire uno dei comandi seguenti per cercare un utente specifico:

```
> B2C Get-User <user-object-id>
> B2C Get-User <filter-query-expression>
```

Ecco alcuni esempi:

```
> B2C Get-User 2bcf1067-90b6-4253-9991-7f16449c2d91
> B2C Get-User $filter=signInNames/any(x:x/value%20eq%20%27joeconsumer@gmail.com%27)
```

### <a name="delete-users"></a>Eliminare gli utenti
Il processo per l'eliminazione di un utente è molto semplice. Usare il metodo HTTP `DELETE` e costruire l'URL con l'ID oggetto corretto:

```
DELETE https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

Per visualizzare un esempio, immettere questo comando e visualizzare la richiesta di eliminazione stampata nella console:

```
> B2C Delete-User <object-id-of-user>
```

Esaminare il metodo `B2CGraphClient.SendGraphDeleteRequest(...)` per informazioni dettagliate su come inviare la richiesta.

È possibile eseguire molte altre azioni con l'API Graph di Azure AD, oltre alla gestione degli utenti. Le [informazioni di riferimento sull'API Graph di Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) illustrano i dettagli di ogni azione, insieme a richieste di esempio.

## <a name="use-custom-attributes"></a>Usare gli attributi personalizzati
La maggior parte delle applicazioni consumer deve archiviare alcune informazioni del profilo utente personalizzate. Per eseguire questa operazione è ad esempio possibile definire un attributo personalizzato nel tenant di B2C. È quindi possibile gestire l'attributo nello stesso modo in cui si gestisce qualsiasi altra proprietà in un oggetto utente. È possibile aggiornare l'attributo, eliminarlo, eseguire una query in base all'attributo, inviarlo come attestazione in un token di accesso e così via.

Per definire un attributo personalizzato nel tenant di B2C, vedere le [informazioni di riferimento sugli attributi personalizzati di B2C](active-directory-b2c-reference-custom-attr.md).

È possibile visualizzare gli attributi personalizzati definiti nel tenant di B2C usando `B2CGraphClient`:

```
> B2C Get-B2C-Application
> B2C Get-Extension-Attribute <object-id-in-the-output-of-the-above-command>
```

L'output di queste funzioni rivela i dettagli di ogni attributo personalizzato, ad esempio:

```JSON
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

È possibile usare il nome completo, ad esempio `extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number`, come proprietà per gli oggetti utente.  Aggiornare il file con estensione json con la nuova proprietà e un valore per la proprietà, quindi eseguire:

```
> B2C Update-User <object-id-of-user> <path-to-json-file>
```

Se si usa `B2CGraphClient`, si ha a disposizione un'applicazione di servizio che può gestire gli utenti del tenant di B2C a livello di codice. `B2CGraphClient` usa la propria identità di applicazione per l'autenticazione all'API Graph di Azure AD. Acquisisce anche i token usando un segreto client. Quando si incorpora questa funzionalità nell'applicazione, tenere presenti alcuni punti chiave per le applicazioni B2C:

* È necessario concedere all'applicazione le autorizzazioni appropriate nel tenant.
* Attualmente è necessario usare ADAL (non MSAL) per ottenere i token di accesso. È anche possibile inviare direttamente messaggi di protocollo, senza usare una raccolta.
* Quando si chiama l'API Graph, usare `api-version=1.6`.
* Quando si creano e aggiornano utenti consumer, sono necessarie alcune proprietà, come illustrato in precedenza.

In caso di domande o richieste relative ad azioni che si vorrebbe eseguire mediante l'API Graph nel tenant di B2C, inserire un commento a questo articolo o inviare una richiesta nel repository di esempi di codice di GitHub.

