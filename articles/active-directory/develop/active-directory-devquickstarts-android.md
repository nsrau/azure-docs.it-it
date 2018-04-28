---
title: Introduzione ad Android per Azure AD | Microsoft Docs
description: Come compilare un'applicazione Android che si integra con Azure AD per l'accesso e chiama le API protette di Azure AD usando OAuth2.0.
services: active-directory
documentationcenter: android
author: danieldobalian
manager: mtillman
editor: ''
ms.assetid: da1ee39f-89d3-4d36-96f1-4eabbc662343
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 11/30/2017
ms.author: dadobali
ms.custom: aaddev
ms.openlocfilehash: 8ea69fbc95b9d9b2452f041e99c46d223581bdac
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2018
---
# <a name="azure-ad-android-getting-started"></a>Introduzione ad Android per Azure AD
[!INCLUDE [active-directory-devquickstarts-switcher](../../../includes/active-directory-devquickstarts-switcher.md)]

Se si sta sviluppando un'applicazione desktop, Azure Active Directory (Azure AD) semplifica e facilita l'autenticazione degli utenti tramite gli account Active Directory locali. Consente inoltre all'applicazione di usare in modo sicuro qualsiasi API Web protetta da Azure AD, ad esempio le API di Office 365 o l'API di Azure.

Per i client Android che devono accedere a risorse protette, Azure AD fornisce Active Directory Authentication Library (ADAL). La funzione di ADAL è di permettere all'app di ottenere facilmente i token di accesso. Per illustrare la semplicità del processo, verrà compilata un'applicazione Android To-Do List che esegue le operazioni seguenti:

* Ottiene i token di accesso per la chiamata all'API To-Do List con il [protocollo di autenticazione OAuth 2.0](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-protocols-oauth-code).
* Ottiene l'elenco attività (To-Do List) dell'utente.
* Disconnette gli utenti.

Per iniziare, è necessario un tenant di Azure AD in cui poter creare gli utenti e registrare un'applicazione. Se non si ha già un tenant, vedere le [informazioni su come ottenerne uno](active-directory-howto-tenant.md).

## <a name="step-1-download-and-run-the-nodejs-rest-api-todo-sample-server"></a>Passaggio 1: Scaricare ed eseguire il server di esempio dell'API REST TODO per Node.js
L'esempio dell'API REST TODO per Node.js è scritto specificamente per funzionare con l'esempio esistente per la compilazione di una singola API REST To-Do del tenant per Azure AD. Questo è un prerequisito per l'Avvio rapido.

Per informazioni sulla procedura di configurazione di questa funzionalità, vedere gli esempi esistenti in [Servizio API REST di esempio di Azure Active Directory per Node.js](active-directory-devquickstarts-webapi-nodejs.md).


## <a name="step-2-register-your-web-api-with-your-azure-ad-tenant"></a>Passaggio 2: Registrare l'API Web con il tenant di Azure AD
Active Directory supporta l'aggiunta di due tipi di applicazioni:

- API Web che offre servizi agli utenti
- Applicazioni, in esecuzione sul Web o in un dispositivo, che accedono a queste API Web

In questo passaggio si registra l'API Web in esecuzione in locale per il test di questo esempio. In genere questa API Web è un servizio REST che offre le funzionalità a cui un'app deve accedere. Azure AD contribuisce alla protezione di qualsiasi endpoint.

Si presuppone che venga eseguita la registrazione dell'API REST TODO indicata in precedenza, ma questa procedura è applicabile a qualsiasi API Web che si vuole proteggere con Azure Active Directory.

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Nella barra superiore fare clic sull'account. Nell'elenco di **Directory** scegliere il tenant di Azure AD in cui si vuole registrare l'applicazione.
3. Fare clic su **Tutti i servizi** nel riquadro sinistro e quindi selezionare **Azure Active Directory**.
4. Fare clic su **Registrazioni per l'app** e scegliere **Aggiungi**.
5. Immettere un nome descrittivo per l'applicazione, ad esempio **TodoListService**, selezionare **Applicazione Web e/o API Web**, quindi fare clic su **Avanti**.
6. Per l'URL di accesso immettere l'URL di base per l'esempio. Per impostazione predefinita, tale valore è `https://localhost:8080`.
7. Fare clic su **OK** per completare la registrazione.
8. Nel portale di Azure passare alla pagina dell'applicazione, individuare il valore dell'ID applicazione e copiarlo. Servirà in un secondo momento durante la configurazione dell'applicazione.
9. Dalla pagina **Impostazioni** -> **Proprietà** aggiornare l'URI dell'ID app. Immettere `https://<your_tenant_name>/TodoListService`. Sostituire `<your_tenant_name>` con il nome del tenant di Azure AD.

## <a name="step-3-register-the-sample-android-native-client-application"></a>Passaggio 3: Registrare l'applicazione client nativa Android di esempio
È necessario registrare l'applicazione Web in questo esempio. In questo modo l'applicazione può comunicare con l'API Web appena registrata. Se l'applicazione non è registrata, Azure AD non le consentirà nemmeno di chiedere di effettuare l'accesso. Questo fa parte della sicurezza del modello.

Si presuppone che venga eseguita la registrazione dell'applicazione di esempio indicata in precedenza, ma questa procedura è applicabile a qualsiasi app in fase di sviluppo.

> [!NOTE]
> Si procede all'inserimento di un'applicazione e di un'API Web in un tenant, perché è possibile compilare un'app che accede a un'API esterna registrata in Azure AD da un altro tenant. In tal caso, ai clienti verrà richiesto di acconsentire all'uso dell'API nell'applicazione. Active Directory Authentication Library per iOS gestisce automaticamente la richiesta di consenso. Iniziando a esaminare funzionalità più avanzate, si può immaginare come questa sia una parte importante del lavoro necessario per accedere alla gamma di API Microsoft da Azure e da Office, oltre che da altri provider di servizi. Per ora, poiché sia l'API Web che l'applicazione sono state registrate nello stesso tenant, non verrà visualizzata alcuna richiesta di consenso. Di solito ciò avviene se si sta sviluppando un'applicazione che verrà usata solo in azienda.

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Nella barra superiore fare clic sull'account. Nell'elenco di **Directory** scegliere il tenant di Azure AD in cui si vuole registrare l'applicazione.
3. Fare clic su **Tutti i servizi** nel riquadro sinistro e quindi selezionare **Azure Active Directory**.
4. Fare clic su **Registrazioni per l'app** e scegliere **Aggiungi**.
5. Immettere un nome descrittivo per l'applicazione, ad esempio **TodoListClient-Android**, selezionare **Applicazione client nativa**, quindi fare clic su **Avanti**.
6. Per l'URI di reindirizzamento, immettere `http://TodoListClient`. Fare clic su **Fine**.
7. Nella pagina dell'applicazione trovare il valore dell'ID applicazione e copiarlo. Servirà in un secondo momento durante la configurazione dell'applicazione.
8. Nella pagina **Impostazioni** selezionare **Autorizzazioni necessarie** e selezionare **Aggiungi**.  Individuare e selezionare TodoListService, aggiungere l'autorizzazione di **Access TodoListService** in **Autorizzazioni delegate** e fare clic su **Operazione completata**.

Per compilare con Maven, è possibile usare pom.xml al livello principale:

1. Clonare il repository in una directory di propria scelta:

  `$ git clone https://github.com/Azure-Samples/active-directory-android.git`  
2. Seguire la procedura illustrata nei [prerequisiti per la configurazione dell'ambiente Maven per Android](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki/Maven).
3. Configurare l'emulatore con SDK 19.
4. Passare alla cartella radice in cui è stato clonato il repository.
5. Eseguire questo comando: `mvn clean install`
6. Passare alla directory dell'esempio di Avvio rapido: `cd samples\hello`.
7. Eseguire questo comando: `mvn android:deploy android:run`

   Dovrebbe essere visualizzato l'avvio dell'app.
8. Immettere le credenziali dell'utente test per provare.

Oltre al pacchetto AAR, verranno inviati i pacchetti JAR.

## <a name="step-4-download-the-android-adal-and-add-it-to-your-eclipse-workspace"></a>Passaggio 4: Scaricare ADAL per Android e aggiungerlo all'area di lavoro di Eclipse
Per semplificare l'operazione, sono disponibili più opzioni per usare ADAL nel progetto Android:

* È possibile usare il codice sorgente per importare la libreria in Eclipse e collegarla all'applicazione.
* Se si usa Android Studio, è possibile usare il formato di pacchetto AAR e fare riferimento ai file binari.

### <a name="option-1-source-zip"></a>Opzione 1: Zip del codice sorgente
Per scaricare una copia del codice sorgente, fare clic su **Download ZIP** (Scarica ZIP) nel lato destro della pagina. In alternativa, [eseguire il download da GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-android/releases).

### <a name="option-2-source-via-git"></a>Opzione 2: Codice sorgente tramite Git
Per ottenere il codice sorgente dell'SDK tramite Git, digitare:

    git clone https://github.com/AzureAD/azure-activedirectory-library-for-android.git
    cd ./azure-activedirectory-library-for-android/src

### <a name="option-3-binaries-via-gradle"></a>Opzione 3: File binari tramite Gradle
È possibile ottenere i file binari dal repository centrale di Maven. Il pacchetto AAR può essere incluso come segue nel progetto in Android Studio:

```gradle
repositories {
    mavenCentral()
    flatDir {
        dirs 'libs'
    }
    maven {
        url "YourLocalMavenRepoPath\\.m2\\repository"
    }
}
dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    compile('com.microsoft.aad:adal:1.1.1') {
        exclude group: 'com.android.support'
    } // Recent version is 1.1.1
}
```

### <a name="option-4-aar-via-maven"></a>Opzione 4: AAR tramite Maven
Se si usa il plug-in M2Eclipse, è possibile specificare la dipendenza nel file pom.xml:

```xml
<dependency>
    <groupId>com.microsoft.aad</groupId>
    <artifactId>adal</artifactId>
    <version>1.1.1</version>
    <type>aar</type>
</dependency>
```


### <a name="option-5-jar-package-inside-the-libs-folder"></a>Opzione 5: Pacchetto JAR nella cartella libs
È possibile ottenere il file JAR dal repository Maven e inserirlo nella cartella **libs** del progetto. È necessario copiare anche le risorse necessarie per il progetto, perché non sono incluse nei pacchetti JAR.

## <a name="step-5-add-references-to-android-adal-to-your-project"></a>Passaggio 5: Aggiungere i riferimenti ad ADAL per Android nel progetto
1. Aggiungere un riferimento al progetto e specificarlo come una libreria Android. In caso di dubbi su questa operazione, è possibile ottenere altre informazioni nel [sito Android Studio](http://developer.android.com/tools/projects/projects-eclipse.html).
2. Aggiungere la dipendenza del progetto per il debug alle impostazioni del progetto.
3. Aggiornare il file AndroidManifest.xml del progetto includendo:

        <uses-permission android:name="android.permission.INTERNET" />
        <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
        <application
            android:allowBackup="true"
            android:debuggable="true"
            android:icon="@drawable/ic_launcher"
            android:label="@string/app_name"
            android:theme="@style/AppTheme" >

            <activity
                android:name="com.microsoft.aad.adal.AuthenticationActivity"
                android:label="@string/title_login_hello_app" >
            </activity>
            ....
        <application/>

4. Creare un'istanza di AuthenticationContext nell'attività principale. I dettagli di questa chiamata non rientrano dell'ambito di questo argomento, ma come punto di partenza è possibile vedere l'[esempio relativo al client nativo Android](https://github.com/AzureAD/azure-activedirectory-library-for-android). Nell'esempio seguente SharedPreferences è la cache predefinita e il formato di Authority è `https://login.microsoftonline.com/yourtenant.onmicrosoft.com`:

    `mContext = new AuthenticationContext(MainActivity.this, authority, true); // mContext is a field in your activity`

5. Copiare il blocco di codice per gestire la fine di AuthenticationActivity dopo che l'utente ha immesso le credenziali e ricevuto un codice di autorizzazione:

        @Override
         protected void onActivityResult(int requestCode, int resultCode, Intent data) {
             super.onActivityResult(requestCode, resultCode, data);
             if (mContext != null) {
                mContext.onActivityResult(requestCode, resultCode, data);
             }
         }

6. Per richiedere un token, definire un callback:

        private AuthenticationCallback<AuthenticationResult> callback = new AuthenticationCallback<AuthenticationResult>() {

            @Override
            public void onError(Exception exc) {
                if (exc instanceof AuthenticationException) {
                    textViewStatus.setText("Cancelled");
                    Log.d(TAG, "Cancelled");
                } else {
                    textViewStatus.setText("Authentication error:" + exc.getMessage());
                    Log.d(TAG, "Authentication error:" + exc.getMessage());
                }
            }

            @Override
            public void onSuccess(AuthenticationResult result) {
                mResult = result;

                if (result == null || result.getAccessToken() == null
                        || result.getAccessToken().isEmpty()) {
                    textViewStatus.setText("Token is empty");
                    Log.d(TAG, "Token is empty");
                } else {
                    // request is successful
                    Log.d(TAG, "Status:" + result.getStatus() + " Expired:"
                            + result.getExpiresOn().toString());
                    textViewStatus.setText(PASSED);
                }
            }
        };

7. Infine, richiedere il token usando il callback:

    `mContext.acquireToken(MainActivity.this, resource, clientId, redirect, user_loginhint, PromptBehavior.Auto, "",
                   callback);`

Ecco una descrizione dei parametri:

* *resource* è obbligatorio ed è la risorsa a cui si sta tentando di accedere.
* *clientid* è obbligatorio e viene fornito da Azure AD.
* *RedirectUri* non è obbligatorio specificarlo per la chiamata acquireToken. È possibile configurarlo come nome del pacchetto.
* *PromptBehavior* facilita la richiesta delle credenziali per ignorare cache e cookie.
* *callback* viene chiamato dopo lo scambio del codice di autorizzazione con un token. Ha un oggetto AuthenticationResult, che include un token di accesso, la data di scadenza e le informazioni sul token ID.
* *acquireTokenSilent* è facoltativo. È possibile chiamarlo per gestire la memorizzazione nella cache e l'aggiornamento del token. Fornisce anche la versione di sincronizzazione. Accetta *userId* come parametro.

        mContext.acquireTokenSilent(resource, clientid, userId, callback );

Tramite questa procedura dettagliata si dovrebbero avere tutte le informazioni necessarie per una corretta integrazione con Azure Active Directory. Per altre informazioni sull'esecuzione di queste operazioni, visitare il repository AzureADSamples/ su GitHub.

## <a name="important-information"></a>Informazioni importanti

### <a name="broker"></a>Gestore
Il componente broker è fornito dal Portale aziendale Intune o dall'app Microsoft Authenticator. L'account viene creato in AccountManager. Il tipo di account è "com.microsoft.workaccount". AccountManager consente solo un singolo account Single Sign-On (SSO). Dopo il completamento della richiesta di verifica del dispositivo per una delle app, viene creato un cookie SSO per questo utente.

Per altre informazioni sulla configurazione tramite broker, vedere l'[articolo del wiki sul broker](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki/Broker). 

### <a name="authority-url-and-ad-fs"></a>URL dell'autorità e AD FS
Active Directory Federation Services (AD FS) non è riconosciuto come servizio token di sicurezza di produzione, quindi è necessario disattivare l'individuazione dell'istanza e passare false al costruttore AuthenticationContext.

L'URL dell'autorità richiede un'istanza del servizio token di sicurezza e il nome del [tenant](https://login.microsoftonline.com/yourtenant.onmicrosoft.com).

### <a name="querying-cache-items"></a>Esecuzione di query sugli elementi della cache
ADAL fornisce una cache predefinita in SharedPrefrecens con alcune semplici funzioni di query nella cache. È possibile ottenere la cache corrente da AuthenticationContext con:

    ITokenCacheStore cache = mContext.getCache();

È anche possibile fornire l'implementazione della cache, se si desidera personalizzarla.

    mContext = new AuthenticationContext(MainActivity.this, authority, true, yourCache);

### <a name="prompt-behavior"></a>Comportamento della richiesta
ADAL include un'opzione per specificare il comportamento delle richieste. Se il token di aggiornamento non è valido e sono richieste le credenziali utente, PromptBehavior.Auto visualizzerà l'interfaccia utente. PromptBehavior.Always ignorerà l'utilizzo della cache e visualizzerà sempre l'interfaccia utente.

### <a name="silent-token-request-from-cache-and-refresh"></a>Richiesta invisibile all'utente dei token dalla cache e aggiornamento
Una richiesta di token invisibile all'utente non usa il popup dell'interfaccia utente e non richiede alcuna attività. Restituisce un token dalla cache, se disponibile. Se il token è scaduto, questo metodo prova ad aggiornarlo. Se il token di aggiornamento è scaduto o non è riuscito, restituisce AuthenticationException.

    Future<AuthenticationResult> result = mContext.acquireTokenSilent(resource, clientid, userId, callback );

Con questo metodo è anche possibile effettuare una chiamata di sincronizzazione. È possibile impostare Null per il callback o usare acquireTokenSilentSync.

### <a name="diagnostics"></a>Diagnostica
Di seguito sono riportate le fonti di informazioni principali per la diagnostica dei problemi:

* Eccezioni
* Log
* Tracce di rete

Si noti che gli ID di correlazione sono fondamentali per la diagnostica della libreria. È possibile impostare gli ID di correlazione per le singole richieste, se si vuole correlare una richiesta ADAL con altre operazioni presenti nel codice. Se non si imposta un ID di correlazione, ADAL genererà un valore casuale. Tutti i messaggi di log e le chiamate di rete verranno quindi contrassegnate con l'ID di correlazione. L'ID generato automaticamente cambia per ogni richiesta.

#### <a name="errors--exceptions"></a>Errori ed eccezioni
Le eccezioni costituiscono il primo elemento di diagnostica. Vengono forniti messaggi di errore che dovrebbero risultare utili, ma se qualcuno non dovesse esserlo, registrare il problema e segnalarlo. Includere le informazioni relative al dispositivo, ad esempio modello e numero di SDK.

Per altre informazioni sugli errori che l'app deve gestire, vedere [Procedure consigliate di gestione degli errori](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-devhowto-adal-error-handling). 

#### <a name="logs"></a>Log
È possibile configurare la libreria in modo che vengano generati messaggi di log che possono risultare utili per diagnosticare i problemi. Per configurare la registrazione, eseguire la chiamata seguente per configurare un callback che ADAL userà per presentare ogni messaggio di log generato.

Per attivare la registrazione, vedere l'[articolo del wiki sulla registrazione](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki/Logging).

### <a name="session-cookies-in-webview"></a>Cookie di sessione in WebView
Android WebView non cancella i cookie di sessione dopo la chiusura dell'app. È possibile gestire questa operazione usando questo codice di esempio:

    CookieSyncManager.createInstance(getApplicationContext());
    CookieManager cookieManager = CookieManager.getInstance();
    cookieManager.removeSessionCookie();
    CookieSyncManager.getInstance().sync();

Per informazioni dettagliate sui cookie, vedere le [informazioni su CookieSyncManager nel sito Android](http://developer.android.com/reference/android/webkit/CookieSyncManager.html).

### <a name="ntlm-dialog-box"></a>Finestra di dialogo NTLM
ADAL versione 1.1.0 supporta la finestra di dialogo NTLM che viene elaborata tramite l'evento onReceivedHttpAuthRequest da WebViewClient. È possibile personalizzare il layout e le stringhe per la finestra di dialogo.

### <a name="cross-app-sso"></a>Accesso Single Sign-On tra app
Informazioni su [Come abilitare l'accesso Single Sign-On tra app su Android usando ADAL](active-directory-sso-android.md).  

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
