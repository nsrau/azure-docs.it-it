<properties
	pageTitle="Introduzione a Cordova per Azure AD | Microsoft Azure"
	description="Come compilare un'applicazione Cordova che si integra con Azure AD per l'accesso e chiama le API protette di Azure AD usando OAuth."
	services="active-directory"
	documentationCenter=""
	authors="vibronet"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="10/13/2015"
	ms.author="vittorib"/>

# Integrare Azure AD con un'app Apache Cordova

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Apache Cordova consente di sviluppare applicazioni HTML5/JavaScript che possono essere eseguite su dispositivi mobili come applicazioni native complete. Con Azure AD è possibile aggiungere funzionalità di autenticazione di livello aziendale alle applicazioni Cordova. Grazie a un plug-in Cordova che racchiude SDK nativi di AD su iOS, Android, Windows Store e Windows Phone, è possibile migliorare la propria applicazione per supportare l'accesso con gli account AD degli utenti, ottenere l'accesso a Office 365 e all'API Azure, nonché proteggere le chiamate all'API Web personalizzata.

In questa esercitazione si userà il plug-in Apache Cordova per Active Directory Authentication Library (ADAL) per migliorare una semplice app con le funzionalità seguenti:

-	Con poche righe di codice, autenticare un utente AD e ottenere un token per chiamare l'API Graph di Azure AD.
-	Usare il token per richiamare l'API Graph per eseguire query sulla directory e visualizzare i risultati.  
-	Sfruttare la cache dei token di ADAL per ridurre al minimo le richieste di autenticazione per l'utente.

A questo scopo è necessario:

2. Registrare un'applicazione con Azure AD.
2. Aggiungere codice all'app per la richiesta di token.
3. Aggiungere codice per usare il token per eseguire query sull'API Graph e visualizzare i risultati.
4. Creare il progetto di distribuzione di Cordova con tutte le piattaforme di destinazione desiderate e il plug-in ADAL per Cordova, quindi testare la soluzione negli emulatori.

## *0. Prerequisiti*

Per completare questa esercitazione, sono necessari:

- Tenant di Azure AD nel quale è disponibile un account con diritti per lo sviluppo di app.
- Ambiente di sviluppo configurato per l'uso di Apache Cordova.  

Se entrambi questi elementi sono già configurati, andare direttamente al passaggio 1.

Se non è disponibile un tenant di Azure AD, è possibile trovare [istruzioni su come ottenerne uno qui](active-directory-howto-tenant.md).

Se nel computer in uso non è configurato Apache Cordova, installare quanto segue:

- [Git](http://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
- [NodeJS](https://nodejs.org/download/)
- [Cordova CLI](https://cordova.apache.org/) (può essere installata facilmente tramite Gestione pacchetti NPM: `npm install -g cordova`)

Si noti che questi componenti dovrebbero funzionare sia su PC che su Mac.

Ogni piattaforma di destinazione ha prerequisiti diversi.

- Per compilare ed eseguire la versione dell'app per tablet/PC Windows o Windows Phone
	- [Visual Studio 2013 per Windows con Update 2 o versione successiva](http://www.visualstudio.com/downloads/download-visual-studio-vs#d-express-windows-8) (Express o un'altra versione).
- Per compilare ed eseguire l'app per iOS
	-   Xcode 5.x o versione successiva. Scaricarlo all'indirizzo http://developer.apple.com/downloads o da [Mac App Store](http://itunes.apple.com/us/app/xcode/id497799835?mt=12).
	-   [ios-sim](https://www.npmjs.org/package/ios-sim): consente di avviare app iOS nel simulatore iOS dalla riga di comando (può essere installato facilmente tramite il terminale `npm install -g ios-sim`).

- Per compilare ed eseguire un'applicazione per Android
	- Installare [Java Development Kit (JDK) 7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) o versione successiva. Assicurarsi che la variabile di ambiente `JAVA_HOME` sia impostata correttamente in base al percorso di installazione di JDK (ad esempio, C:\\Programmi\\Java\\jdk1.7.0\_75).
	- Installare [Android SDK](http://developer.android.com/sdk/installing/index.html?pkg=tools) e aggiungere il percorso `<android-sdk-location>\tools` (ad esempio, C:\\tools\\Android\\android-sdk\\tools) alla variabile di ambiente `PATH`.
	- Aprire Android SDK Manager (ad esempio, tramite il terminale `android`) e installare
    - *Android 5.0.1 (API 21)* Platform SDK
    - *Android SDK Build Tools* versione 19.1.0 o successiva
    - *Android Support Repository* (funzionalità aggiuntive)

  Android SDK non fornisce alcuna istanza dell'emulatore predefinito. Crearne uno nuovo eseguendo `android avd` dal terminale e quindi selezionare *Create* per eseguire l'app Android nell'emulatore. È consigliato *API Level* 19 o superiore; vedere[AVD Manager](http://developer.android.com/tools/help/avd-manager.html) per altre informazioni sulle opzioni di creazione e dell'emulatore Android.


## *1. Registrare un'applicazione con Azure AD*

Nota: questo passaggio è facoltativo. L'esercitazione fornisce valori di cui è stato eseguito preventivamente il provisioning, che consentiranno di vedere l'esempio in azione senza eseguire alcun provisioning nel proprio tenant. È tuttavia consigliabile eseguire questo passaggio e acquisire familiarità con il processo, in quanto sarà necessario quando si creeranno applicazioni personalizzate.

Azure AD rilascerà token solo alle applicazioni note. Prima di poter usare Azure AD dall'app, è necessario creare una voce nel proprio tenant. Per registrare una nuova applicazione nel tenant:

- Accedere al portale di gestione di Azure.
- Nel pannello di navigazione a sinistra fare clic su Active Directory.
- Selezionare il tenant in cui si desidera registrare l'applicazione.
- Fare clic sulla scheda Applicazioni, quindi fare clic su Aggiungi nel pannello in basso.
- Seguire le istruzioni e creare una nuova "Applicazione client nativa".
    - Il nome dell'applicazione deve essere una descrizione per gli utenti finali.
    -	"URI di reindirizzamento" è l'URI usato per restituire i token all'app. Immettere `http://MyDirectorySearcherApp`.

Dopo avere completato la registrazione, AAD assegnerà all'app un identificatore client univoco. Questo valore sarà necessario nelle sezioni successive: è possibile trovarlo nella scheda Configura dell'app appena creata.

## *2. Clonare i repository necessari per l'esercitazione*

Dalla shell o dalla riga di comando digitare il comando seguente:

    git clone https://github.com/AzureAD/azure-activedirectory-library-for-cordova.git
    git clone -b skeleton https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-Cordova.git

## *3. Creare l'app Cordova*

Esistono diversi metodi per la creazione di applicazioni Cordova. In questa esercitazione si userà l'interfaccia della riga di comando (CLI) di Cordova. Dalla shell o dalla riga di comando digitare il comando seguente:


     cordova create DirSearchClient --copy-from="NativeClient-MultiTarget-Cordova/DirSearchClient"

Verranno creati la struttura di cartelle e lo scaffolding per il progetto Cordova, copiando il contenuto dal progetto iniziale nella sottocartella www. Passare alla nuova cartella DirSearchClient.

    cd .\DirSearchClient

Aggiungere il plug-in whitelist necessario per richiamare l'API Graph.

     cordova plugin add https://github.com/apache/cordova-plugin-whitelist.git

Aggiungere quindi tutte le piattaforme che si prevede di supportare. Per avere un esempio funzionante, è necessario eseguire almeno uno dei comandi seguenti. Non sarà possibile emulare iOS in Windows o Windows/Windows Phone su un Mac.

    cordova platform add android@97718a0a25ec50fedf7b023ae63bfcffbcfafb4b
    cordova platform add ios
    cordova platform add windows

Infine è possibile aggiungere il plug-in ADAL per Cordova al progetto.

    cordova plugin add ../azure-activedirectory-library-for-cordova

## *3. Aggiungere codice per autenticare gli utenti e ottenere token da AAD*

L'applicazione che si sviluppa in questa esercitazione fornirà una funzionalità di ricerca di directory essenziale, in cui l'utente può digitare l'alias di qualsiasi utente nella directory e visualizzare alcuni attributi di base. Il progetto iniziale contiene la definizione dell'interfaccia utente di base dell'app (in www/index.html) e lo scaffolding che collega i cicli degli eventi dell'app di base, i binding dell'interfaccia utente e la logica di visualizzazione dei risultati (in www/js/index.js). L'unica cosa affidata allo sviluppatore è l'aggiunta della logica che implementa le attività relative all'identità.

La prima cosa da fare consiste nell'introdurre nel codice i valori di protocollo usati da AAD per l'identificazione dell'app e delle risorse di destinazione. Questi valori verranno usati in seguito per costruire le richieste di token. Inserire il frammento seguente all'inizio del file index.js.

```javascript
    var authority = "https://login.windows.net/common",
    redirectUri = "http://MyDirectorySearcherApp",
    resourceUri = "https://graph.windows.net",
    clientId = "a5d92493-ae5a-4a9f-bcbf-9f1d354067d3",
    graphApiVersion = "2013-11-08";
```

I valori `redirectUri` e `clientId` dovranno corrispondere ai valori che descrivono l'app in AAD. È possibile trovarli nella scheda Configura nel portale di Azure, come descritto nel precedente passaggio 1 di questa esercitazione. Nota: se si è scelto di non registrare una nuova app nel proprio tenant, è possibile incollare semplicemente i valori predefiniti riportati sopra così come sono, che consentiranno di visualizzare l'esempio in esecuzione. È tuttavia consigliabile creare una voce personalizzata per le app destinate all'ambiente di produzione.

A questo punto, è necessario aggiungere il codice per la richiesta del token effettivo. Inserire il frammento seguente tra le definizioni `search `e `renderdata `.

```javascript
    // Shows user authentication dialog if required.
    authenticate: function (authCompletedCallback) {

        app.context = new Microsoft.ADAL.AuthenticationContext(authority);
        app.context.tokenCache.readItems().then(function (items) {
            if (items.length > 0) {
                authority = items[0].authority;
                app.context = new Microsoft.ADAL.AuthenticationContext(authority);
            }
            // Attempt to authorize user silently
            app.context.acquireTokenSilentAsync(resourceUri, clientId)
            .then(authCompletedCallback, function () {
                // We require user cridentials so triggers authentication dialog
                app.context.acquireTokenAsync(resourceUri, clientId, redirectUri)
                .then(authCompletedCallback, function (err) {
                    app.error("Failed to authenticate: " + err);
                });
            });
        });

    },
```
Si esaminerà ora la funzione suddividendola nelle due parti principali. Questo esempio è progettato per funzionare con qualsiasi tenant, invece di essere associato a uno in particolare. Usa l'endpoint "/common" che consente all'utente di immettere qualsiasi account al momento dell'autenticazione e indirizza la richiesta al tenant di appartenenza. La prima parte del metodo esamina la cache ADAL per verificare se è già archiviato un token. In questo caso, usa i tenant da cui proviene per reinizializzare ADAL. Questo approccio è necessario per evitare richieste aggiuntive, in quanto l'uso di "/common" comporta sempre la richiesta all'utente di immettere un nuovo account. ```javascript
        app.context = new Microsoft.ADAL.AuthenticationContext(authority);
        app.context.tokenCache.readItems().then(function (items) {
            if (items.length > 0) {
                authority = items[0].authority;
                app.context = new Microsoft.ADAL.AuthenticationContext(authority);
            }
``` La seconda parte del metodo esegue la richiesta di token vera e propria. Il metodo `acquireTokenSilentAsync` chiede ad ADAL di restituire un token per la risorsa specificata senza mostrare alcuna esperienza utente. Ciò può verificarsi se nella cache è già archiviato un token di accesso appropriato o se è presente un token di aggiornamento che può essere usato per ottenere un nuovo token di accesso senza mostrare alcuna richiesta. Se il tentativo non riesce, si eseguirà il fallback a `acquireTokenAsync`, che richiederà in modo visibile all'utente di autenticarsi. ```javascript
            // Attempt to authorize user silently
            app.context.acquireTokenSilentAsync(resourceUri, clientId)
            .then(authCompletedCallback, function () {
                // We require user cridentials so triggers authentication dialog
                app.context.acquireTokenAsync(resourceUri, clientId, redirectUri)
                .then(authCompletedCallback, function (err) {
                    app.error("Failed to authenticate: " + err);
                });
            });
``` Dopo avere ottenuto il token, si potrà finalmente richiamare l'API Graph ed eseguire la query di ricerca che si desidera. Inserire il frammento seguente sotto la definizione di `authenticate`.

```javascript
// Makes Api call to receive user list.
    requestData: function (authResult, searchText) {
        var req = new XMLHttpRequest();
        var url = resourceUri + "/" + authResult.tenantId + "/users?api-version=" + graphApiVersion;
        url = searchText ? url + "&$filter=mailNickname eq '" + searchText + "'" : url + "&$top=10";

        req.open("GET", url, true);
        req.setRequestHeader('Authorization', 'Bearer ' + authResult.accessToken);

        req.onload = function(e) {
            if (e.target.status >= 200 && e.target.status < 300) {
                app.renderData(JSON.parse(e.target.response));
                return;
            }
            app.error('Data request failed: ' + e.target.response);
        };
        req.onerror = function(e) {
            app.error('Data request failed: ' + e.error);
        }

        req.send();
    },

```
I file usati come punto di partenza hanno fornito un'esperienza utente essenziale per l'immissione dell'alias di un utente in una casella di testo. Questo metodo usa tale valore per costruire una query, combinarla con il token di accesso, inviarla a Graph e analizzare i risultati. Il metodo renderData, già presente nel file usato come punto di partenza, si occupa della visualizzazione dei risultati.

## *4. Eseguire*
L'app è finalmente pronta per essere eseguita. Il funzionamento è molto semplice: dopo l'avvio dell'app, immettere nella casella di testo l'alias dell'utente che si vuole cercare, quindi fare clic sul pulsante. Verrà richiesto di autenticarsi. Dopo l'autenticazione e il completamento della ricerca, verranno visualizzati gli attributi relativi all'utente cercato. Le esecuzioni successive verranno eseguite senza visualizzare alcuna richiesta, grazie alla presenza nella cache del token acquisito in precedenza. I passaggi concreti per l'esecuzione dell'app variano in base alla piattaforma.


##### Per compilare ed eseguire la versione dell'applicazione per tablet/PC Windows

   `cordova run windows`

   __Nota__: durante la prima esecuzione è possibile che venga richiesto di accedere per ottenere una licenza per sviluppatori. Per altre informazioni, vedere [Ottenere una licenza per sviluppatori](https://msdn.microsoft.com/library/windows/apps/hh974578.aspx).


##### Per compilare ed eseguire un'applicazione in Windows Phone 8.1

   Per l'esecuzione su un dispositivo connesso: `cordova run windows --device -- --phone`

   Per l'esecuzione nell'emulatore predefinito: `cordova emulate windows -- --phone`

   Usare `cordova run windows --list -- --phone` per vedere tutte le destinazioni disponibili e `cordova run windows --target=<target_name> -- --phone` per eseguire l'applicazione su un dispositivo o un emulatore specifico (ad esempio, `cordova run windows --target="Emulator 8.1 720P 4.7 inch" -- --phone`).
##### Per compilare ed eseguire l'applicazione su un dispositivo Android

   Per l'esecuzione su un dispositivo connesso: `cordova run android --device`

   Per l'esecuzione nell'emulatore predefinito: `cordova emulate android`

   __Nota__: accertarsi di avere creato l'istanza dell'emulatore con *AVD Manager* in quanto è indicato nella sezione *Prerequisiti*.

   Usare `cordova run android --list` per vedere tutte le destinazioni disponibili e `cordova run android --target=<target_name>` per eseguire l'applicazione su un dispositivo o un emulatore specifico (ad esempio, `cordova run android --target="Nexus4_emulator"`).

##### Per compilare ed eseguire l'applicazione su un dispositivo iOS

   Per l'esecuzione su un dispositivo connesso: `cordova run ios --device`

   Per l'esecuzione nell'emulatore predefinito: `cordova emulate ios`

   __Nota__: accertarsi di avere installato il pacchetto `ios-sim` per eseguire l'emulatore. Per altre informazioni, vedere la sezione *Prerequisiti*.

    Use `cordova run ios --list` to see all available targets and `cordova run ios --target=<target_name>` to run application on specific device or emulator (for example,  `cordova run android --target="iPhone-6"`).

Usare `cordova run --help` per visualizzare altre opzioni di compilazione ed esecuzione.

Come riferimento, viene fornito l'esempio completato (senza i valori di configurazione) qui. A questo punto è possibile passare a scenari più avanzati e più interessanti. È possibile:

[Proteggere un'API Web Node.js con Azure AD >>](active-directory-devquickstarts-webapi-nodejs.md)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]

<!---HONumber=Oct15_HO3-->