<properties
	pageTitle="Autenticazione utente per le app per le API nel servizio app di Azure | Microsoft Azure"
	description="Informazioni su come proteggere un'app per le API del servizio app di Azure consentendo l'accesso solo agli utenti autenticati."
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
	ms.date="01/26/2016"
	ms.author="tdykstra"/>

# Autenticazione utente per le app per le API nel servizio app di Azure

[AZURE.INCLUDE [selettore](../../includes/app-service-api-auth-selector.md)]

## Panoramica

In questo articolo si apprenderà:

* Come proteggere un'app per le API del servizio app in modo che possa essere chiamata solo dagli utenti autenticati.
* Come configurare un provider di autenticazione, con dettagli relativi ad Azure Active Directory (Azure AD).
* Come utilizzare un'app per le API protetta con [Active Directory Authentication Library (ADAL) per JavaScript](https://github.com/AzureAD/azure-activedirectory-library-for-js).

L'articolo contiene due sezioni:

* La sezione [Come configurare l'autenticazione utente nel servizio app di Azure](#authconfig) spiega in generale come configurare l'autenticazione utente per qualsiasi app per le API. È applicabile anche a tutti i framework supportati dal servizio app, ad esempio .NET, Node.js e Java.

* Il [resto dell'articolo](#tutorialstart) illustra in dettaglio come configurare un'applicazione .NET di esempio eseguita nel servizio app, in modo che usi Azure Active Directory per l'autenticazione utente.

## <a id="authconfig"></a> Come configurare l'autenticazione utente nel servizio app di Azure

Questa sezione fornisce istruzioni generali applicabili a tutte le app per le API. Per i passaggi specifici dell'applicazione di esempio .NET To Do List, vedere [Proseguimento delle esercitazioni introduttive su .NET](#tutorialstart).

1. Nel [portale di Azure](https://portal.azure.com/) passare al pannello **App per le API** dell'app per le API che si vuole proteggere e quindi fare clic su **Impostazioni**.

2. Nel pannello **Impostazioni** trovare la sezione **Funzionalità** e quindi fare clic su **Autenticazione/Autorizzazione**.

	![](./media/app-service-api-dotnet-user-principal-auth/features.png)

3. Nel pannello **Autenticazione/Autorizzazione** fare clic su **Sì**.

4. Selezionare una delle opzioni disponibili nell'elenco a discesa **Azione da eseguire quando la richiesta non è autenticata**.

	* Se si vuole consentire solo alle chiamate autenticate di raggiungere l'app per le API, scegliere una delle opzioni in **Accedi con**. Questa opzione consente di proteggere l'app per le API senza scrivere codice eseguibile al suo interno.

	* Se si vuole consentire a tutte le chiamate di raggiungere l'app per le API, scegliere **Consenti richiesta (nessuna azione)**. È possibile usare questa opzione per indirizzare i chiamanti non autenticati a una scelta di provider di autenticazione. Con questa opzione è necessario scrivere codice per gestire l'autorizzazione.

	Per altre informazioni, vedere [Autenticazione e autorizzazione per app per le API nel servizio app di Azure](app-service-api-authentication.md#multiple-protection-options).

5. Selezionare una o più opzioni in **Provider di autenticazione**.

	L'immagine mostra le scelte che richiedono l'autenticazione di tutti i chiamanti con Azure AD.
 
	![](./media/app-service-api-dotnet-user-principal-auth/authblade.png)

	Quando si sceglie un provider di autenticazione, il portale visualizza un pannello di configurazione per quel provider.

	Per istruzioni dettagliate che illustrano come configurare i pannelli di configurazione dei provider di autenticazione, vedere [Come configurare un'applicazione del servizio app per usare l'account di accesso di Azure Active Directory](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md). Con questo collegamento si accede a un articolo su Azure AD, che contiene anche schede per il collegamento ad articoli relativi ad altri provider di autenticazione.

7. Dopo aver completato il pannello di configurazione del provider di autenticazione, fare clic su **OK**.

7. Nel pannello **Autenticazione/Autorizzazione** fare clic su **Salva**.

Fatto questo, il servizio app autenticherà tutte le chiamate API prima che raggiungano l'app per le API. I servizi di autenticazione funzionano allo stesso modo per tutti i linguaggi supportati dal servizio app, inclusi .NET, Node.js e Java.

Per effettuare chiamate API autenticate, il chiamante include token di connessione OAuth 2.0 del provider di autenticazione nell'intestazione dell'autorizzazione delle richieste HTTP. Il token può essere acquisito con l'SDK del provider di autenticazione.

## <a id="tutorialstart"></a> Proseguimento delle esercitazioni introduttive su .NET

Se si sta seguendo la serie introduttiva su Node.js o Java per le app per le API, passare all'articolo successivo [Autenticazione dell'entità servizio per app per le API del servizio app di Azure](app-service-api-dotnet-service-principal-auth.md).

Se si sta seguendo la serie introduttiva su .NET per le app per le API ed è già stata distribuita l'applicazione di esempio come indicato nella [prima](app-service-api-dotnet-get-started.md) e nella [seconda](app-service-api-cors-consume-javascript.md) esercitazione, passare alla sezione [Configurare l'autenticazione](#azureauth).

Se non sono state eseguite la prima e la seconda esercitazione e si vuole seguire questa, assicurarsi prima di tutto di avere tutti i [Prerequisiti](app-service-api-dotnet-get-started.md#prerequisites) della serie. Seguire quindi questa procedura per scaricare e distribuire l'applicazione di esempio. La procedura è un duplicato di quella prevista nelle prime due esercitazioni, ma qui le istruzioni sono abbreviate.

1. Scaricare l'applicazione di esempio.

	a. Scaricarla dal repository [Azure-Samples/app-service-api-dotnet-to-do-list](https://github.com/Azure-Samples/app-service-api-dotnet-to-do-list).

	a. Aprire la soluzione ToDoList in Visual Studio 2015 e compilarla per ripristinare i pacchetti NuGet.

2. Distribuire il progetto ToDoListDataAPI nella nuova app per le API.

	a. Nel progetto ToDoListDataAPI aprire il file *App_Start/SwaggerConfig.cs* e rimuovere il commento dal codice **EnableSwaggerUi**.

	b. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto ToDoListDataAPI e quindi scegliere **Pubblica**.

	c. Nel passaggio **Profilo** della procedura guidata **Pubblica sito Web** fare clic su **Servizio app di Microsoft Azure**.

	d. Nella finestra di dialogo del **servizio app**, scegliere la **sottoscrizione** di Azure da usare e quindi fare clic su **Nuovo**.

	e. Nella scheda **Hosting** della finestra di dialogo **Crea servizio app** fare clic su **Tipo di modifica** e quindi su **App per le API**.

	f. In **Immettere il nome dell'app per le API** specificare un nome, quale ToDoListDataAPI più un numero in modo che sia univoco nel dominio *azurewebsites.net*, ad esempio ToDoListDataAPI1230.

	g. Nell'elenco a discesa **Gruppo di risorse** immettere un nome, ad esempio TodoListGroup, per creare un nuovo gruppo di risorse.

	h. Nell'elenco a discesa **Piano di servizio app** fare clic su **Nuovo** e immettere le informazioni necessarie nella finestra di dialogo **Configura piano di servizio app**.

	i. Fare clic su **Create**.

	j. Fare clic su **Pubblica**.

3. Distribuire il progetto ToDoListAPI in una nuova app per le API.

	a. Nel progetto ToDoListAPI aprire *Controllers\ToDoListController.cs* e modificare `http://localhost:45914` in `https://{your ToDoListDataAPI app name}.azurewebsites.net`.

	b. Per la distribuzione del progetto ToDoListAPI seguire la stessa procedura usata per il progetto ToDoListDataAPI. Non dimenticare di modificare il tipo in **App per le API**.

4. Distribuire il progetto ToDoListAngular in una nuova app Web.

	a. Nel progetto ToDoListAngular aprire il file *app/scripts/todoListSvc.js*.

	b. Impostare come commento la riga che imposta `apiEndpoint` sull'URL localhost, rimuovere il commento dalla riga che imposta `apiEndPoint` sull'URL azurewebsites.net e sostituire il segnaposto con il nome effettivo dell'app per le API creata per ToDoListAPI.

	c. Per la distribuzione del progetto ToDoListAPI seguire la stessa procedura usata per il progetto ToDoListDataAPI, **eccetto la modifica del tipo da app Web in app per le API**.

5. Configurare CORS per l'app per le API in Azure.

	a. Accedere al [portale di Azure](https://portal.azure.com/) e passare all'app per le API creata per il progetto ToDoListAPI.

	b. Nel pannello **App per le API** fare clic su **Impostazioni**.

	c. Trovare la sezione **API** e quindi fare clic su **CORS**.

	d. Nella casella di testo immettere l'URL da cui si vogliono consentire le chiamate. Per questa esercitazione è l'URL dell'app Web creata per il progetto ToDoListAngular. Ad esempio, immettere "https://todolistangular.azurewebsites.net".

	e. Fare clic su **Save**.

6. Aprire l'URL HTTPS dell'app Web in un browser e verificare che sia possibile visualizzare, aggiungere, modificare ed eliminare attività.

## <a id="azureauth"></a> Configurare l'autenticazione in Azure

A questo punto l'applicazione è in esecuzione nel servizio app di Azure senza richiedere l'autenticazione degli utenti. In questa sezione si aggiungerà l'autenticazione eseguendo queste attività:

* Configurare il servizio app perché richieda l'autenticazione di Azure Active Directory (Azure AD) per chiamare l'app per le API di livello intermedio.
* Creare un'applicazione Azure AD.
* Configurare l'applicazione Azure AD per inviare il token di connessione dopo l'accesso al front-end AngularJS. 

### Configurare l'autenticazione nel servizio app

1. Accedere al [portale di Azure](https://portal.azure.com/) e passare al pannello **App per le API** relativo all'app per le API creata per il progetto ToDoListAPI.

2. Fare clic su **Settings**

2. Nel pannello **Impostazioni** trovare la sezione **Funzionalità** e quindi fare clic su **Autenticazione/Autorizzazione**.

	![](./media/app-service-api-dotnet-user-principal-auth/features.png)

3. Nel pannello **Autenticazione/Autorizzazione** fare clic su **Sì**.

4. Nell'elenco a discesa **Azione da eseguire quando la richiesta non è autenticata** selezionare **Accedi con Azure Active Directory**.

	Questa opzione assicura che nessuna richiesta non autenticata raggiungerà l'app per le API.

5. In **Provider di autenticazione** fare clic su **Azure Active Directory**.

	![](./media/app-service-api-dotnet-user-principal-auth/authblade.png)

6. Nel pannello **Impostazioni di Azure Active Directory** fare clic su **Rapide**.

	![](./media/app-service-api-dotnet-user-principal-auth/aadsettings.png)

	Con l'opzione **Rapida** il servizio app può creare automaticamente un'applicazione Azure AD nel [tenant](https://msdn.microsoft.com/it-IT/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant) di Azure AD.

	Non è necessario creare un tenant, perché ne esiste già uno per ogni account Azure.

7. In **Modalità di gestione** fare clic su **Crea nuova app AD**.

	Il portale inserisce un valore predefinito nella casella di input **Crea app**.
	
	![](./media/app-service-api-dotnet-user-principal-auth/aadsettings2.png)

8. Prendere nota del valore nella casella di input **Crea app**. Più avanti sarà necessario cercare questa applicazione AAD nel portale di Azure classico.

	Azure creerà automaticamente un'applicazione Azure AD nel tenant di Azure AD. Per impostazione predefinita, all'applicazione Azure AD viene assegnato lo stesso nome dell'app per le API. Se si preferisce, è possibile immettere un nome diverso.
 
7. Fare clic su **OK**.

7. Nel pannello **Autenticazione/Autorizzazione** fare clic su **Salva**.

Ora solo gli utenti nel tenant di Azure AD possono chiamare l'app per le API.

### Facoltativo: testare l'app per le API

1. In un browser andare all'URL dell'app per le API. Nel pannello **App per le API** del portale di Azure fare clic sul collegamento in **URL**.  

	Si viene reindirizzati a una schermata di accesso, perché alle richieste non autenticate non è consentito raggiungere l'app per le API.

	Se il browser visualizza la pagina di conferma della creazione dell'app, potrebbe essere già connesso. In questo caso, aprire una finestra InPrivate o di esplorazione in incognito e passare all'URL dell'app per le API.

2. Accedere con le credenziali per un utente incluso nel tenant di Azure AD.

	Dopo avere effettuato l'accesso, viene visualizzata una pagina che informa che l'operazione di creazione è riuscita.

9. Chiudere il browser.

### Configurare l'applicazione Azure AD

Quando è stata configurata l'autenticazione di Azure AD, il servizio app ha creato automaticamente un'applicazione Azure AD. Per impostazione predefinita, la nuova applicazione Azure AD è stata configurata per fornire il token di connessione all'URL dell'app per le API. In questa sezione si configurerà l'applicazione Azure AD in modo che fornisca il token di connessione al front-end AngularJS, invece che direttamente all'app per le API di livello intermedio. Il front-end AngularJS invierà il token all'app per le API contemporaneamente alla chiamata.

11. Nel [portale di Azure classico](https://manage.windowsazure.com/) passare a **Azure Active Directory**.

	È necessario accedere al portale classico, perché alcune impostazioni di Azure Active Directory a cui è necessario accedere non sono ancora disponibili nel portale di Azure corrente.

12. Nella scheda **Directory** fare clic sul tenant AAD.

	![](./media/app-service-api-dotnet-user-principal-auth/selecttenant.png)

14. Fare clic su **Applicazioni > Applicazioni di proprietà dell'azienda** e quindi fare clic sul segno di spunta.

	Per visualizzare la nuova applicazione potrebbe essere necessario aggiornare la pagina.

15. Nell'elenco delle applicazioni fare clic sul nome di quella creata automaticamente da Azure quando è stata abilitata l'autenticazione per l'app per le API.

	![](./media/app-service-api-dotnet-user-principal-auth/appstab.png)

16. Fare clic su **Configure**.

	![](./media/app-service-api-dotnet-user-principal-auth/configure.png)

17. Impostare **URL accesso** sull'URL dell'app Web AngularJS, senza barra finale.

	Ad esempio: https://todolistangular.azurewebsites.net

	![](./media/app-service-api-dotnet-user-principal-auth/signonurlazure.png)

17. Impostare **URL di risposta** sull'URL dell'app Web, senza barra finale.

	Ad esempio: https://todolistsangular.azurewebsites.net

16. Fare clic su **Salva**.

	![](./media/app-service-api-dotnet-user-principal-auth/replyurlazure.png)

15. Nella parte inferiore della pagina fare clic su **Gestisci manifesto > Scarica manifesto**.

	![](./media/app-service-api-dotnet-user-principal-auth/downloadmanifest.png)

17. Scaricare il file in una posizione in cui si possa modificarlo.

16. Nel file manifesto scaricato cercare la proprietà `oauth2AllowImplicitFlow`. Modificare il valore di questa proprietà da `false` a `true` e quindi salvare il file.

	Questa impostazione è necessaria per l'accesso da un'applicazione a singola pagina JavaScript e consente di restituire il token di connessione Oauth 2.0 nel frammento di URL.

16. Fare clic su **Gestisci manifesto > Carica manifesto** e caricare il file aggiornato nel passaggio precedente.

17. Copiare il valore **ID client** e salvarlo in una posizione da cui poterlo recuperare più tardi.

## Configurare il progetto ToDoListAngular per l'uso dell'autenticazione

In questa sezione si modificherà il front-end AngularJS in modo che usi Active Directory Authentication Library (ADAL) per JS per acquisire un token di connessione per l'utente connesso da Azure AD. Il codice includerà il token nelle richieste HTTP inviate al livello intermedio, come illustrato nel diagramma seguente.

![](./media/app-service-api-dotnet-user-principal-auth/appdiagram.png)

Apportare le modifiche seguenti ai file nel progetto ToDoListAngular.

1. Aprire il file *index.html*.

2. Rimuovere il commento dalle righe che fanno riferimento agli script di Active Directory Authentication Library (ADAL) per JS.

		<script src="app/scripts/adal.js"></script>
		<script src="app/scripts/adal-angular.js"></script>

1. Aprire il file *app/scripts/app.js*.

2. Impostare come commento il blocco di codice contrassegnato con "without authentication" e rimuovere il commento dal blocco di codice contrassegnato con "with authentication".

	Questa modifica si riferisce al provider di autenticazione ADAL JS e fornisce i valori di configurazione. Nei passaggi seguenti si imposteranno i valori di configurazione per l'app per le API e per l'applicazione Azure AD.

8. Nel codice che imposta la variabile `endpoints` impostare l'URL dell'API sull'URL dell'app per le API creata per il progetto ToDoListAPI e impostare l'ID dell'applicazione Azure AD sull'ID client copiato dal portale di Azure classico.

	Il codice avrà un aspetto simile all'esempio seguente:

		var endpoints = {
		    "https://todolistapi0121.azurewebsites.net/": "1cf55bc9-9ed8-4df31cf55bc9-9ed8-4df3"
		};

9. Nella chiamata a `adalProvider.init` impostare `tenant` sul nome del tenant e `clientId` sullo stesso valore usato nel passaggio precedente.

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

	Le modifiche a `app.js` specificano che il codice chiamante e l'API chiamata si trovano nella stessa applicazione Azure AD.

1. Aprire il file *app/scripts/homeCtrl.js*.

2. Impostare come commento il blocco di codice contrassegnato con "without authentication" e rimuovere il commento dal blocco di codice contrassegnato con "with authentication".

1. Aprire il file *app/scripts/indexCtrl.js*.

2. Impostare come commento il blocco di codice contrassegnato con "without authentication" e rimuovere il commento dal blocco di codice contrassegnato con "with authentication".

### Distribuire il progetto ToDoListAngular in Azure

8. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto ToDoListAngular e quindi scegliere **Pubblica**.

9. Fare clic su **Pubblica**.

	Visual Studio distribuisce il progetto e apre una finestra del browser all'URL di base dell'app Web.

	È comunque necessario apportare una modifica all'app per le API di livello intermedio per poter testare l'applicazione.

10. Chiudere il browser.

## Configurare il progetto ToDoListAPI per l'uso dell'autenticazione

Attualmente il progetto ToDoListAPI invia "*" come valore `owner` a ToDoListDataAPI. In questa sezione si modificherà il codice per l'invio dell'ID dell'utente connesso.

Apportare le modifiche seguenti al progetto ToDoListAPI.

1. Aprire il file *Controllers/ToDoListController.cs* e rimuovere il commento dalla riga in ogni metodo di azione che imposta `owner` sul valore attestazione `NameIdentifier` di Azure AD. Ad esempio:

		owner = ((ClaimsIdentity)User.Identity).FindFirst(ClaimTypes.NameIdentifier).Value;

### Distribuire il progetto ToDoListAPI in Azure

8. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto ToDoListAPI e quindi scegliere **Pubblica**.

9. Fare clic su **Pubblica**.

	Visual Studio distribuisce il progetto e apre una finestra del browser all'URL di base dell'app per le API.

10. Chiudere il browser.

### Test dell'applicazione

9. Passare all'URL dell'app Web **usando HTTPS, non HTTP**.

8. Fare clic sulla scheda **To Do List**.

	Verrà richiesto di effettuare l'accesso.

9. Accedere con le credenziali di un utente che appartiene al tenant AAD.

10. Verrà visualizzata la pagina **To Do List**.

	![](./media/app-service-api-dotnet-user-principal-auth/webappindex.png)

	Non vengono visualizzate attività perché fino a questo momento erano tutte per owner "*". Ora il livello intermedio richiede attività per l'utente connesso, che però non sono ancora state create.

11. Aggiungere alcune attività per verificare che l'applicazione funzioni.

12. In un'altra finestra del browser passare all'URL dell'interfaccia utente di Swagger per l'app per le API ToDoListDataAPI e fare clic su **ToDoList > Get**. Immettere un asterisco come valore del parametro `owner` e quindi fare clic su **Try it out**.

	La risposta mostra che nella proprietà Owner le nuove attività hanno l'ID utente effettivo di Azure AD.

	![](./media/app-service-api-dotnet-user-principal-auth/todolistapiauth.png)


## Compilazione dei progetti da zero

I due progetti API Web sono stati creati usando il modello di progetto **App per le API di Azure** e sostituendo il controller Values predefinito con un controller ToDoList.

Per informazioni su come creare un'applicazione a singola pagina AngularJS con un back-end API Web 2, vedere l'articolo relativo al [laboratorio pratico sulla compilazione di un'applicazione a singola pagina con l'API Web ASP.NET e Angular.js](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs). Per informazioni su come aggiungere il codice di autenticazione di Azure AD, vedere le risorse seguenti:

* [Protezione di app AngularJS a singola pagina con Azure AD](../active-directory/active-directory-devquickstarts-angular.md).
* [Introduzione ad ADAL JS v1](http://www.cloudidentity.com/blog/2015/02/19/introducing-adal-js-v1/)

## Risoluzione dei problemi

Se l'applicazione è stata eseguita correttamente senza autenticazione, ma non funziona quando si implementa l'autenticazione, il più delle volte il problema è dovuto a impostazioni di configurazione non corrette o non coerenti. Iniziare a controllare tutte le impostazioni nel servizio app di Azure e in Azure Active Directory. Ecco alcuni suggerimenti specifici:

* Nella scheda **Configura** di Azure AD verificare il contenuto della casella **URL di risposta**.
* In Azure AD scaricare il manifesto e assicurarsi che `oauth2AllowImplicitFlow` sia stato modificato correttamente in `true`. 
* Nel codice sorgente di AngularJS controllare l'URL dell'app per le API di livello intermedio e l'ID client di Azure AD.
* Dopo avere configurato il codice in un progetto, verificare di avere ridistribuito quel progetto e non uno degli altri.
* Verificare che si stia passando a URL HTTPS nel browser e non a URL HTTP.
* Verificare che CORS sia ancora abilitato nell'app per le API del livello intermedio, consentendo le chiamate al livello intermedio dall'URL HTTPS front-end. In caso di dubbio che il problema sia correlato a CORS, provare "*" come URL di origine consentito.
* Assicurarsi di ottenere il maggior numero possibile di informazioni nei messaggi di errore impostando [customErrors mode su Off](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md#remoteview).
* La scheda relativa alla console degli strumenti di sviluppo del browser include spesso più informazioni sugli errori e si potranno esaminare le richieste HTTP nella scheda relativa alla rete.

## Passaggi successivi

In questa esercitazione si è appreso come usare l'autenticazione del servizio app per un'app per le API e come chiamare l'app per le API con la libreria ADAL JS. Nell'esercitazione successiva si apprenderà come [proteggere l'accesso all'app per le API per gli scenari da servizio a servizio](app-service-api-dotnet-service-principal-auth.md).

<!---HONumber=AcomDC_0204_2016-->