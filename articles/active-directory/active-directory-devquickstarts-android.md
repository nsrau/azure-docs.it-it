<properties
	pageTitle="Introduzione ad Android per Azure AD | Microsoft Azure"
	description="Come compilare un'applicazione Android che si integra con Azure AD per l'accesso e chiama le API protette di Azure AD usando OAuth."
	services="active-directory"
	documentationCenter="android"
	authors="brandwe"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="09/16/2016"
	ms.author="brandwe"/>

# Integrare Azure AD in un'app per Android

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Se si sta sviluppando un'applicazione desktop, Azure AD semplifica e facilita l'autenticazione degli utenti tramite gli account Active Directory. Consente inoltre all'applicazione di usare in modo sicuro qualsiasi API Web protetta da Azure AD, ad esempio le API di Office 365 o l'API di Azure.

Per i client Android che devono accedere a risorse protette, Azure AD fornisce Active Directory Authentication Library (ADAL). La funzione di ADAL è di permettere all'app di ottenere facilmente i token di accesso. Per far capire quanto è semplice, verrà compilata un'applicazione Android To-Do List che:

-	Ottiene i token di accesso per la chiamata all'API To-Do List con il [protocollo di autenticazione OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx).
-	Ottiene l'elenco attività (To-Do List) dell'utente.
-	Disconnette gli utenti.

Per iniziare, sarà necessario un tenant di Azure AD in cui poter creare gli utenti e registrare un'applicazione. Se non si ha già un tenant, vedere le [informazioni su come ottenerne uno](active-directory-howto-tenant.md).

> [AZURE.TIP] Si consiglia di provare l'anteprima del nuovo [portale per sviluppatori](https://identity.microsoft.com/Docs/Android) che consentirà di imparare a usare Azure Active Directory in pochi minuti. Il portale per sviluppatori guida l'utente nel processo di registrazione di un'app e di integrazione di Azure AD nel codice. Al termine si otterrà una semplice applicazione in grado di autenticare gli utenti nel tenant e un back-end che può accettare i token ed eseguire la convalida.

## Passaggio 1: Scaricare ed eseguire il server di esempio dell'API REST TODO per Node.js

Questo esempio è scritto specificamente per funzionare con l'esempio esistente per la compilazione di una singola API REST To-Do del tenant per Microsoft Azure Active Directory. Questo è un prerequisito per l'Avvio rapido.

Per informazioni su come configurarlo, vedere gli esempi esistenti qui:

* [Servizio API REST di esempio di Microsoft Azure Active Directory per Node.js](active-directory-devquickstarts-webapi-nodejs.md)

## Passaggio 2: Registrare l'API Web con il tenant di Microsoft Azure AD

**Che operazioni si stanno eseguendo?**

*Microsoft Active Directory supporta l'aggiunta di due tipi di applicazioni. API Web che offrono servizi a utenti e applicazioni (sul Web o in un'applicazione in esecuzione su un dispositivo) che accedono a tali API Web. In questo passaggio si registrerà l'API Web in esecuzione in locale per il test di questo esempio. In genere questa API Web è un servizio REST che offre le funzionalità a cui un'app deve accedere. Microsoft Azure Active Directory è in grado di proteggere qualsiasi endpoint.*

*Qui si presume che verrà registrata l'API REST TODO citata sopra, ma questa procedura è valida per qualsiasi API Web che si vuole proteggere con Azure Active Directory.*

Passaggi per registrare un'API Web con Microsoft Azure AD

1. Accedere al [portale di gestione di Azure](https://manage.windowsazure.com).
2. Fare clic su Active Directory nel pannello di navigazione a sinistra.
3. Fare clic sul tenant della directory in cui si vuole registrare l'applicazione di esempio.
4. Fare clic sulla scheda Applicazioni.
5. Nel pannello fare clic su Aggiungi.
6. Fare clic su "Aggiungi un'applicazione che l'organizzazione sta sviluppando".
7. Immettere un nome descrittivo per l'applicazione, ad esempio "TodoListService", selezionare "Applicazione Web e/o API Web", quindi fare clic su Avanti.
8. Come URL di accesso immettere l'URL di base per l'esempio, che per impostazione predefinita è `https://localhost:8080`.
9. Come URI ID app immettere `https://<your_tenant_name>/TodoListService`, sostituendo `<your_tenant_name>` con il nome del tenant di Azure AD. Fare clic su OK per completare la registrazione.
10. Nel portale di Azure fare clic sulla scheda Configura dell'applicazione.
11. **Trovare il valore dell'ID client e prenderne nota**, perché sarà necessario in un secondo momento durante la configurazione dell'applicazione.

## Passaggio 3: Registrare l'applicazione client nativa Android di esempio

La registrazione dell'applicazione Web è il primo passaggio. Successivamente, sarà necessario anche informare Azure Active Directory dell'applicazione. In questo modo l'applicazione può comunicare con l'API Web appena registrata.

**Che operazioni si stanno eseguendo?**

*Come indicato in precedenza, Microsoft Azure Active Directory supporta l'aggiunta di due tipi di applicazioni. API Web che offrono servizi a utenti e applicazioni (sul Web o in un'applicazione in esecuzione su un dispositivo) che accedono a tali API Web. In questo passaggio si registrerà l'applicazione di questo esempio. La registrazione è necessaria per permettere all'applicazione di richiedere l'accesso all'API Web appena registrata. Se l'applicazione non è registrata, Azure Active Directory non le consentirà nemmeno di chiedere di effettuare l'accesso. Questo fa parte della sicurezza del modello.*

*Qui si presume che verrà registrata l'applicazione di esempio citata sopra, ma questa procedura è valida per qualsiasi app si voglia sviluppare.*

**Perché si inseriscono sia un'applicazione che un'API Web in un solo tenant?**

*Come si può immaginare, è possibile compilare un'app che accede a un'API esterna registrata in Azure Active Directory da un altro tenant. In tal caso, ai clienti verrà richiesto di acconsentire all'uso dell'API nell'applicazione. L'aspetto interessante è che Active Directory Authentication Library per iOS gestisce automaticamente la richiesta di consenso. Iniziando a usare funzionalità più avanzate, si può immaginare come questa sia una parte importante del lavoro necessario per accedere alla gamma di API Microsoft da Azure e da Office, oltre che da altri provider di servizi. Per ora, poiché sia l'API Web che l'applicazione sono state registrate nello stesso tenant, non verrà visualizzata alcuna richiesta di consenso. Di solito ciò avviene se si sta sviluppando un'applicazione che verrà usata solo in azienda.*

1. Accedere al [portale di gestione di Azure](https://manage.windowsazure.com).
2. Fare clic su Active Directory nel pannello di navigazione a sinistra.
3. Fare clic sul tenant della directory in cui si vuole registrare l'applicazione di esempio.
4. Fare clic sulla scheda Applicazioni.
5. Nel pannello fare clic su Aggiungi.
6. Fare clic su "Aggiungi un'applicazione che l'organizzazione sta sviluppando".
7. Immettere un nome descrittivo per l'applicazione, ad esempio "TodoListClient-Android", selezionare "Applicazione client nativa", quindi fare clic su Avanti.
8. Per l'URI di reindirizzamento, immettere `http://TodoListClient`. Fare clic su Fine.
9. Fare clic sulla scheda Configura dell'applicazione.
10. Trovare il valore dell'ID client e prenderne nota, perché sarà necessario in un secondo momento durante la configurazione dell'applicazione.
11. In "Autorizzazioni per altre applicazioni" fare clic su "Aggiungi applicazione". Selezionare "Altro" nell'elenco a discesa "Mostra", quindi fare clic sul segno di spunta superiore. Individuare e fare clic su TodoListService, quindi fare clic sul segno di spunta inferiore per aggiungere l'applicazione. Selezionare "Accesso a TodoListService" dall'elenco a discesa "Autorizzazioni delegate", quindi salvare la configurazione.



Per compilare con Maven, è possibile usare pom.xml al livello principale.

  * Clonare il repository in una directory di propria scelta:

  `$ git clone git@github.com:AzureADSamples/NativeClient-Android.git`

  * Seguire i passaggi nella [sezione dei prerequisiti per la configurazione di Maven per Android](https://github.com/MSOpenTech/azure-activedirectory-library-for-android/wiki/Setting-up-maven-environment-for-Android).
  * Configurare l'emulatore con SDK 19.
  * Passare alla cartella radice in cui è stato clonato il repository.
  * Eseguire il comando: mvn clean install
  * Passare alla directory dell'esempio di Avvio rapido: cd samples\hello
  * Eseguire il comando: mvn android:deploy android:run
  * Dovrebbe essere visualizzato l'avvio dell'app.
  * Immettere le credenziali dell'utente test per provare.

Oltre al pacchetto AAR, verranno inviati anche i pacchetti JAR.

### Passaggio 4: Scaricare ADAL per Android e aggiungerlo all'area di lavoro di Eclipse

Per semplificare l'operazione, sono disponibili più opzioni per usare questa libreria nel progetto Android:

* È possibile usare il codice sorgente per importare la libreria in Eclipse e collegarla all'applicazione.
* Se si usa Android Studio, è possibile usare il formato di pacchetto *AAR* e fare riferimento ai file binari.

####Opzione 1: Zip del codice sorgente

Per scaricare una copia del codice sorgente, fare clic su "Download del file ZIP" sul lato destro della pagina o fare clic [qui](https://github.com/AzureAD/azure-activedirectory-library-for-android/archive/v1.0.9.tar.gz).

####Opzione 2: Codice sorgente tramite Git

Per ottenere il codice sorgente dell'SDK tramite Git, digitare semplicemente:

    git clone git@github.com:AzureAD/azure-activedirectory-library-for-android.git
    cd ./azure-activedirectory-library-for-android/src

####Opzione 3: File binari tramite Gradle

È possibile ottenere i file binari dal repository centrale di Maven. Il pacchetto AAR può essere incluso come segue nel progetto in Android Studio:

```gradle
repositories {
    mavenCentral()
    flatDir {
        dirs 'libs'
    }
    maven {
        url "YourLocalMavenRepoPath\.m2\repository"
    }
}
dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    compile('com.microsoft.aad:adal:1.1.1') {
        exclude group: 'com.android.support'
    } // Recent version is 1.1.1
}
```

####Opzione 4: AAR tramite Maven

Se si usa il plug-in m2e in Eclipse, è possibile specificare la dipendenza nel file pom.xml:

```xml
<dependency>
    <groupId>com.microsoft.aad</groupId>
    <artifactId>adal</artifactId>
    <version>1.1.1</version>
    <type>aar</type>
</dependency>
```


####Opzione 5: Pacchetto JAR nella cartella libs
È possibile ottenere il file JAR dal repository Maven e inserirlo nella cartella *libs* del progetto. È necessario copiare anche le risorse necessarie per il progetto, perché non sono incluse nei pacchetti JAR.


### Passaggio 5: Aggiungere i riferimenti ad ADAL per Android nel progetto


2. Aggiungere un riferimento al progetto e specificarlo come una libreria Android. In caso di dubbi su come eseguire questa operazione, [fare clic qui per altre informazioni](http://developer.android.com/tools/projects/projects-eclipse.html).

3. Aggiungere la dipendenza del progetto per il debug alle impostazioni del progetto.

4. Aggiornare il file AndroidManifest.xml del progetto includendo:

    ```Java
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
    ```

7. Creare un'istanza di AuthenticationContext nell'attività principale. I dettagli di questa chiamata non rientrano dell'ambito di questo LEGGIMI, ma come punto di partenza è possibile vedere l'[l'esempio di client nativo Android](https://github.com/AzureADSamples/NativeClient-Android). Di seguito è riportato un esempio:

    ```Java
    // Authority is in the form of https://login.windows.net/yourtenant.onmicrosoft.com
    mContext = new AuthenticationContext(MainActivity.this, authority, true); // This will use SharedPreferences as            default cache
    ```
  * NOTA: mContext è un campo dell'attività.

8. Copiare il blocco di codice per gestire la fine di AuthenticationActivity dopo che l'utente ha immesso le credenziali e ricevuto il codice di autorizzazione:

    ```Java
     @Override
     protected void onActivityResult(int requestCode, int resultCode, Intent data) {
         super.onActivityResult(requestCode, resultCode, data);
         if (mContext != null) {
             mContext.onActivityResult(requestCode, resultCode, data);
         }
     }
    ```

9. Per richiedere un token, definire un callback.

    ```Java
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
    ```
10. Infine, richiedere il token usando il callback:

    ```Java
     mContext.acquireToken(MainActivity.this, resource, clientId, redirect, user_loginhint, PromptBehavior.Auto, "",
                    callback);
    ```

Descrizione dei parametri:

  * Resource è obbligatorio ed è la risorsa a cui si sta tentando di accedere.
  * Clientid è obbligatorio e viene fornito dal portale di Azure AD.
  * È possibile configurare redirectUri come nome del pacchetto. Non è obbligatorio specificarlo per la chiamata acquireToken.
  * PromptBehavior facilita la richiesta delle credenziali per ignorare cache e cookie.
  * Callback verrà chiamato dopo lo scambio del codice di autorizzazione con un token.

  Callback avrà un oggetto AuthenticationResult che include informazioni su accesstoken, data di scadenza e idtoken.

Facoltativo: **acquireTokenSilent**

È possibile chiamare **acquireTokenSilent** per gestire la memorizzazione nella cache e l'aggiornamento dei token. Fornisce anche la versione di sincronizzazione. Accetta userid come parametro.

    ```java
     mContext.acquireTokenSilent(resource, clientid, userId, callback );
    ```

11. **Gestore**:
  l'app del portale aziendale di Microsoft Intune fornirà il componente gestore. ADAL userà l'account del gestore, se è stato creato un account utente per questo autenticatore e lo sviluppatore ha scelto di non ignorarlo. Lo sviluppatore può ignorare l'utente gestore con:

    ```java
     AuthenticationSettings.Instance.setSkipBroker(true);
    ```

 Lo sviluppatore dovrà registrare un URI di reindirizzamento speciale per l'utilizzo da parte del gestore. L'URI di reindirizzamento è nel formato msauth://packagename/Base64UrlencodedSignature. È possibile ottenere l'URI di reindirizzamento per l'app usando lo script "brokerRedirectPrint.ps1" o la chiamata API mContext.getBrokerRedirectUri. La firma è correlata ai certificati di firma.

 Il modello di gestore attuale è per un solo utente. AuthenticationContext fornisce il metodo API per ottenere l'utente gestore.

 ```java
 String brokerAccount =  mContext.getBrokerUser();
 ```
 Se l'account è valido, verrà restituito l'utente gestore.

 Il manifesto dell'app dovrà avere le autorizzazioni per usare gli account di AccountManager: http://developer.android.com/reference/android/accounts/AccountManager.html

 * GET\_ACCOUNTS
 * USE\_CREDENTIALS
 * MANAGE\_ACCOUNTS


Tramite questa procedura dettagliata si dovrebbero avere tutte le informazioni necessarie per una corretta integrazione con Azure Active Directory. Per altre informazioni sull'esecuzione di queste operazioni, visitare il repository AzureADSamples/ su GitHub.

## Informazioni importanti

### Personalizzazione

Le risorse del progetto della libreria possono essere sovrascritte dalle risorse dell'applicazione. Ciò si verifica durante la compilazione dell'app. Per questo motivo, è possibile personalizzare il layout di Authentication Activity come si preferisce. È necessario accertarsi che venga mantenuto l'ID dei controlli usati da ADAL (Webview).

### Gestore

Il componente gestore verrà fornito con l'app del portale aziendale di Microsoft Intune. L'account verrà creato in Account Manager. Il tipo di account è "com.microsoft.workaccount" e consente l'uso solo di un account SSO (Single Sign-On). Dopo il completamento della richiesta di verifica del dispositivo per una delle app, verrà creato un cookie SSO per questo utente.

### URL dell'autorità e ADFS

ADFS non è riconosciuto come servizio token di sicurezza di produzione, quindi è necessario disattivare l'individuazione dell'istanza e passare false al costruttore AuthenticationContext.

L'URL dell'autorità richiede un'istanza del servizio token di sicurezza e il nome del tenant: https://login.windows.net/yourtenant.onmicrosoft.com

### Esecuzione di query sugli elementi della cache

ADAL fornisce una cache predefinita in SharedPrefrecens con alcune semplici funzioni di query nella cache. È possibile ottenere la cache corrente da AuthenticationContext con:
```Java
 ITokenCacheStore cache = mContext.getCache();
```
È anche possibile fornire l'implementazione della cache, se si desidera personalizzarla.
```Java
mContext = new AuthenticationContext(MainActivity.this, authority, true, yourCache);
```

### PromptBehavior

ADAL include un'opzione per specificare il comportamento delle richieste. Se il token di aggiornamento non è valido e sono richieste le credenziali utente, PromptBehavior.Auto visualizzerà l'interfaccia utente. PromptBehavior.Always ignorerà l'utilizzo della cache e visualizzerà sempre l'interfaccia utente.

### Richiesta invisibile all'utente dei token dalla cache e aggiornamento

Questo metodo non visualizza l'interfaccia utente e non richiede alcuna attività. Restituisce un token dalla cache, se disponibile. Se il token è scaduto, tenterà di aggiornarlo. Se il token di aggiornamento è scaduto o non è riuscito, restituirà AuthenticationException.

    ```Java
    Future<AuthenticationResult> result = mContext.acquireTokenSilent(resource, clientid, userId, callback );
    ```

Con questo metodo è anche possibile effettuare una chiamata di sincronizzazione. È possibile impostare Null per il callback o usare acquireTokenSilentSync.

### Diagnostica

Di seguito sono riportate le fonti di informazioni principali per la diagnostica dei problemi:

+ Eccezioni
+ Log
+ Tracce di rete

Si noti anche che gli ID di correlazione sono fondamentali per la diagnostica della libreria. È possibile impostare gli ID di correlazione per le singole richieste, se si vuole correlare una richiesta ADAL con altre operazioni presenti nel codice. Se non si imposta un ID di correlazione, ADAL ne genera uno casuale e tutti i messaggi di log e le chiamate di rete saranno contrassegnate con l'ID di correlazione. L'ID generato automaticamente cambia per ogni richiesta.

#### Eccezioni

Questo è ovviamente il primo evento di diagnostica. Vengono forniti messaggi di errore che dovrebbero risultare utili, ma se qualcuno non dovesse esserlo, registrare il problema e segnalarlo. Specificare anche le informazioni relative al dispositivo, ad esempio modello e numero di SDK.

#### Log

È possibile configurare la libreria in modo che vengano generati messaggi di log che possono risultare utili per diagnosticare i problemi. Per configurare la registrazione, eseguire la chiamata seguente per configurare un callback che ADAL userà per presentare ogni messaggio di log generato.


 ```Java
 Logger.getInstance().setExternalLogger(new ILogger() {
     @Override
     public void Log(String tag, String message, String additionalMessage, LogLevel level, ADALError errorCode) {
      ...
      // You can write this to logfile depending on level or errorcode.
      writeToLogFile(getApplicationContext(), tag +":" + message + "-" + additionalMessage);
     }
 }
 ```
I messaggi possono essere scritti in un file di log personalizzato, come illustrato di seguito. Non esiste purtroppo un metodo standard per ottenere i log da un dispositivo. A questo scopo, sono disponibili alcuni servizi che possono risultare utili. È anche possibile crearne di personalizzati, ad esempio per l'invio del file a un server.

```Java
private syncronized void writeToLogFile(Context ctx, String msg) {
       File directory = ctx.getDir(ctx.getPackageName(), Context.MODE_PRIVATE);
       File logFile = new File(directory, "logfile");
       FileOutputStream outputStream = new FileOutputStream(logFile, true);
       OutputStreamWriter osw = new OutputStreamWriter(outputStream);
       osw.write(msg);
       osw.flush();
       osw.close();
}
```

##### Livello di registrazione

+ Error (eccezioni)
+ Warn (avviso)
+ Info (a scopo informativo)
+ Verbose (più dettagli)

Il livello di registrazione viene impostato nel modo seguente:
```Java
Logger.getInstance().setLogLevel(Logger.LogLevel.Verbose);
 ```

 Tutti i messaggi di log vengono inviati a logcat, oltre agli eventuali callback di log personalizzati.
  È possibile ottenere un log nel formato di file logcat come illustrato di seguito:

 ```
  adb logcat > "C:\logmsg\logfile.txt"
 ```
 Altri esempi sui comandi adb sono disponibili qui: https://developer.android.com/tools/debugging/debugging-log.html#startingLogcat

#### Tracce di rete

Per acquisire il traffico HTTP generato da ADAL sono disponibili diversi strumenti. Ciò è particolarmente utile se si ha familiarità con il protocollo OAuth o se è necessario fornire informazioni di diagnostica a Microsoft o altri canali di supporto.

Fiddler è lo strumento di traccia HTTP più semplice. Usare i collegamenti seguenti per configurarlo in modo che registri correttamente il traffico di rete ADAL. Perché sia utile, è necessario configurare fiddler o qualsiasi altro strumento, come Charles, per la registrazione del traffico SSL non crittografato. NOTA: le tracce generate in questo modo possono contenere informazioni con privilegi elevati, ad esempio token di accesso, nomi utente e password. Se si usano account di produzione, non condividere queste tracce con terze parti. Se è necessario fornire una traccia a terzi per ottenere il supporto, riprodurre il problema con un account temporaneo usando nomi utente e password che è possibile condividere senza problemi.

+ [Configurazione di Fiddler per Android](http://docs.telerik.com/fiddler/configure-fiddler/tasks/ConfigureForAndroid)
+ [Configurare regole di Fiddler per ADAL](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki/How-to-listen-to-httpUrlConnection-in-Android-app-from-Fiddler)


### Modalità della finestra di dialogo
Il metodo acquireToken senza attività supporta prompt della finestra di dialogo.

### Crittografia

ADAL crittografa i token e li archivia in SharedPreferences per impostazione predefinita. È possibile esaminare la classe StorageHelper per visualizzare i dettagli. Android ha introdotto AndroidKeyStore 4.3 (API18) per l'archiviazione protetta delle chiavi private. ADAL lo usa per API18 e versioni successive. Se si vuole usare ADAL per versioni precedenti dell'SDK, è necessario fornire la chiave privata in AuthenticationSettings.INSTANCE.setSecretKey.

### Richiesta di connessione di Oauth2

La classe AuthenticationParameters fornisce la funzionalità per ottenere authorization\_uri dalla richiesta di connessione di Oauth2.

### Cookie di sessione in Webview

Webview per Android non cancella i cookie di sessione dopo la chiusura dell'app. È possibile gestire questo aspetto con il codice di esempio seguente:
```java
CookieSyncManager.createInstance(getApplicationContext());
CookieManager cookieManager = CookieManager.getInstance();
cookieManager.removeSessionCookie();
CookieSyncManager.getInstance().sync();
```
Altre informazioni sui cookie: http://developer.android.com/reference/android/webkit/CookieSyncManager.html

### Override delle risorse

La libreria ADAL include stringhe in inglese per i due messaggi ProgressDialog seguenti,

che dovranno essere sovrascritti dall'applicazione se si vogliono usare stringhe localizzate.

```Java
<string name="app_loading">Loading...</string>
<string name="broker_processing">Broker is processing</string>
<string name="http_auth_dialog_username">Username</string>
<string name="http_auth_dialog_password">Password</string>
<string name="http_auth_dialog_title">Sign In</string>
<string name="http_auth_dialog_login">Login</string>
<string name="http_auth_dialog_cancel">Cancel</string>
```

=======

### Finestra di dialogo NTLM
ADAL versione 1.1.0 supporta la finestra di dialogo NTLM che viene elaborata tramite l'evento onReceivedHttpAuthRequest da WebViewClient. È possibile personalizzare il layout della finestra di dialogo e le stringhe.

### Accesso Single Sign-On tra app
Informazioni su [Come abilitare l'accesso Single Sign-On tra app su Android usando ADAL](active-directory-sso-android.md)


[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]

<!---HONumber=AcomDC_0928_2016-->