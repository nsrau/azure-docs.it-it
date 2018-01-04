
# <a name="call-the-microsoft-graph-api-from-an-ios-application"></a>Chiamare l'API Graph Microsoft da un'applicazione iOS

Questa guida viene illustrato come un'applicazione iOS nativa (Swift) può chiamare le API che richiedono i token di accesso dall'endpoint v 2.0 di Microsoft Azure Active Directory (Azure AD). Nella Guida viene spiegato come ottenere i token di accesso e usarle nelle chiamate all'API Graph Microsoft e altre API.

Dopo aver completato gli esercizi in questa Guida, l'applicazione può chiamare un'API protetta da qualsiasi società o organizzazione con Azure AD. L'applicazione di effettuare chiamate di API protette utilizzando l'account personale come outlook.com, live.com e ad altri utenti, nonché gli account aziendali o dell'istituto di istruzione.

## <a name="prerequisites"></a>Prerequisiti
- XCode versione 8. x è obbligatorio per l'esempio che viene creato in questa Guida. È possibile scaricare XCode dal [sito Web iTunes](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12 "URL di Download di XCode").
- Il [Carthage](https://github.com/Carthage/Carthage) gestione dipendenza è richiesto per la gestione dei pacchetti.

## <a name="how-this-guide-works"></a>Come interpretare questa guida

![Come interpretare questa guida](media/active-directory-develop-guidedsetup-ios-introduction/iosintro.png)

In questa Guida, l'applicazione di esempio consente a un'applicazione iOS interrogare l'API di Microsoft Graph o un'API che accetta i token dall'endpoint v 2.0 AD Azure web. Per questo scenario, un token viene aggiunto alle richieste HTTP tramite il **autorizzazione** intestazione. L'acquisizione e il rinnovo del token vengono gestiti da Microsoft Authentication Library (MSAL).


### <a name="handle-token-acquisition-for-access-to-protected-web-apis"></a>Gestire l'acquisizione del token per l'accesso ad API web protetta

Se l'utente viene autenticato, l'applicazione di esempio riceve un token. Il token viene usato per eseguire query di una web API protetta da endpoint v 2.0 di Azure AD o l'API di Microsoft Graph.

API, ad esempio Microsoft Graph, richiedono un token di accesso per consentire l'accesso a risorse specifiche. I token sono necessari per leggere un profilo utente, accedere calendario dell'utente, inviare un messaggio di posta elettronica e così via. L'applicazione può richiedere un token di accesso tramite MSAL e specificando gli ambiti di API. Il token di accesso viene aggiunto a HTTP **autorizzazione** intestazione per ogni chiamata eseguita rispetto alla risorsa protetta.

La memorizzazione nella cache e l'aggiornamento dei token di accesso vengono gestiti dalla libreria MSAL e non devono quindi essere effettuati dall'applicazione.


## <a name="libraries"></a>Librerie

Questa guida usa la libreria seguente:

|Libreria|DESCRIZIONE|
|---|---|
|[MSAL.framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc)|Anteprima di Microsoft Authentication Library per iOS|

