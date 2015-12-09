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
	ms.date="11/27/2015"
	ms.author="tdykstra"/>

# Utilizzare un'app per le API da JavaScript tramite CORS

[AZURE.INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

## Panoramica

Questa esercitazione illustra come usare un'app per le API dal codice JavaScript in un sito Web servito da un dominio diverso dall'app per le API. Il client di esempio usa AngularJS.

![](./media/app-service-api-cors-consume-javascript/homepageazure.png)
 
Questa è la seconda di una serie di esercitazioni che illustrano come usare le app per le API nel servizio app di Azure. Per informazioni sulla serie, vedere [Introduzione alle app per le API e ad ASP.NET nel servizio app di Azure](app-service-api-dotnet-get-started.md).

## Introduzione a CORS

Per motivi di sicurezza, il comportamento predefinito dei browser impedisce a JavaScript di eseguire chiamate API a un dominio diverso da quello da cui deriva il codice JavaScript. Ad esempio, è possibile effettuare una chiamata da una pagina Web contoso.com a un endpoint dell'API contoso.com, ma non a un endpoint di fabrikam.com. CORS (Cross Origin Resource Sharing) è un protocollo Internet progettato per abilitare scenari in cui è necessario effettuare tali chiamate API tra domini. In esempio di tale scenario nel servizio app di Azure si ha quando il client JavaScript viene eseguito in un'app Web e l'API viene eseguita in un'app per le API.

In un progetto API Web è possibile installare pacchetti NuGet CORS consentono di specificare nel codice i domini da cui l'API accetterà chiamate JavaScript. In alternativa, è possibile usare la funzionalità CORS incorporata nel servizio app di Azure per specificare da quali domini l'app per le API accetterà le chiamate. La prima metà di questa esercitazione illustra come usare la funzionalità di Azure, che funziona per tutte le lingue supportate dal servizio app per le API. La seconda parte è facoltativa e illustra il metodo del pacchetto NuGet che funziona con l'API Web ASP.NET.

## Progetto di esempio ContactsList.Angular

In questa esercitazione verranno usati i progetti di esempio scaricati nella prima esercitazione di questa serie e le risorse di Azure (app per le API e app Web) create nella prima esercitazione.

Il progetto ContactsList.Angular è un semplice client di AngularJS per il progetto di API Web ContactsList.API. Il codice JavaScript AngularJS che chiama l'API si trova nel file *index.html* nel progetto ContactsList.Angular. Il codice definisce le funzioni e le aggiunge all'oggetto `$scope`, come illustrato di seguito dove il metodo Get dell'API è definito come `$scope.refresh()`.

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

È possibile creare una nuova app Web per distribuire il progetto AngularJS, ma per questa esercitazione si distribuirà la stessa app Web creata in precedenza. Il nome dell'app Web può riflettere il fatto che in questa app è stato distribuito un progetto MVC ASP.NET in origine, ma dopo questa distribuzione eseguirà il codice AngularJS.

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

## Configurare CORS per l'app per le API di destinazione in Azure

8. In un'altra finestra del browser passare al [portale di Azure](https://portal.azure.com/).

9. Passare al pannello App per le API per l'app per le API creata nella prima esercitazione.

10. Fare clic su **Impostazioni**.

11. Trovare la sezione **API** e quindi fare clic su **CORS**.

12. Nella casella di testo immettere l'URL dell'app Web creata nella prima esercitazione per il front-end MVC ASP.NET. Ad esempio, se l'app Web è denominata ContactsListMVC, immettere "http://contactslistmvc.azurewebsites.net".

	Si noti che, in alternativa all'immissione di un URL, è possibile immettere un asterisco (*) per specificare che vengono accettati tutti i domini di origine.

13. Fare clic su **Save**.

	![](./media/app-service-api-cors-consume-javascript/corsinportal.png)

14. Tornare alla finestra del browser che visualizza il client AngularJS in esecuzione nell'app Web di Azure e aggiornare la pagina o fare clic sul pulsante **Aggiorna**.

	Nella pagina ora sono visualizzati i contatti archiviati nel file system dell'app per le API di Azure.

	![](./media/app-service-api-cors-consume-javascript/homepageazure.png)

## Configurare CORS per l'app per le API di destinazione nel codice dell'API Web

Se non si vuole usare il supporto di CORS per il servizio app di Azure, in alternativa è possibile abilitare CORS nel codice dell'API Web ASP.NET. Questo processo è documentato in dettglio nell'articolo relativo all'[abilitazione delle richieste tra origini nell'API Web ASP.NET 2](http://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api). Per le app per le API create con l'API Web ASP.NET il processo è esattamente lo stesso, ma è riepilogato di seguito.

In questa sezione si disabiliterà il supporto di CORS per il servizio app di Azure e quindi si abiliterà il supporto di CORS per l'API Web. La disabilitazione del supporto di CORS per Azure prima di abilitare il supporto dell'API Web non è un passaggio facoltativo. Se si usano entrambi i metodi insieme, CORS per Azure avrà la precedenza e CORS per l'API Web non avrà effetto. Ad esempio, se si abilita un dominio di origine in Azure e si abilitano tutti i domini di origine nel codice dell'API Web, l'app per le API di Azure accetterà solo chiamate dal dominio specificato in Azure.

### Disabilitare il supporto di CORS per Azure

1. Per disabilitare il supporto di CORS per Azure, tornare al portale di Azure e cancellare l'URL immesso nel pannello CORS, quindi fare clic su **Salva**.
 
3.  Tornare all'URL dell'app Web in cui è stata distribuita l'app AngularJS e aggiornare la pagina o fare clic sul pulsante **Aggiorna**.

	Verrà visualizzato di nuovo l'errore relativo al caricamento dei contatti.

	![](./media/app-service-api-cors-consume-javascript/corserror.png)

### Abilitare il supporto di CORS per l'API Web

La funzionalità CORS per l'API Web è fornita dal pacchetto NuGet [Microsoft.AspNet.WebApi.Cors](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Cors/). Il pacchetto è già installato nell'applicazione di esempio scaricata e per abilitare CORS è sufficiente rimuovere il commento in una parte del codice.

1. Nel progetto ContactsList.API aprire il file *App\_Start/WebApiConfig.cs*. Rimuovere il commento dalla riga di codice config.EnableCors nel metodo **Register** di **WebApiConfig**. 

	Dopo aver aggiornato il file, il codice avrà un aspetto simile all'esempio seguente:

		public static class WebApiConfig
	    {
	        public static void Register(HttpConfiguration config)
	        {
	            // Web API configuration and services
	            
		        // Uncomment the following line to control CORS by using Web API code
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

1. Aprire il file *Controllers/ContactsController.cs* e rimuovere il commento dalla riga che aggiunge l'attributo `EnableCors` alla classe `ContactsController`.

		namespace ContactList.Controllers
		{
		    [HttpOperationExceptionFilterAttribute]
		    [EnableCors(origins:"*", headers:"*", methods: "*")]
		    public class ContactsController : ApiController
 
	È anche possibile applicare l'attributo ai singoli metodi di azione invece che all'intero controller.

	> **Nota**: l'uso dei caratteri jolly per tutti i parametri con l'attributo `EnableCors` è concepito solo a scopo dimostrativo e rende accessibile l'API a tutte le origini e tutte le richieste HTTP. Usare questo attributo con cautela.

### Distribuire l'API in Azure e ripetere il test del front-end

8. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto ContactsList.API e quindi scegliere **Pubblica**.

	Viene visualizzata automaticamente la procedura guidata **Pubblica sito Web** in corrispondenza del passaggio **Anteprima** per il profilo di pubblicazione dell'app per le API creata in precedenza, perché è l'ultima destinazione in cui si è distribuito il progetto.

3.  Fare clic su **Pubblica**.

	![](./media/app-service-api-cors-consume-javascript/pubapi.png)

	Una volta completata la distribuzione, nel browser verrà aperta una finestra all'URL dell'app per le API, che visualizza quindi una pagina per confermare che "la creazione dell'app Web è stata completata".

3.  Tornare all'URL dell'app Web in cui è stata distribuita l'app AngularJS e aggiornare la pagina o fare clic sul pulsante **Aggiorna**.

	La pagina viene caricata di nuovo correttamente.

	![](./media/app-service-api-cors-consume-javascript/homepageazure.png)

## Riabilitare il supporto di CORS per Azure

Per usare i servizi di autenticazione predefiniti di Azure nelle esercitazioni seguenti, è necessario usare CORS di Azure invece di CORS per l'API Web.
 
1. Per abilitare il supporto di CORS per Azure, tornare al pannello CORS nel portale di Azure per l'app per le API e immettere di nuovo l'URL dell'app Web.

13. Fare clic su **Salva**.

	![](./media/app-service-api-cors-consume-javascript/corsinportal.png)

## Passaggi successivi 

In questa esercitazione si sono visti due modi per attivare il supporto di CORS in modo che il codice del client JavaScript possa chiamare un'API in un dominio diverso. Nell'esercitazione successiva si apprenderà come [limitare l'accesso a un'app per le API in modo che possano accedervi solo gli utenti autenticati](app-service-api-dotnet-user-principal-auth.md).

<!---HONumber=AcomDC_1203_2015-->