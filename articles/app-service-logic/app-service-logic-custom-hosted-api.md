<properties 
	pageTitle="Chiamare un'API personalizzata nelle app per la logica" 
	description="Uso dell'API personalizzata ospitata nel servizio app con App per la logica" 
	authors="stepsic-microsoft-com" 
	manager="dwrede" 
	editor="" 
	services="app-service\logic" 
	documentationCenter=""/>

<tags ms.service="app-service-logic" ms.workload="integration" ms.tgt_pltfrm="na" ms.devlang="na"
	
	ms.topic="article"
	ms.date="10/07/2015"
	ms.author="stepsic"/>
	
# Uso dell'API personalizzata ospitata nel servizio app con App per la logica

Anche se la funzionalità App per la logica include un set completo di oltre 40 connettori, è possibile chiamare l'API personalizzata che può eseguire il codice personalizzato. Uno dei modi più semplici e scalabili per ospitare un'API Web personalizzata consiste nell'usare il servizio app. Questo articolo illustra come chiamare un'API Web ospitata in un'app Web del servizio app.

## Distribuire l'app Web

Prima di tutto è necessario distribuire l'API come un'app Web nel servizio app. Queste istruzioni riguardano la distribuzione di base: [Creare un'app Web ASP.NET](web-sites-dotnet-get-started.md).

Assicurarsi di ottenere l'**URL** dell'app Web, visualizzato in **Informazioni di base** nella parte superiore dell'app Web.

## Chiamata all'API

Iniziare dalla creazione di una nuova app per la logica vuota. Dopo aver creato un'app per la logica vuota, fare clic su **Modifica** o **Trigger e azioni** e selezionare **Crea da zero**.

Prima di tutto è opportuno usare un trigger di ricorrenza oppure fare clic su **Esegui app per la logica manualmente**. Si eseguirà quindi la chiamata all'API effettiva. A questo scopo, fare clic sull'azione **HTTP** verde sul lato destro.

1. Scegliere il **Metodo**, che sarà definito nel codice dell'API.
2. Nella sezione **URL** incollare l'**URL** dell'app Web distribuita.
3. Se sono richieste eventuali **Intestazioni**, includerle nel formato JSON simile al seguente: `{"Content-type" : "application/json", "Accept" : "application/json" }`
4. Se l'API è pubblica, lasciare vuoto il campo **Autenticazione**. Se si vuole proteggere le chiamate all'API, vedere le sezioni seguenti.
5. Infine includere il **Corpo** della domanda definita nell'API.

Fare clic su **Salva** nella barra dei comandi. Se si fa clic su **Esegui adesso**, dovrebbe essere visualizzata la chiamata all'API e la risposta nell'elenco di esecuzione.

Ciò funziona in modo ottimale se l'API è pubblica, ma se si vuole proteggere l'API, ci sono un paio di modi diversi per eseguire questa operazione:

1. *Non è richiesta alcuna modifica del codice*: per proteggere l'API è possibile usare Azure Active Directory senza richiedere modifiche del codice o la ridistribuzione.
2. Applicare l'autenticazione di base, l'autenticazione AAD o l'autenticazione del certificato nel codice dell'API. 

## Protezione delle chiamate all'API senza modifica del codice 

In questa sezione si creeranno due applicazioni di Azure Active Directory, una per l'app per la logica e una per l'app Web. Si autenticheranno le chiamate all'app Web usando l'entità servizio (ID client e segreto) associata all'applicazione AAD per l'app per la logica. Infine si includerà l'ID applicazione nella definizione di app per la logica.

### Parte 1: Configurazione dell'identità applicazione per l'app per la logica

Ecco ciò che userà l'app per la logica per autenticarsi con Active Directory. È *necessario* eseguire questa operazione una sola volta per la directory. Ad esempio, si può scegliere di usare la stessa identità per tutte le app per la logica, anche se è possibile crearne una univoca per ogni app per la logica, se necessario. A questo scopo, è possibile usare l'interfaccia utente oppure PowerShell.

#### Creare l'identità dell'applicazione tramite il portale di Azure

1. Passare ad [Active Directory nel portale di Azure](https://manage.windowsazure.com/#Workspaces/ActiveDirectoryExtension/directory) e selezionare la directory usata per l'app Web.
2. Fare clic sulla scheda **Applicazioni**.
3. In fondo alla pagina fare clic su **Aggiungi** nella barra dei comandi.
4. Assegnare un Nome all'identità e fare clic sulla freccia Avanti.
5. Nelle due caselle di testo inserire una stringa univoca formattata come dominio e fare clic sul segno di spunta.
6. Fare clic sulla scheda **Configura** per questa applicazione.
7. Copiare l'**ID client** che verrà usato nell'app per la logica.
8. Nella sezione **Chiavi** fare clic su **Seleziona durata** e scegliere 1 o 2 anni.
9. Scegliere il pulsante **Salva** nella parte inferiore dello schermo. Potrebbe essere necessario attendere alcuni secondi.
10. Ora assicurarsi di copiare la chiave nella casella. Verrà usata anche nell'app per la logica.

#### Creare l'identità dell'applicazione tramite PowerShell
1. `Switch-AzureMode AzureResourceManager`
2. `Add-AzureAccount`
3. `New-AzureADApplication -DisplayName "MyLogicAppID" -HomePage "http://someranddomain.tld" -IdentifierUris "http://someranddomain.tld" -Password "Pass@word1!"`
4. Assicurarsi di copiare i valori di **ID tenant**, **ID applicazione** e la password usati.

### Parte 2: Proteggere l'app Web con un'identità dell'app AAD

Se l'app Web è già stata distribuita, si può semplicemente abilitarla nel portale. In caso contrario, si può fare in modo che l'autorizzazione venga abilitata come parte della distribuzione di Gestione risorse di Azure.

#### Abilitare l'autorizzazione nel portale di Azure

1. Passare all'app Web e fare clic su **Impostazioni** nella barra dei comandi.
2. Fare clic su **Autorizzazione/Autenticazione**. 
3. Impostare su **Attivata**.

A questo punto verrà creata automaticamente un'applicazione. L'ID client di questa applicazione sarà necessario nella parte 3, quindi si dovrà:

1. Passare ad [Active Directory nel portale di Azure](https://manage.windowsazure.com/#Workspaces/ActiveDirectoryExtension/directory) e selezionare la directory. 
2. Cercare l'app nella casella di ricerca.
3. Fare clic sull'app nell'elenco.
4. Fare clic sulla scheda **Configura**.
5. Dovrebbe essere visualizzato l'**ID client**.

#### Distribuzione dell'app Web con un modello ARM

Prima di tutto, è necessario creare un'applicazione per l'app Web. Dovrà essere diversa dall'applicazione usata per l'app per la logica. Iniziare seguendo i passaggi nella parte 1 precedente, ma ora per **HomePage** e **IdentifierUris** usare l'https://**URL** effettivo dell'app Web.

>[AZURE.NOTE]Quando si crea l'applicazione per l'app Web, è necessario usare l'[approccio tramite il portale di Azure](https://manage.windowsazure.com/#Workspaces/ActiveDirectoryExtension/directory), perché il cmdlet di PowerShell non configura le autorizzazioni necessarie per l'accesso degli utenti a un sito Web.

Una volta che si avranno l'ID client e l'ID tenant, includere il codice seguente come risorsa secondaria dell'app Web nel modello di distribuzione:

```
"resources" : [
	{
		"apiVersion" : "2015-08-01",
		"name" : "web",
		"type" : "config",
		"dependsOn" : [
			"[concat('Microsoft.Web/sites/','parameters('webAppName'))]"
		],
		"properties" : {
			"siteAuthEnabled": true,
			"siteAuthSettings": {
			  "clientId": "<<clientID>>",
			  "issuer": "https://sts.windows.net/<<tenantID>>/",
			}
		}
	}
]
```

Per eseguire automaticamente la distribuzione di un'app Web vuota e un'app per la logica insieme che usano AAD, fare clic sul pulsante seguente: [![Distribuzione in Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-logic-app-custom-api%2Fazuredeploy.json)

Per un modello completo, vedere [Chiamate dell'app per la logica un'API personalizzata ospitata in un servizio app e protetta da AAD](https://github.com/Azure/azure-quickstart-templates/blob/master/201-logic-app-custom-api/azuredeploy.json).


### Parte 3: Popolare la sezione Autorizzazione nell'app per la logica

Nella sezione **Autorizzazione** dell'azione **HTTP**: `{"tenant":"<<tenantId>>", "audience":"<<clientID from Part 2>>", "clientId":"<<clientID from Part 1>>","secret": "<<Password or Key from Part 1>>","type":"ActiveDirectoryOAuth" }`

| Elemento | Descrizione |
|---------|-------------|
| type | Tipo di autenticazione. Per l'autenticazione ActiveDirectoryOAuth, il valore è ActiveDirectoryOAuth. |
| tenant | Identificativo del tenant usato per identificare il tenant di Active Directory. |
| audience | Obbligatorio. Risorsa a cui ci si connette. |
| clientID | Identificativo del client per l'applicazione Azure AD. |
| secret | Obbligatorio. Segreto del client che richiede il token. | 

Questa impostazione è già configurata nel modello precedente, ma se si crea direttamente l'app per la logica, sarà necessario includere l'intera sezione relativa all'autorizzazione.

## Protezione dell'API nel codice

### Autenticazione del certificato

È possibile usare i certificati client per convalidare le richieste in ingresso all'app Web. Per informazioni su come configurare il codice, vedere [Come configurare l'autenticazione reciproca TLS per un'app Web](app-service-web-configure-tls-mutual-auth.md).

Nella sezione *Autorizzazione* è necessario fornire: `{"type": "clientcertificate","password": "test","pfx": "long-pfx-key"}`.

| Elemento | Descrizione |
|---------|-------------|
| type | Obbligatorio. Tipo di autenticazione. Per i certificati client SSL, il valore deve essere ClientCertificate. |
| pfx | Obbligatorio. I contenuti del file PFX in codifica Base64. |
| password | Obbligatorio. La password per accedere al file PFX. |

### Autenticazione di base

Per convalidare le richieste in ingresso, è possibile usare l'autenticazione di base, ovvero nome utente e password. L'autenticazione di base è un modello comune ed è possibile eseguirla in qualsiasi linguaggio usato per compilare l'app.

Nella sezione *Autorizzazione* è necessario fornire: `{"type": "basic","username": "test","password": "test"}`.

| Elemento | Descrizione |
|---------|-------------|
| type | Obbligatorio. Tipo di autenticazione. Per l'autenticazione di Base, il valore deve essere Basic. |
| username | Obbligatorio. Nome utente da autenticare. |
| password | Obbligatorio. Password da autenticare. |
 
### Gestire l'autorizzazione AAD nel codice

Per impostazione predefinita, l'autenticazione di Azure Active Directory che si abilita nel portale non esegue un'autorizzazione con granularità fine. Ad esempio, non blocca l'API su un utente o un'app specifica, ma solo su un tenant particolare.

Se si vuole limitare l'API solo all'app per la logica, ad esempio nel codice, è possibile estrarre l'intestazione che contiene il JWT e verificare chi è il chiamante, rifiutando le richieste che non corrispondono.

Proseguendo, se si vuole implementarla interamente nel codice e sfruttare la funzionalità del portale, è possibile leggere questo articolo: [Usare Active Directory per l'autenticazione nel servizio app di Azure](web-sites-authentication-authorization.md).

Sarà comunque necessario seguire la procedura precedente per creare un'identità dell'applicazione per l'app per la logica e usarla per chiamare l'API.

<!---HONumber=Oct15_HO3-->