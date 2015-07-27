<properties 
	pageTitle="Accedere a un'app per le API di Azure usando HTML e JavaScript" 
	description="Informazioni su come accedere al back-end dell'app per le API usando HTML e JavaScript" 
	services="app-service\api" 
	documentationCenter=".net"
	authors="bradygaster"
	manager="mohisri" 
	editor="tdykstra"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="JavaScript" 
	ms.topic="article" 
	ms.date="04/20/2015" 
	ms.author="bradygaster"/>

# Usare un'app per le API di Azure usando HTML e JavaScript

## Panoramica

Questo articolo illustra come creare un client HTML e JavaScript per un'[app per le API](app-service-api-apps-why-best-platform.md) nel [servizio app di Azure](/documentation/services/app-service/). In questo articolo si presuppone che l'utente abbia una conoscenza approfondita dei linguaggi HTML e JavaScript. Verrà usato il framework JavaScript [AngularJS](https://angularjs.org/) per effettuare chiamate REST all'app per le API.

Prima di iniziare, è consigliabile leggere gli articoli seguenti.

1. Nell'esercitazione [Creare un'app per le API](app-service-dotnet-create-api-app.md) è stato creato un progetto di app per le API.
2. Nell'esercitazione [Distribuire un'app per le API](app-service-dotnet-deploy-api-app.md) l'app per le API viene distribuita nella sottoscrizione di Azure.
3. Nell'esercitazione [Eseguire il debug di un'app per le API](../app-service-dotnet-remotely-debug-api-app.md) viene usato Visual Studio per eseguire in modalità remota il debug del codice mentre questo è in esecuzione in Azure.

In questo articolo si farà riferimento alle conoscenze acquisite nelle esercitazioni precedenti per illustrare come le applicazioni HTML possono usare JavaScript per accedere alle app per le API back-end.

## Abilitazione della condivisione CORS

In genere, la condivisione CORS (condivisione di risorse tra le origini) è necessaria nelle applicazioni HTML che verranno servite da host diversi dall'API stessa. Con le app per le API, sono disponibili almeno due opzioni per l'abilitazione della condivisione CORS. Questa sezione descrive entrambe le opzioni.

### Abilitazione della condivisione CORS per i gateway di app per le API

È possibile configurare i gateway di app per le API per abilitare la condivisione CORS tramite il portale di anteprima di Azure. Se si aggiunge l'*impostazione dell'applicazione* **MS_CrossDomainOrigins** è possibile specificare gli URL autorizzati a chiamare l'app per le API. Questa sezione spiega come usare la proprietà *appSetting* per abilitare la condivisione CORS a livello del gateway dell'API.

1. Passare al pannello del portale di anteprima di Azure relativo all'app per le API per cui si intende abilitare la condivisione CORS. Fare clic sull'icona *Gateway* per l'app per le API. 

	![Clic sul pulsante Gateway dell'app per le API](./media/app-service-api-javascript-client/19-api-app-blade.png)

1. Fare clic sul collegamento **Host gateway** nel pannello del portale.

	![Clic sul collegamento host Gateway dell'app per le API](./media/app-service-api-javascript-client/20-gateway-host-blade.png)

1. Fare clic sul collegamento **Tutte le impostazioni** nel pannello del portale.

	![Collegamento Tutte le impostazioni del gateway](./media/app-service-api-javascript-client/21-gateway-blade-all-settings.png)

1. Fare clic sul pulsante **Impostazioni applicazione** nel pannello del portale.

	![Impostazioni applicazione del gateway](./media/app-service-api-javascript-client/22-gateway-app-settings-blade.png)

1. Aggiungere l'impostazione dell'applicazione **MS_CrossDomainOrigins**. Impostare il valore sull'elenco con valori delimitati da virgole relativo agli host HTTP per cui si vuole fornire l'accesso all'app per le API. Se si vuole fornire l'accesso a più host, il valore della proprietà *appSetting* può essere impostato come illustrato nel codice seguente.

		http://foo.azurewebsites.net, https://foo.azurewebsites.net, http://contactlistwebapp.azurewebsites.net

	La sezione seguente illustra come creare un'applicazione Web separata contenente una pagina HTML semplice che chiama l'app per le API da un'app Web in esecuzione nello stesso gruppo di risorse di Azure dell'app per le API. Poiché questo è l'unico host autorizzato ad accedere all'app per le API, questa impostazione verrà configurata per contenere un host.

		http://contactlistwebapp.azurewebsites.net

	La schermata seguente mostra l'aspetto che dovrà avere l'impostazione dopo averla salvata nel portale di anteprima di Azure.

	![](./media/app-service-api-javascript-client/23-app-settings-set.png)

L'impostazione dell'applicazione **MS_CrossDomainOrigins** è descritta in dettaglio nel post di blog relativo agli [aggiornamenti del progetto di servizio mobile di Azure per .NET](http://azure.microsoft.com/blog/2014/07/28/azure-mobile-services-net-updates/). Per altre informazioni sull'impostazione, leggere questo post.

### Abilitazione della condivisione CORS nel codice dell'API Web

La procedura per l'abilitazione della condivisione CORS nell'API Web è descritta in modo dettagliato nell'articolo relativo all'[abilitazione di richieste tra le origini nell'API Web ASP.NET 2](http://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api). Per le app per le API create usando l'API Web ASP.NET il processo è esattamente lo stesso e verrà riepilogato più avanti. Se la condivisione CORS è già stata abilitata, è possibile ignorare questa sezione in quanto l'app per le API dovrebbe già essere configurata correttamente.

1. La funzionalità CORS è fornita dal pacchetto NuGet [Microsoft.AspNet.WebApi.Cors](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Cors/). Per installare il pacchetto, aprire la **Console di Gestione pacchetti** ed eseguire lo script PowerShell seguente. 

		Install-Package Microsoft.AspNet.WebApi.Cors

1. Una volta completata l'esecuzione del comando, la console di Gestione pacchetti e il file **packages.config** rifletteranno l'aggiunta del nuovo pacchetto NuGet.

	![File apiapp.json e cartella Metadata in Esplora soluzioni](./media/app-service-api-javascript-client/01-cors-installed.png)

1. Aprire il file *App_Start/WebApiConfig.cs*. Aggiungere la riga di codice seguente al metodo **Register** della classe **WebApiConfig** nel file.

		config.EnableCors();

	Dopo aver aggiornato il file, il codice avrà un aspetto simile al seguente:

		public static class WebApiConfig
	    {
	        public static void Register(HttpConfiguration config)
	        {
	            // Web API configuration and services
	            
				config.EnableCors(); /* -- NEW CODE -- */
	
	            // Web API routes
	            config.MapHttpAttributeRoutes();
	
	            config.Routes.MapHttpRoute(
	                name: "DefaultApi",
	                routeTemplate: "api/{controller}/{id}",
	                defaults: new { id = RouteParameter.Optional }
	            );
	        }
	    }

1. L'ultimo passaggio per l'abilitazione della condivisione CORS consiste nell'indicare i singoli metodi di azione da abilitare. Aggiungere l'attributo **EnableCors** in ogni metodo o nell'intero controller, come descritto nel codice seguente.

	> **Nota**: l'uso dei caratteri jolly per tutti i parametri con l'attributo EnableCors è consentito solo a fini dimostrativi e comporta l'abilitazione della condivisione per tutte le origini e tutte le richieste HTTP. Usare questo attributo con cautela, valutando i rischi.

		using ContactList.Models;
		using System.Collections.Generic;
		using System.Web.Http;
		using System.Web.Http.Cors;
	
		namespace ContactList.Controllers
		{
		    [EnableCors(origins:"*", headers:"*", methods: "*")] /* -- NEW CODE -- */
		    public class ContactsController : ApiController
		    {
		        [HttpGet]
		        public IEnumerable<Contact> Get()
		        {
		            return new Contact[]
		            {
		                new Contact { Id = 1, EmailAddress = "barney@contoso.com", Name = "Barney Poland"},
		                new Contact { Id = 2, EmailAddress = "lacy@contoso.com", Name = "Lacy Barrera"},
		                new Contact { Id = 3, EmailAddress = "lora@microsoft.com", Name = "Lora Riggs"}
		            };
		        }
		
		        [HttpPost]
		        public Contact Post([FromBody] Contact contact)
		        {
		            return contact;
		        }
		    }
		}
1. Se l'app per le API è già stata distribuita in Azure prima dell'aggiunta del supporto per la condivisione CORS, distribuire di nuovo il codice per abilitare la condivisione nell'API ospitata da Azure. 

## Creazione di un'app Web per l'utilizzo dell'app per le API

In questa sezione verrà creata un'applicazione Web vuota, verrà installato e usato AngularJS e si assocerà un semplice front-end HTML all'app per le API. L'app Web verrà distribuita nel servizio app di Azure. L'app Web HTML verrà associata all'app per le API, visualizzerà i dati da essa recuperati e fornirà agli utenti una semplice interfaccia utente per l'API Contacts.

1. Fare clic con il pulsante destro del mouse sulla soluzione creata in precedenza nell'esercitazione [Creare un'app per le API](app-service-dotnet-create-api-app.md) e scegliere **Aggiungi -> Nuovo progetto**

	![File apiapp.json e cartella Metadata in Esplora soluzioni](./media/app-service-api-javascript-client/02-add-project.png)

1. Selezionare il modello **Applicazione Web ASP.NET**.

	![File apiapp.json e cartella Metadata in Esplora soluzioni](./media/app-service-api-javascript-client/03-new-web-project.png)

1. Selezionare il modello **Vuoto** dalla finestra di dialogo ASP.NET.

	![File apiapp.json e cartella Metadata in Esplora soluzioni](./media/app-service-api-javascript-client/04-empty-web.png)

1. Selezionare **Gestione pacchetti** o scegliere la voce del menu di scelta rapida **Gestisci pacchetti NuGet** per installare il pacchetto NuGet [AngularJS](https://www.nuget.org/packages/angularjs).

	![File apiapp.json e cartella Metadata in Esplora soluzioni](./media/app-service-api-javascript-client/05-install-angular.png)

1. Selezionare **Gestione pacchetti** o scegliere la voce del menu di scelta rapida **Gestisci pacchetti NuGet** per installare il pacchetto NuGet [Bootstrap](https://www.nuget.org/packages/bootstrap).

	![File apiapp.json e cartella Metadata in Esplora soluzioni](./media/app-service-api-javascript-client/05-install-bootstrap.png)

1. Aggiungere un nuovo file HTML per il progetto di applicazione Web.

	![File apiapp.json e cartella Metadata in Esplora soluzioni](./media/app-service-api-javascript-client/06-new-html-file.png)

1. Assegnare al file il nome *index.html*.

	![File apiapp.json e cartella Metadata in Esplora soluzioni](./media/app-service-api-javascript-client/07-index-html.png)

1. Aggiungere i file JavaScript AngularJS e CSS Bootstrap alla pagina HTML, usare un modello Bootstrap semplice ([come questo](http://getbootstrap.com/examples/starter-template/)) e creare un tag di script vuoto per preparare la pagina.
	
	> Nota: i commenti nel codice HTML e JavaScript descrivono i passaggi successivi illustrati in questa sezione.

		<!DOCTYPE html>
		<html xmlns="http://www.w3.org/1999/xhtml">
		<head>
		    <title>Contacts HTML Client</title>
		    <link href="Content/bootstrap.css" rel="stylesheet" />
		    <style type="text/css">
		        body {
		            margin-top: 60px;
		        }
		    </style>
		</head>
		<body>
		
		    <nav class="navbar navbar-inverse navbar-fixed-top">
		        <div class="container">
		            <div class="navbar-header">
		                <button type="button" class="navbar-toggle collapsed" data-toggle="collapse" data-target="#navbar" aria-expanded="false" aria-controls="navbar">
		                    <span class="sr-only">Toggle navigation</span>
		                    <span class="icon-bar"></span>
		                    <span class="icon-bar"></span>
		                    <span class="icon-bar"></span>
		                </button>
		                <a class="navbar-brand" href="#">Contacts</a>
		            </div>
		            <div id="navbar" class="collapse navbar-collapse">
		                <ul class="nav navbar-nav"></ul>
		            </div>
		        </div>
		    </nav>
		
		    <div class="container">
		        <!-- contacts ui here -->
		    </div>
		
		    <script src="Scripts/angular.js" type="text/javascript"></script>
		    <script type="text/javascript">
		        /* client javascript code here */
		    </script>
		
		</body>
		</html>

1. Aggiungere il codice della tabella HTML seguente nell'elemento **container** *div* nel codice HTML.

		<!-- contacts ui here -->
        <table class="table table-striped" ng-app="myApp" ng-controller="contactListCtrl">
            <thead>
                <tr>
                    <th>ID</th>
                    <th>Name</th>
                    <th>Email</th>
                    <th></th>
                </tr>
            </thead>
            <tbody>
                <tr ng-repeat="con in contacts">
                    <td>[[con.Id]]</td>
                    <td>[[con.Name</td>
                    <td>[[con.EmailAddress]]</td>
                    <td></td>
                </tr>
            </tbody>
            <tfoot>
                <tr>
                    <th>Create a new Contact</th>
                    <th colspan="2">API Status: [[status]]</th>
                    <th><button class="btn btn-sm btn-info" ng-click="refresh()">Refresh</button></th>
                </tr>
                <tr>
                    <td><input type="text" placeholder="ID" ng-model="newId" /></td>
                    <td><input type="text" placeholder="Name" ng-model="newName" /></td>
                    <td><input type="text" placeholder="Email Address" ng-model="newEmail" /></td>
                    <td><button class="btn btn-sm btn-success" ng-click="create()">Create</button></td>
                </tr>
            </tfoot>
        </table>

1. Negli elementi `tbody` e `tfoot` sostituire ogni parentesi [ con una parentesi { e ogni parentesi ] con una parentesi }. Al momento questo sito non è in grado di visualizzare le espressioni con doppie parentesi graffe nei blocchi di codice.

2. Fare clic con il pulsante destro del mouse sul file *index.html* e scegliere **Imposta come pagina iniziale**.

3. Fare clic con il pulsante destro del mouse sul file *index.html* e scegliere **Visualizza nel browser**.

	Notare gli elementi handlebars del modello nell'output HTML. Questi elementi HTML verranno associati ai dati tramite AngularJS nel passaggio successivo.

	![File apiapp.json e cartella Metadata in Esplora soluzioni](./media/app-service-api-javascript-client/09-template-ui.png)

1. Aggiungere il codice JavaScript seguente al file *index.html* per chiamare l'API e associare i dati dell'interfaccia utente HTML all'output dell'API.

		/* client javascript code here */
        angular.module('myApp', []).controller('contactListCtrl', function ($scope, $http) {
            $scope.baseUrl = 'http://localhost:1578';

            $scope.refresh = function () {
                $scope.status = "Refreshing Contacts...";
                $http({
                    method: 'GET',
                    url: $scope.baseUrl + '/api/contacts',
                    headers: {
                        'Content-Type': 'application/json'
                    }
                }).success(function (data) {
                    $scope.contacts = data;
                    $scope.status = "Contacts loaded";
                }).error(function (data, status) {
                    $scope.status = "Error loading contacts";
                });
            };

            $scope.create = function () {
                $scope.status = "Creating a new contact";

                $http({
                    method: 'POST',
                    url: $scope.baseUrl + '/api/contacts',
                    headers: {
                        'Content-Type': 'application/json'
                    },
                    data: {
                        'Id': $scope.newId,
                        'Name': $scope.newName,
                        'EmailAddress': $scope.newEmail
                    }
                }).success(function (data) {
                    $scope.status = "Contact created";
                    $scope.refresh();
                    $scope.newId = 0;
                    $scope.newName = '';
                    $scope.newEmail = '';
                });
            };

            $scope.refresh();
        });

1\. Nel codice appena aggiunto al file index.html sostituire il numero di porta nell'URL di base (`http://localhost:1578`) con il numero di porta effettivo per il progetto API.

	> **Note** Don't use the port number of the HTML client project. You can right-click the API project and click **Debug > Start New Instance** to get a browser window that shows the port number.

1. Assicurarsi che quando si esegue il client HTML sia in esecuzione anche il progetto di app per le API, perché in caso contrario il codice HTML JavaScript non funzionerà correttamente. Fare clic con il pulsante destro del mouse sulla soluzione e scegliere **Proprietà**. Impostare quindi entrambi i progetti su **Avvia senza eseguire debug** e assicurarsi che il progetto API venga eseguito per primo. 

	![File apiapp.json e cartella Metadata in Esplora soluzioni](./media/app-service-api-javascript-client/10-run-both-web-projects.png)

1. Eseguire la soluzione e verificare che il client HTML/JavaScript si connetta al progetto di app per le API e che visualizzi i dati del progetto.

	![File apiapp.json e cartella Metadata in Esplora soluzioni](./media/app-service-api-javascript-client/11-web-client-running.png)

## Distribuzione dell'app Web

In questa sezione verrà distribuito il client HTML/JavaScript come un'app Web del servizio app. Al termine della distribuzione, sarà necessario modificare l'URL usato da JavaScript per usare l'app per le API distribuita.

> Nota: in questa sezione si presuppone che l'utente abbia letto e completato l'esercitazione [Distribuire un'app per le API](app-service-dotnet-deploy-api-app.md) o che abbia già distribuito in precedenza un'app per le API.

1. Nel portale di anteprima di Azure aprire il pannello dell'app per le API. Fare clic sull'URL nel pannello per aprirlo nel browser. Una volta aperto, copiare l'URL dell'app per le API dalla barra degli indirizzi del browser. 

	![File apiapp.json e cartella Metadata in Esplora soluzioni](./media/app-service-api-javascript-client/12-open-api-app-from-blade.png)

1. Incollare l'URL dell'app per le API in modo da sovrascrivere il valore precedente per la proprietà **$scope.baseUrl** nel codice JavaScript.

		$scope.baseUrl = 'https://microsoft-apiappf7e042ba8e5233ab4312021d2aae5d86.azurewebsites.net';

	Notare che l'URL specifica HTTPS. L'uso di HTTPS non è facoltativo, in quanto le app per le API non supportano HTTP.

1. Fare clic con il pulsante destro del mouse sul progetto Web HTML/JavaScript e scegliere **Pubblica** nel menu di scelta rapida.

	![File apiapp.json e cartella Metadata in Esplora soluzioni](./media/app-service-api-javascript-client/13-publish-web-app.png)

1. Nella finestra di dialogo Pubblica sul Web selezionare l'opzione **App Web di Microsoft Azure**.

	![File apiapp.json e cartella Metadata in Esplora soluzioni](./media/app-service-api-javascript-client/14-publish-web-dialog.png)

1. Fare clic sul pulsante **Nuova** per creare una nuova app Web.

	![File apiapp.json e cartella Metadata in Esplora soluzioni](./media/app-service-api-javascript-client/15-new-web-app.png)

1. Selezionare lo stesso piano di hosting dell'app e lo stesso gruppo di risorse in cui l'app per le API è in esecuzione.

	> **Nota**: questo non è un requisito, ma ai fini della dimostrazione consente di semplificare la pulizia delle risorse di Azure in un secondo tempo, se tutti gli elementi sono contenuti in un solo gruppo di risorse.

	![File apiapp.json e cartella Metadata in Esplora soluzioni](./media/app-service-api-javascript-client/16-new-web-app-creation-dialog.png)

1. Completare la procedura di pubblicazione sul Web per distribuire il client HTML/Javascript nelle app Web del servizio app.
1. Una volta distribuita l'app Web, verrà aperta automaticamente nel Web browser e visualizzerà i dati dall'app per le API. 

	![File apiapp.json e cartella Metadata in Esplora soluzioni](./media/app-service-api-javascript-client/17-web-app-running-in-ie.png)

1. A questo punto, se si passa al gruppo di risorse è possibile visualizzare la nuova app Web in esecuzione insieme all'app per le API.

	![File apiapp.json e cartella Metadata in Esplora soluzioni](./media/app-service-api-javascript-client/18-web-app-visible-in-resource-group.png)

## Passaggi successivi 

In questo esempio è stato descritto come usare AngularJS come piattaforma JavaScript per accedere ai back-end delle app per le API. È possibile modificare la funzionalità di accesso REST per usare qualsiasi altro framework JavaScript.

Questo esempio mostra l'accesso non autenticato a un'app per le API. Per informazioni sull'autenticazione nel servizio app, vedere [Autenticazione per app per le API e per dispositivi mobili](../app-service/app-service-authentication-overview.md).

<!---HONumber=July15_HO3-->