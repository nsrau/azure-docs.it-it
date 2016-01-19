<properties
	pageTitle="Utilizzare un'app per le API da JavaScript tramite CORS | Microsoft Azure"
	description="Informazioni su come utilizzare un'app per le API nel servizio app di Azure da un client JavaScript e con CORS."
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
	ms.date="01/05/2016"
	ms.author="tdykstra"/>

# Utilizzare un'app per le API da JavaScript tramite CORS

[AZURE.INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

## Panoramica

Questa esercitazione illustra come usare un'app per le API dal codice JavaScript in un sito Web servito da un dominio diverso dall'app per le API. Il client di esempio usa AngularJS.

![](./media/app-service-api-cors-consume-javascript/homepageazure.png)
 
Questa è la seconda di una serie di esercitazioni sull'uso delle app per le API nel servizio app di Azure. Per passare alla prima della serie, scegliere il primo argomento nell'elenco a discesa **Argomento** nella parte superiore della pagina.

## Supporto CORS nel servizio app di Azure

Per motivi di sicurezza, il comportamento predefinito dei browser impedisce a JavaScript di eseguire chiamate API a un dominio diverso da quello da cui deriva il codice JavaScript. Ad esempio, è possibile effettuare una chiamata da una pagina Web contoso.com a un endpoint dell'API contoso.com, ma non a un endpoint di fabrikam.com. CORS (Cross Origin Resource Sharing) è un protocollo Internet progettato per abilitare scenari in cui è necessario effettuare tali chiamate API tra domini. In esempio di tale scenario nel servizio app di Azure si ha quando il client JavaScript viene eseguito in un'app Web e l'API viene eseguita in un'app per le API.

Il servizio app di Azure offre un modo semplice per configurare i domini autorizzati a chiamare un'app per le API e la funzionalità CORS è compatibile con tutti i linguaggi supportati dal servizio app per le API, ad esempio Java e Node.js.

## Come seguire questa esercitazione

Questa esercitazione fa uso di un'applicazione di esempio per cui viene scaricata e creata un'app per le API nella [prima esercitazione per la versione ASP.NET di questa serie](app-service-api-dotnet-get-started.md).

Se si stanno seguendo le esercitazioni introduttive di Java o Node.JS, andare direttamente alla [sezione relativa alla configurazione di CORS](#corsconfig) per istruzioni generali valide per tutte le app per le API.

## Progetto di esempio ContactsList.Angular

Nell'[applicazione di esempio ContactsList](https://github.com/Azure-Samples/app-service-api-dotnet-contact-list) il progetto ContactsList.Angular è un semplice client di AngularJS per il progetto di API Web ContactsList.API.

Il codice JavaScript AngularJS che chiama l'API si trova nel file *index.html* nel progetto ContactsList.Angular. Il codice definisce le funzioni e le aggiunge all'oggetto `$scope`, come illustrato di seguito dove il metodo Get dell'API è definito come `$scope.refresh()`.

		angular.module('myApp', []).controller('contactListCtrl', function ($scope, $http) {
		    $scope.baseurl = 'http://localhost:51864';
		
		    $scope.refresh = function () {
		        $scope.status = "Refreshing Contacts...";
		        $http({
		            method: 'GET',
		            url: $scope.baseUrl + '/api/contacts',
		            headers: {
		                'Content-Type': 'application/json'
		            }
		        }).then(function (results) {
		            $scope.contacts = results.data;
		            $scope.status = "Contacts loaded";
		        }, function (err) {
		            $scope.status = "Error loading contacts";
		        });
		    };
		
		    // POST and DELETE not shown
		
		    $scope.refresh();
		});

Il codice chiama il metodo $scope.refresh() quando la pagina viene caricata (alla fine del frammento mostrato in precedenza) e collegata al pulsante **Aggiorna** nell'interfaccia utente.

		<th><button class="btn btn-sm btn-info" ng-click="refresh()">Refresh</button></th>

## Eseguire il progetto di AngularJS in locale

In questa sezione si verificare di poter eseguire il client localmente e chiamare l'API mentre è anch'essa in esecuzione in locale.

**Nota:** queste istruzioni sono valide per i browser Internet Explorer ed Edge che consentono le chiamate JavaScript multiorigine da e verso gli URL `http://localhost`. Se si usa Chrome, avviare il browser con l'opzione `--disable-web-security`. Se si usa Firefox, saltare questa sezione.

1. Impostare i progetti ContactsList.API e ContactsList.Angular come progetti di avvio, con ContactsList.API avviato prima di ContactsList.Angular. 

2. Premere F5 per avviare il progetto.

	L'interfaccia utente di AngularJS visualizza i contatti archiviati in locale e può essere usata per aggiungere ed eliminare contatti.

	![](./media/app-service-api-cors-consume-javascript/homepagelocal.png)

3. Chiudere le finestre del browser.

## Modificare il progetto AngularJS in modo che punti all'app per le API di Azure 

Si eseguirà quindi il front-end AngularJS nel cloud e si chiamerà il back-end dell'API in esecuzione nel cloud. Prima di distribuire il front-end in Azure, è necessario modificare l'endpoint dell'API nel progetto AngularJS, in modo che il codice chiami l'app per le API di Azure creata in precedenza.

1. Nel progetto ContactsList.Angular aprire *index.html*.

2. Impostare come commento la riga che imposta `baseUrl` sull'URL localhost, rimuovere il commento dalla riga che imposta `baseUrl` su un URL azurewebsites.net, quindi sostituire il segnaposto con il nome effettivo dell'app per le API creata in precedenza. Se l'app per le API è stata denominata ContactsListAPI, il codice sarà simile all'esempio seguente.

		$scope.baseUrl = 'https://ContactsListAPI.azurewebsites.net';
		//$scope.baseUrl = 'http://localhost:51864';

### Distribuire il progetto ContactsList.Angular nell'app Web

È possibile creare una nuova app Web per distribuire il progetto AngularJS, ma per questa esercitazione si distribuirà la stessa app Web creata nell'esercitazione precedente. Il nome dell'app Web può riflettere il fatto che in questa app è stato distribuito un progetto MVC ASP.NET in origine, ma dopo questa distribuzione eseguirà il codice AngularJS.

8. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto ContactsList.Angular e quindi scegliere **Pubblica**.

9. Fare clic sulla scheda **Profilo**.

3.  Nel passaggio **Profilo** della procedura guidata **Pubblica sito Web** fare clic su **Servizio app di Microsoft Azure**.

4. Nella finestra di dialogo **Servizio App** scegliere la sottoscrizione.

5. Con **Visualizzazione** impostata sul valore predefinito **Gruppo di risorse**, espandere il gruppo di risorse creato per questa serie di esercitazioni.

7. Selezionare l'app Web creata nella prima esercitazione, assicurandosi di non selezionare l'app per le API, quindi fare clic su **OK**.

8. Fare clic sulla scheda **Impostazioni**.

9. Espandere **Opzioni pubblicazione file**, quindi selezionare **Rimuovi file aggiuntivi nella destinazione**.

	![](./media/app-service-api-cors-consume-javascript/removeadditionalfiles.png)

	In genere quando si distribuisce un progetto Web in un'app Web del servizio app esistente, non si vuole usare l'opzione "Rimuovi file aggiuntivi" perché le eventuali modifiche sono in genere aggiornamenti o nuovi file. In questo caso si distribuisce un progetto diverso nella stessa app Web, quindi sono probabilmente presenti molti file dalla distribuzione precedente che non sono necessari in quella nuova.

10. Fare clic su **Pubblica**.

	Visual Studio distribuisce il progetto ContactsList.Angular nella nuova app Web e apre un browser all'URL dell'app Web. Nel browser verrà visualizzata la stessa interfaccia utente di AngularJS visualizzata mentre era in esecuzione in locale, ma ora l'operazione non riesce perché il server front-end è in esecuzione in un dominio (l'URL dell'app Web), diverso dal back-end (l'URL dell'app per le API).

	![](./media/app-service-api-cors-consume-javascript/corserror.png)

## <a id="corsconfig"></a>Configurare CORS per l'app per le API di destinazione in Azure

8. In un'altra finestra del browser passare al [portale di Azure](https://portal.azure.com/).

9. Fare clic su **Sfoglia > App per le API** e quindi selezionare l'app per le API di destinazione. Per questa esercitazione si tratta dell'app per le API creata nella prima esercitazione per il progetto ContactsList.API.

10. Nel pannello **App per le API** fare clic su **Impostazioni**.

11. Trovare la sezione **API** e quindi fare clic su **CORS**.

12. Nella casella di testo immettere l'URL da cui consentire le chiamate. Ad esempio, se l'applicazione JavaScript è stata distribuita a un'app Web denominata ContactsListMVC, immettere "http://contactslistmvc.azurewebsites.net".

	Si noti che, in alternativa all'immissione di un URL, è possibile immettere un asterisco (*) per specificare che vengono accettati tutti i domini di origine.

13. Fare clic su **Save**.

	![](./media/app-service-api-cors-consume-javascript/corsinportal.png)

14. Passare alla finestra del browser che visualizza il client AngularJS e aggiornare la pagina o fare clic sul pulsante **Aggiorna**.

	Nella pagina ora sono visualizzati i contatti archiviati nel file system dell'app per le API di Azure.

	![](./media/app-service-api-cors-consume-javascript/homepageazure.png)

### CORS negli strumenti di Gestione risorse di Azure

È anche possibile configurare CORS per un'app per le API usando strumenti di Gestione risorse di Azure come Azure PowerShell, CLI o [Esplora risorse](https://resources.azure.com/).

Impostare la proprietà `cors` sul tipo di risorsa Microsoft.Web/sites/config per la risorsa <site name>/web. Ad esempio, in **Esplora risorse**, passare a **sottoscrizioni > {sottoscrizione} > gruppi di risorse > {gruppo di risorse} > provider > Microsoft.Web > siti > {sito} > config > web** per visualizzare la proprietà cors:

		"cors": {
		    "allowedOrigins": [
		        "contactslistmvc.azurewebsites.net"
		    ]
		}

## Supporto di CORS nel codice API Web

In un progetto di API Web è possibile installare il pacchetto NuGet [Microsoft.AspNet.WebApi.Cors](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Cors/) che consente di specificare nel codice i domini da cui l'API accetterà chiamate JavaScript.
 
Il supporto di CORS per l'API Web è più flessibile del supporto di CORS per il servizio app. Ad esempio, nel codice è possibile specificare origini accettate diverse a seconda del metodo di azione, mentre per CORS per il servizio app si specifica un set di origini accettate per tutti i metodi di un'app per le API.

### CORS per il servizio app ha la precedenza su CORS per l'API Web

Non cercare di usare sia CORS per l'API Web che CORS per il servizio app in un'app per le API. CORS per il servizio app avrà la precedenza e CORS per l'API Web non avrà effetto. Ad esempio, se si abilita un dominio di origine nel servizio app e si abilitano tutti i domini di origine nel codice dell'API Web, l'app per le API di Azure accetterà solo chiamate dal dominio specificato in Azure.

### Come abilitare CORS nel codice dell'API Web

I passaggi seguenti riepilogano il processo di abilitazione del supporto di CORS per l'API Web. Per altre informazioni, vedere [Abilitare la condivisione di richieste tra le origini nelle API Web 2 di ASP.NET](http://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api).

1. In un progetto di API Web, includere una riga di codice `config.EnableCors()` nel metodo **Register** di **WebApiConfig**, come nell'esempio seguente. 

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

1. Nel controller dell'API Web aggiungere l'attributo `EnableCors` alla classe `ContactsController` o a singoli metodi di azione. Nell'esempio seguente il supporto CORS si applica all'intero controller.

		namespace ContactList.Controllers
		{
		    [HttpOperationExceptionFilterAttribute]
		    [EnableCors(origins:"*", headers:"*", methods: "*")]
		    public class ContactsController : ApiController
 
	> **Nota**: l'uso dei caratteri jolly per tutti i parametri con l'attributo `EnableCors` è concepito solo a scopo dimostrativo e rende accessibile l'API a tutte le origini e tutte le richieste HTTP. Usare questo attributo con cautela.

## Passaggi successivi 

In questa esercitazione è stato illustrato come attivare il supporto CORS del servizio app perché il codice del client JavaScript possa chiamare un'API in un dominio diverso. Nel prossimo articolo della serie introduttiva alle app per le API viene illustrata l'[autenticazione per le app per le API del servizio app](app-service-api-authentication.md).

<!---HONumber=AcomDC_0114_2016-->