<properties
	pageTitle="Anteprima di Azure AD B2C: Utilizzo di API Graph | Microsoft Azure"
	description="Come chiamare l'API Graph per un tenant B2C utilizzando l'identità di un'applicazione per automatizzare il processo."
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
	ms.date="10/08/2015"
	ms.author="dastrock"/>

# Anteprima di Azure AD B2C: Utilizzo di API Graph

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-graph-switcher](../../includes/active-directory-b2c-devquickstarts-graph-switcher.md)]-->

I tenant di Azure AD B2C tendono ad avere dimensioni molto grandi, il che significa che molte attività comuni di gestione dei tenant devono essere eseguite a livello di programmazione. Un esempio primario è la gestione degli utenti. Se è necessario eseguire la migrazione di un archivio utenti già esistente in un tenant B2C o se si desidera ospitare la registrazione degli utenti nella propria pagina, è possibile farlo tramite la creazione di account utente in Azure AD. Questi tipi di attività richiedono la possibilità di creare, leggere, aggiornare ed eliminare gli account utente utilizzando l'API Graph di AD di Azure.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]
	
Per i tenant B2C esistono essenzialmente due modalità di comunicazione con l'API Graph.

- Per le attività interattive da eseguire una sola volta, probabilmente si preferirà eseguire le attività di gestione con un account amministratore nel tenant B2C. In questa modalità è necessario che l’amministratore acceda con le proprie credenziali prima di eseguire le chiamate all'API Graph.
- Per le attività automatizzate e continue, si preferirà eseguire le attività di gestione utilizzando un tipo di account del servizio a cui assegnare i privilegi necessari. In Azure AD,ciò è possibile tramite la registrazione di un'applicazione e l'autenticazione in Azure AD utilizzando "identità applicazione", mediante la [Concessione di credenziali Client OAuth 2.0](active-directory-authentication-scenarios.md#daemon-or-server-application-to-web-api). In questo caso, l’applicazione chiama l'API Graph che agisce come se stesso, anziché come un determinato utente.  

In questo articolo viene illustrato come eseguire il secondo caso di utilizzo automatizzato tramite la creazione di un "B2CGraphClient" .NET 4.5 che esegue le operazioni CRUD utente. Per provare le varie procedure, il client avrà un'interfaccia della riga di comando di Windows che consente di richiamare diversi metodi. Tuttavia, il codice viene scritto affinché si comporti in modo automatico e non interattivo. Di seguito sono riportati i requisiti iniziali.

## Ottenere un tenant di Azure AD B2C

Prima di creare l'applicazione, gli utenti o di interagire con Azure AD, saranno necessari un tenant Azure AD B2C e un account amministratore globale in tale tenant. Per ottenerli, attenersi alla guida in [Introduzione a Azure AD B2C](active-directory-b2c-get-started.md).

## Registrare un'applicazione di servizio nel tenant

Dopo aver creato una directory B2C, è necessario creare l'applicazione di servizio usando i cmdlet di PowerShell per Azure AD. Innanzitutto, scaricare e installare l’[Assistente per l’accesso a Microsoft Online Services](http://go.microsoft.com/fwlink/?LinkID=286152). Quindi è possibile scaricare e installare il [modulo di Azure Active Directory a 64-bit per Windows Powershell](http://go.microsoft.com/fwlink/p/?linkid=236297).

> [AZURE.NOTE]Per usare l'API Graph con il tenant B2C, sarà necessario registrare un'applicazione dedicata con PowerShell, seguendo queste istruzioni. Non è possibile utilizzare nuovamente le applicazioni B2C già esistenti che sono state registrate nel portale di Azure. Si tratta di una limitazione dell'anteprima AD B2C Azure che verrà rimossa in futuro - in quel punto questo articolo verrà aggiornato.

Dopo aver installato il modulo di PowerShell, aprire PowerShell e connettersi al tenant B2C. Dopo aver eseguito `Get-Credential`, verranno richiesti un nome utente e una password. Immetterli nell'account di amministrazione del tenant B2C.

```
> $msolcred = Get-Credential
> Connect-MsolService -credential $msolcred
``` 

Prima di creare l'applicazione, è necessario generare un nuovo “segreto client”. L'applicazione utilizzerà il segreto client per l'autenticazione in Azure AD e per acquisire i token di accesso. È possibile generare un segreto valido privato in Powershell:

```
> $bytes = New-Object Byte[] 32
> $rand = [System.Security.Cryptography.RandomNumberGenerator]::Create()
> $rand.GetBytes($bytes)
> $rand.Dispose()
> $newClientSecret = [System.Convert]::ToBase64String($bytes)
> $newClientSecret
``` 

Il comando finale precedente stampa il nuovo segreto client. Copiarlo in un luogo sicuro, poiché sarà necessario a breve. È ora possibile creare l'applicazione, fornendo il nuovo segreto client come credenziale per l'app:

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

Se la creazione dell'applicazione ha esito positivo, essa stampa alcune proprietà dell'applicazione come quelle precedenti. Sono necessarie `ObjectId` e `AppPrincipalId`, quindi copiare anche questi valori.

Dopo aver creato un'applicazione nel tenant B2C, è necessario assegnarle le autorizzazioni necessarie per eseguire le operazioni CRUD (Create, Read, Udate, Delete) per gli utenti. È necessario assegnare all'applicazione tre diversi ruoli: Directory Readers (per la lettura degli utenti), Directory Writers (per la creazione e l’aggiornamento degli utenti) e User Account Administrator (per l'eliminazione degli utenti). Questi ruoli dispongono di identificatori noti, pertanto è possibile eseguire i comandi seguenti, sostituendo il parametro `-RoleMemberObjectId` con `ObjectId` di cui sopra. Per visualizzare l'elenco di tutti i ruoli della directory, provare a eseguire `Get-MsolRole`.

```
> Add-MsolRoleMember -RoleObjectId 88d8e3e3-8f55-4a1e-953a-9b9898b8876b -RoleMemberObjectId <Your-ObjectId> -RoleMemberType servicePrincipal
> Add-MsolRoleMember -RoleObjectId 9360feb5-f418-4baa-8175-e2a00bac4301 -RoleMemberObjectId <Your-ObjectId> -RoleMemberType servicePrincipal
> Add-MsolRoleMember -RoleObjectId fe930be7-5e62-47db-91af-98c3a49a38b1 -RoleMemberObjectId <Your-ObjectId> -RoleMemberType servicePrincipal
```  

A questo punto è disponibile un'applicazione con le autorizzazioni per creare, leggere, aggiornare ed eliminare gli utenti dal tenant B2C. Di seguito è possibile trovare il codice che la utilizza.

## Scaricare, configurare e compilare il codice di esempio

In primo luogo, scaricare il codice di esempio e eseguirlo. Quindi, osservare ciò che accade dietro le quinte. È possibile [scaricare il codice di esempio come file zip](https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet/archive/master.zip) o clonarlo in una directory di propria scelta:

```
git clone https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet.git
```

Aprire la soluzione Visual Studio `B2CGraphClient\B2CGraphClient.sln` in Visual Studio. Nel progetto `B2CGraphClient`, aprire il file `App.config`. Sostituire le tre impostazioni dell'app con valori personalizzati, come illustrato di seguito:

```
<appSettings>
    <add key="b2c:Tenant" value="contosob2c.onmicrosoft.com" />
    <add key="b2c:ClientId" value="{The AppPrincipalId from above}" />
    <add key="b2c:ClientSecret" value="{The client secret you generated above}" />
</appSettings>
```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

A questo punto, fare clic sulla soluzione `B2CGraphClient` e ricompilare l’esempio. Se l’operazione ha esito positivo, ci sarà un file eseguibile `B2C.exe` in `B2CGraphClient\bin\Debug`.

## CRUD utente con API Graph

Per utilizzare B2CGraphClient, aprire un prompt dei comandi cmd di Windows e cd nella directory `Debug`. Eseguire quindi il comando `B2C Help`.

```
> cd B2CGraphClient\bin\Debug
> B2C Help
```

Verrà visualizzata una breve descrizione di ogni comando. Quando si richiama uno di questi comandi, B2CGraphClient invierà una richiesta per all’API Graph di Azure AD.

### Recupero di un token di accesso

Qualsiasi richiesta all'API Graph richiederà un token di accesso per l'autenticazione. B2CGraphClient utilizza l'open source Active Directory Authentication Library (ADAL) per acquisire i token di accesso. Certamente non sarà necessario usare ADAL per ottenere i token. E’ possibile ottenere i token creando manualmente le richieste HTTP. ADAL semplifica l’acquisizione del token fornendo un'API semplice e avendo cura di alcuni dettagli importanti, come la memorizzazione nella cache dei token di accesso.

> [AZURE.NOTE]Questo esempio di codice utilizza intenzionalmente ADAL v2, la versione di ADAL generalmente disponibile. L’esempio di codice NON utilizza ADAL v4, ovvero una versione di anteprima progettata per l'utilizzo con Azure AD B2C. Per l'anteprima di Azure AD B2C, è necessario utilizzare ADAL v2 per comunicare con l'API Graph. In futuro, si abiliterà l’accesso API Graph con ADAL v4, in modo che non sia necessario utilizzare due diverse versioni di ADAL nella soluzione completa Azure AD B2C.

Quando si esegue B2CGraphClient, si crea un'istanza di classe `B2CGraphClient`. Il costruttore per questa classe imposta lo scaffolding di autenticazione di ADAL:

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
	// provided to Azure AD in order to receive an access_token using the app's identity.
	this.credential = new ClientCredential(clientId, clientSecret);
}
```

Utilizzare il comando `B2C Get-User` come esempio. Quando si richiama `Get-User` senza input aggiuntivi, l’interfaccia della riga di comando chiama il metodo `B2CGraphClient.GetAllUsers(...)`. Questo metodo chiama `B2CGraphClient.SendGraphGetRequest(...)`, che invia una richiesta HTTP GET all'API Graph. Prima di inviare la richiesta GET, esso ottiene un token di accesso utilizza ADAL:

```C#
public async Task<string> SendGraphGetRequest(string api, string query)
{
	// First, use ADAL to acquire a token using the app's identity (the credential)
	// The first parameter is the resource we want an access_token for; in this case, the Graph API.
	AuthenticationResult result = authContext.AcquireToken("https://graph.windows.net", credential);

	... 

```

Come si può notare, è possibile ottenere un token di accesso per l'API Graph tramite il metodo di chiamata di ADAL `AuthenticationContext.AcquireToken(...)`. ADAL restituirà un token di accesso che rappresenta l'identità dell'applicazione.

### Lettura degli utenti

Quando si desidera ottenere un elenco degli utenti da API Graph o ottenere un particolare utente, è possibile inviare una richiesta HTTP GET all’endpoint `/users`. Una richiesta per tutti gli utenti in un tenant avrà il seguente aspetto:

```
GET https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=beta
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```  

Per vedere la richiesta in azione, provare a eseguire:

 ```
 > B2C Get-User
 ```
 
Esistono due aspetti importanti da notare qui:

- Il token di accesso acquisito tramite ADAL è stato aggiunto all’intestazione `Authorization` utilizzando lo schema `Bearer`.
- Per i tenant B2C, è necessario usare il parametro della query `api-version=beta`.


> [AZURE.NOTE]La versione beta dell’API Graph di Azure AD fornisce funzionalità di anteprima. Consultare [questo post del blog del team di API Graph](http://blogs.msdn.com/b/aadgraphteam/archive/2015/04/10/graph-api-versioning-and-the-new-beta-version.aspx) per ulteriori informazioni sulla versione beta.

Entrambi questi dettagli vengono gestiti con il metodo `B2CGraphClient.SendGraphGetRequest(...)`:

```C#
public async Task<string> SendGraphGetRequest(string api, string query)
{
	...
	
	// For B2C user managment, be sure to use the beta Graph API version.
	HttpClient http = new HttpClient();
	string url = "https://graph.windows.net/" + tenant + api + "?" + "api-version=beta";
	if (!string.IsNullOrEmpty(query))
	{
		url += "&" + query;
	}
	
	// Append the access token for the Graph API to the Authorization header of the request, using the Bearer scheme.
	HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get, url);
	request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
	HttpResponseMessage response = await http.SendAsync(request);
	
	... 
```
		
### Creazione di account utente consumer 

Quando si creano gli account utente nel tenant B2C, è possibile inviare una richiesta HTTP POST all'endpoint `/users`:

```
POST https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=beta
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 338

{
	// These properties are all required for creating consumer users.
	 
	"accountEnabled": true,
	"alternativeSignInNamesInfo": [             // controls what identifier the user uses to sign into their account
		{
			"type": "emailAddress",             // can be 'emailAddress' or 'userName'
			"value": "joeconsumer@gmail.com"
		}
	],
	"creationType": "NameCoexistence",          // always set to 'NameCoexistence'
	"displayName": "Joe Consumer",				// a value that can be used for diplaying to the end-user
	"mailNickname": "joec",						// a mail alias for the user
	"passwordProfile": {
		"password": "P@ssword!",
		"forceChangePasswordNextLogin": false   // always set to false
	},
	"passwordPolicies": "DisablePasswordExpiration"
}
```

Tutte le proprietà incluse nella richiesta precedente sono necessarie per la creazione di utenti consumer. I commenti `//` stati inclusi a scopo illustrativo e non devono essere inclusi in una richiesta effettiva.

Per vedere la richiesta in azione, provare a eseguire uno dei seguenti comandi:

```
> B2C Create-User ..\..\..\usertemplate-email.json
> B2C Create-User ..\..\..\usertemplate-username.json
```

Il comando `Create-User` accetta un file `.json` come parametro di input, che contiene una rappresentazione JSON di un oggetto utente. Sono disponibili due file di esempio `.json` inclusi nel codice di esempio `usertemplate-email.json` e `usertemplate-username.json` che è possibile modificare in base alle esigenze. Oltre ai campi obbligatori precedenti, esistono numerosi campi facoltativi inclusi in questi file che è possibile utilizzare. Sono disponibili informazioni dettagliate su questi campi aggiuntivi nel [riferimento all'entità di API Graph di Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#UserEntity).

E’ possibile osservare il modo in cui questa richiesta POST è costruita in `B2CGraphClient.SendGraphPostRequest(...)`, che:

- Collega un token di accesso all’intestazione `Authorization` della richiesta.
- Operazioni Set `api-version=beta`.
- Include l'oggetto utente JSON nel corpo della richiesta.

### Aggiornamento degli account utente consumer

L'aggiornamento degli oggetti utente è molto simile alla creazione di oggetti utente, ma utilizza il verbo HTTP PATCH:

```
PATCH https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=beta
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 37

{
	"displayName": "Joe Consumer",				// this request only updates the user's displayName
}
```

È possibile provare ad eseguire l'aggiornamento di un utente aggiornando i file JSON con i nuovi dati, e utilizzando B2CGraphClient per eseguire uno dei seguenti comandi:

```
> B2C Update-User <user-object-id> ..\..\..\usertemplate-email.json
> B2C Update-User <user-object-id> ..\..\..\usertemplate-username.json
```
	
Esaminare il metodo `B2CGraphClient.SendGraphPatchRequest(...)` per informazioni dettagliate su come inviare la richiesta.

### Eliminazione di utenti

L'eliminazione di un utente è semplice: è sufficiente utilizzare il verbo HTTP DELETE e costruire l'URL con l'ID oggetto corretto:

```
DELETE https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=beta
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

Per vedere un esempio, provare il comando seguente e vedere la richiesta DELETE risultante stampata nella console:

```
> B2C Delete-User <object-id-of-user>
```

Esaminare il metodo `B2CGraphClient.SendGraphDeleteRequest(...)` per informazioni dettagliate su come inviare la richiesta.

Esistono molte altre azioni che è possibile eseguire con l'API Graph di Azure AD oltre alla gestione degli utenti. Il [riferimento all'API Graph di Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) fornisce i dettagli di ogni azione, insieme a richieste di esempio.


## Utilizzo di attributi personalizzati

Quasi tutte le applicazioni consumer devono archiviare un tipo di informazioni del profilo utente personalizzate. Per eseguire questa operazione è possibile definire un attributo personalizzato nel tenant B2C, che consenta di gestire tale attributo come qualsiasi altra proprietà su un oggetto utente. È possibile aggiornare l'attributo, eliminarlo, eseguire una query tramite esso, inviarlo come attestazione in un token di accesso e così via.

Per definire un attributo personalizzato nel tenant B2C, vedere le [informazioni di riferimento sugli attributi personalizzati nell'anteprima di B2C](active-directory-b2c-reference-custom-attr.md).

È possibile visualizzare gli attributi personalizzati definiti nel tenant B2C tramite B2CGraphClient:

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

È possibile utilizzare il nome completo, ad esempio `extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number` come proprietà per gli oggetti utente. Aggiornare semplicemente il file `.json` con la nuova proprietà, un valore per la proprietà ed eseguire:

```
> B2C Update-User <object-id-of-user> <path-to-json-file>
```

L'operazione è terminata. Con B2CGraphClient, ora si dispone di un'applicazione di servizio in grado di gestire gli utenti del tenant B2C a livello di programmazione. Esso utilizza la propria identità di applicazione per l'autenticazione nell'API Graph di Azure AD e acquisisce i token utilizzando un segreto client. Quando si incorpora questa funzionalità nell'applicazione, tenere presenti alcuni punti chiave per le applicazioni B2C:

- È necessario concedere all'applicazione le autorizzazioni appropriate nel tenant
- Per ora, è necessario utilizzare ADAL v2 per ottenere i token di accesso (o è possibile inviare messaggi di protocollo direttamente, senza una libreria)
- Quando si chiama l'API Graph, utilizzare [`api-version=beta`](http://blogs.msdn.com/b/aadgraphteam/archive/2015/04/10/graph-api-versioning-and-the-new-beta-version.aspx).
- Durante la creazione e l’aggiornamento degli utenti consumer, esistono alcune proprietà necessarie, descritte in precedenza.

> [AZURE.IMPORTANT]Sarà necessario tenere conto delle caratteristiche di replica del servizio directory sottostante ad Azure AD B2C (leggere [questo](http://blogs.technet.com/b/ad/archive/2014/09/02/azure-ad-under-the-hood-of-our-geo-redundant-highly-available-geo-distributed-cloud-directory.aspx) articolo per altre informazioni) quando si usa l'API Graph di Azure AD nell'app B2C. Dopo che un utente effettua l'iscrizione per l'app B2C tramite criteri di **iscrizione**, se si tenta immediatamente di leggere l'oggetto utente tramite l'API Graph di Azure AD nell'app, potrebbe non essere disponibile. Sarà necessario attendere alcuni secondi per il completamento del processo di replica. È prevista in futuro la pubblicazione di linee guida più concrete sulla "garanzia di coerenza di lettura/scrittura" fornita dall'API Graph di Azure AD e il servizio directory, al momento della disponibilità del servizio a livello generale.

Per domande o richieste sulle azioni che si desidera eseguire con l'API Graph sul tenant B2C, invitiamo gli utenti a contattarci. Lasciare un commento nell'articolo o aggiungere un post nel repository GitHub del codice di esempio.

<!---HONumber=Nov15_HO3-->