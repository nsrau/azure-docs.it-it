---
title: Introduzione a Cordova per Azure AD | Microsoft Docs
description: Come compilare un'applicazione Cordova che si integra con Azure AD per l'accesso e chiama le API protette di Azure AD usando OAuth.
services: active-directory
documentationcenter: 
author: vibronet
manager: mtillman
editor: 
ms.assetid: b1a8d7bd-7ad6-44d5-8ccb-5255bb623345
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 11/30/2017
ms.author: vittorib
ms.custom: aaddev
ms.openlocfilehash: b489add83a462d1d3902831d63be0b70e2443718
ms.sourcegitcommit: 234c397676d8d7ba3b5ab9fe4cb6724b60cb7d25
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/20/2017
---
# <a name="azure-ad-cordova-getting-started"></a>Guida introduttiva Azure Cordova di Active Directory
[!INCLUDE [active-directory-devquickstarts-switcher](../../../includes/active-directory-devquickstarts-switcher.md)]

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

È possibile usare Apache Cordova per sviluppare applicazioni HTML5/JavaScript che possono essere eseguite su dispositivi mobili come applicazioni native complete. Con Azure Active Directory (Azure AD) è possibile aggiungere funzionalità di autenticazione di livello aziendale alle applicazioni Cordova.

Un plug-in Cordova esegue il wrapping di SDK nativi di Azure AD in iOS, Android, Windows Store e Windows Phone. Usando questo plug-in è possibile migliorare l'applicazione, in modo da supportare l'accesso con gli account Active Directory di Windows Server degli utenti, ottenere l'accesso alle API di Office 365 e Azure e contribuire alla protezione delle chiamate all'API Web personalizzata.

In questa esercitazione viene usato il plug-in Apache Cordova per Active Directory Authentication Library (ADAL) per migliorare una semplice app aggiungendo le funzionalità seguenti:

* Sono sufficienti poche righe di codice per autenticare un utente e ottenere un token.
* Usare il token per richiamare l'API Graph per eseguire query sulla directory e visualizzare i risultati.  
* Usare la cache dei token di ADAL per ridurre al minimo le richieste di autenticazione per l'utente.

Per apportare i miglioramenti, è necessario eseguire queste operazioni:

1. Registrare un'applicazione con Azure AD.
2. Aggiungere codice all'app per la richiesta di token.
3. Aggiungere codice per usare il token per eseguire query sull'API Graph e visualizzare i risultati.
4. Creare il progetto di distribuzione di Cordova con tutte le piattaforme di destinazione desiderate e il plug-in ADAL per Cordova, quindi testare la soluzione negli emulatori.

## <a name="prerequisites"></a>Prerequisiti
Per completare questa esercitazione, sono necessari:

* Tenant di Azure AD nel quale è disponibile un account con diritti per lo sviluppo di app.
* Ambiente di sviluppo configurato per l'uso di Apache Cordova.  

Se entrambi questi elementi sono già configurati, andare direttamente al passaggio 1.

Se non è disponibile un tenant di Azure AD, vedere le [istruzioni su come ottenerne uno](active-directory-howto-tenant.md).

Se nel computer in uso non è configurato Apache Cordova, installare quanto segue:

* [Git](http://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
* [Node.js](https://nodejs.org/download/)
* [Interfaccia della riga di comando di Cordova](https://cordova.apache.org/) (può essere installata facilmente tramite Gestione pacchetti NPM: `npm install -g cordova`)

Le installazioni precedenti dovrebbero funzionare sia su PC che su Mac.

Ogni piattaforma di destinazione ha prerequisiti diversi:

* Per compilare ed eseguire un'app per Windows Tablet/PC o Windows Phone:
  * Installare [Visual Studio 2013 per Windows con Update 2 o versione successiva](http://www.visualstudio.com/downloads/download-visual-studio-vs#d-express-windows-8) (Express o altra versione) oppure [Visual Studio 2015](https://www.visualstudio.com/downloads/download-visual-studio-vs#d-community).

* Per compilare ed eseguire un'app per iOS:

  * Installare Xcode 6.x o versione successiva. Scaricarlo dal [sito Apple Developer](http://developer.apple.com/downloads) o da [Mac App Store](http://itunes.apple.com/us/app/xcode/id497799835?mt=12).
  * Installare [ios-sim](https://www.npmjs.org/package/ios-sim). È possibile usarlo per avviare le app iOS nel simulatore iOS dalla riga di comando. È possibile installarlo con facilità tramite il terminale: `npm install -g ios-sim`.
* Per compilare ed eseguire un'app per Android:

  * Installare [Java Development Kit (JDK) 7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) o versione successiva. Assicurarsi che la variabile di ambiente `JAVA_HOME` sia impostata correttamente in base al percorso di installazione di JDK, ad esempio, C:\Programmi\Java\jdk1.7.0_75.
  * Installare [Android SDK](http://developer.android.com/sdk/installing/index.html?pkg=tools) e aggiungere il percorso `<android-sdk-location>\tools`, ad esempio C:\tools\Android\android-sdk\tools, alla variabile di ambiente `PATH`.
  * Aprire Android SDK Manager, ad esempio, tramite il terminale `android`, e installare:
    * *Android 5.0.1 (API 21)* Platform SDK
    * *Android SDK Build Tools* 19.1.0 o versione successiva
    * *Android Support Repository* (funzionalità aggiuntive)

  Android SDK non fornisce alcuna istanza dell'emulatore predefinito. Crearne una eseguendo `android avd` dal terminale e quindi selezionare **Create** (Crea) per eseguire l'app Android nell'emulatore. È consigliabile usare un' API di livello 19 o superiore. Per altre informazioni sull'emulatore Android e sulle opzioni di creazione, vedere [AVD Manager](http://developer.android.com/tools/help/avd-manager.html) nel sito Android.

## <a name="step-1-register-an-application-with-azure-ad"></a>Passaggio 1: Registrare un'applicazione con Azure AD
Questo passaggio è facoltativo. L'esercitazione fornisce valori di cui è stato effettuato preventivamente il provisioning, che consentono di vedere l'esempio in azione senza effettuare alcun provisioning nel proprio tenant. È tuttavia consigliabile eseguire questo passaggio e acquisire familiarità con il processo, perché sarà necessario quando si creano applicazioni personalizzate.

Azure AD rilascia token solo alle applicazioni note. Prima di poter usare Azure AD dall'app, è necessario creare una voce nel proprio tenant. Per registrare una nuova applicazione nel tenant:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Nella barra superiore fare clic sull'account. Nell'elenco di **Directory** scegliere il tenant di Azure AD in cui si vuole registrare l'applicazione.
3. Fare clic su **More Services** (Altri servizi) nel riquadro sinistro, quindi selezionare **Azure Active Directory**.
4. Fare clic su **Registrazioni per l'app** e scegliere **Aggiungi**.
5. Seguire le istruzioni e creare un'**Applicazione client nativa**. Benché le app Cordova siano basate su HTML, verrà creata un'applicazione client nativa. L'opzione **Applicazione client nativa** deve essere selezionata per consentire il funzionamento dell'applicazione.
  * Il **nome** descrive l'applicazione agli utenti.
  * **URI di reindirizzamento** è l'URI usato per restituire i token all'app. Immettere **http://MyDirectorySearcherApp**.

Al termine della registrazione, Azure AD assegna un ID applicazione univoco all'app. Questo valore servirà nelle sezioni successive. È possibile trovarlo nella scheda applicazione dell'app appena creata.

Per eseguire `DirSearchClient Sample`, assegnare all'app appena creata l'autorizzazione ad eseguire query nell'API Graph di Azure AD:

1. Nella pagina **Impostazioni** selezionare **Autorizzazioni necessarie** e selezionare **Aggiungi**.  
2. Per l'applicazione Azure Active Directory, selezionare **Microsoft Graph** come API e aggiungere l'autorizzazione **Access the directory as the signed-in user** (Accesso alla directory come utente connesso) in **Autorizzazioni delegate**.  In questo modo l'applicazione può cercare gli utenti nell'API Graph.

## <a name="step-2-clone-the-sample-app-repository"></a>Passaggio 2: Clonare il repository di app di esempio
Dalla shell o dalla riga di comando digitare il comando seguente:

    git clone -b skeleton https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-Cordova.git

## <a name="step-3-create-the-cordova-app"></a>Passaggio 3: Creare l'app Cordova
Esistono diversi metodi per la creazione di applicazioni Cordova. In questa esercitazione si userà l'interfaccia della riga di comando (CLI) di Cordova.

1. Dalla shell o dalla riga di comando digitare il comando seguente:

        cordova create DirSearchClient

   Questo comando crea la struttura di cartelle e lo scaffolding per il progetto Cordova.

2. Passare alla nuova cartella DirSearchClient:

        cd .\DirSearchClient

3. Copiare il contenuto del progetto di partenza nella sottocartella www usando File Manager o il comando seguente nella shell:

  * Windows: `xcopy ..\NativeClient-MultiTarget-Cordova\DirSearchClient www /E /Y`
  * Mac: `cp -r  ../NativeClient-MultiTarget-Cordova/DirSearchClient/* www`

4. Aggiungere il plug-in relativo all'elenco elementi consentiti. Questa operazione è necessaria per richiamare l'API Graph.

        cordova plugin add cordova-plugin-whitelist

5. Aggiungere tutte le piattaforme che si prevede di supportare. Per avere un esempio funzionante, è necessario eseguire almeno uno dei comandi seguenti. Si noti che non sarà possibile emulare iOS in Windows o emulare Windows in Mac.

        cordova platform add android
        cordova platform add ios
        cordova platform add windows

6. Aggiungere il plug-in ADAL per Cordova al progetto:

        cordova plugin add cordova-plugin-ms-adal

## <a name="step-4-add-code-to-authenticate-users-and-obtain-tokens-from-azure-ad"></a>Passaggio 4: Aggiungere codice per autenticare gli utenti e ottenere i token da Azure AD
L'applicazione sviluppata in questa esercitazione fornirà una semplice funzionalità di ricerca nella directory. L'utente può quindi digitare l'alias di qualsiasi utente nella directory e visualizzare alcuni attributi di base. Il progetto iniziale contiene la definizione dell'interfaccia utente di base dell'app (in www/index.html) e lo scaffolding che collega i cicli degli eventi dell'app di base, i binding dell'interfaccia utente e la logica di visualizzazione dei risultati (in www/js/index.js). L'unica cosa affidata allo sviluppatore è l'aggiunta della logica che implementa le attività relative all'identità.

La prima cosa da fare nel codice consiste nell'introdurre i valori di protocollo usati da Azure AD per l'identificazione dell'app e delle risorse di destinazione. Questi valori verranno usati in seguito per costruire le richieste di token. Inserire il frammento seguente all'inizio del file index.js:

```javascript
var authority = "https://login.microsoftonline.com/common",
    redirectUri = "http://MyDirectorySearcherApp",
    resourceUri = "https://graph.windows.net",
    clientId = "a5d92493-ae5a-4a9f-bcbf-9f1d354067d3",
    graphApiVersion = "2013-11-08";
```

I valori `redirectUri` e `clientId` devono corrispondere ai valori che descrivono l'app in Azure AD. È possibile trovarli nella scheda **Configura** nel portale di Azure, come descritto in precedenza nel Passaggio 1 di questa esercitazione.

> [!NOTE]
> Se si è scelto di non registrare una nuova app nel tenant, è sufficiente incollare i valori preconfigurati senza modifica. È quindi possibile visualizzare l'esecuzione dell'esempio, anche se occorre creare sempre una voce personalizzata per le app destinate alla produzione.

Aggiungere quindi il codice di richiesta del token. Inserire il frammento seguente tra le definizioni `search`e `renderData`:

```javascript
    // Shows the user authentication dialog box if required
    authenticate: function (authCompletedCallback) {

        app.context = new Microsoft.ADAL.AuthenticationContext(authority);
        app.context.tokenCache.readItems().then(function (items) {
            if (items.length > 0) {
                authority = items[0].authority;
                app.context = new Microsoft.ADAL.AuthenticationContext(authority);
            }
            // Attempt to authorize the user silently
            app.context.acquireTokenSilentAsync(resourceUri, clientId)
            .then(authCompletedCallback, function () {
                // We require user credentials, so this triggers the authentication dialog box
                app.context.acquireTokenAsync(resourceUri, clientId, redirectUri)
                .then(authCompletedCallback, function (err) {
                    app.error("Failed to authenticate: " + err);
                });
            });
        });

    },
```
Si esaminerà ora la funzione suddividendola nelle due parti principali.
Questo esempio è progettato per funzionare con qualsiasi tenant, invece di essere associato a uno in particolare. Usa l'endpoint "/common" che consente all'utente di immettere qualsiasi account al momento dell'autenticazione e indirizza la richiesta al tenant di appartenenza.

La prima parte del metodo esamina la cache ADAL per verificare se è già stato archiviato un token. In tale caso, il metodo usa i tenant di provenienza del token per la reinizializzazione di ADAL. Questo approccio è necessario per evitare richieste aggiuntive, in quanto l'uso di "/common" comporta sempre la richiesta all'utente di immettere un nuovo account.

```javascript
        app.context = new Microsoft.ADAL.AuthenticationContext(authority);
        app.context.tokenCache.readItems().then(function (items) {
            if (items.length > 0) {
                authority = items[0].authority;
                app.context = new Microsoft.ADAL.AuthenticationContext(authority);
            }
```
La seconda parte del metodo esegue la richiesta di token vera e propria. Il metodo `acquireTokenSilentAsync` chiede ad ADAL di restituire un token per la risorsa specificata senza mostrare alcuna esperienza utente. Ciò può verificarsi se nella cache è già archiviato un token di accesso appropriato o se è presente un token di aggiornamento che può essere usato per ottenere un nuovo token di accesso senza mostrare alcuna richiesta. Se il tentativo non riesce, si eseguirà il fallback a `acquireTokenAsync`, che richiederà in modo visibile all'utente di autenticarsi.

```javascript
            // Attempt to authorize the user silently
            app.context.acquireTokenSilentAsync(resourceUri, clientId)
            .then(authCompletedCallback, function () {
                // We require user credentials, so this triggers the authentication dialog box
                app.context.acquireTokenAsync(resourceUri, clientId, redirectUri)
                .then(authCompletedCallback, function (err) {
                    app.error("Failed to authenticate: " + err);
                });
            });
```
Dopo avere ottenuto il token, si potrà finalmente richiamare l'API Graph ed eseguire la query di ricerca specifica. Inserire il frammento seguente sotto la definizione di `authenticate`:

```javascript
// Makes an API call to receive the user list
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
I file usati come punto di partenza hanno fornito un'esperienza utente essenziale per l'immissione dell'alias di un utente in una casella di testo. Questo metodo usa tale valore per costruire una query, combinarla con il token di accesso, inviarla a Microsoft Graph e analizzare i risultati. Il metodo `renderData`, già presente nel file usato come punto di partenza, si occupa della visualizzazione dei risultati.

## <a name="step-5-run-the-app"></a>Passaggio 5: Eseguire l'app
L'app è finalmente pronta per essere eseguita. Il funzionamento è semplice: dopo l'avvio dell'app, immettere l'alias dell'utente che si vuole cercare, quindi fare clic sul pulsante. Viene richiesto di autenticarsi. Dopo l'autenticazione e il completamento della ricerca, vengono visualizzati gli attributi relativi all'utente cercato.

Le esecuzioni successive verranno eseguite senza visualizzare alcuna richiesta, grazie alla presenza nella cache del token acquisito in precedenza.

I passaggi concreti per l'esecuzione dell'app variano in base alla piattaforma.

### <a name="windows-10"></a>Windows 10
   Tablet/PC: `cordova run windows --archs=x64 -- --appx=uap`

   Dispositivi mobili (richiede un dispositivo Windows 10 Mobile connesso a un PC): `cordova run windows --archs=arm -- --appx=uap --phone`

   > [!NOTE]
   > Durante la prima esecuzione è possibile che venga richiesto di accedere per ottenere una licenza per sviluppatori. Per altre informazioni, vedere [Licenza per sviluppatori](https://msdn.microsoft.com/library/windows/apps/hh974578.aspx).

### <a name="windows-81-tabletpc"></a>Tablet/PC con Windows 8.1
   `cordova run windows`

   > [!NOTE]
   > Durante la prima esecuzione è possibile che venga richiesto di accedere per ottenere una licenza per sviluppatori. Per altre informazioni, vedere [Licenza per sviluppatori](https://msdn.microsoft.com/library/windows/apps/hh974578.aspx).

### <a name="windows-phone-81"></a>Windows Phone 8.1
   Per l'esecuzione su un dispositivo connesso: `cordova run windows --device -- --phone`

   Per l'esecuzione nell'emulatore predefinito: `cordova emulate windows -- --phone`

   Usare `cordova run windows --list -- --phone` per vedere tutte le destinazioni disponibili e `cordova run windows --target=<target_name> -- --phone` per eseguire l'applicazione su un dispositivo o un emulatore specifico, ad esempio, `cordova run windows --target="Emulator 8.1 720P 4.7 inch" -- --phone`.

### <a name="android"></a>Android
   Per l'esecuzione su un dispositivo connesso: `cordova run android --device`

   Per l'esecuzione nell'emulatore predefinito: `cordova emulate android`

   Assicurarsi di avere creato l'istanza dell'emulatore con AVD Manager, come indicato nella sezione Prerequisiti.

   Usare `cordova run android --list` per vedere tutte le destinazioni disponibili e `cordova run android --target=<target_name>` per eseguire l'applicazione su un dispositivo o un emulatore specifico, ad esempio, `cordova run android --target="Nexus4_emulator"`.

### <a name="ios"></a>iOS
   Per l'esecuzione su un dispositivo connesso: `cordova run ios --device`

   Per l'esecuzione nell'emulatore predefinito: `cordova emulate ios`

   > [!NOTE]
   > Assicurarsi che il pacchetto `ios-sim` sia installato per l'esecuzione sull'emulatore. Per altre informazioni, vedere la sezione "Prerequisiti".

    Use `cordova run ios --list` to see all available targets and `cordova run ios --target=<target_name>` to run the application on specific device or emulator (for example, `cordova run android --target="iPhone-6"`).

    Use `cordova run --help` to see additional build and run options.

## <a name="next-steps"></a>Passaggi successivi
Come riferimento, l'esempio completo (senza i valori di configurazione) è disponibile in [GitHub](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-Cordova/tree/complete/DirSearchClient).

A questo punto è possibile passare a scenari più avanzati e più interessanti. Vedere [Proteggere un'API Web Node.js con Azure AD](active-directory-devquickstarts-webapi-nodejs.md).

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
