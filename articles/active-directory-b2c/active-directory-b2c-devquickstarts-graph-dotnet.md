<properties
	pageTitle="Anteprima di Azure Active Directory B2C: Usare l'API Graph | Microsoft Azure"
	description="Come chiamare l'API Graph per un tenant di B2C usando l'identità di un'applicazione per automatizzare il processo."
	services="active-directory-b2c"
	documentationCenter=".net"
	authors="dstrockis"
	manager="msmbaldwin"
	editor=""/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="01/21/2016"
	ms.author="dastrock"/>

# Anteprima di Azure AD B2C: Usare l'API Graph


<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-graph-switcher](../../includes/active-directory-b2c-devquickstarts-graph-switcher.md)]-->

I tenant di Azure Active Directory (Azure AD) B2C tendono ad avere dimensioni molto grandi, il che significa che molte attività comuni di gestione dei tenant devono essere eseguite a livello di programmazione, ad esempio la gestione degli utenti. Potrebbe essere necessario eseguire la migrazione di un archivio utenti esistente in un tenant di B2C. È consigliabile ospitare la registrazione degli utenti nella propria pagina e creare account utente in Azure AD in background. Questi tipi di attività richiedono la possibilità di creare, leggere, aggiornare ed eliminare gli account utente. È possibile eseguire queste attività usando l'API Graph di Azure AD.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

Per i tenant di B2C esistono essenzialmente due modalità di comunicazione con l'API Graph.

- Per le attività interattive da eseguire una sola volta, è consigliabile eseguire le attività con un account amministratore nel tenant di B2C. Questa modalità richiede che un amministratore acceda con le credenziali prima di effettuare chiamate all'API Graph.
- Per le attività automatizzate e continue è consigliabile usare un account del servizio a cui assegnare i privilegi necessari per eseguire le attività di gestione. In Azure AD è possibile ottenere questo risultato registrando un'applicazione ed effettuando l'autenticazione in Azure AD. A questo scopo, usare un **ID applicazione** che usa le [credenziali concesse per il client OAuth 2.0](../active-directory/active-directory-authentication-scenarios.md#daemon-or-server-application-to-web-api). In questo caso l'applicazione agisce autonomamente, non come utente, per chiamare l'API Graph.

In questo articolo verrà illustrato come eseguire il caso di uso automatizzato. A scopo dimostrativo, verrà compilato un `B2CGraphClient` .NET 4.5 che esegue operazioni di creazione, lettura, aggiornamento ed eliminazione (CRUD, Create, Read, Update, Delete) di utenti. Il client avrà un'interfaccia della riga di comando di Windows che consente di richiamare diversi metodi. Tuttavia, il codice viene scritto affinché si comporti in modo automatico e non interattivo.

## Ottenere un tenant di Azure AD B2C

Prima di creare applicazioni o utenti oppure di interagire con Azure AD, saranno necessari un tenant di Azure AD B2C e un account amministratore globale in tale tenant. Se non è già disponibile un tenant, vedere l'[introduzione ad Azure AD B2C](active-directory-b2c-get-started.md).

## Registrare un'applicazione di servizio nel tenant

Dopo aver creato un tenant di B2C, è necessario creare l'applicazione di servizio usando i cmdlet di PowerShell per Azure AD. Scaricare e installare prima di tutto l'[Assistente per l’accesso a Microsoft Online Services](http://go.microsoft.com/fwlink/?LinkID=286152). Scaricare e installare quindi il [modulo di Azure Active Directory a 64 bit per Windows PowerShell](http://go.microsoft.com/fwlink/p/?linkid=236297).

> [AZURE.NOTE]
Per usare l'API Graph con il tenant di B2C, sarà necessario registrare un'applicazione dedicata con PowerShell, seguendo le istruzioni disponibili in questo articolo. Non è possibile usare nuovamente le applicazioni B2C già esistenti che sono state registrate nel portale di Azure. Si tratta di una limitazione dell'anteprima di Azure AD B2C che si prevede verrà rimossa in futuro. L'articolo verrà aggiornato dopo la rimozione.

Dopo aver installato il modulo di PowerShell, aprire PowerShell e connettersi al tenant di B2C. Dopo avere eseguito `Get-Credential`, verranno richiesti un nome utente e una password. Immettere il nome utente e la password dell'account amministratore del tenant di B2C.

```
> $msolcred = Get-Credential
> Connect-MsolService -credential $msolcred
```

Prima di creare l'applicazione, è necessario generare un nuovo **segreto client **. L'applicazione userà il segreto client per l'autenticazione in Azure AD e per acquisire i token di accesso. È possibile generare un segreto valido in PowerShell:

```
> $bytes = New-Object Byte[] 32
> $rand = [System.Security.Cryptography.RandomNumberGenerator]::Create()
> $rand.GetBytes($bytes)
> $rand.Dispose()
> $newClientSecret = [System.Convert]::ToBase64String($bytes)
> $newClientSecret
```

Il comando finale stampa il nuovo segreto client. Copiarlo in una posizione sicura. Sarà necessario più avanti. È ora possibile creare l'applicazione, fornendo il nuovo segreto client come credenziale per l'app:

```
> New-MsolServicePrincipal -DisplayName "My New B2C Graph API App" -Type password -Value $newClientSecret

DisplayName           : My New B2C Graph API App
ServicePrincipalNames : {dd02c40f-1325-46c2-a118-4659db8a55d5}
ObjectId              : e2bde258-6691-410b-879c-b1f88d9ef664
AppPrincipalId        : dd02c40f-1325-46c2-a118-4659db8a55d5
TrustedForDelegation  : False
AccountEnabled        : True
Addresses             : {}
KeyType               : Password
KeyId                 : a261e39d-953e-4d6a-8d70-1f915e054ef9
StartDate             : 9/2/2015 1:33:09 AM
EndDate               : 9/2/2016 1:33:09 AM
Usage                 : Verify
```

Se l'applicazione è stata creata, vengono stampate le proprietà dell'applicazione, analoghe alle precedenti. Saranno necessari sia `ObjectId` che `AppPrincipalId`, quindi occorre copiare anche questi valori.

Dopo aver creato un'applicazione nel tenant di B2C, è necessario assegnarle le autorizzazioni necessarie per eseguire le operazioni CRUD (Create, Read, Udate, Delete) per gli utenti. Assegnare tre ruoli all'applicazione, ovvero lettori di directory (per leggere gli utenti), scrittori di directory (per creare e aggiornare gli utenti) e un amministratore account utente (per eliminare gli utenti). Questi ruoli dispongono di identificatori noti, quindi è possibile sostituire il parametro `-RoleMemberObjectId` con il valore `ObjectId` precedente ed eseguire i comandi seguenti. Per visualizzare l'elenco di tutti i ruoli della directory, provare a eseguire `Get-MsolRole`.

```
> Add-MsolRoleMember -RoleObjectId 88d8e3e3-8f55-4a1e-953a-9b9898b8876b -RoleMemberObjectId <Your-ObjectId> -RoleMemberType servicePrincipal
> Add-MsolRoleMember -RoleObjectId 9360feb5-f418-4baa-8175-e2a00bac4301 -RoleMemberObjectId <Your-ObjectId> -RoleMemberType servicePrincipal
> Add-MsolRoleMember -RoleObjectId fe930be7-5e62-47db-91af-98c3a49a38b1 -RoleMemberObjectId <Your-ObjectId> -RoleMemberType servicePrincipal
```

A questo punto è disponibile un'applicazione con le autorizzazioni per creare, leggere, aggiornare ed eliminare gli utenti dal tenant di B2C.

## Scaricare, configurare e compilare il codice di esempio

Scaricare prima di tutto il codice di esempio ed eseguirlo. Esaminare quindi il codice. È possibile [scaricare il codice di esempio come file con estensione zip](https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet/archive/master.zip). È anche possibile clonarlo nella directory desiderata:

```
git clone https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet.git
```

Aprire la soluzione Visual Studio `B2CGraphClient\B2CGraphClient.sln` in Visual Studio. Nel progetto `B2CGraphClient`, aprire il file `App.config`. Sostituire le tre impostazioni dell'app con valori personalizzati:

```
<appSettings>
    <add key="b2c:Tenant" value="contosob2c.onmicrosoft.com" />
    <add key="b2c:ClientId" value="{The AppPrincipalId from above}" />
    <add key="b2c:ClientSecret" value="{The client secret you generated above}" />
</appSettings>
```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

Fare quindi clic sulla soluzione `B2CGraphClient` e ricompilare l'esempio. Se l'operazione ha esito positivo, dovrebbe essere ora disponibile un file eseguibile `B2C.exe` in `B2CGraphClient\bin\Debug`.

## Creare operazioni CRUD utente usando l'API Graph

Per usare B2CGraphClient, aprire un prompt dei comandi `cmd` di Windows e cambiare la directory specificando la directory `Debug`. Eseguire quindi il comando `B2C Help`.

```
> cd B2CGraphClient\bin\Debug
> B2C Help
```

Verrà visualizzata una breve descrizione di ogni comando. Ogni volta che si richiama uno di questi comandi, `B2CGraphClient` invia una richiesta all'API Graph di Azure AD.

### Ottenere un token di accesso

Qualsiasi richiesta per l'API Graph richiede un token di accesso per l'autenticazione. `B2CGraphClient` usa la Active Directory Authentication Library (ADAL) open source per semplificare l'acquisizione di token di accesso. ADAL semplifica l'acquisizione del token fornendo un'API semplice e avendo cura di alcuni dettagli importanti, come la memorizzazione nella cache dei token di accesso. Non è tuttavia necessario usare ADAL per ottenere i token. È anche possibile ottenere i token creando richieste HTTP.

> [AZURE.NOTE]
	Questo esempio di codice usa ADAL v2, la versione di ADAL disponibile a livello generale. L'esempio di codice non usa ADAL v4, ovvero una versione di anteprima da usare con Azure AD B2C. Per l'anteprima di Azure AD B2C, è necessario utilizzare ADAL v2 per comunicare con l'API Graph. In futuro è prevista l'abilitazione dell'accesso all'API Graph con ADAL v4, in modo che non sia necessario usare due versioni di ADAL nella soluzione completa Azure AD B2C.

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

Il comando `B2C Get-User` verrà usato come esempio. Quando si richiama `B2C Get-User` senza input aggiuntivi, l’interfaccia della riga di comando chiama il metodo `B2CGraphClient.GetAllUsers(...)`. Questo metodo chiama `B2CGraphClient.SendGraphGetRequest(...)`, che invia una richiesta HTTP GET all'API Graph. Prima di inviare la richiesta GET, `B2CGraphClient.SendGraphGetRequest(...)` ottiene un token di accesso usando ADAL:

```C#
public async Task<string> SendGraphGetRequest(string api, string query)
{
	// First, use ADAL to acquire a token by using the app's identity (the credential)
	// The first parameter is the resource we want an access_token for; in this case, the Graph API.
	AuthenticationResult result = authContext.AcquireToken("https://graph.windows.net", credential);

	...

```

È possibile ottenere un token di accesso per l'API Graph chiamando il metodo `AuthenticationContext.AcquireToken(...)` di ADAL. ADAL restituisce quindi un valore `access_token` che rappresenta l'identità dell'applicazione.

### Leggere gli utenti

Quando si vuole ottenere un elenco di utenti oppure un utente specifico dall'API Graph, è possibile inviare una richiesta HTTP `GET` all'endpoint `/users`. Una richiesta per tutti gli utenti in un tenant ha un aspetto analogo al seguente:

```
GET https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=beta
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

Per visualizzare questa richiesta, eseguire:

 ```
 > B2C Get-User
 ```

Esistono due aspetti importanti da notare:

- Il token di accesso acquisito tramite ADAL viene aggiunto all'intestazione `Authorization` mediante lo schema `Bearer`.
- Per i tenant B2C, è necessario usare il parametro della query `api-version=beta`.


> [AZURE.NOTE]
	La versione beta dell’API Graph di Azure AD fornisce funzionalità di anteprima. Vedere [questo post del blog del team di API Graph](http://blogs.msdn.com/b/aadgraphteam/archive/2015/04/10/graph-api-versioning-and-the-new-beta-version.aspx) per altre informazioni sulla versione beta.

Entrambi questi dettagli vengono gestiti con il metodo `B2CGraphClient.SendGraphGetRequest(...)`:

```C#
public async Task<string> SendGraphGetRequest(string api, string query)
{
	...

	// For B2C user management, be sure to use the beta Graph API version.
	HttpClient http = new HttpClient();
	string url = "https://graph.windows.net/" + tenant + api + "?" + "api-version=beta";
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

### Creare account utente consumer

Quando si creano gli account utente nel tenant di B2C, è possibile inviare una richiesta HTTP `POST` all'endpoint `/users`:

```
POST https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=beta
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 338

{
	// All of these properties are required to create consumer users.

	"accountEnabled": true,
	"alternativeSignInNamesInfo": [             // controls which identifier the user uses to sign in to the account
		{
			"type": "emailAddress",             // can be 'emailAddress' or 'userName'
			"value": "joeconsumer@gmail.com"
		}
	],
	"creationType": "NameCoexistence",          // always set to 'NameCoexistence'
	"displayName": "Joe Consumer",				// a value that can be used for displaying to the end user
	"mailNickname": "joec",						// an email alias for the user
	"passwordProfile": {
		"password": "P@ssword!",
		"forceChangePasswordNextLogin": false   // always set to false
	},
	"passwordPolicies": "DisablePasswordExpiration"
}
```

Tutte le proprietà di questa richiesta sono necessarie per creare utenti consumer. I commenti `//` sono stati inclusi a scopo illustrativo. Non includerli in una richiesta effettiva.

Per visualizzare la richiesta, eseguire uno dei comandi seguenti:

```
> B2C Create-User ..\..\..\usertemplate-email.json
> B2C Create-User ..\..\..\usertemplate-username.json
```

Il comando `Create-User` accetta un file con estensione json come parametro di input. Il file include una rappresentazione JSON di un oggetto utente. Nel codice di esempio sono presenti due file con estensione json: `usertemplate-email.json` e `usertemplate-username.json`. È possibile modificare questi file per adeguarli alle proprie esigenze. Oltre ai campi obbligatori precedenti, questi file includono alcuni campi facoltativi che possono essere usati. Per informazioni dettagliate sui campi facoltativi, vedere il [riferimento alle entità dell'API Graph di Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#UserEntity).

Per informazioni sul modo in cui viene costruita la richiesta POST, vedere `B2CGraphClient.SendGraphPostRequest(...)`.

- Collega un token di accesso all'intestazione `Authorization` della richiesta.
- Configura `api-version=beta`.
- Include l'oggetto utente JSON nel corpo della richiesta.

### Aggiornare gli account utente consumer

Quando si aggiornano gli oggetti utente, il processo è simile a quello per la creazione di oggetti utente, ma questo processo usa il metodo HTTP `PATCH`:

```
PATCH https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=beta
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 37

{
	"displayName": "Joe Consumer",				// this request updates only the user's displayName
}
```

Provare ad aggiornare un utente aggiornando i file JSON con nuovi dati. È quindi possibile usare `B2CGraphClient` per eseguire uno di questi comandi:

```
> B2C Update-User <user-object-id> ..\..\..\usertemplate-email.json
> B2C Update-User <user-object-id> ..\..\..\usertemplate-username.json
```

Esaminare il metodo `B2CGraphClient.SendGraphPatchRequest(...)` per informazioni dettagliate su come inviare la richiesta.

### Eliminare gli utenti

Il processo per l'eliminazione di un utente è molto semplice. Usare il metodo HTTP `DELETE` e costruire l'URL con l'ID oggetto corretto:

```
DELETE https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=beta
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

Per visualizzare un esempio, immettere questo comando e visualizzare la richiesta di eliminazione stampata nella console:

```
> B2C Delete-User <object-id-of-user>
```

Esaminare il metodo `B2CGraphClient.SendGraphDeleteRequest(...)` per informazioni dettagliate su come inviare la richiesta.

È possibile eseguire molte altre azioni con l'API Graph di Azure AD, oltre alla gestione degli utenti. Il [riferimento all'API Graph di Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) fornisce i dettagli di ogni azione, insieme a richieste di esempio.

## Usare gli attributi personalizzati

La maggior parte delle applicazioni consumer deve archiviare alcune informazioni del profilo utente personalizzate. Per eseguire questa operazione è ad esempio possibile definire un attributo personalizzato nel tenant di B2C. È quindi possibile gestire l'attributo nello stesso modo in cui si gestisce qualsiasi altra proprietà in un oggetto utente. È possibile aggiornare l'attributo, eliminarlo, eseguire una query in base all'attributo, inviarlo come attestazione in un token di accesso e così via.

Per definire un attributo personalizzato nel tenant di B2C, vedere le [informazioni di riferimento sugli attributi personalizzati nell'anteprima di B2C](active-directory-b2c-reference-custom-attr.md).

È possibile visualizzare gli attributi personalizzati definiti nel tenant di B2C tramite `B2CGraphClient`:

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

È possibile usare il nome completo, ad esempio `extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number` come proprietà per gli oggetti utente. Aggiornare il file con estensione json con la nuova proprietà e un valore per la proprietà, quindi eseguire:

```
> B2C Update-User <object-id-of-user> <path-to-json-file>
```

Se si usa `B2CGraphClient`, si ha a disposizione un'applicazione di servizio che può gestire gli utenti del tenant di B2C a livello di codice. `B2CGraphClient` usa la propria identità di applicazione per l'autenticazione all'API Graph di Azure AD. Acquisisce anche i token usando un segreto client. Quando si incorpora questa funzionalità nell'applicazione, tenere presenti alcuni punti chiave per le applicazioni B2C:

- È necessario concedere all'applicazione le autorizzazioni appropriate nel tenant.
- Attualmente è necessario usare ADAL v2 per ottenere i token di accesso. È anche possibile inviare direttamente messaggi di protocollo, senza usare una raccolta.
- Quando si chiama l'API Graph, usare [`api-version=beta`](http://blogs.msdn.com/b/aadgraphteam/archive/2015/04/10/graph-api-versioning-and-the-new-beta-version.aspx).
- Quando si creano e aggiornano utenti consumer, sono necessarie alcune proprietà, come illustrato in precedenza.

> [AZURE.IMPORTANT] Sarà necessario tenere conto delle caratteristiche di replica del servizio directory sottostante ad Azure AD B2C quando si usa l'API Graph di Azure AD nell'app B2C. Per altre informazioni, vedere [questo articolo](http://blogs.technet.com/b/ad/archive/2014/09/02/azure-ad-under-the-hood-of-our-geo-redundant-highly-available-geo-distributed-cloud-directory.aspx). Dopo che l'utente effettua l'iscrizione per l'app B2C usando criteri di **accesso**, se si prova immediatamente a leggere l'oggetto utente usando l'API Graph di Azure AD nell'app, è possibile che non sia disponibile. È necessario attendere alcuni secondi per il completamento del processo di replica. È prevista in futuro la pubblicazione di linee guida più concrete sulla "garanzia di coerenza di lettura/scrittura" fornita dall'API Graph di Azure AD e il servizio directory, al momento della disponibilità del servizio a livello generale.

In caso di domande o richieste relative ad azioni che si vorrebbe eseguire mediante l'API Graph nel tenant di B2C, inserire un commento a questo articolo o inviare una richiesta nel repository di esempi di codice di GitHub.

<!---HONumber=AcomDC_0302_2016-->