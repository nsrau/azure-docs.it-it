
# <a name="call-the-microsoft-graph-api-from-an-android-app"></a>Chiamare l'API Microsoft Graph da un'app Android

Questa guida illustra come un'applicazione Android nativa può ottenere un token di accesso e chiamare l'API Microsoft Graph o altre API che richiedono token di accesso dall'endpoint di Azure Active Directory v2.

Al termine della guida, l'applicazione potrà accettare accessi sia di account personali (ad esempio outlook.com, live.com e di altro tipo) sia di account aziendali e di istituti di istruzione di qualsiasi azienda od organizzazione che usa Azure Active Directory. L'applicazione chiamerà quindi un'API protetta dall'endpoint di Azure Active Directory v2.  

## <a name="how-this-sample-works"></a>Come interpretare questo esempio
![Come interpretare questo esempio](media/active-directory-develop-guidedsetup-android-intro/android-intro.png)

L'applicazione di esempio creata in questa guida è basata su uno scenario in cui un'applicazione Android viene usata per eseguire query su un'API Web che accetta token dall'endpoint di Azure Active Directory v2 (in questo caso l'API Microsoft Graph). Per questo scenario, l'applicazione aggiunge il token acquisito alle richieste HTTP tramite l'intestazione dell'autorizzazione. L'acquisizione e il rinnovo del token vengono gestiti automaticamente da Microsoft Authentication Library (MSAL).

## <a name="prerequisites"></a>prerequisiti
* Questa configurazione guidata è basata su Android Studio, ma è accettabile anche qualsiasi altro ambiente di sviluppo di applicazioni Android. 
* È necessario Android SDK 21 o versione successiva (è consigliato SDK 25).
* Per questa versione di MSAL per Android è necessario Google Chrome o un Web browser che usa schede personalizzate.

> [!NOTE]
> Google Chrome non è incluso con Visual Studio Emulator for Android. È consigliabile testare questo codice in un emulatore con API 25 o in un'immagine con API 21 o versione successiva in cui è installato Google Chrome.

## <a name="handling-token-acquisition-for-accessing-protected-web-apis"></a>Gestione dell'acquisizione di token per l'accesso ad API Web protette

Dopo l'autenticazione dell'utente, l'applicazione di esempio riceve un token di accesso che può essere usato per eseguire query sull'API Microsoft Graph o su un'API Web protetta da Microsoft Azure Active Directory v2.

Le API come Microsoft Graph richiedono un token di accesso per consentire l'accesso a specifiche risorse. È necessario un token di accesso, ad esempio, per leggere il profilo di un utente, accedere al calendario di un utente o inviare posta elettronica. L'applicazione può richiedere un token di accesso usando MSAL per accedere alle risorse tramite la specifica degli ambiti dell'API. Questo token di accesso viene quindi aggiunto all'intestazione di autorizzazione HTTP per ogni chiamata effettuata per la risorsa protetta. 

La memorizzazione nella cache e l'aggiornamento dei token di accesso vengono gestiti da MSAL e non devono quindi essere eseguiti dall'applicazione.

## <a name="libraries"></a>Librerie

Questa guida usa le librerie seguenti:

|Libreria|DESCRIZIONE|
|---|---|
|[com.microsoft.identity.client](http://javadoc.io/doc/com.microsoft.identity.client/msal)|Microsoft Authentication Library (MSAL)|
