<properties
	pageTitle="Utilizzare un'app per le API da JavaScript tramite CORS | Microsoft Azure"
	description="Informazioni su come utilizzare un'app per le API del servizio app di Azure da un client JavaScript e con CORS."
	services="app-service\api"
	documentationCenter=".net"
	authors="tdykstra"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-api"
	ms.workload="na"
	ms.tgt_pltfrm="dotnet"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="01/26/2016"
	ms.author="tdykstra"/>

# Utilizzare un'app per le API da JavaScript tramite CORS

## Panoramica

Questo articolo contiene due sezioni:

* La sezione [Come configurare CORS](#corsconfig) spiega in generale come configurare CORS per qualsiasi app per le API. È applicabile anche a tutti i framework supportati dal servizio app, ad esempio .NET, Node.js e Java. 

* Il [resto dell'articolo](#tutorialstart) illustra in dettaglio la distribuzione di un'applicazione .NET di esempio e la configurazione di CORS, in modo che il front-end JavaScript possa chiamare il back-end dell'API Web.

## <a id="corsconfig"></a>Come configurare CORS nel servizio app di Azure

### Informazioni su CORS

Per motivi di sicurezza, i browser impediscono a JavaScript di eseguire chiamate API a un dominio diverso da quello da cui proviene il codice JavaScript. Ad esempio, è possibile effettuare una chiamata da una pagina Web contoso.com a un endpoint API contoso.com, ma non a un endpoint di fabrikam.com. CORS (Cross Origin Resource Sharing) è un protocollo Internet progettato per abilitare scenari in cui è necessario effettuare tali chiamate API tra domini. Un esempio di questo scenario nel servizio app di Azure si ha quando il client JavaScript viene eseguito in un'app Web, mentre l'API viene eseguita in un'app per le API.

### Supporto per CORS nel servizio app

Il servizio app offre un modo semplice per configurare i domini autorizzati a chiamare un'app per le API e la funzionalità CORS è compatibile con tutti i linguaggi supportati dal servizio app per le API.

### Configurare CORS nel portale di Azure

8. Nel browser passare al [portale di Azure](https://portal.azure.com/).

9. Fare clic su **Esplora > App per le API**.

	![](./media/app-service-api-cors-consume-javascript/browseapiapps.png)

11. Selezionare l'app per le API di destinazione.

	![](./media/app-service-api-cors-consume-javascript/selectapiapp.png)

10. Nel pannello **App per le API** fare clic su **Impostazioni**.

	![](./media/app-service-api-cors-consume-javascript/clicksettings.png)

11. Trovare la sezione **API** e quindi fare clic su **CORS**.

12. Nella casella di testo immettere l'URL o gli URL da cui consentire le chiamate JavaScript.

	Ad esempio, se l'applicazione JavaScript è stata distribuita in un'app Web denominata todolistangular, immettere "https://todolistangular.azurewebsites.net". In alternativa, è possibile immettere un asterisco (*) per specificare che vengono accettati tutti i domini di origine.

13. Fare clic su **Save**.

	![](./media/app-service-api-cors-consume-javascript/corsinportal.png)

	Dopo aver fatto clic su **Salva**, l'app per le API accetterà le chiamate JavaScript dall'URL o dagli URL specificati.

### Configurare CORS con gli strumenti di Gestione risorse di Azure

È anche possibile configurare CORS per un'app per le API con strumenti da riga di comando, ad esempio Azure PowerShell o l'interfaccia della riga di comando multipiattaforma di Azure, oppure con [Esplora risorse](https://resources.azure.com/).

In questi strumenti impostare la proprietà `cors` sul tipo di risorsa Microsoft.Web/sites/config per la risorsa <site name>/web. Ad esempio, in **Esplora risorse**, passare a **sottoscrizioni > {sottoscrizione} > gruppi di risorse > {gruppo di risorse} > provider > Microsoft.Web > siti > {sito} > config > web** per visualizzare la proprietà cors:

		"cors": {
		    "allowedOrigins": [
		        "todolistangular.azurewebsites.net"
		    ]
		}

## <a id="tutorialstart"></a> Proseguimento dell'esercitazione introduttiva su .NET

Se si sta seguendo la serie introduttiva su Node.js o Java per le app per le API, passare all'articolo successivo [Autenticazione e autorizzazione per app per le API del servizio app di Azure](app-service-api-authentication.md).

Il resto di questo articolo è una continuazione della serie introduttiva su .NET e si presuppone che sia stata completata correttamente [la prima esercitazione](app-service-api-dotnet-get-started.md).

## Distribuire il progetto ToDoListAngular in una nuova app Web

Nella [prima esercitazione](app-service-api-dotnet-get-started.md) è stata creata un'app per le API di livello intermedio e un'app per le API di livello dati. In questa esercitazione si creerà un'app Web di applicazione a singola pagina che chiama l'app per le API di livello intermedio. Per consentire il funzionamento dell'applicazione a singola pagina, si dovrà abilitare CORS nell'app per le API di livello intermedio.

Nell'[applicazione di esempio ToDoList](https://github.com/Azure-Samples/app-service-api-dotnet-todo-list) il progetto ToDoListAngular è un semplice client AngularJS che chiama il progetto API Web ToDoListAPI di livello intermedio. Il codice JavaScript nel file *app/scripts/todoListSvc.js* chiama l'API usando il provider HTTP AngularJS.

		angular.module('todoApp')
		.factory('todoListSvc', ['$http', function ($http) {
		    var apiEndpoint = "http://localhost:46439";
		
		    $http.defaults.useXDomain = true;
		    delete $http.defaults.headers.common['X-Requested-With']; 
		
		    return {
		        getItems : function(){
		            return $http.get(apiEndpoint + '/api/TodoList');
		        },

		        /* Get by ID, Put, and Delete methods not shown */

		        postItem : function(item){
		            return $http.post(apiEndpoint + '/api/TodoList', item);
		        }
		    };
		}]);

### Configurare il progetto ToDoListAngular per chiamare l'app per le API ToDoListAPI 

Prima di distribuire il front-end in Azure, è necessario modificare l'endpoint dell'API nel progetto AngularJS, in modo che il codice chiami l'app per le API di Azure ToDoListAPI creata nell'esercitazione precedente.

1. Nel progetto ToDoListAngular aprire il file *app/scripts/todoListSvc.js*.

2. Impostare come commento la riga che imposta `apiEndpoint` sull'URL localhost, rimuovere il commento dalla riga che imposta `apiEndPoint` su un URL azurewebsites.net, quindi sostituire il segnaposto con il nome effettivo dell'app per le API creata in precedenza. Se l'app per le API è stata denominata ToDoListAPI0125, il codice sarà simile all'esempio seguente.

		var apiEndPoint = 'https://todolistapi0125.azurewebsites.net';
		//var apiEndPoint = 'http://localhost:45914';

3. Salvare le modifiche.

### Creare una nuova app Web per il progetto ToDoListAngular

La procedura per creare una nuova app Web e per distribuirvi un progetto è la stessa illustrata nella prima esercitazione di questa serie, eccetto il fatto che non si modifica il tipo da **App Web** ad **App per le API**.

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto ToDoListAngular e quindi scegliere **Pubblica**.

3.  Nella scheda **Profilo** della procedura guidata **Pubblica sito Web** fare clic su **Servizio app di Microsoft Azure**.

5. Nella finestra di dialogo **Servizio App** fare clic su **Nuovo**.

3. Nella scheda **Hosting** della finestra di dialogo **Crea servizio app** verificare che il tipo sia **App Web**.

4. Immettere un **Nome app Web** che sia univoco nel dominio *azurewebsites.net*.

5. Scegliere la **Sottoscrizione** di Azure da usare.

6. Nell'elenco a discesa **Gruppo di risorse** selezionare il nome del gruppo di risorse creato in precedenza.

4. Nell'elenco a discesa **Piano di servizio app** scegliere lo stesso piano creato in precedenza.

7. Fare clic su **Crea**.

	Visual Studio crea l'app Web, crea un profilo di pubblicazione per l'app e visualizza il passaggio **Connessione** della procedura guidata **Pubblica sito Web**.

### Distribuire il progetto Web ToDoListAngular nella nuova app Web

*  Nel passaggio **Connessione** della procedura guidata **Pubblica sito Web** fare clic su **Pubblica**.

	Visual Studio distribuisce il progetto ToDoListAngular nella nuova app Web e apre un browser all'URL dell'app Web.

### Testare l'applicazione senza avere abilitato CORS 

2. Negli strumenti di sviluppo del browser aprire la finestra della console.

3. Nella finestra del browser che visualizza l'interfaccia utente di AngularJS fare clic sul collegamento **To Do List**.

	Il codice JavaScript prova a chiamare l'app per le API di livello intermedio, ma la chiamata non riesce perché il front-end è in esecuzione in un dominio diverso (URL dell'app web) da quello del back-end (URL dell'app per le API). La finestra della console degli strumenti di sviluppo del browser mostra un messaggio di errore tra origini.

	![](./media/app-service-api-cors-consume-javascript/consoleaccessdenied.png)

## Configurare CORS nel servizio app di Azure

In questa sezione si configurerà l'app per le API di livello intermedio per consentire le chiamate JavaScript dall'app Web creata per il progetto ToDoListAngular.
 
8. Nel browser passare al [portale di Azure](https://portal.azure.com/).

9. Passare all'app per le API ToDoListAPI (livello intermedio).

10. Nel pannello **App per le API** fare clic su **Impostazioni**.

11. Trovare la sezione **API** e quindi fare clic su **CORS**.

12. Nella casella di testo immettere l'URL per l'app Web (front-end) ToDoListAngular. Ad esempio, se è stato distribuito il progetto ToDoListAngular in un'app Web denominata todolistangular0121, consentire le chiamate dall'URL `https://todolistangular0121.azurewebsites.net`.

	In alternativa, è possibile immettere un asterisco (*) per specificare che vengono accettati tutti i domini di origine.

13. Fare clic su **Save**.

	![](./media/app-service-api-cors-consume-javascript/corsinportal.png)


### Testare l'applicazione con CORS abilitato

* Aprire un browser all'URL HTTPS dell'app Web. 

	Questa volta l'applicazione consente di visualizzare, aggiungere, modificare ed eliminare attività.

	![](./media/app-service-api-cors-consume-javascript/corssuccess.png)

## CORS del servizio app e CORS di API Web

In un progetto di API Web è possibile installare il pacchetto NuGet [Microsoft.AspNet.WebApi.Cors](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Cors/) per specificare nel codice i domini da cui l'API accetterà chiamate JavaScript.
 
Non cercare di usare sia CORS per l'API Web che CORS per il servizio app in un'app per le API. CORS per il servizio app avrà la precedenza e CORS per l'API Web non avrà effetto. Ad esempio, se si abilita un dominio di origine nel servizio app e si abilitano tutti i domini di origine nel codice dell'API Web, l'app per le API di Azure accetterà solo chiamate dal dominio specificato in Azure.

Il supporto di CORS per l'API Web è più flessibile del supporto di CORS per il servizio app. Ad esempio, nel codice è possibile specificare origini accettate diverse a seconda del metodo di azione, mentre per CORS per il servizio app si specifica un set di origini accettate per tutti i metodi di un'app per le API.

### Come abilitare CORS nel codice dell'API Web

I passaggi seguenti riepilogano il processo di abilitazione del supporto di CORS per l'API Web. Per altre informazioni, vedere l'articolo relativo all'[abilitazione della condivisione di richieste tra le origini nelle API Web ASP.NET 2](http://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api).

1. In un progetto di API Web includere una riga di codice `config.EnableCors()` nel metodo **Register** della classe **WebApiConfig**, come nell'esempio seguente. 

		public static class WebApiConfig
	    {
	        public static void Register(HttpConfiguration config)
	        {
	            // Web API configuration and services
	            
		        // The following line enables you to control CORS by using Web API code
				config.EnableCors();
	
	            // Web API routes
	            config.MapHttpAttributeRoutes();
	
	            config.Routes.MapHttpRoute(
	                name: "DefaultApi",
	                routeTemplate: "api/{controller}/{id}",
	                defaults: new { id = RouteParameter.Optional }
	            );
	        }
	    }

1. Nel controller dell'API Web aggiungere l'attributo `EnableCors` alla classe controller o a singoli metodi di azione. Nell'esempio seguente il supporto di CORS si applica all'intero controller.

		namespace ToDoListAPI.Controllers
		{
		    [HttpOperationExceptionFilterAttribute]
		    [EnableCors(origins:"*", headers:"*", methods: "*")]
		    public class ToDoListController : ApiController
 
	> **Nota**: l'uso dei caratteri jolly per tutti i parametri con l'attributo `EnableCors` è concepito solo a scopo dimostrativo e rende accessibile l'API a tutte le origini e tutte le richieste HTTP. Usare questo attributo con cautela.

## Passaggi successivi 

In questa esercitazione è stato illustrato come abilitare il supporto per CORS del servizio app perché il codice JavaScript del client possa chiamare un'API in un dominio diverso. Nel prossimo articolo della serie introduttiva alle app per le API viene illustrata l'[autenticazione per le app per le API del servizio app](app-service-api-authentication.md).

<!---HONumber=AcomDC_0204_2016-->