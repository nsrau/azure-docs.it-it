<properties 
	pageTitle="Chiamare un'app per le API da un client autenticato" 
	description="Informazioni su come chiamare un'app per le API di Azure da un client di app Web autenticato da Azure Active Directory." 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/26/2015" 
	ms.author="tdykstra"/>

# Chiamare un'app per le API di Azure da un client di app Web autenticato da Azure Active Directory

## Panoramica

Questa esercitazione illustra come chiamare un'app per le API protetta da Azure Active Directory (AAD) da un'app Web protetta a sua volta da AAD. Un'app Web e un'app per le API saranno in esecuzione nella sottoscrizione di Azure al termine dell'esercitazione. L'app Web mostrerà i dati ricevuti tramite la chiamata all'app per le API, come illustrato nella schermata seguente.

![](./media/app-service-api-authentication-client-flow/aboutpage.png)

Verrà illustrato come passare credenziali di AAD dall'app Web all'app per le API, in modo che l'app per le API non richieda di nuovo l'accesso all'utente.

Si eseguirà la procedura seguente:

- Creare un'app per le API e configurarla in modo che usi l'autenticazione AAD.
- Creare un'app Web e configurarla in modo che usi l'autenticazione AAD.
- Aggiungere codice all'app Web in modo da chiamare l'app per le API protetta.
- Distribuire l'app Web e l'app per le API in Azure.
- Eseguire test per verificare che l'app Web riesca a chiamare l'app per le API.

### Autenticazione del flusso client

Il codice aggiunto all'app Web implementerà un processo denominato autenticazione del [flusso client](../app-service/app-service-authentication-overview.md#client-flow). Il diagramma seguente illustra il processo per ottenere il token di accesso di AAD e scambiarlo con un token di app per le API (Zumo).

![](./media/app-service-api-authentication-client-flow/clientflow.png)

Se non si ha familiarità con il ruolo del gateway dell'app per le API nell'autenticazione delle app per le API, vedere [Autenticazione per le app per le API e per dispositivi mobili](../app-service/app-service-authentication-overview.md).

## Prerequisiti

Prima di iniziare l'esercitazione, assicurarsi che [Visual Studio 2015](https://www.visualstudio.com/) e [Azure SDK per .NET](http://go.microsoft.com/fwlink/?linkid=518003) siano installati. Le stesse istruzioni sono applicabili anche a Visual Studio 2013.

L'esercitazione presuppone che si sia in grado di usare progetti Web in Visual Studio.

## Creare e proteggere un'app per le API con AAD

1. Creare o scaricare un progetto app per le API.
 
	* Per creare un progetto, seguire le istruzioni disponibili in [Creare un'app per le API](app-service-dotnet-create-api-app.md).

	* Per scaricare un progetto, vedere nel [repository ContactsList di GitHub](https://github.com/tdykstra/ContactsList).

	È ora disponibile un progetto API Web che restituisce dati di contatto.

	![](./media/app-service-api-authentication-client-flow/swaggerlocal.png)

2. Distribuire il progetto app per le API in una nuova app per le API in Azure.

	Seguire le istruzioni disponibili in [Distribuire un'app per le API](app-service-dotnet-deploy-api-app.md).

	Nel [portale di anteprima di Azure] il pannello **Definizione dell'API** per l'app per le API mostra ora i metodi Get e Post del progetto API Web distribuito.

	![](./media/app-service-api-authentication-client-flow/apiappinportal.png)

4. Proteggere l'app per le API usando Azure Active Directory (AAD) come provider di identità.
 
	Seguire le istruzioni per AAD disponibili in [Proteggere un'app per le API](app-service-api-dotnet-add-authentication.md). Non è necessario seguire le istruzioni della sezione **Usare Postman per inviare una richiesta Post** dell'esercitazione.

	Al termine, il pannello **Azure Active Directory** dell'app per le API nel [portale di anteprima di Azure] includerà l'ID del client dell'applicazione AAD e il tenant di AAD.

	![](./media/app-service-api-authentication-client-flow/aadblade.png)

	La scheda **Configura** dell'applicazione AAD nel [portale di Azure] include i valori URL ID APP e URL di risposta dell'app per le API.

	![](./media/app-service-api-authentication-client-flow/aadconfig.png)

## Creare e proteggere un'app Web con AAD

In questa sezione è stato scaricato e configurato un progetto Web configurato per l'autenticazione AAD. Il progetto include una pagina **About** che mostra informazioni sulle attestazioni per l'utente connesso.

![](./media/app-service-api-authentication-client-flow/aboutpagestart.png)

Questa pagina verrà modificata successivamente mediante l'aggiunta di una sezione per la visualizzazione di informazioni di contatto recuperate dall'app per le API.

1. Scaricare il progetto Web dal [repository WebApp-GroupClaims-DotNet](https://github.com/AzureADSamples/WebApp-GroupClaims-DotNet/)
 
2. Seguire le istruzioni per **Come eseguire l'esempio** nel [file Leggimi](https://github.com/AzureADSamples/WebApp-GroupClaims-DotNet/blob/master/README.md), con le eccezioni seguenti:
 
	* È possibile usare Visual Studio 2015, anche se il file Leggimi richiede l'uso di Visual Studio 2013. 

	* Usare l'applicazione AAD già creata, invece di crearne una nuova.
 
	* Usare lo stesso **URI ID app** già disponibile per l'applicazione AAD. Non modificarlo applicando il formato specificato nel file Leggimi.
	
	* Modificare le impostazioni dell'applicazione di AAD come indicato; in particolare, impostare gli URL di accesso e risposta sull'URL di base per l'applicazione di esempio.

L'uso dello stesso URI ID app creato per l'app per le API consente di usare lo stesso token di accesso di AAD per l'app Web e l'app per le API. Se si applicasse all'URI ID app il formato indicato nel file Leggimi, sarebbe possibile accedere all'app Web ma non all'app per le API. Non sarebbe possibile passare il token di AAD al gateway di app per le API per ottenere un token Zumo, perché il gateway prevede un token per un URI ID app costituito dall'URL del gateway URL e "/login/aad".

## Aggiungere il codice client generato per l'app per le API

In questa sezione si aggiunge codice generato automaticamente per un'interfaccia tipizzata per la chiamata dell'app per le API.

8.	In **Esplora soluzioni** in Visual Studio fare clic con il pulsante destro del mouse sul progetto WebApp-GroupClaims-DotNet, quindi scegliere **Aggiungi > Client app per le API di Azure**.

9.	Nella finestra di dialogo **Aggiungi client app per le API di Microsoft Azure** selezionare l'app per le API protetta con AAD.

	Al termine della generazione di codice, in **Esplora soluzioni** verrà visualizzata una nuova cartella con il nome dell'app per le API. Questa cartella contiene il codice che implementa le classi client e i modelli di dati.

10. Risolvere i riferimenti ambigui causati dal codice generato in *ContactsList/ContactsExtensions.cs*: modificare le due istanze di `Task.Factory.StartNew` in `System.Threading.Tasks.Task.Factory.StartNew`.
 
## Aggiungere codice per scambiare il token di AAD con un token Zumo

1.	In HomeController.cs aggiungere istruzioni `using` per l’SDK del servizio app e il serializzatore JSON.

		using Microsoft.Azure.AppService;
		using Newtonsoft.Json.Linq;

2. Aggiungere costanti per l'URL del gateway e l'URI ID app dell'applicazione AAD. Assicurarsi che l'URL del gateway sia https, non http.

		private const string GATEWAY_URL = "https://clientflowgroupaeb4ae60b7cb4f3d966dfa43b6607f30.azurewebsites.net/";
		private const string APP_ID_URI = GATEWAY_URL + "login/aad";

2.	Aggiungere un metodo che ottiene un oggetto client per chiamare l'app per le API.

		public async Task<AppServiceClient> GetAppServiceClient()
		{
		    var appServiceClient = new AppServiceClient(GATEWAY_URL);
		    string userObjectID = ClaimsPrincipal.Current.FindFirst
		        ("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
		
		    var authContext = new AuthenticationContext
		        (ConfigHelper.Authority, new TokenDbCache(userObjectID));
		
		    ClientCredential credential = new ClientCredential
		        (ConfigHelper.ClientId, ConfigHelper.AppKey);
		
		    // Get the AAD token.
		    AuthenticationResult result = authContext.AcquireToken(APP_ID_URI, credential);
		    var aadToken = new JObject();
		    aadToken["access_token"] = result.AccessToken;
		
		    // Send the AAD token to the gateway and get a Zumo token
		    var appServiceUser = await appServiceClient.LoginAsync
		        ("aad", aadToken).ConfigureAwait(false);
		
		    return appServiceClient;
		}

	In questo codice `ConfigHelper.Authority` si risolve in "https://login.microsoftonline.com/{tenant}", ad esempio: "https://login.microsoftonline.com/contoso.onmicrosoft.com".

2.	Aggiungere codice immediatamente prima dell'istruzione `return View()` alla fine del metodo `About` per chiamare l'app per le API. Nel passaggio successivo verrà aggiunto codice alla visualizzazione `About` per mostrare i dati restituiti.

		var appServiceClient = await GetAppServiceClient();
		var client = appServiceClient.CreateContactsList();
		var contacts = client.Contacts.Get();
		ViewData["contacts"] = contacts;

3. In *Views/Home/About.cshtml* aggiungere codice immediatamente dopo l'intestazione `h2` per visualizzare le informazioni di contatto.

		<h3>Contacts</h3>
		<table class="table table-striped table-bordered table-condensed table-hover">
		    <tr>
		        <th>Name</th>
		        <th>Email</th>
		    </tr>
		
		    @foreach (WebAppGroupClaimsDotNet.Models.Contact contact in (IList<WebAppGroupClaimsDotNet.Models.Contact>)ViewData["contacts"])
		    {
		        <tr>
		            <td>@contact.Name</td>
		            <td>@contact.EmailAddress</td>
		        </tr>
		    }
		
		</table>


3. Nel file Web.config dell'applicazione aggiungere il reindirizzamento di binding dopo il tag `<assemblyBinding>` di apertura.

		<dependentAssembly>
		  <assemblyIdentity name="System.Net.Http.Primitives" publicKeyToken="b03f5f7f11d50a3a" culture="neutral"/>
		  <bindingRedirect oldVersion="0.0.0.0-4.2.28.0" newVersion="4.2.28.0"/>
		</dependentAssembly>

	Senza questo reindirizzamento del binding, l'app avrà esito negativo, perché App Service SDK include una dipendenza da System.Net.Http.Primitives versione 1.5.0.0, ma la versione usata dal progetto è 4.2.28.0.
 
3. Seguire le istruzioni disponibili nella sezione relativa alla **distribuzione dell'esempio in Azure** nel [file Leggimi](https://github.com/AzureADSamples/WebApp-GroupClaims-DotNet/).

5. Eseguire l'app.

	![](./media/app-service-api-authentication-client-flow/homepage.png)

6. Fare clic su **Accedi**, quindi immettere le credenziali di un utente nel dominio di AAD usato per questa applicazione.

	![](./media/app-service-api-authentication-client-flow/signedin.png)

7. Fare clic su **About**.

	Il codice aggiunto al controller e alla visualizzazione About viene eseguito e mostra che l'autenticazione all'app per le API è riuscita e che è stato chiamato il rispettivo metodo Get.

	![](./media/app-service-api-authentication-client-flow/aboutpage.png)

## Risoluzione dei problemi

### Errori HTTP 400 

Assicurarsi che l'URL del gateway sia https, non http. Quando viene copiato direttamente dal portale di anteprima di Azure, è possibile che corrisponda a http.
 
### Errori di SQL Server

L'applicazione richiede l'accesso a un database SQL Server identificato dalla stringa di connessione GroupClaimContext. Assicurarsi che la stringa di connessione nel sito distribuito faccia riferimento a un'istanza di database SQL. È possibile inserire la stringa di connessione corretta nel file Web.config distribuito o nelle impostazioni di configurazione del runtime di Azure.

## Ringraziamenti

Si ringrazia Govind S. Yadav ([@govindsyadav](https://twitter.com/govindsyadav)) per la collaborazione allo sviluppo di questa esercitazione.

## Passaggi successivi

È stato illustrato come eseguire l'autenticazione del flusso client per le app per le API del servizio app. Per informazioni su altri modi per gestire l'autenticazione nelle app per le API, vedere [Autenticazione per le app per le API e per dispositivi mobili](../app-service/app-service-authentication-overview.md).

[portale di Azure]: https://manage.windowsazure.com/
[portale di anteprima di Azure]: https://portal.azure.com/

<!---HONumber=Oct15_HO3-->