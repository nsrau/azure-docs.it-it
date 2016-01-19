<properties
	pageTitle="Autenticazione utente per le app per le API nel servizio app di Azure | Microsoft Azure"
	description="Informazioni su come proteggere un'app per le API nel servizio app di Azure consentendo l'accesso solo agli utenti autenticati."
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
	ms.topic="hero-article"
	ms.date="01/08/2016"
	ms.author="tdykstra"/>

# Autenticazione utente per le app per le API nel servizio app di Azure

[AZURE.INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

## Panoramica

Questa esercitazione illustra come usare le funzionalità di autenticazione e autorizzazione del servizio app di Azure per proteggere un'app per le API e come utilizzare le app per le API per conto degli utenti finali. Il provider di autenticazione illustrato nell'esercitazione è Azure Active Directory, l'API è API Web ASP.NET e il client è un'applicazione a singola pagina AngularJS in esecuzione in un browser.

![](./media/app-service-api-dotnet-user-principal-auth/contactspageazure.png)
 
## Autenticazione e autorizzazione nel servizio app

Per un'introduzione alle funzionalità di autenticazione usate in questa esercitazione, vedere l'esercitazione precedente della serie [Autenticazione e autorizzazione per app per le API nel servizio app di Azure](app-service-api-authentication.md).

## Come seguire questa esercitazione

Questa esercitazione si basa su un'applicazione di esempio per cui viene scaricata e creata un'app per le API nella [prima esercitazione della serie introduttiva alle app per le API e ASP.NET](app-service-api-dotnet-get-started.md).

## Progetto di esempio ContactsList.Angular.AAD

Nell'[applicazione di esempio ContactsList](https://github.com/Azure-Samples/app-service-api-dotnet-contact-list) il progetto ContactsList.Angular.AAD è un client AngularJS che include il codice necessario per l'uso con Azure Active Directory. Il codice è basato su un esempio per AAD disponibile nel repository [Azure-Samples/active-directory-angularjs-singlepageapp-dotnet-webapi](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp-dotnet-webapi).

Il codice nel progetto ContactsList.Angular.AAD è strutturato in modo diverso rispetto al progetto ContactsLists.Angular più semplice. Il codice che chiama l'API si trova nel file *app/scripts/contactsSvc.js* nel progetto ContactsList.Angular.AAD.

		angular.module('contactsListApp')
		.factory('contactsSvc', ['$http', function ($http) {
		    //var apiEndpoint = "https://{your api app name}.azurewebsites.net";
		    var apiEndpoint = "https://localhost:44300";
		
		    $http.defaults.useXDomain = true;
		    delete $http.defaults.headers.common['X-Requested-With']; 
		
		    return {
		        getItems: function () {
		            return $http.get(apiEndpoint + '/api/contacts');
		        },
		        getItem : function(id){
		            return $http.get(apiEndpoint + '/api/contacts/' + id);
		        },
		        postItem : function(item){
		            return $http.post(apiEndpoint + '/api/contacts', item);
		        },
		        putItem : function(item){
		            return $http.put(apiEndpoint + '/api/contacts/', item);
		        },
		        deleteItem : function(id){
		            return $http({
		                method: 'DELETE',
		                url: apiEndpoint + '/api/contacts/' + id
		            });
		        }
		    };
		}]);

In questo caso, il metodo `Get` è denominato `getItems`. Nel controller (*app/scripts/contactsCtrl.js*) `getItems` è associato all'oggetto `$scope.populate`.

		$scope.populate = function () {
		    contactsSvc.getItems().then(function (results) {
		        $scope.contactsList = results.data;
		        $scope.loadingMessage = "";
		    }, function (err) {
		        $scope.loadingMessage = "";
		        $scope.error = "Error: " + err;
		    });
		};

Nella visualizzazione (*app/views/Contacts.html*) l'oggetto $scope.populate viene chiamato durante l'inizializzazione.

		<div ng-init="populate()">

Il codice aggiuntivo per eseguire l'accesso e includere un token di autorizzazione con le richieste API viene fornito da [Azure Active Directory Authentication Library per JavaScript](https://github.com/AzureAD/azure-activedirectory-library-for-js), nei file *adal.js* e *adal-angular.js*.

Nel file *app.js* il codice passa le informazioni di configurazione e il provider `$http` alla funzione `adalProvider.init`. Le informazioni di configurazione includono l'ID client dell'applicazione AAD relativo a ogni endpoint dell'API e l'ID client relativo a questa app AngularJS. La funzione `init` aggiunge intercettori al provider `$http`, che questi aggiungono al token di autorizzazione per le richieste.

		var endpoints = { 
		    //"https://{your api app name}.azurewebsites.net/": "{your client id}"
		    "https://localhost:44300/": "{your client id}"
		};

		adalProvider.init(
		    {
		        instance: 'https://login.microsoftonline.com/', 
		        tenant: '{your tenant url}',
		        clientId: '{your client id}',
		        extraQueryParameter: 'nux=1',
		        endpoints: endpoints
		        //cacheLocation: 'localStorage', // enable this for 
		    },
		    $httpProvider
		    );

## Configurare l'autenticazione e l'autorizzazione in Azure

1. Nel [portale di Azure](https://portal.azure.com/) passare al pannello **App per le API** dell'app per le API da proteggere in modo che possa essere chiamata solo da utenti autenticati. Per questa esercitazione, scegliere l'app per le API in cui è stato distribuito il progetto ContactsList.API.

2. Fare clic su **Impostazioni**.

2. Trovare la sezione **Funzionalità** e quindi fare clic su **Autenticazione/Autorizzazione**.

	![](./media/app-service-api-dotnet-user-principal-auth/features.png)

3. Nel pannello **Autenticazione/Autorizzazione** fare clic su **Sì**.

4. Nell'elenco a discesa **Azione da eseguire quando la richiesta non è autenticata** selezionare **Accedi con Azure Active Directory**.

5. In **Provider di autenticazione** fare clic su **Azure Active Directory**.

	![](./media/app-service-api-dotnet-user-principal-auth/authblade.png)

6. Nel pannello **Impostazioni di Azure Active Directory** fare clic su **Rapide**.

	![](./media/app-service-api-dotnet-user-principal-auth/aadsettings.png)

	In questo modo Azure creerà automaticamente un'applicazione AAD nel tenant AAD. Prendere nota del nome della nuova applicazione AAD, perché sarà necessario selezionarlo più avanti quando si passerà al portale di Azure classico per ottenere l'ID client della nuova applicazione AAD.

7. Fare clic su **OK**.

10. Nel pannello **Autenticazione/Autorizzazione** fare clic su **Salva**.

8. Per verificare che l'app per le API sia protetta, passare all'URL dell'app per le API e scegliere + `/swagger` come nella prima esercitazione per usare l'interfaccia utente di Swagger.

	Questa volta si verrà reindirizzati a una pagina di accesso.

	![](./media/app-service-api-dotnet-user-principal-auth/loginpage.png)

11. Nel [portale di Azure classico](https://manage.windowsazure.com/) passare a **Azure Active Directory**.

	È necessario accedere al portale classico perché alcune impostazioni di Azure Active Directory a cui è necessario accedere non sono ancora disponibili nel portale di Azure corrente.

12. Nella scheda **Directory** fare clic sul tenant AAD.

	![](./media/app-service-api-dotnet-user-principal-auth/selecttenant.png)

14. Fare clic su **Applicazioni > Applicazioni di proprietà dell'azienda** e quindi fare clic sul segno di spunta.

	Per visualizzare la nuova applicazione potrebbe essere necessario aggiornare la pagina.

15. Nell'elenco delle applicazioni fare clic sul nome di quella creata automaticamente da Azure quando è stata abilitata l'autenticazione per l'app per le API.

	![](./media/app-service-api-dotnet-user-principal-auth/appstab.png)

16. Fare clic su **Configure**.

	![](./media/app-service-api-dotnet-user-principal-auth/configure.png)

15. Nella parte inferiore della pagina fare clic su **Gestisci manifesto > Scarica manifesto** e salvare il file in un percorso in cui è possibile modificarlo.

16. Nel file manifesto scaricato cercare la proprietà `oauth2AllowImplicitFlow`. Modificare il valore di questa proprietà da `false` a `true` e quindi salvare il file.

	Questa impostazione è necessaria per l'accesso da un'applicazione a singola pagina JavaScript e consente di restituire il token di connessione Oauth 2.0 nel frammento di URL.

16. Fare clic su **Gestisci manifesto > Carica manifesto** e caricare il file aggiornato nel passaggio precedente.

17. Tenere aperta questa pagina per poter copiare e incollare i valori da qui e aggiornare i valori nella pagina nei passaggi successivi dell'esercitazione.

## Configurare il progetto ContactsList.Angular.AAD per chiamare l'app per le API di Azure

Le istruzioni riportate di seguito illustrano come distribuire l'applicazione ed eseguirla in Azure, ma con alcune modifiche è possibile eseguirla in locale. Il codice di esempio contiene endpoint di URL localhost. Per l'esecuzione in locale, configurare i progetti per SSL e usare gli URL SSL localhost nel codice del progetto e nella configurazione dell'applicazione AAD. Durante l'esecuzione in locale, il codice AngularJS consente solo agli utenti collegati di chiamare l'API, che però potrebbe essere chiamata anche da chiamanti non autenticati da altri client.

1. Nel progetto ContactsList.Angular.AAD aprire il file *app/scripts/app.js*.

8. Nel codice che imposta la variabile `endpoints` impostare come commento l'endpoint localhost e rimuovere il commento dall'endpoint di Azure.

10. Sostituire "yourclientid" con il valore effettivo dell'ID client dell'applicazione AAD nella scheda **Configura** del portale classico per l'applicazione AAD.

2. Sostituire "{your api app name}" con il nome dell'app per le API in cui è stato distribuito il progetto ContactsList.API.

	Il codice avrà un aspetto simile all'esempio seguente:

		var endpoints = {
		    "https://contactslistapi.azurewebsites.net/": "1cf55bc9-9ed8-4df31cf55bc9-9ed8-4df3"
		    //"https://localhost:44300/": "1cf55bc9-9ed8-4df31cf55bc9-9ed8-4df3"
		};

9. Nel file *app.js*, nella chiamata a `adalProvider.init` sostituire "{your tenant url}" e "{your client id}" con i valori effettivi.

	Il codice avrà un aspetto simile all'esempio seguente:

		adalProvider.init(
		    {
		        instance: 'https://login.microsoftonline.com/', 
		        tenant: 'contoso.onmicrosoft.com',
		        clientId: '1cf55bc9-9ed8-4df31cf55bc9-9ed8-4df3',
		        extraQueryParameter: 'nux=1',
		        endpoints: endpoints
		    },
		    $httpProvider
		    );

1. Nel file *app/scripts/contactsSvc.js* apportare la stessa modifica all'URL dell'endpoint, da localhost a URL di app per le API, fatta in precedenza in *app.js*.

	Il codice avrà un aspetto simile all'esempio seguente:

		var apiEndpoint = "https://contactslistapi.azurewebsites.net";
		//var apiEndpoint = "https://localhost:44300";

## Configurare l'applicazione AAD per l'app Web di Azure

1. Nella scheda **Configura** per l'applicazione AAD del portale classico eliminare l'URL già presente nel campo **URL di accesso** e sostituirlo con l'URL di base dell'app Web inclusa la barra finale. Si noti che questo è l'URL dell'app Web che eseguirà il codice AngularJS, non l'URL dell'app per le API.

	![](./media/app-service-api-dotnet-user-principal-auth/signonurlazure.png)

3. Nel campo **URL di risposta** sostituire l'URL già presente con l'URL di base dell'app Web.

	![](./media/app-service-api-dotnet-user-principal-auth/replyurlazure.png)

4. Fare clic su **Salva**.

## Distribuire il progetto ContactsList.Angular.AAD in Azure

8. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto ContactsList.Angular.AAD e quindi scegliere **Pubblica**.

9. Fare clic su **Servizio app di Microsoft Azure**.

10. Nell'elenco a discesa **Sottoscrizione** della finestra di dialogo **Servizio app** selezionare la sottoscrizione.

11. Espandere il gruppo di risorse creato per questa esercitazione e selezionare l'app Web creata nella seconda esercitazione.

	![](./media/app-service-api-dotnet-user-principal-auth/deploytowebapp.png)

12. Fare clic su **OK**.

12. Nella procedura guidata **Pubblica sul Web** fare clic sulla scheda **Connessione** e nella casella **URL di destinazione** sostituire `http://` con `https://`

	![](./media/app-service-api-dotnet-user-principal-auth/httpsinconntab.png)

	Questa impostazione determina l'URL che viene aperto nel browser predefinito dopo che è stata completata la distribuzione.

12. Nella procedura guidata **Pubblica sul Web** fare clic sulla scheda **Impostazioni**, espandere **Opzioni pubblicazione file** e quindi selezionare la casella di controllo **Rimuovi i file aggiuntivi nella destinazione**.

7. Fare clic su **Pubblica**.

	Visual Studio distribuisce il progetto e apre una finestra del browser alla pagina iniziale dell'applicazione.

## Testare l'app Web AngularJS in Azure

8. Fare clic sulla scheda **Contatti**.

	Verrà richiesto di effettuare l'accesso.

9. Accedere con le credenziali di un utente che appartiene al tenant AAD.

10. Verrà visualizzata la pagina **Contatti**.

	![](./media/app-service-api-dotnet-user-principal-auth/contactspageazure.png)

A questo punto, il front-end può chiamare l'API per conto di un utente autenticato, ma gli utenti non autenticati non potranno chiamare l'API.

## Passaggi successivi

In questa esercitazione è stata usata la funzionalità di autenticazione/autorizzazione del servizio app per limitare l'accesso a un'app per le API in modo che possa essere chiamata solo agli utenti autenticati. Nell'esercitazione successiva della serie si apprenderà come [limitare l'accesso all'app per le API per gli scenari da servizio a servizio](app-service-api-dotnet-service-principal-auth.md).

<!---HONumber=AcomDC_0114_2016-->