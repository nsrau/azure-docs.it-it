<properties
	pageTitle="App Android v2.0 di Azure AD | Microsoft Azure"
	description="Come compilare un'app per Android che consente agli utenti di accedere con un account Microsoft personale, aziendale o dell'istituto di istruzione e chiama l'API Graph usando librerie di terze parti."
	services="active-directory"
	documentationCenter=""
	authors="brandwe"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/31/2016"
	ms.author="brandwe"/>

#  Aggiungere informazioni di accesso a un'app Android usando una libreria di terze parti con l'API Graph mediante l'endpoint v2.0

La piattaforma delle identità Microsoft usa standard aperti, ad esempio OAuth2 e OpenID Connect. Questo consente agli sviluppatori di sfruttare le librerie che vogliono integrare con i servizi. Per aiutare gli sviluppatori a usare la piattaforma con altre librerie, sono state scritte alcune procedure dettagliate come questa, che illustrano come configurare le librerie di terze parti per connettersi alla piattaforma delle identità Microsoft. La maggior parte delle librerie che implementano [la specifica OAuth2 RFC6749](https://tools.ietf.org/html/rfc6749) potranno connettersi alla piattaforma delle identità Microsoft.

Questa applicazione consentirà a un utente di accedere all'organizzazione e quindi di cercare qualcuno nell'organizzazione usando l'API Graph.

Se non si ha familiarità con OAuth2 o OpenID, gran parte di questo esempio risulterà poco chiara. È consigliabile vedere la breve [panoramica del protocollo documentata qui](active-directory-v2-protocols-oauth-code.md).


> [AZURE.NOTE]
	Per alcune funzionalità della piattaforma che trovano espressione in questi standard, ad esempio l'accesso condizionale e la gestione criteri di Intune, è necessario usare le librerie di identità di Microsoft Azure.


> [AZURE.NOTE]
	Non tutti gli scenari e le funzionalità di Azure Active Directory sono supportati dall'endpoint v2.0. Per determinare se è consigliabile usare l'endpoint 2.0, leggere l'articolo relativo alle [limitazioni della versione 2.0](active-directory-v2-limitations.md).


## Scaricare
Il codice per questa esercitazione è salvato [su GitHub](git@github.com:Azure-Samples/active-directory-android-native-oidcandroidlib-v2.git). Per seguire la procedura è possibile [scaricare la struttura dell'app come file con estensione zip](git@github.com:Azure-Samples/active-directory-android-native-oidcandroidlib-v2.git/archive/skeleton.zip) o clonare la struttura:

```
git clone --branch skeleton git@github.com:Azure-Samples/active-directory-android-native-oidcandroidlib-v2.git
```

Oppure eseguire il download e iniziare subito:

```
git@github.com:Azure-Samples/active-directory-android-native-oidcandroidlib-v2.git
```

## Registrare un'app
Creare una nuova app in [apps.dev.microsoft.com](https://apps.dev.microsoft.com) o seguire questa [procedura dettagliata](active-directory-v2-app-registration.md). Verificare di:

- Copiare l'**ID applicazione** assegnato all'app, perché verrà richiesto a breve.
- Aggiungere la piattaforma **Mobile** per l'app.
- Annotare il **Redirect URI** dal portale. È necessario usare il valore predefinito `https://login.microsoftonline.com/common/oauth2/nativeclient`.


## Scaricare la libreria di terze parti nxoauth2 e avviare un'area di lavoro

Per questa procedura dettagliata si userà OIDCAndroidLib di GitHub, una libreria OAuth2 basata sul codice OpenID Connect di Google. Implementa il profilo dell'applicazione nativa e supporta l'endpoint di autorizzazione dell'utente finale. Ecco tutto ciò che serve per l'integrazione con la piattaforma delle identità Microsoft.

*  Clone

Iniziare clonando il repository OIDCAndroidLib nel computer.

```
git@github.com:kalemontes/OIDCAndroidLib.git
```

![Android Studio](media/active-directory-android-native-oidcandroidlib-v2/emotes-url.png)

### Configurare l'ambiente Android Studio

*  Creare il progetto. Creare un nuovo progetto di Android Studio e seguire la procedura guidata predefinita.

![Android Studio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample1.PNG)

![Android Studio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample2.PNG)

![Android Studio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample3.PNG)

*  Configurare i moduli del progetto. Il modo più semplice per impostare i moduli è spostare il repository clonato nel percorso del progetto. È anche possibile iniziare creando il progetto, quindi clonarlo direttamente nel percorso del progetto.

![Android Studio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample4_1.PNG)

Aprire quindi le impostazioni dei moduli del progetto dal menu contestuale o usando il collegamento `Ctrl + Alt + Maj + S`.

![Android Studio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample4.PNG)

Dal momento che sono necessarie solo le impostazioni del contenitore del progetto, rimuovere il modulo dell'app predefinito.

![Android Studio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample5.PNG)

Ora è necessario importare i moduli dal repository clonato al progetto corrente.

![Android Studio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample6.PNG) ![Android Studio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample7.PNG)

> Ripetere questi passaggi per il modulo `oidlib-sample`.

Controllare le dipendenze di oidclib dal modulo `oidlib-sample`.

![Android Studio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample8.PNG)

Fare clic su "OK" e attendere la sincronizzazione di Gradle.

Il file settings.gradle sarà simile al seguente.

![Android Studio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample8_1.PNG)

*  Compilare l'app di esempio per verificare che l'esempio venga eseguito correttamente.

Non sarà ancora possibile usarlo con Azure Active Directory. Prima è necessario configurare alcuni endpoint per assicurarsi di non avere problemi con Android Studio prima di iniziare a personalizzare l'app di esempio.

Compilare ed eseguire `oidlib-sample` come destinazione in Android Studio.

![Android Studio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample9.png)

*  Eseguire la pulizia

È possibile eliminare tranquillamente la directory `app ` rimasta dopo la rimozione del modulo dal progetto perché Android Studio non la elimina per motivi di sicurezza.

![Android Studio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample12.PNG)

È anche possibile rimuovere la configurazione di esecuzione rimasta sempre dopo la rimozione del modulo dal progetto aprendo il menu "Edit Configurations" (Modifica configurazioni).

![Android Studio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample10.PNG) ![Android Studio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample11.PNG)

## Configurare gli endpoint dell'esempio

Ora che `oidlib-sample` è correttamente in esecuzione, verranno modificati alcuni endpoint per l'integrazione con Azure Active Directory.

* Configurare il client

Aprire il file `oidc_clientconf.xml` e apportare le modifiche seguenti:

1. Poiché si stanno usando solo flussi OAuth2 per ottenere un token e chiamare l'API Graph, il client verrà impostato solo per eseguire OAuth2. L'uso di OIDC verrà illustrato in un esempio successivo.

```xml
    <bool name="oidc_oauth2only">true</bool>
```

2. Configurare l'ID client ricevuto dal portale di registrazione.

```xml
    <string name="oidc_clientId">86172f9d-a1ae-4348-aafa-7b3e5d1b36f5</string>
    <string name="oidc_clientSecret"></string>
```

3. Configurare l'URI di reindirizzamento ricevuto dal portale di registrazione.

```xml
    <string name="oidc_redirectUrl">https://login.microsoftonline.com/common/oauth2/nativeclient</string>
```

4. Configurare gli ambiti necessari per accedere all'API Graph.

```xml
    <string-array name="oidc_scopes">
        <item>openid</item>
        <item>User.ReadBasic.All</item>
        <item>offline_access</item>
    </string-array>
```

Qui si imposta `oidc_scopes`. Gli ambiti che verranno richiesti per questa applicazione sono indicati da `User.ReadBasic.All` che consente di leggere il profilo di base di tutti gli utenti nella directory. Altre informazioni su tutti gli ambiti che è possibile [usare con Microsft Graph sono disponibili qui](https://graph.microsoft.io/docs/authorization/permission_scopes).

Per informazioni su `openid` o `offline_access` come ambiti in OpenID Connect, vedere una breve [panoramica del protocollo documentata qui](active-directory-v2-protocols-oauth-code.md).

* Configurare gli endpoint client

Aprire il file `oidc_endpoints.xml` e apportare le modifiche seguenti:

```xml
<!-- Stores OpenID Connect provider endpoints. -->
<resources>
    <string name="op_authorizationEnpoint">https://login.microsoftonline.com/common/oauth2/v2.0/authorize</string>
    <string name="op_tokenEndpoint">https://login.microsoftonline.com/common/oauth2/v2.0/token</string>
    <string name="op_userInfoEndpoint">https://www.example.com/oauth2/userinfo</string>
    <string name="op_revocationEndpoint">https://www.example.com/oauth2/revoketoken</string>
</resources>
```

Se si usa OAuth2 come protocollo, questi endpoint non devono mai essere modificati.

> [AZURE.NOTE] 
Gli endpoint per `userInfoEndpoint` e `revocationEndpoint` non sono attualmente supportati da Azure Active Directory, quindi verranno lasciati con i valori predefiniti di example.com per ricordare che non sono disponibili nell'esempio.


## Configurare una chiamata API Graph

Aprire il file `HomeActivity.java` e apportare le modifiche seguenti:

```Java
   //TODO: set your protected resource url
    private static final String protectedResUrl = "https://graph.microsoft.com/v1.0/me/";
```

Qui viene eseguita una semplice chiamata API Graph che restituirà le informazioni.

## Operazione completata

Queste sono tutte le modifiche necessarie. Eseguire l'applicazione `oidlib-sample` e fare clic sulle informazioni di accesso. O

Una volta eseguita l'autenticazione, premere il pulsante "Request Protected Resource" (Richiedi risorsa protetta) per testare la chiamata all'API Graph.

## Ottenere aggiornamenti della sicurezza per il prodotto

È consigliabile ricevere notifiche in caso di problemi di sicurezza. A tale scopo, visitare [questa pagina](https://technet.microsoft.com/security/dd252948) e sottoscrivere gli avvisi di sicurezza.

<!---HONumber=AcomDC_0629_2016-->