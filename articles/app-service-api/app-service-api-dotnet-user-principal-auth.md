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
	ms.topic="article"
	ms.date="06/30/2016"
	ms.author="tdykstra"/>

# Autenticazione utente per le app per le API nel servizio app di Azure

## Panoramica

Questo articolo illustra come proteggere un'app per le API di Azure in modo che possa essere chiamata solo dagli utenti autenticati. Questo articolo presuppone che sia stato letto l'articolo [Autenticazione e autorizzazione nel servizio app di Azure](../app-service/app-service-authentication-overview.md).

Si apprenderà come:

* Come configurare un provider di autenticazione, con dettagli relativi ad Azure Active Directory (Azure AD).
* Come utilizzare un'app per le API protetta con [Active Directory Authentication Library (ADAL) per JavaScript](https://github.com/AzureAD/azure-activedirectory-library-for-js).

L'articolo contiene due sezioni:

* La sezione [Come configurare l'autenticazione utente nel servizio app di Azure](#authconfig) spiega in generale come configurare l'autenticazione utente per qualsiasi app per le API. È applicabile anche a tutti i framework supportati dal servizio app, ad esempio .NET, Node.js e Java.

* A partire dalla sezione [Proseguimento delle esercitazioni sulle app per le API .NET](#tutorialstart), l'articolo illustra in dettaglio la configurazione di un'applicazione di esempio con un back-end .NET e un front-end AngularJS. L'applicazione usa Azure Active Directory per l'autenticazione utente.

## <a id="authconfig"></a> Come configurare l'autenticazione utente nel servizio app di Azure

Questa sezione fornisce istruzioni generali applicabili a tutte le app per le API. Per i passaggi specifici dell'applicazione di esempio .NET To Do List, vedere [Proseguimento delle esercitazioni sulle app per le API .NET](#tutorialstart).

1. Nel [portale di Azure](https://portal.azure.com/) passare al pannello **Impostazioni** dell'app per le API da proteggere, individuare la sezione **Funzionalità**, quindi fare clic su **Autenticazione/Autorizzazione**.

	![Autenticazione/Autorizzazione del portale di Azure](./media/app-service-api-dotnet-user-principal-auth/features.png)

3. Nel pannello **Autenticazione/Autorizzazione** fare clic su **Sì**.

4. Selezionare una delle opzioni disponibili nell'elenco a discesa **Azione da eseguire quando la richiesta non è autenticata**.

	* Se si vuole consentire solo alle chiamate autenticate di raggiungere l'app per le API, scegliere una delle opzioni in **Accedi con**. Questa opzione consente di proteggere l'app per le API senza scrivere codice eseguibile al suo interno.

	* Se si vuole consentire a tutte le chiamate di raggiungere l'app per le API, scegliere **Consenti richiesta (nessuna azione)**. È possibile usare questa opzione per indirizzare i chiamanti non autenticati a una scelta di provider di autenticazione. Con questa opzione è necessario scrivere codice per gestire l'autorizzazione.

	Per altre informazioni, vedere [Autenticazione e autorizzazione per app per le API nel servizio app di Azure](app-service-api-authentication.md#multiple-protection-options).

5. Selezionare o più **Provider di autenticazione**.

	L'immagine mostra le scelte che richiedono l'autenticazione di tutti i chiamanti con Azure AD.
 
	![Pannello Autenticazione/Autorizzazione del portale di Azure](./media/app-service-api-dotnet-user-principal-auth/authblade.png)

	Quando si sceglie un provider di autenticazione, il portale visualizza un pannello di configurazione per quel provider.

	Per istruzioni dettagliate che illustrano come configurare i pannelli di configurazione dei provider di autenticazione, vedere [Come configurare un'applicazione del servizio app per usare l'account di accesso di Azure Active Directory](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md). Con questo collegamento si accede a un articolo su Azure AD, che contiene anche schede per il collegamento ad articoli relativi ad altri provider di autenticazione.

7. Dopo aver completato le operazioni nel pannello di configurazione del provider di autenticazione, fare clic su **OK**.

7. Nel pannello **Autenticazione/Autorizzazione** fare clic su **Salva**.

Fatto questo, il servizio app autenticherà tutte le chiamate API prima che raggiungano l'app per le API. I servizi di autenticazione funzionano allo stesso modo per tutti i linguaggi supportati dal servizio app, inclusi .NET, Node.js e Java.

Per effettuare chiamate API autenticate, il chiamante include token di connessione OAuth 2.0 del provider di autenticazione nell'intestazione dell'autorizzazione delle richieste HTTP. Il token può essere acquisito con l'SDK del provider di autenticazione.

## <a id="tutorialstart"></a> Proseguimento delle esercitazioni sulle app per le API .NET

Se si stanno seguendo le esercitazioni su Node.js o Java per le app per le API, passare all'articolo successivo [Autenticazione dell'entità servizio per app per le API nel servizio app di Azure](app-service-api-dotnet-service-principal-auth.md).

Se si sta seguendo la serie di esercitazioni su .NET per le app per le API ed è già stata distribuita l'applicazione di esempio come indicato nella [prima](app-service-api-dotnet-get-started.md) e nella [seconda](app-service-api-cors-consume-javascript.md) esercitazione, passare alla sezione [Configurare l'autenticazione nel servizio app e in Azure AD](#azureauth).

Se si desidera seguire questa esercitazione senza passare per la prima e la seconda, eseguire la procedura seguente, che illustra come iniziare tramite un processo automatizzato per distribuire l'applicazione di esempio.

>[AZURE.NOTE] I passaggi seguenti conducono allo stesso punto di partenza a cui si arriva seguendo le prime due esercitazioni, con una sola eccezione: Visual Studio non sa a quale app Web o app per le API verrà distribuito ogni progetto. Ciò significa che in questa esercitazione non sono disponibili istruzioni precise per eseguire la distribuzione alle destinazioni corrette. Se non si è in grado di capire come eseguire in modo autonomo la procedura di distribuzione, è consigliabile seguire la serie di esercitazioni partendo dalla [prima](app-service-api-dotnet-get-started.md) anziché iniziare con il processo di distribuzione automatizzata illustrato qui.

1. Assicurarsi di soddisfare tutti i prerequisiti indicati nella [prima esercitazione](app-service-api-dotnet-get-started.md). Oltre ai prerequisiti indicati, queste esercitazioni sull'autenticazione presuppongono che l'utente abbia già usato le app Web del servizio app e le app per le API in Visual Studio e il portale di Azure.

2. Fare clic sul pulsante **Deploy to Azure** (Distribuisci in Azure) nel [file leggimi del repository di esempio di To Do List](https://github.com/azure-samples/app-service-api-dotnet-todo-list/blob/master/readme.md) per distribuire le app per le API e l'app Web. Prendere nota del gruppo di risorse di Azure che viene creato, poiché questa informazione potrà essere usata in un secondo tempo per la ricerca dei nomi delle app Web e delle app per le API.
 
3. Scaricare o clonare il [repository di esempio di To Do List](https://github.com/Azure-Samples/app-service-api-dotnet-todo-list) per ottenere il codice da usare in locale in Visual Studio.

## <a id="azureauth"></a> Configurare l'autenticazione nel servizio app e in Azure AD

A questo punto l'applicazione è in esecuzione nel servizio app di Azure senza richiedere l'autenticazione degli utenti. In questa sezione si aggiungerà l'autenticazione eseguendo queste attività:

* Configurare il servizio app perché richieda l'autenticazione di Azure Active Directory (Azure AD) per chiamare l'app per le API di livello intermedio.
* Creare un'applicazione Azure AD.
* Configurare l'applicazione Azure AD per inviare il token di connessione dopo l'accesso al front-end AngularJS.

Se si verificano problemi mentre si seguono le istruzioni dell'esercitazione, vedere la sezione [Risoluzione dei problemi](#troubleshooting) alla fine dell'esercitazione.
 
### Configurare l'autenticazione per l'app per le API di livello intermedio

1. Nel [portale di Azure](https://portal.azure.com/) passare al pannello **Impostazioni** dell'app per le API creata per il progetto ToDoListAPI, individuare la sezione **Funzionalità**, quindi fare clic su **Autenticazione/Autorizzazione**.

	![Autenticazione/Autorizzazione del portale di Azure](./media/app-service-api-dotnet-user-principal-auth/features.png)

3. Nel pannello **Autenticazione/Autorizzazione** fare clic su **Sì**.

4. Nell'elenco a discesa **Azione da eseguire quando la richiesta non è autenticata** selezionare **Accedi con Azure Active Directory**.

	Questa opzione assicura che nessuna richiesta non autenticata raggiungerà l'app per le API.

5. In **Provider di autenticazione** fare clic su **Azure Active Directory**.

	![Pannello Autenticazione/Autorizzazione del portale di Azure](./media/app-service-api-dotnet-user-principal-auth/authblade.png)

6. Nel pannello **Impostazioni di Azure Active Directory** fare clic su **Rapide**.

	![Opzione Rapida di Autenticazione/Autorizzazione del portale di Azure](./media/app-service-api-dotnet-user-principal-auth/aadsettings.png)

	Con l'opzione **Rapida** il servizio app può creare automaticamente un'applicazione Azure AD nel [tenant](https://msdn.microsoft.com/it-IT/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant) di Azure AD.

	Non è necessario creare un tenant, perché ne esiste già uno per ogni account Azure.

7. In **Modalità di gestione** fare clic su **Crea nuova app AD**, se non è già selezionata, e annotare il valore nella casella di testo **Crea app** per cercare questa applicazione AAD nel portale di Azure classico in un secondo momento.

	![Impostazioni di Azure AD del portale di Azure](./media/app-service-api-dotnet-user-principal-auth/aadsettings2.png)

	Azure creerà automaticamente un'applicazione Azure AD con il nome indicato nel tenant di Azure AD. Per impostazione predefinita, all'applicazione Azure AD viene assegnato lo stesso nome dell'app per le API. Se si preferisce, è possibile immettere un nome diverso.
 
7. Fare clic su **OK**.

7. Nel pannello **Autenticazione/Autorizzazione** fare clic su **Salva**.

	![Fare clic su Salva.](./media/app-service-api-dotnet-user-principal-auth/authsave.png)

Ora solo gli utenti nel tenant di Azure AD possono chiamare l'app per le API.

### Facoltativo: testare l'app per le API

1. In un browser passare all'URL dell'app per le API. Nel pannello **App per le API** del portale di Azure fare clic sul collegamento in **URL**.

	Si viene reindirizzati a una schermata di accesso, perché alle richieste non autenticate non è consentito raggiungere l'app per le API.

	Se il browser visualizza la pagina di conferma della creazione dell'app, potrebbe essere già connesso. In questo caso, aprire una finestra InPrivate o di esplorazione in incognito e passare all'URL dell'app per le API.

2. Accedere con le credenziali per un utente incluso nel tenant di Azure AD.

	Dopo avere effettuato l'accesso, viene visualizzata una pagina che informa che l'operazione di creazione è riuscita.

9. Chiudere il browser.

### Configurare l'applicazione Azure AD

Quando è stata configurata l'autenticazione di Azure AD, il servizio app ha creato automaticamente un'applicazione Azure AD. Per impostazione predefinita, la nuova applicazione Azure AD è stata configurata per fornire il token di connessione all'URL dell'app per le API. In questa sezione si configurerà l'applicazione Azure AD in modo che fornisca il token di connessione al front-end AngularJS, invece che direttamente all'app per le API di livello intermedio. Il front-end AngularJS invierà il token all'app per le API contemporaneamente alla chiamata.

>[AZURE.NOTE] Per mantenere il processo quanto più semplice possibile, questa esercitazione usa una sola applicazione Azure AD per l'app per le API front-end e di livello intermedio. Un'altra opzione consiste nell'usare due applicazioni Azure AD. In questo caso è necessario concedere all'applicazione Azure AD dell'app front-end le autorizzazioni per chiamare l'applicazione Azure AD dell'app di livello intermedio. Questo approccio con più applicazioni consente un controllo più granulare sulle autorizzazioni per ogni livello.

11. Nel [portale di Azure classico](https://manage.windowsazure.com/) passare a **Azure Active Directory**.

	È necessario accedere al portale classico, perché alcune impostazioni di Azure Active Directory a cui è necessario accedere non sono ancora disponibili nel portale di Azure corrente.

12. Nella scheda **Directory** fare clic sul tenant AAD.

	![Azure AD nel portale classico](./media/app-service-api-dotnet-user-principal-auth/selecttenant.png)

14. Fare clic su **Applicazioni > Applicazioni di proprietà dell'azienda** e quindi fare clic sul segno di spunta.

	Per visualizzare la nuova applicazione potrebbe essere necessario aggiornare la pagina.

15. Nell'elenco delle applicazioni fare clic sul nome di quella creata automaticamente da Azure quando è stata abilitata l'autenticazione per l'app per le API.

	![Scheda Applicazioni Azure AD](./media/app-service-api-dotnet-user-principal-auth/appstab.png)

16. Fare clic su **Configure**.

	![Scheda Configura di Azure AD](./media/app-service-api-dotnet-user-principal-auth/configure.png)

17. Impostare come **URL di accesso** l'URL dell'app Web AngularJS, senza barra finale.

	Ad esempio: https://todolistangular.azurewebsites.net

	![URL di accesso](./media/app-service-api-dotnet-user-principal-auth/signonurlazure.png)

17. Impostare come **URL di risposta** l'URL dell'app Web, senza barra finale.

	Ad esempio: https://todolistsangular.azurewebsites.net

16. Fare clic su **Save**.

	![URL di risposta](./media/app-service-api-dotnet-user-principal-auth/replyurlazure.png)

15. Nella parte inferiore della pagina fare clic su **Gestisci manifesto > Scarica manifesto**.

	![Scaricare il manifesto](./media/app-service-api-dotnet-user-principal-auth/downloadmanifest.png)

17. Scaricare il file in una posizione in cui si possa modificarlo.

16. Nel file manifesto scaricato cercare la proprietà `oauth2AllowImplicitFlow`. Modificare il valore di questa proprietà da `false` a `true` e quindi salvare il file.

	Questa impostazione è necessaria per l'accesso da un'applicazione a singola pagina JavaScript e consente di restituire il token di connessione Oauth 2.0 nel frammento di URL.

16. Fare clic su **Gestisci manifesto > Carica manifesto** e caricare il file aggiornato nel passaggio precedente.

	![Caricare il manifesto](./media/app-service-api-dotnet-user-principal-auth/uploadmanifest.png)

17. Copiare il valore **ID client** e salvarlo in una posizione da cui poterlo recuperare più tardi.

## Configurare il progetto ToDoListAngular per l'uso dell'autenticazione

In questa sezione si modificherà il front-end AngularJS in modo che usi Active Directory Authentication Library (ADAL) per JS per acquisire un token di connessione per l'utente connesso da Azure AD. Il codice includerà il token nelle richieste HTTP inviate al livello intermedio, come illustrato nel diagramma seguente.

![Diagramma di autenticazione utente](./media/app-service-api-dotnet-user-principal-auth/appdiagram.png)

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

8. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto ToDoListAngular e scegliere **Pubblica**.

9. Fare clic su **Pubblica**.

	Visual Studio distribuisce il progetto e apre una finestra del browser all'URL di base dell'app Web. Viene visualizzata la pagina dell'errore 403, che rappresenta la risposta normale quando si tenta di passare a un URL di base dell'API Web da un browser.

	È comunque necessario apportare una modifica all'app per le API di livello intermedio per poter testare l'applicazione.

10. Chiudere il browser.

## Configurare il progetto ToDoListAPI per l'uso dell'autenticazione

Attualmente il progetto ToDoListAPI invia "*" come valore `owner` a ToDoListDataAPI. In questa sezione si modificherà il codice per l'invio dell'ID dell'utente connesso.

Apportare le modifiche seguenti al progetto ToDoListAPI.

1. Aprire il file *Controllers/ToDoListController.cs* e rimuovere il commento dalla riga in ogni metodo di azione che imposta `owner` sul valore attestazione `NameIdentifier` di Azure AD. ad esempio:

		owner = ((ClaimsIdentity)User.Identity).FindFirst(ClaimTypes.NameIdentifier).Value;

	**Importante**: non rimuovere il commento dal codice nel metodo `ToDoListDataAPI`. Questa operazione verrà eseguita successivamente durante l'esercitazione sull'autenticazione dell'entità servizio.

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

10. Viene visualizzata la pagina **To Do List**.

	![Pagina To Do List](./media/app-service-api-dotnet-user-principal-auth/webappindex.png)

	Non vengono visualizzate attività perché fino a questo momento erano tutte per owner "*". Ora il livello intermedio richiede attività per l'utente connesso, che però non sono ancora state create.

11. Aggiungere alcune attività per verificare che l'applicazione funzioni.

12. In un'altra finestra del browser passare all'URL dell'interfaccia utente di Swagger per l'app per le API ToDoListDataAPI e fare clic su **ToDoList > Get**. Immettere un asterisco come valore del parametro `owner` e fare clic su **Prova**.

	La risposta mostra che nella proprietà Owner le nuove attività hanno l'ID utente effettivo di Azure AD.

	![ID proprietario nella risposta JSON](./media/app-service-api-dotnet-user-principal-auth/todolistapiauth.png)


## Compilazione dei progetti da zero

I due progetti API Web sono stati creati usando il modello di progetto **App per le API di Azure** e sostituendo il controller predefinito Values con un controller ToDoList.

Per informazioni su come creare un'applicazione AngularJS a singola pagina con un back-end API Web 2, vedere il [laboratorio pratico sulla compilazione di un'applicazione a singola pagina con l'API Web ASP.NET e Angular.js](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs). Per informazioni su come aggiungere il codice di autenticazione di Azure AD, vedere le risorse seguenti:

* [Protezione di app AngularJS a singola pagina con Azure AD](../active-directory/active-directory-devquickstarts-angular.md).
* [Introduzione ad ADAL JS v1](http://www.cloudidentity.com/blog/2015/02/19/introducing-adal-js-v1/)

## Risoluzione dei problemi

[AZURE.INCLUDE [risoluzione dei problemi](../../includes/app-service-api-auth-troubleshooting.md)]

* Assicurarsi di non confondere ToDoListAPI (livello intermedio) e ToDoListDataAPI (livello dati). Ad esempio, verificare di aver aggiunto l'autenticazione all'app per le API di livello intermedio, non di livello dati.
* Verificare che il codice sorgente di AngularJS faccia riferimento all'URL dell'app per le API di livello intermedio (ToDoListAPI, non ToDoListDataAPI) e all'ID client di Azure AD corretto.

## Passaggi successivi

In questa esercitazione si è appreso come usare l'autenticazione del servizio app per un'app per le API e come chiamare l'app per le API con la libreria ADAL JS. Nell'esercitazione successiva si apprenderà come [proteggere l'accesso all'app per le API per gli scenari da servizio a servizio](app-service-api-dotnet-service-principal-auth.md).

<!---HONumber=AcomDC_0706_2016-->