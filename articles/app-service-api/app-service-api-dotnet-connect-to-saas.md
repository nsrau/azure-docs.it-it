<properties 
	pageTitle="App per le API ASP.NET di connettore SaaS personalizzata nel servizio app di Azure" 
	description="Informazioni su come scrivere codice che connette a una piattaforma SaaS da un'app per le API e come chiamare l'app per le API da un client .NET." 
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
	ms.date="09/22/2015" 
	ms.author="tdykstra"/>

# Connettersi a una piattaforma SaaS da un'app per le API ASP.NET nel servizio app di Azure

## Panoramica

Questa esercitazione spiega come codificare e configurare un'[app per le API](app-service-api-apps-why-best-platform.md) che si connette a una [piattaforma Software-as-a-Service (SaaS)](../app-service/app-service-authentication-overview.md#obotosaas) con [App Service API app SDK for .NET](http://www.nuget.org/packages/Microsoft.Azure.AppService.ApiApps.Service/). L'esercitazione spiega anche come chiamare l'app per le API da un client .NET usando [App Service SDK for .NET](http://www.nuget.org/packages/Microsoft.Azure.AppService). Al termine dell'esercitazione, si disporrà di un client app console .NET che chiama un'app per le API .NET in esecuzione nel servizio app di Azure. L'app per le API chiama l'API Dropbox e restituisce un elenco di file e cartelle nell'account Dropbox dell'utente.

Invece di scrivere il codice che chiama un'API SaaS direttamente da un'app per le API personalizzata, è possibile chiamare un'[app per le API di un connettore](../app-service-logic/app-service-logic-what-are-biztalk-api-apps.md) predefinito. Per informazioni in proposito, vedere [Distribuire e configurare un'app per le API del connettore SaaS](app-service-api-connnect-your-app-to-saas-connector.md).

L'esercitazione fornisce istruzioni dettagliate sulle operazioni seguenti:

* Creare un progetto app per le API in Visual Studio. 
* Configurare il file *apiapp.json* per consentire all'app per le API di connettersi al servizio Dropbox.
* Aggiungere il codice che chiama Dropbox e restituisce i risultati.
* Creare una nuova app per le API in Azure.
* Distribuire il progetto nell'app per le API.
* Configurare l'app per le API.
* Configurare il gateway.
* Creare un client di prova.
* Eseguire il client di prova.

## Prerequisiti

L'esercitazione presuppone quanto segue:

* Avere completato le esercitazioni [Creare un'app per le API](app-service-dotnet-create-api-app.md) e [Distribuire un'app per le API](app-service-dotnet-deploy-api-app.md).
  
* Avere una conoscenza di base dell'architettura del gateway del servizio app di Azure App per l'autenticazione, presentata[Autenticazione per app per le API e per dispositivi mobili](app-service-authentication-overview.md).

* Saper usare le app per le API nel portale di anteprima di Azure, come spiegato in [Come spostarsi ai pannelli App per le API e Gateway](app-service-api-manage-in-portal.md#navigate).

## Creare il progetto app per le API
 
Quando le istruzioni richiedono l'immissione di un nome per il progetto, indicare *SimpleDropbox*.

[AZURE.INCLUDE [app-service-api-create](../../includes/app-service-api-create.md)]

## Configurare il file *apiapp.json*

Perché un'app per le API effettui chiamate in uscita a una piattaforma SaaS, la piattaforma SaaS deve essere specificata nel file *apiapp.json*.

1. Aprire il file *apiapp.json* e aggiungere una proprietà `authentication` come illustrato di seguito (sarà necessario aggiungere anche una virgola dopo la proprietà precedente):

		"authentication": [
		  {
		    "type": "dropbox"
		  }
		]

	Il file apiapp.json completo sarà simile a questo esempio:

		{
		    "$schema": "http://json-schema.org/schemas/2014-11-01/apiapp.json#",
		    "id": "SimpleDropBox",
		    "namespace": "microsoft.com",
		    "gateway": "2015-01-14",
		    "version": "1.0.0",
		    "title": "SimpleDropBox",
		    "summary": "",
		    "author": "",
		    "endpoints": {
		        "apiDefinition": "/swagger/docs/v1",
		        "status": null
		    },
		    "authentication": [
		      {
		        "type": "dropbox"
		      }
		    ]
		}

2. Salvare il file.

L'impostazione della proprietà `authentication` ha due effetti:

* Il portale visualizza nel pannello dell'app per le API l'interfaccia utente che consente di immettere i valori dell'ID client e del segreto client della piattaforma SaaS.

	![](./media/app-service-api-dotnet-connect-to-saas/authblade.png)

* Consente all'app per le API di recuperare dal gateway il token di accesso del provider SaaS da usare quando si chiama l'API del provider SaaS.

La proprietà `authentication` è una matrice, ma questa versione di anteprima non supporta la specifica di più provider.

Per un elenco delle piattaforme supportate, vedere [Ottenere il consenso dell'utente per accedere ad altre piattaforme SaaS](../app-service/app-service-authentication-overview.md#obotosaas).

È possibile specificare anche gli ambiti, come in questo esempio:

		"authentication": [
		  {
		    "type": "google",
		    "scopes": ["https://www.googleapis.com/auth/userinfo.email", "https://www.googleapis.com/auth/userinfo.profile"]
		  }
		]

Gli ambiti disponibili vengono definiti da ogni provider SaaS e si trovano nel portale per sviluppatori del provider.

## Aggiungere il codice che chiama Dropbox

1. Installare il pacchetto NuGet [DropboxRestAPI](https://www.nuget.org/packages/DropboxRestAPI) nel progetto SimpleDropbox.

	* Dal menu **Strumenti** fare clic su **Gestione pacchetti NuGet > Console di Gestione pacchetti**.

	* Nella finestra **Console di Gestione pacchetti** immettere questo comando:
	 
			install-package DropboxRestAPI  

1. Aprire *Controllers\\ValuesController.cs* e sostituire tutto il codice nel file con il codice seguente.

		using DropboxRestAPI;
		using Microsoft.Azure.AppService.ApiApps.Service;
		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Net;
		using System.Net.Http;
		using System.Threading.Tasks;
		using System.Web.Http;
		
		namespace SimpleDropBox2.Controllers
		{
		    public class ValuesController : ApiController
		    {
		        public async Task<IEnumerable<string>> Get()
		        {
		            // Retrieve the token from the gateway
		            var runtime = Runtime.FromAppSettings(Request);
		            var dropboxTokenResult = await runtime.CurrentUser.GetRawTokenAsync("dropbox");
		
		            // Create a Dropbox client object that will send the token
		            // with REST API calls to Dropbox.
		            var dropboxClient = new Client(
		                new Options
		                {
		                    AccessToken = dropboxTokenResult.Properties["AccessToken"]
		                }
		            );
		
		            // Call the Dropbox API
		            var metadata = await dropboxClient.Core.Metadata.MetadataAsync("/");
		
		            // Return a list of files and folders.
		            return metadata.contents.Select(md => md.path);
		        }
		    }
		}

	Prima che il client chiami questo metodo, l'utente ha effettuato l'accesso a Dropbox e ha concesso all'app per le API il consenso ad accedere all'account Dropbox dell'utente. Dropbox conferma tale consenso fornendo un token di accesso al gateway del servizio app. Questo codice recupera il token dal gateway e lo include in una chiamata all'API Dropbox, come illustrato nei passaggi 6 e 7 del diagramma seguente.

	![](./media/app-service-api-dotnet-connect-to-saas/saastoken.png)

2. Compilare il progetto.

## Creare un'app per le API in Azure

In questa sezione si usa la procedura guidata **Pubblica sul Web** di Visual Studio per creare una nuova app per le API in Azure. Quando le istruzioni richiedono l'immissione di un nome per l'app per le API, indicare *SimpleDropbox*.

[AZURE.INCLUDE [app-service-api-pub-web-create](../../includes/app-service-api-pub-web-create.md)]

## Distribuire il codice

Usare la stessa procedura guidata **Pubblica sul Web** per distribuire il codice nella nuova app per le API.

[AZURE.INCLUDE [app-service-api-pub-web-deploy](../../includes/app-service-api-pub-web-deploy.md)]

## Configurare l'autenticazione per le chiamate in arrivo

Perché il servizio app di Azure consenta le chiamate in uscita autenticate dall'app per le API, l'app per le API deve richiedere anche che le chiamate in arrivo provengano da utenti autenticati. Questo non è un requisito generale di OAuth 2.0, ma è un requisito dell'architettura del gateway del servizio app attualmente implementato.

Le schermate contenute in questa sezione mostrano un'app per le API ContactsList, ma il processo è lo stesso per l'app per le API SimpleDropbox API che viene creata in questa esercitazione.

### Configurare l'app per le API in modo da rendere obbligatoria l'autenticazione delle chiamate in arrivo

[AZURE.INCLUDE [app-service-api-config-auth](../../includes/app-service-api-config-auth.md)]

### Configurare un provider di identità nel gateway

[AZURE.INCLUDE [app-service-api-gateway-config-auth](../../includes/app-service-api-gateway-config-auth.md)]

## Configurare l'autenticazione per le chiamate in uscita

Per consentire all'app per le API di chiamare l'API Dropbox, è necessario scambiare le impostazioni tra l'app per le API e un'app Dropbox creata nel sito per sviluppatori Dropbox.

### Creare un'app Dropbox nel sito Dropbox.com

[AZURE.INCLUDE [app-service-api-create-dropbox-app](../../includes/app-service-api-create-dropbox-app.md)]

### Scambiare le impostazioni tra l'app Dropbox e l'app per le API

I passaggi seguenti fanno riferimento a un'app per le API del connettore Dropbox, ma le procedure e l'interfaccia utente sono le stesse per l'app per le API SimpleDropbox che viene creata in questa esercitazione.

> **Nota:** se non sono presenti i campi per l'ID client e il segreto client di Dropbox nel pannello **Autenticazione** dell'app per le API SimpleDropbox, verificare di avere riavviato il gateway come illustrato dopo la distribuzione del progetto app per le API nell'app per le API. Il valore "dropbox" nella proprietà `authentication` del file *apiapp.json* distribuito in precedenza consente al portale di visualizzare questi campi.

[AZURE.INCLUDE [app-service-api-exchange-dropbox-settings](../../includes/app-service-api-exchange-dropbox-settings.md)]

## Creare un client di prova

In questa sezione si crea un progetto app console che usa il codice client generato da Visual Studio per chiamare l'app per le API SimpleDropbox. L'app console crea un'istanza di un controllo Windows Form del browser per gestire l'interazione dell'utente con il gateway e le pagine Web di accesso a Dropbox.

### Creare il progetto

1. In Visual Studio creare un nuovo progetto di Applicazione console e chiamarlo *SimpleDropboxTest*.

2. Impostare un riferimento a System.Windows.Forms.
 
	* In **Esplora soluzioni** fare clic con il pulsante destro del mouse su **Riferimenti**, quindi scegliere **Aggiungi riferimento**.

	* Selezionare la casella di controllo a sinistra di **System.Windows.Forms** e quindi fare clic su **OK**.
	 
	![](./media/app-service-api-dotnet-connect-to-saas/setref.png)

	L'applicazione console userà l'assembly di Windows Form per creare un'istanza di un controllo del browser quando deve consentire all'utente di accedere al gateway e a Dropbox.

### Aggiungere il codice client generato

Le schermate contenute in questa sezione mostrano un progetto ContactsList e un'app per le API, ma per questa esercitazione selezionare il progetto SimpleDropboxTest e l'app per le API SimpleDropbox.

[AZURE.INCLUDE [app-service-api-dotnet-add-generated-client](../../includes/app-service-api-dotnet-add-generated-client.md)]

### Aggiungere il codice per chiamare l'app per le API

3. Aprire *Program.cs* e sostituire il codice con il codice seguente.
		
		using Microsoft.Azure.AppService;
		using Newtonsoft.Json;
		using System;
		using System.Collections.Generic;
		using System.Diagnostics;
		using System.Linq;
		using System.Text;
		using System.Threading.Tasks;
		using System.Windows.Forms;
		
		namespace SimpleDropboxTest
		{
		    enum Step
		    {
		        GatewayLogin,
		        GetSaaSConsentLink,
		        GetUserConsent
		    }
		
		    class Program
		    {
		        private const string GATEWAY_URL = @"{gateway url}";
		        private const string URL_TOKEN = "#token=";
		        private const string SAAS_URL = "dropbox.com";
		        private static Form frm = new Form();
		        private static string responseURL = "";
		        private static Step step;
		        [STAThread]
		        static void Main(string[] args)
		        {
		            // Create the web browser control
		            WebBrowser browser = new WebBrowser();
		            browser.Dock = DockStyle.Fill;
		            browser.Navigated += CheckResponseURL;
		            frm.Controls.Add(browser);
		            frm.Width = 640;
		            frm.Height = 480;
		
		            // Create the gateway and API app clients.
		            AppServiceClient appServiceClient = new AppServiceClient(GATEWAY_URL);
		            SimpleDropbox simpleDropboxClient = appServiceClient.CreateSimpleDropbox();
		
		            // Navigate browser to gateway login URL for configured identity provider.
		            // Identity provider for this example is Azure Active Directory.
		            step = Step.GatewayLogin;
		            browser.Navigate(string.Format(@"{0}/login/aad", GATEWAY_URL));
		            frm.ShowDialog();
		            Console.WriteLine("Logged in to gateway, response URL=" + responseURL);
		
		            // Get user ID and Zumo token from return URL, then call 
		            // the gateway URL to log in the gateway client.
		            var encodedJson = responseURL.Substring(responseURL.IndexOf(URL_TOKEN) + URL_TOKEN.Length);
		            var decodedJson = Uri.UnescapeDataString(encodedJson);
		            var result = JsonConvert.DeserializeObject<dynamic>(decodedJson);
		            string userId = result.user.userId;
		            string userToken = result.authenticationToken;
		            appServiceClient.SetCurrentUser(userId, userToken);
		
		            // Call gateway API to get consent link URL for target SaaS platform.
		            // SaaS platform for this example is Dropbox.
		            // See the tutorial for an explanation of
		            // the redirectURL parameter for GetConsentLinkAsync
		            var gatewayConsentLink = appServiceClient.GetConsentLinkAsync("SimpleDropbox", GATEWAY_URL).Result;
		            Console.WriteLine("\nGot gateway consent link, URL=" + gatewayConsentLink);
		
		            // Navigate browser to consent link URL returned from gateway.
		            // Response URL will be the SaaS logon link
		            step = Step.GetSaaSConsentLink;
		            browser.Navigate(gatewayConsentLink);
		            frm.ShowDialog();
		            Console.WriteLine("\nGot SaaS consent link response, URL=" + responseURL);
		
		            // Navigate browser to login/consent link for SaaS platform.
		            step = Step.GetUserConsent;
		            browser.Navigate(responseURL);
		            frm.ShowDialog();
		            Console.WriteLine("\nGot Dropbox login response, URL=" + responseURL);
		
		            Console.WriteLine("\nResponse from SaaS Provider");
		            var response = simpleDropboxClient.Values.Get();
		            foreach (string s in response)
		            {
		                Console.WriteLine(s);
		            }
		            Console.Read();
		        }
		
		        static void CheckResponseURL(object sender, WebBrowserNavigatedEventArgs e)
		        {
		            if ((step == Step.GatewayLogin && e.Url.AbsoluteUri.IndexOf(URL_TOKEN) > -1)
		                || (step == Step.GetSaaSConsentLink && e.Url.AbsoluteUri.IndexOf(SAAS_URL) > -1)
		                || (step == Step.GetUserConsent && e.Url.AbsoluteUri.IndexOf(GATEWAY_URL) > -1))
		            {
		                responseURL = e.Url.AbsoluteUri;
		                frm.Close();
		            }
		        }
		
		    }
		}

1. Sostituire {gateway url} con l'URL effettivo del gateway.
 
	È possibile ottenere l'URL del gateway dal pannello **Gateway** nel portale:

	![](./media/app-service-api-dotnet-connect-to-saas/gwurl.png)

		private const string GATEWAY_URL = @"https://sd1aeb4ae60b7cb4f3d966dfa43b660.azurewebsites.net";

	> **Importante**: assicurarsi che l'URL del gateway inizi con `https://` e non con `http://`. **Se si copia http:// dal portale, è necessario sostituirlo con https:// quando lo si incolla nel codice.**

### Descrizione del codice

Questa applicazione console è progettata per usare una quantità minima di codice per illustrare i passaggi che un'app client deve eseguire. Un'applicazione di produzione non sarebbe in genere un'applicazione console e implementerebbe la gestione e la registrazione degli errori.

Ecco una panoramica delle operazioni eseguite nel codice:

* Apre un browser per l'URL di accesso al gateway per il provider di identità configurato, in questo caso Azure Active Directory. 
	 
* Gestisce l'URL di risposta previsto dopo l'accesso dell'utente: estrae l'ID utente e il token Zumo e li passa all'oggetto client del servizio app.

* Usa l'oggetto client del servizio app per recuperare un URL del gateway che reindirizzerà al collegamento Dropbox per l'accesso e il consenso. Passaggio 1 nel diagramma.

* Apre un browser per l'URL di consenso del gateway. Il browser viene reindirizzato al collegamento per l'accesso e il consenso Dropbox. Passaggio 2 nel diagramma.
	 
* Chiude il browser dopo che l'utente ha effettuato l'accesso e dato il consenso in Dropbox.com. Passaggio 3 nel diagramma.
 
* Chiama l'app per le API. Passaggio 5 nel diagramma. Il passaggio 4 viene eseguito in background tra Dropbox.com e il gateway, i passaggi 6 e 7 vengono eseguiti dall'app per le API e non dal client.

![](./media/app-service-api-dotnet-connect-to-saas/saastoken.png)

Note aggiuntive:

* L'attributo `STAThread` nel metodo `Main` è richiesto dal controllo del Web browser e non è correlato alla configurazione o alla chiamata dell'app per le API.

* L'URL di accesso al gateway indicato termina con `/aad` per Azure Active Directory.

		browser.Navigate(string.Format(@"{0}/login/aad", GATEWAY_URL));

	Ecco i valori da usare per gli altri provider: * "microsoftaccount" * "facebook" * "twitter" * "google" <br/>
<br/>


* Il secondo parametro per il metodo `GetConsentLinkAsync()` è l'URL callback a cui il server di consenso reindirizza dopo che l'utente accede a Dropbox e dà il consenso ad accedere all'account dell'utente.

		var gatewayConsentLink = appServiceClient.GetConsentLinkAsync("SimpleDropbox", GATEWAY_URL).Result;

	Per questo parametro di solito si specifica la successiva pagina Web a cui l'utente deve passare nell'applicazione client. Poiché questo codice dimostrativo fa parte di un'app console, non è presente alcuna pagina applicazione a cui passare e il codice specifica l'URL del gateway come pagina di destinazione semplicemente per comodità.

	L'applicazione client deve verificare di venire reindirizzata a questo URL e che non siano presenti messaggi di errore. Se il processo di accesso/consenso non riesce, l'URL di reindirizzamento potrebbe contenere un messaggio di errore in querystring. Per altre informazioni, vedere la sezione [Risoluzione dei problemi](#troubleshooting).

## Testare

1. Eseguire l'applicazione console SimpleDropboxTest.

2. Nella prima pagina accedere con le credenziali di Azure Active Directory (o con le credenziali di un altro provider di identità, ad esempio Google o Twitter, se è quello configurato nel gateway).

	![](./media/app-service-api-dotnet-connect-to-saas/aadlogon.png)

3. Nella pagina di accesso di Dropbox.com accedere con le credenziali di Dropbox.

	![](./media/app-service-api-dotnet-connect-to-saas/dblogon.png)

4. Nella pagina di consenso di Dropbox concedere all'applicazione l'autorizzazione per accedere ai dati.

	![](./media/app-service-api-dotnet-connect-to-saas/dbconsent.png)

	L'app console chiama quindi l'app per le API e restituisce un elenco dei file nell'account Dropbox.

	![](./media/app-service-api-dotnet-connect-to-saas/testclient.png)

## Risoluzione dei problemi

In questa sezione sono disponibili gli argomenti seguenti:

* [Errore HTTP 405 dopo l'accesso al gateway](#405)
* [Errore HTTP 400 invece della pagina di accesso di Dropbox](#400)
* [Errore HTTP 403 quando si chiama l'app per le API](#403)

### <a id="405"></a> Errore HTTP 405 dopo l'accesso al gateway

Se viene visualizzato l'errore HTTP 405 quando il codice chiama GetConsentLinkAsync, verificare di avere usato https:// e non http:// per l'URL del gateway.

![](./media/app-service-api-dotnet-connect-to-saas/http405.png)

L'errore 405 (Metodo non concesso) viene visualizzato perché il client tenta di eseguire una richiesta POST HTTP non SSL, il gateway reindirizza a **https://* e il reindirizzamento genera una richiesta GET. L'URL per recuperare un collegamento di consenso accetta solo richieste POST.

### <a id="400"></a>Errore HTTP 400 invece della pagina di accesso di Dropbox

Verificare che l'**ID client** nel pannello **Autenticazione** dell'app per le API sia corretto e che non siano presenti spazi iniziali o finali.

### <a id="403"></a> Errore HTTP 403 quando si chiama l'app per le API

* Verificare che il **Livello di accesso** dell'app per le API sia impostato su **Pubblico (autenticato)** e non su **Interno**.

* Verificare che il **Segreto client** nel pannello **Autenticazione** dell'app per le API sia corretto e che non siano presenti spazi iniziali o finali.

L'URL di reindirizzamento dopo l'accesso a Dropbox potrebbe essere simile al seguente:

	https://sd1aeb4ae60b7cb4f3d966dfa43b6607f30.azurewebsites.net/?error=RmFpbGVkIHRvIGV4Y2hhbmdlIGNvZGUgZm9yIHRva2VuLiBEZXRhaWxzOiB7ImVycm9yX2Rlc2NyaXB0aW9uIjogIkludmFsaWQgY2xpZW50X2lkIG9yIGNsaWVudF9zZWNyZXQiLCAiZXJyb3IiOiAiaW52YWxpZF9jbGllbnQifQ%3d%3d

Se si rimuove %3d%3d dalla fine del valore querystring `error`, si ottiene una stringa con codifica base64 valida. Decodificare la stringa per visualizzare il messaggio di errore:

	Failed to exchange code for token. Details: {"error_description": "Invalid client_id or client_secret", "error": "invalid_client"}

## Passaggi successivi

È stato illustrato come codificare e configurare un'app per le API che si connette a una piattaforma SaaS. Per i collegamenti ad altre esercitazioni su come gestire l'autenticazione nelle app per le api, vedere [Autenticazione per app per le API e per dispositivi mobili - Passaggi successivi](../app-service/app-service-authentication-overview.md#next-steps).

[Azure preview portal]: https://portal.azure.com/
[Azure portal]: https://manage.windowsazure.com/

<!---HONumber=Oct15_HO1-->