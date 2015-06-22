<properties
	pageTitle="Come compilare un'app AngularJS con singole pagine con Azure AD"
	description="Illustra l'uso di Active Directory Authentication Library (ADAL) per JavaScript per proteggere un'app con singole pagine basata su AngularJS, implementata con un back-end di API Web ASP.NET, che chiama un'altra API Web ASP.NET mediante CORS."
	services="active-directory"
	documentationCenter=""
	authors="dstrockis"
	manager="terrylan"
	editor="LisaToft"/>

<tags
	ms.service="active-directory"
	ms.devlang="javascript"
	ms.topic="article" 
	ms.tgt_pltfrm="na"
	ms.workload="identity"
	ms.date="05/11/2015"
	ms.author="justinha"/>


# Come compilare un'app AngularJS con singole pagine con Azure AD

Questa esercitazione illustra l'uso di Active Directory Authentication Library (ADAL) per JavaScript per proteggere un'app con singole pagine basata su AngularJS, implementata con un back-end di API Web ASP.NET, che chiama un'altra API Web ASP.NET mediante CORS. Per esaminare l'esempio di codice per questa esercitazione, vedere [AzureADSamples/SinglePageApp-AngularJS-DotNet](https://github.com/AzureADSamples/SinglePageApp-AngularJS-DotNet) su GitHub.

ADAL per JavaScript è una libreria open source. Per le opzioni di distribuzione, il codice sorgente e i contributi, vedere il [repository di ADAL per JavaScript](https://github.com/AzureAD/azure-activedirectory-library-for-js).

Per altre informazioni sul funzionamento dei protocolli in questo e altri scenari, vedere [Scenari di autenticazione per Azure AD](http://go.microsoft.com/fwlink/?LinkId=394414).

## Come eseguire questo esempio

Le procedure iniziali sono molto semplici. Per eseguire questo esempio, è necessario disporre di:

- Visual Studio 2013
- Connessione Internet
- Sottoscrizione di Azure (è sufficiente una [versione di valutazione gratuita](https://account.windowsazure.com/organization))

A ogni sottoscrizione di Azure è associato un tenant di Azure Active Directory (Azure AD). Tutte le funzionalità di Azure AD usate in questo esempio sono disponibili gratuitamente.

## Clonare o scaricare questo repository

Dalla shell o dalla riga di comando: `git clone https://github.com/AzureADSamples/SinglePageApp-WebAPI-AngularJS-DotNet.git`

## Registrare il servizio "To Go API" nel tenant di Azure Active Directory

1. Accedere al [portale di gestione di Azure](https://manage.windowsazure.com).
2. Fare clic su **Active Directory** nel pannello di navigazione a sinistra.
3. Fare clic sulla directory in cui si vuole registrare l'applicazione di esempio.
4. Fare clic sulla scheda **Applicazioni**.
5. Nel pannello fare clic su **Aggiungi**.
6. Fare clic su **Aggiungi un'applicazione che l'organizzazione sta sviluppando**.
7. Immettere un nome descrittivo per l'applicazione, ad esempio "To Go API", selezionare **Applicazione Web** e/o **API Web** e fare clic su **Avanti**.
8. Come URL di accesso, immettere l'URL di base per l'esempio, che per impostazione predefinita è `https://localhost:44327/`.
9. Per l'URI ID app immettere `https://<your_directory_name>/ToGoAPI`, sostituendo `<your_directory_name>` con il nome della directory di Azure AD. Salvare la configurazione.

La procedura è stata completata. Prima di procedere al passaggio successivo, è necessario individuare l'URI ID app dell'API specifica.

1. Sempre dal portale di Azure fare clic sulla scheda **Configura** dell'applicazione.
2. Individuare il valore dell'URI ID app e copiarlo negli Appunti.

## Configurare il servizio "To Go API" per l'uso del tenant di Azure Active Directory

1. Aprire la soluzione in Visual Studio 2013.
2. Nel progetto ToGoAPI aprire il file `web.config`.
3. Trovare la chiave dell'app `ida:Tenant` e sostituire il valore con il nome del tenant di Azure AD.
4. Trovare la chiave dell'app `ida:Audience` e sostituire il valore con l'URI ID app copiato dal portale di Azure.
5. Sempre nel progetto ToGoAPI aprire il file `Controllers/ToGoListController.cs`. Nell'attributo `[EnableCors...]` immettere l'indirizzo del client To Do SPA. Per impostazione predefinita, questo è `https://localhost:44326`. Assicurarsi di omettere la barra finale.
5. Nel progetto TodoSPA aprire il file `App/Scripts/App.js` e individuare la dichiarazione dell'oggetto `endpoints`.
6. Immettere un mapping del percorso dell'endpoint di To Go API con il relativo identificatore di risorsa o URI ID app. Il nome della proprietà dell'oggetto `endpoints` deve corrispondere all'indirizzo di To Go API. Per impostazione predefinita, questo è `https://localhost:44327/`. Il valore di questa proprietà deve corrispondere all'URI ID app copiato dal portale, ad esempio `https://<your_tenant_name>/ToGoAPI`.
8. Non occorre impostare ancora gli altri valori di configurazione. Verranno configurati successivamente.
9. Sempre nel progetto TodoSPA aprire il file `App/Scripts/toGoListSvc.js`. Sostituire il valore della variabile `apiEndpoint` con l'indirizzo di To Go API. Per impostazione predefinita, questo è `https://localhost:44327/`.

## Registrare l'applicazione "To Do" con singole pagine nel tenant di Azure Active Directory

1. Accedere un'altra volta al [portale di gestione di Azure](https://manage.windowsazure.com).
2. Fare clic su **Active Directory** nel pannello di navigazione a sinistra.
3. Fare clic sul tenant in cui si desidera registrare l'applicazione di esempio.
4. Fare clic sulla scheda **Applicazioni**.
5. Nel pannello fare clic su **Aggiungi**.
6. Fare clic su **Aggiungi un'applicazione che l'organizzazione sta sviluppando**.
7. Immettere un nome descrittivo per l'applicazione, ad esempio "To Do SPA", selezionare **Applicazione Web e/o API Web** e fare clic su **Avanti**.
8. Come URL di accesso, immettere l'URL di base per l'esempio, che per impostazione predefinita è `https://localhost:44326/`.
9. Per l'URI ID app immettere `https://<your_directory_name>/ToDoSPA`, sostituendo `<your_directory_name>` con il nome della directory di Azure AD.
10. Nella sezione **Autorizzazioni per altre applicazioni** fare clic su **Aggiungi applicazione**. Selezionare **Altro** nell'elenco a discesa **Mostra** e fare clic sul segno di spunta superiore. Individuare e fare clic su To Go API, quindi fare clic sul segno di spunta inferiore per aggiungere l'applicazione. Selezionare **Accesso a To Go API** nell'elenco a discesa **Autorizzazioni delegate** e quindi salvare la configurazione.

La procedura è stata completata. Prima di procedere al passaggio successivo, è necessario individuare l'ID client dell'applicazione.

1. Sempre dal portale di Azure fare clic sulla scheda **Configura** dell'applicazione.
2. Individuare il valore dell'ID client e copiarlo negli Appunti.


## Abilitare la concessione implicita OAuth2 per l'applicazione

Per impostazione predefinita, le applicazioni sottoposte a provisioning in Azure AD non sono abilitate per l'uso della concessione implicita OAuth2. Per eseguire questo esempio, sarà necessario dare il consenso esplicito.

1. Dopo l'esecuzione dei passaggi precedenti, il browser visualizza ancora il portale di gestione di Azure e in particolare la scheda **Configura** della voce dell'applicazione.
2. Usando il pulsante **Gestisci manifesto** nel pannello, scaricare il file manifesto per l'applicazione e salvarlo su disco.
3. Aprire il file manifesto in un editor di testo. Cercare la proprietà `oauth2AllowImplicitFlow`. Come si può notare, la proprietà è impostata su `false` Impostarla invece su `true` e salvare il file.
4. Usando il pulsante **Gestisci manifesto**, caricare il file manifesto aggiornato. Salvare la configurazione dell'app.

## Configurare il servizio "To Do SPA" per l'uso del tenant di Azure Active Directory

1. Aprire la soluzione in Visual Studio 2013.
2. Nel progetto TodoSPA aprire il file `web.config`.
3. Trovare la chiave dell'app `ida:Tenant` e sostituire il valore con il nome della directory di Azure AD.
4. Trovare la chiave dell'app `ida:Audience` e sostituire il valore con l'ID client dal portale di Azure.
5. Sempre nel progetto TodoSPA aprire un'altra volta il file `App/Scripts/App.js` e individuare la riga `adalAuthenticationServiceProvider.init(`.
6. Sostituire il valore di `tenant` con il nome della directory di Azure AD.
7. Sostituire il valore di `clientId` con l'ID client dal portale di Azure.

## Eseguire l'esempio

Cancellare la soluzione, ricompilarla ed eseguirla.

È possibile attivare l'esperienza di accesso facendo clic sul collegamento di accesso nell'angolo superiore destro oppure facendo direttamente clic sulla scheda **To Do List** o **To Go List**. Esplorare l'esempio effettuando l'accesso, aggiungendo elementi all'elenco di azioni, rimuovendo l'account utente e ricominciando dall'inizio. Aggiungere luoghi all'elenco di posti da vedere, eseguendo operazioni CRUD in To Go API usando CORS.

## Come distribuire questo esempio in Azure

Per distribuire To Do SPA e To Go API in Siti Web di Azure, creare due siti Web, pubblicare ogni progetto in un sito Web e aggiornare entrambi i progetti in modo che facciano riferimento ai nuovi percorso invece che a IIS Express.

### Creare il sito Web di Azure per To Go API

1. Accedere al [portale di gestione di Azure](https://manage.windowsazure.com).
2. Fare clic su **Siti Web** nel pannello di navigazione a sinistra.
3. Fare clic su **Nuovo** nell'angolo inferiore sinistro, selezionare **Calcolo** > **Sito Web** > **Creazione personalizzata**, selezionare il piano e l'area di hosting e assegnare un nome al sito Web, ad esempio togo-contoso.azurewebsites.net. Selezionare un database da usare o crearne uno nuovo. Fare clic su **Crea sito Web**.
4. Dopo la creazione del sito Web, farvi clic per gestirlo. Per questa procedura, scaricare il file con estensione publishsettings e salvarlo. È anche possibile usare altri meccanismi di distribuzione, ad esempio un controllo del codice sorgente. Per altre informazioni sull'uso di un file con estensione publishsettings, vedere [Procedura: Connettersi alla sottoscrizione](../install-configure-powershell.md#Connect).

### Creare il sito Web di Azure per To Do SPA

1. Passare al [portale di gestione di Azure](https://manage.windowsazure.com).
2. Fare clic su **Siti Web** nel pannello di navigazione a sinistra.
3. Fare clic su **Nuovo** nell'angolo inferiore sinistro, selezionare **Calcolo** > **Sito Web** > **Creazione personalizzata**, selezionare il piano e l'area di hosting e assegnare un nome al sito Web, ad esempio todo-contoso.azurewebsites.net. Selezionare un database da usare. È possibile usare lo stesso database specificato per To Go API. Fare clic su **Crea sito Web**.
4. Dopo la creazione del sito Web, farvi clic per gestirlo. Scaricare di nuovo il file con estensione publishsettings per questo sito e quindi salvarlo.

### Aggiornare entrambi i progetti per l'uso di Siti Web di Azure

1. In Visual Studio passare al progetto TodoSPA.
2. Sono necessarie due modifiche. In `App\Scripts\app.js` sostituire il nome della proprietà dell'oggetto `endpoints` specificando il nuovo indirizzo di To Go API, ad esempio `https://togo-contoso.azurewebsites.net/`. In `App\Scripts\toGoListSvc.js` sostituire la variabile `apiEndpoint` con lo stesso valore.
3. Nel progetto ToGoAPI è necessaria solo una modifica. In `Controllers\ToGoListController.cs` aggiornare l'attributo `[EnableCors...]` in base al nuovo indirizzo di To Do SPA, ad esempio `https://todo-contoso.azurewebsites.net`. Assicurarsi di nuovo di omettere la barra finale.

### Pubblicare To Go API in Siti Web di Azure

1. Passare a Visual Studio e quindi al progetto ToGoAPI. Fare clic con il pulsante destro del mouse sul progetto in Esplora soluzioni e scegliere **Pubblica**. Fare clic su **Importa** e importare il profilo di pubblicazione scaricato per To Go API.
6. Nella scheda **Connessione** aggiornare l'URL di destinazione in modo che sia https, ad esempio https://togo-constoso.azurewebsites.net. Fare clic su **Avanti**.
7. Nella scheda **Impostazioni** assicurarsi che l'opzione **Abilita autenticazione a livello aziendale** NON sia selezionata. Fare clic su **Pubblica**.
8. Visual Studio pubblicherà il progetto e aprirà automaticamente nel browser la pagina corrispondente all'URL del progetto. Se viene visualizzata la pagina Web predefinita del progetto, la pubblicazione è riuscita.

### Pubblicare To Do SPA in Siti Web di Azure

1. Passare a Visual Studio e quindi al progetto TodoSPA. Fare clic con il pulsante destro del mouse sul progetto in Esplora soluzioni e scegliere **Pubblica**. Fare clic su **Importa** e importare il file con estensione publishsettings scaricato per To Do SPA.
6. Nella scheda **Connessione** aggiornare l'URL di destinazione in modo che sia https, ad esempio https://todo-contoso.azurewebsites.net. Fare clic su **Avanti**.
7. Nella scheda **Impostazioni** assicurarsi che l'opzione **Abilita autenticazione a livello aziendale** NON sia selezionata. Fare clic su **Pubblica**.
8. Visual Studio pubblicherà il progetto e aprirà automaticamente nel browser la pagina corrispondente all'URL del progetto. Se viene visualizzata la pagina Web predefinita del progetto, la pubblicazione è riuscita.

### Aggiornare la configurazione di To Do SPA nel tenant di Active Directory

1. Passare al [portale di gestione di Azure](https://manage.windowsazure.com).
2. Nel pannello di navigazione a sinistra fare clic su **Active Directory** e selezionare il tenant.
3. Nella scheda **Applicazioni** selezionare l'applicazione **To Do SPA**.
4. Nella scheda **Configura** aggiornare i campi **URL accesso** e **URL di risposta** nell'indirizzo di SPA, ad esempio https://todo-contoso.azurewebsites.net. Salvare la configurazione.

## Informazioni sul codice

Di seguito sono elencati i file chiave contenenti logica di autenticazione:

- **App.js**: inserisce la dipendenza dal modulo ADAL, fornisce i valori di configurazione dell'app usati da ADAL per la definizione delle interazioni del protocollo con Azure AD e indica le route cui non è possibile accedere senza prima eseguire l'autenticazione.

- **index.html**: contiene un riferimento ad adal.js.

- **HomeController.js**: mostra come usare in modo ottimale i metodi login() e logOut() in ADAL.

- **UserDataController.js**: mostra come estrarre informazioni sugli utenti da id_token memorizzato nella cache.

Un ringraziamento speciale a @matvelloso per il contributo alla creazione di questa esercitazione.


## Passaggi successivi

Le seguenti risorse aggiuntive illustrano come usare Azure AD per l'aggiunta di autenticazione e autorizzazione alle applicazioni Web e alle API Web:

+ [Esempi di codice di Azure Active Directory](https://msdn.microsoft.com/library/azure/dn646737.aspx)
+ [Scenari di autenticazione per Azure AD](https://msdn.microsoft.com/library/azure/dn499820.aspx)

<!---HONumber=58--> 