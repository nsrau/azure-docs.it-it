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
	ms.date="11/30/2015"
	ms.author="tdykstra"/>

# Autenticazione utente per le app per le API nel servizio app di Azure

[AZURE.INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

## Panoramica

Questa esercitazione illustra come usare le funzionalità di autenticazione e autorizzazione del servizio app di Azure per proteggere un'app per le API e come utilizzare le app per le API per conto degli utenti finali. Il provider di autenticazione illustrato nell'esercitazione è Azure Active Directory e il client di esempio è un'applicazione a singola pagina AngularJS in esecuzione in un browser.

![](./media/app-service-api-dotnet-user-principal-auth/contactspageazure.png)
 
Questa è la terza di una serie di esercitazioni che illustrano come usare le app per le API nel servizio app di Azure. Per informazioni sulla serie, vedere [Introduzione alle app per le API e ad ASP.NET nel servizio app di Azure](app-service-api-dotnet-get-started.md).

## Autenticazione e autorizzazione nel servizio app di Azure

Il servizio app di Azure offre servizi incorporati per l'autenticazione e autorizzazione. È anche possibile gestire l'autenticazione nel codice, ma il servizio app offre una soluzione chiavi in mano per ridurre al minimo la quantità di codice da scrivere e gestire.

È possibile usare uno qualsiasi dei cinque provider di autenticazione supportati dal servizio app: Azure Active Directory, Facebook, Google, Twitter e account Microsoft. È anche possibile proteggere un'API scritta in qualsiasi linguaggio supportato dal servizio app. Senza scrivere neanche una riga di codice nell'API, si può richiedere che per accedere all'API sia necessario eseguire l'accesso o usare un token.

Il servizio app di Azure gestisce l'autenticazione e lascia che l'autorizzazione sia gestita dal codice. È possibile configurare il servizio app per consentire solo agli utenti autenticati o a tutti gli utenti di chiamare l'API. In entrambi i casi, il servizio app passa le informazioni di autenticazione all'app nelle intestazioni HTTP e il codice può usare tali informazioni per prendere le decisioni appropriate circa l'autorizzazione.

* In un'API .NET è possibile usare l'attributo `Authorize` e per l'autorizzazione con granularità fine è possibile scrivere facilmente codice basato su attestazioni. Le informazioni sulle attestazioni sono popolate automaticamente nelle classi .NET.

* Per le API scritte in altri linguaggi, il servizio app passa il token JWT nell'intestazione dell'autorizzazione di una richiesta HTTP. Azure imposta anche alcune intestazioni speciali (ad esempio, `x-ms-client-principal-id`) per fornire l'accesso semplificato alle attestazioni più importanti.

Per altre informazioni sui servizi di autenticazione e autorizzazione nel servizio app di Azure, vedere [Espansione di autenticazione e autorizzazione del servizio app](/blog/announcing-app-service-authentication-authorization/) e [App per le API del servizio app - Modifiche apportate](app-service-api-whats-changed.md).

## Progetto di esempio ContactsList.Angular.AAD

In questa esercitazione verranno usati i progetti di esempio scaricati nella [prima esercitazione di questa serie](app-service-api-dotnet-get-started.md) e le risorse di Azure (app per le API e app Web) create nella prima esercitazione.

Il progetto ContactsList.Angular.AAD è un client AngularJS che include il codice necessario per l'utilizzo con Azure Active Directory. Il codice è basato su un esempio per AAD disponibile nel repository [Azure-esempi/active-directory-angularjs-singlepageapp](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp).

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

In questo caso, il metodo `Get` è denominato `getItems` e, nel controller (*app/scripts/contactsCtrl.js*), `getItems` è associato all'oggetto `$scope.populate`.

		$scope.populate = function () {
		    contactsSvc.getItems().then(function (results) {
		        $scope.contactsList = results.data;
		        $scope.loadingMessage = "";
		    }, function (err) {
		        $scope.loadingMessage = "";
		        $scope.error = "Error: " + err;
		    });
		};

Nella visualizzazione (*app/views/Contacts.html*), l'oggetto $scope.populate viene chiamato durante l'inizializzazione.

		<div ng-init="populate()">

## Configurare l'autenticazione e l'autorizzazione in Azure

1. Nel [portale di Azure](https://portal.azure.com/) passare al pannello **App per le API** dell'app per le API creata nella prima esercitazione e quindi fare clic su **Impostazioni**.

2. Trovare la sezione **Funzionalità** e quindi fare clic su **Autenticazione/Autorizzazione**.

	![](./media/app-service-api-dotnet-user-principal-auth/features.png)

3. Nel pannello **Autenticazione/Autorizzazione** fare clic su **Sì**.

4. Nell'elenco a discesa **Azione da eseguire quando la richiesta non è autenticata** selezionare **Accedi con Azure Active Directory**.

5. In **Provider di autenticazione** fare clic su **Azure Active Directory**.

	![](./media/app-service-api-dotnet-user-principal-auth/authblade.png)

6. Nel pannello **Impostazioni di Azure Active Directory** fare clic su **Rapide**

	![](./media/app-service-api-dotnet-user-principal-auth/aadsettings.png)

	Azure creerà automaticamente un'applicazione AAD nel tenant AAD. Prendere nota del nome della nuova applicazione AAD, perché sarà necessario selezionarlo più avanti quando si passerà al portale di Azure classico per ottenere l'ID client della nuova applicazione AAD.

7. Fare clic su **OK**.

10. Nel pannello **Autenticazione/Autorizzazione** fare clic su **Salva**.

8. Per verificare che l'app per le API sia protetta, passare all'URL dell'app per le API e scegliere + `/swagger` come nella prima esercitazione per usare l'interfaccia utente di Swagger.

	Questa volta si verrà reindirizzati a una pagina di accesso.

	![](./media/app-service-api-dotnet-user-principal-auth/loginpage.png)

11. Nel [portale di Azure classico](https://manage.windowsazure.com/) passare a **Azure Active Directory**.

12. Nella scheda Directory fare clic sul tenant AAD.

	![](./media/app-service-api-dotnet-user-principal-auth/selecttenant.png)

14. Fare clic su **Applicazioni > Applicazioni di proprietà dell'azienda** e quindi fare clic sul segno di spunta.

	Per visualizzare la nuova applicazione potrebbe essere necessario aggiornare la pagina.

15. Nell'elenco delle applicazioni fare clic sul nome di quella creata automaticamente da Azure quando è stata abilitata l'autenticazione per l'app per le API.

	![](./media/app-service-api-dotnet-user-principal-auth/appstab.png)

16. Fare clic su **Configura**.

	![](./media/app-service-api-dotnet-user-principal-auth/configure.png)

15. Nella parte inferiore della pagina fare clic su **Gestisci manifesto > Scarica manifesto** e salvare il file in un percorso in cui è possibile modificarlo.

16. Nel file manifesto scaricato cercare la proprietà `oauth2AllowImplicitFlow`. Modificare il valore di questa proprietà da `false` a `true` e quindi salvare il file.

16. Fare clic su **Gestisci manifesto > Carica manifesto** e caricare il file aggiornato nel passaggio precedente.

17. Tenere aperta questa pagina per poter copiare e incollare i valori da qui e aggiornare i valori nella pagina nei passaggi successivi dell'esercitazione.

## Configurare i progetti di Visual Studio per HTTPS

1. In **Esplora soluzioni** fare clic sul progetto ContactList.API e quindi nella finestra **Proprietà** impostare **SSL abilitato** su **True**.

2. Copiare il valore URL SSL.

	![](./media/app-service-api-dotnet-user-principal-auth/enablessl.png)

3. Fare clic con il pulsante destro del mouse sul progetto ContactsList.API e quindi scegliere **Proprietà**.

5. Fare clic sulla scheda **Web**, incollare l'URL SSL nel campo **URL progetto** e quindi salvare le modifiche.

	![](./media/app-service-api-dotnet-user-principal-auth/setprojecturl.png)

1. Seguire la stessa procedura per abilitare SSL per il progetto ContactsList.Angular.AAD.

2. Impostare il progetto ContactsList.API e i progetti ContactsList.Angular.AAD come progetti di avvio e impostare il progetto ContactsList.API per essere avviato per primo.

## Aggiornare le impostazioni relative ad AAD e all'URL dell'endpoint nel progetto ContactsList.Angular.AAD.

7. Nel progetto ContactsList.Angular.AAD aprire il file *app/scripts/app.js*.

8. Nel codice che imposta la variabile `endpoints` assicurarsi che sia selezionato l'URL SSL corretto per il progetto ContactsList.API e sostituire entrambe le istanze di "yourclientid" con il valore effettivo dell'ID client dell'applicazione AAD in base alla scheda **Configura** del portale classico per l'applicazione AAD. Assicurarsi che l'URL dell'endpoint termini con una barra.

	Il codice avrà un aspetto simile all'esempio seguente:

		var endpoints = {
		    //"https://{your api app name}.azurewebsites.net/": "1cf55bc9-9ed8-4df31cf55bc9-9ed8-4df3"
		    "https://localhost:44300/": "1cf55bc9-9ed8-4df31cf55bc9-9ed8-4df3"
		};

9. Nel file *app.js*, nel codice `adalProvider.init` sostituire "{your tenant url}" e "{your client id}" con i valori effettivi.

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

10. In *app/scripts/contactsSvc.js* assicurarsi che il valore di apiEndpoint sia impostato sull'URL SSL corretto per il progetto ContactsList.API.

		//var apiEndpoint = "https://{your api app name}.azurewebsites.net";
		var apiEndpoint = "https://localhost:44300";

## Aggiornare le impostazioni di AAD nel progetto ContactsList.API

1. Aprire il file *Web.config* dell'applicazione nel progetto ContactsList.API.

2. Nell'elemento appSettings impostare ida:Authority sul valore "https://login.windows.net/{your tenant url}" e impostare ida:ClientId sull'ID client dell'applicazione AAD, come illustrato nell'esempio seguente.

		<appSettings>
		  <add key="ida:Authority" value="https://login.windows.net/contoso.onmicrosoft.com" />
		  <add key="ida:ClientId" value="1cf55bc9-9ed8-4df31cf55bc9-9ed8-4df3" />
		</appSettings>

## Configurare l'applicazione AAD per localhost 

1. Nella scheda **Configura** per l'applicazione AAD del portale classico, incollare l'URL SSL del progetto ContactsList.Angular.AAD nel campo **URL di accesso** e rimuovere la barra finale.

	![](./media/app-service-api-dotnet-user-principal-auth/signonurl.png)

3. Nella parte inferiore della scheda **Configura**, nel campo **URL di risposta** incollare l'URL SSL del progetto ContactsList.Angular.AAD, sostituendo il valore esistente e lasciando la barra finale invariata.

	![](./media/app-service-api-dotnet-user-principal-auth/replyurl.png)

4. Fare clic su **Salva**.

## Eseguire i progetti API e client in locale

Durante l'esecuzione di HTTPS in locale, si riceveranno messaggi di avviso relativi al certificato SSL di IIS Express per localhost. Per l'esecuzione in locale è possibile ignorare questi messaggi. Se si preferisce, è possibile ignorare questa sezione e passare direttamente alla sezione successiva per la preparazione dell'esecuzione dell'applicazione e dell'API in Azure.

Se si verificano errori di accesso, provare a usare un altro browser o aprire l'URL del progetto AngularJS in una finestra di navigazione in incognito o InPrivate, ad esempio `https://localhost:44301`.

5. In Visual Studio premere F5 per eseguire i progetti API e AngularJS in locale.

	Verrà visualizzata la scheda iniziale dell'applicazione AngularJS.

10. Fare clic sulla scheda **Login**.

	Verrà richiesto di effettuare l'accesso.

7. Accedere con le credenziali utente per un utente incluso nel tenant AAD.

10. Fare clic sulla scheda **Contacts**.

	Verrà visualizzata la pagina **Contacts**.

	![](./media/app-service-api-dotnet-user-principal-auth/contactspagelocal.png)

11. Chiudere le finestre del browser.

Per poter chiamare l'API e visualizzare i contatti, il codice del progetto ContactsList.Angular.AAD richiede di eseguire l'accesso. Tuttavia, nulla impedisce agli utenti non autenticati di chiamare l'API. È possibile verificarlo eseguendo l'interfaccia utente di Swagger nella finestra del browser aperta all'URL SSL del progetto ContactsList.API. L'API verrà protetta dai chiamanti non autenticati solo quando è in esecuzione nel servizio app di Azure.

Nelle sezioni seguenti verranno configurati i progetti e AAD per l'esecuzione del client e dell'API nel servizio app di Azure e i progetti verranno prima distribuiti e quindi testati in Azure.

## Configurare il progetto ContactsList.Angular.AAD per chiamare l'app per le API di Azure

1. Nel progetto ContactsList.Angular.AAD aprire il file *app/scripts/app.js*.

2. Impostare come commento l'endpoint localhost, rimuovere il commento per l'endpoint di Azure e sostituire "{your api app name}" con il nome dell'app per le API effettivo.

	Il codice avrà un aspetto simile all'esempio seguente:

		var endpoints = {
		    "https://contactslistapi.azurewebsites.net/": "1cf55bc9-9ed8-4df31cf55bc9-9ed8-4df3"
		    //"https://localhost:44300/": "1cf55bc9-9ed8-4df31cf55bc9-9ed8-4df3"
		};

1. Modificare allo stesso modo l'URL dell'endpoint nel file *app/scripts/contactsSvc.js*.

	Il codice avrà un aspetto simile all'esempio seguente:

		var apiEndpoint = "https://contactslistapi.azurewebsites.net";
		//var apiEndpoint = "https://localhost:44300";


## Configurare l'applicazione AAD per l'app Web di Azure

1. Nella scheda **Configura** per l'applicazione AAD del portale classico eliminare l'URL SSL del progetto ContactsList.Angular.AAD nel campo **URL di accesso** e sostituirlo con l'URL di base dell'app Web senza la barra finale. Si noti che questo è l'URL dell'app Web e non l'URL dell'app per le API.

	![](./media/app-service-api-dotnet-user-principal-auth/signonurlazure.png)

3. Nel campo **URL di risposta** sostituire l'URL SSL del progetto ContactsList.Angular.AAD con l'URL di base dell'app Web, lasciando la barra finale invariata.

	![](./media/app-service-api-dotnet-user-principal-auth/replyurlazure.png)

4. Fare clic su **Salva**.

## Distribuire il progetto ContactsList.API in Azure

8. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto ContactsList.API e quindi scegliere **Pubblica**.

	La procedura guidata **Pubblica sul Web** si aprirà sull'ultimo profilo di pubblicazione usato per questo progetto, che consente di eseguire la distribuzione nell'app per le API creata in precedenza.

7. Fare clic su **Pubblica**.

8. Chiudere la finestra del browser che viene aperta automaticamente.

## Distribuire il progetto ContactsList.Angular.AAD in Azure

8. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto ContactsList.Angular.API e quindi scegliere **Pubblica**.

9. Fare clic su **Servizio app di Microsoft Azure**.

10. Nell'elenco a discesa **Sottoscrizione** della finestra di dialogo **Servizio app** selezionare la sottoscrizione.

11. Espandere il gruppo di risorse creato per questa esercitazione e selezionare l'app Web creata nella seconda esercitazione.

	![](./media/app-service-api-dotnet-user-principal-auth/deploytowebapp.png)

12. Fare clic su **OK**.

12. Nella procedura guidata **Pubblica sul Web** fare clic sulla scheda **Connessione** e nella casella **URL di destinazione** sostituire `http://` con `https://`

	![](./media/app-service-api-dotnet-user-principal-auth/httpsinconntab.png)

12. Nella procedura guidata **Pubblica sul Web** fare clic sulla scheda **Impostazioni**, espandere **Opzioni pubblicazione file** e quindi selezionare la casella di controllo **Rimuovi i file aggiuntivi nella destinazione**.

7. Fare clic su **Pubblica**.

	Visual Studio distribuisce il progetto e apre una finestra del browser alla pagina iniziale dell'applicazione.

## Testare l'app Web AngularJS in Azure

8. Fare clic sulla scheda **Contacts**.

	Verrà richiesto di effettuare l'accesso.

9. Accedere con le credenziali di un utente che appartiene al tenant AAD.

10. Verrà visualizzata la pagina **Contacts**.

	![](./media/app-service-api-dotnet-user-principal-auth/contactspageazure.png)

11. Per verificare che l'app per le API sia protetta, passare all'URL corrispondente dell'interfaccia utente di Swagger in una finestra del browser per la navigazione in incognito o InPrivate.

	Si verrà reindirizzati a una pagina di accesso.

	A questo punto, il front-end può chiamare l'API per conto di un utente autenticato, ma gli utenti non autenticati non potranno chiamare l'API.

## Passaggi successivi

In questa esercitazione è stata usata la funzionalità di autenticazione/autorizzazione del servizio app per limitare l'accesso a un'app per le API in modo che possa essere chiamata solo agli utenti autenticati. Nell'esercitazione successiva della serie si apprenderà come [limitare l'accesso all'app per le API per gli scenari da servizio a servizio](app-service-api-dotnet-service-principal-auth.md).

<!---HONumber=AcomDC_1203_2015-->