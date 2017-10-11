---
title: App Android v2.0 di Azure Active Directory | Documentazione Microsoft
description: Come compilare un'app per Android che consente agli utenti di accedere con un account Microsoft personale, aziendale o dell'istituto di istruzione e chiama l'API Graph usando librerie di terze parti.
services: active-directory
documentationcenter: 
author: danieldobalian
manager: mbaldwin
editor: 
ms.assetid: 16294c07-f27d-45c9-833f-7dbb12083794
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2017
ms.author: dadobali
ms.custom: aaddev
ms.openlocfilehash: c0a5a818c61f7af7ff04bf890b54e8364f3b21b1
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="add-sign-in-to-an-android-app-using-a-third-party-library-with-graph-api-using-the-v20-endpoint"></a>Aggiungere informazioni di accesso a un'app Android usando una libreria di terze parti con l'API Graph mediante l'endpoint v2.0
La piattaforma delle identità Microsoft usa standard aperti, ad esempio OAuth2 e OpenID Connect. Gli sviluppatori possono usare qualsiasi libreria che desiderano integrare ai servizi. Per aiutare gli sviluppatori a usare la piattaforma con altre librerie, sono state scritte alcune procedure dettagliate come questa, che illustrano come configurare le librerie di terze parti per connettersi alla piattaforma delle identità Microsoft. La maggior parte delle librerie che implementano [la specifica OAuth2 RFC6749](https://tools.ietf.org/html/rfc6749) possono connettersi alla piattaforma delle identità Microsoft.

Con l'applicazione creata in questa procedura dettagliata, gli utenti possono accedere alla propria organizzazione e cercare in modo indipendente all'interno dell'organizzazione tramite l'API Graph.

Se non si ha familiarità con OAuth2 o OpenID Connect, gran parte di questo esempio risulterà poco chiara. Per approfondimenti, è consigliabile leggere [Protocolli della versione 2.0: flusso del codice di autorizzazione di OAuth 2.0](active-directory-v2-protocols-oauth-code.md).

> [!NOTE]
> Per alcune funzionalità della piattaforma che trovano espressione negli standard OAuth2 o OpenID Connect, ad esempio l'accesso condizionale e la gestione criteri di Intune, è necessario usare le librerie di identità open source di Microsoft Azure.
> 
> 

Non tutti gli scenari e le funzionalità di Azure Active Directory sono supportati dall'endpoint v2.0.

> [!NOTE]
> Per determinare se è necessario usare l'endpoint v2.0, leggere le informazioni sulle [limitazioni v2.0](active-directory-v2-limitations.md).
> 
> 

## <a name="download-the-code-from-github"></a>Scaricare il codice da GitHub
Il codice per questa esercitazione è salvato [su GitHub](https://github.com/Azure-Samples/active-directory-android-native-oidcandroidlib-v2).  Per seguire la procedura è possibile [scaricare la struttura dell'app come file con estensione zip](https://github.com/Azure-Samples/active-directory-android-native-oidcandroidlib-v2/archive/skeleton.zip) o clonare la struttura:

```
git clone --branch skeleton git@github.com:Azure-Samples/active-directory-android-native-oidcandroidlib-v2.git
```

È anche possibile scaricare l'esempio e iniziare subito:

```
git@github.com:Azure-Samples/active-directory-android-native-oidcandroidlib-v2.git
```

## <a name="register-an-app"></a>Registrare un'app
Creare una nuova app nel [portale di registrazione delle applicazioni](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) o seguire la procedura riportata in [Come registrare un'app con l'endpoint v2.0](active-directory-v2-app-registration.md).  Verificare di:

* Copiare l' **ID applicazione** assegnato all'app, perché verrà richiesto a breve.
* Aggiungere la piattaforma **Mobile** per l'app.

> Nota: il portale di registrazione delle applicazioni offre un valore per **URI di reindirizzamento** . In questo esempio, tuttavia, è necessario usare il valore predefinito `https://login.microsoftonline.com/common/oauth2/nativeclient`.
> 
> 

## <a name="download-the-nxoauth2-third-party-library-and-create-a-workspace"></a>Scaricare la libreria di terze parti NXOAuth2 e creare un'area di lavoro
Per questa procedura dettagliata si userà OIDCAndroidLib da GitHub, una libreria OAuth2 basata sul codice OpenID Connect di Google. Questa libreria implementa il profilo dell'applicazione nativa e supporta l'endpoint di autorizzazione dell'utente. Ecco tutto ciò che serve per l'integrazione con la piattaforma delle identità di Microsoft.

Clonare il repository OIDCAndroidLib sul computer.

```
git@github.com:kalemontes/OIDCAndroidLib.git
```

![Android Studio](../media/active-directory-android-native-oidcandroidlib-v2/emotes-url.png)

## <a name="set-up-your-android-studio-environment"></a>Configurare l'ambiente Android Studio
1. Creare un nuovo progetto Android Studio e accettare le impostazioni predefinite nella procedura guidata.
   
    ![Creare un nuovo progetto in Android Studio](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample1.PNG)
   
    ![Target Android devices (Dispositivi Android di destinazione)](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample2.PNG)
   
    ![Aggiungere un'attività a un dispositivo mobile](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample3.PNG)
2. Per impostare i moduli del progetto, spostare il repository clonato nel percorso del progetto. È anche possibile creare il progetto e clonarlo direttamente nel percorso del progetto.
   
    ![Moduli del progetto](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample4_1.PNG)
3. Aprire le impostazioni dei moduli del progetto tramite il menu di scelta rapida o tramite la combinazione di tasti Ctrl+Alt+Maj+S.
   
    ![Impostazioni dei moduli del progetto](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample4.PNG)
4. Dal momento che sono necessarie solo le impostazioni del contenitore del progetto, rimuovere il modulo dell'app predefinito.
   
    ![Modulo predefinito dell'app](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample5.PNG)
5. Importare i moduli dal repository clonato al progetto corrente.
   
    ![Progetto di importazione gradle](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample6.PNG) ![crea una nuova pagina modulo](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample7.PNG)
6. Ripetere questi passaggi per il modulo `oidlib-sample` .
7. Controllare le dipendenze di oidclib del modulo `oidlib-sample` .
   
    ![Dipendenze oidclib sul modulo oidlib di esempio](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample8.PNG)
8. Fare clic su **OK** e attendere la sincronizzazione di Gradle.
   
    Il file settings.gradle sarà simile al seguente:
   
    ![Schermata di settings.gradle](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample8_1.PNG)
9. Compilare l'app di esempio per verificare che l'esempio venga eseguito correttamente.
   
    Non sarà ancora possibile usarlo con Azure Active Directory. Prima è necessario configurare alcuni endpoint per assicurarsi di non avere problemi con Android Studio prima di iniziare a personalizzare l'app di esempio.
10. Compilare ed eseguire `oidlib-sample` come destinazione in Android Studio.
    
    ![Progresso della compilazione oidlib di esempio](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample9.png)
11. Eliminare la directory `app ` rimasta dopo la rimozione del modulo dal progetto perché Android Studio non la elimina per motivi di sicurezza.
    
    ![Struttura dei file che include la directory dell'applicazione](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample12.PNG)
12. Aprire il menu **Edit Configurations** (Modifica configurazioni) per rimuovere la configurazione di esecuzione rimasta dopo la rimozione del modulo dal progetto.
    
    ![Menu Edit Configurations (Modifica configurazioni)](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample10.PNG)
    ![Configurazione di esecuzione dell'app](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample11.PNG)

## <a name="configure-the-endpoints-of-the-sample"></a>Configurare gli endpoint dell'esempio
Ora che `oidlib-sample` è correttamente in esecuzione, verranno modificati alcuni endpoint per l'integrazione con Azure Active Directory.

### <a name="configure-your-client-by-editing-the-oidcclientconfxml-file"></a>Configurare il client modificando il file oidc_clientconf.xml
1. Poiché si stanno usando solo flussi OAuth2 per ottenere un token e chiamare l'API Graph, impostare il client solo per eseguire OAuth2. OIDC verrà illustrato in un esempio successivo.
   
    ```xml
        <bool name="oidc_oauth2only">true</bool>
    ```
2. Configurare l'ID client ricevuto dal portale di registrazione.
   
    ```xml
        <string name="oidc_clientId">86172f9d-a1ae-4348-aafa-7b3e5d1b36f5</string>
        <string name="oidc_clientSecret"></string>
    ```
3. Configurare l'URI di reindirizzamento con quello riportato di seguito.
   
    ```xml
        <string name="oidc_redirectUrl">https://login.microsoftonline.com/common/oauth2/nativeclient</string>
    ```
4. Configurare gli ambiti necessari per accedere all'API Graph.
   
    ```xml
        <string-array name="oidc_scopes">
            <item>openid</item>
            <item>https://graph.microsoft.com/User.Read</item>
            <item>offline_access</item>
        </string-array>
    ```

Il valore `User.Read` in `oidc_scopes` consente di leggere il profilo di base dell'utente connesso.
Per altre informazioni su tutti gli ambiti disponibili, vedere [Microsoft Graph permission scopes](https://graph.microsoft.io/docs/authorization/permission_scopes)(Ambiti di autorizzazione di Microsoft Graph).

Se si necessita di spiegazioni sugli ambiti `openid` o `offline_access` in OpenID Connect, vedere [Protocolli della versione 2.0: flusso del codice di autorizzazione di OAuth 2.0](active-directory-v2-protocols-oauth-code.md).

### <a name="configure-your-client-endpoints-by-editing-the-oidcendpointsxml-file"></a>Configurare gli endpoint client modificando il file oidc_endpoints.xml
* Aprire il file `oidc_endpoints.xml` e apportare le modifiche seguenti:
  
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

> [!NOTE]
> Gli endpoint per `userInfoEndpoint` e `revocationEndpoint` non sono attualmente supportati da Azure Active Directory. Se si lasciano questi valori con il valore example.com predefinito, verrà ricordato che non sono disponibili nell'esempio :-)
> 
> 

## <a name="configure-a-graph-api-call"></a>Configurare una chiamata API Graph
* Aprire il file `HomeActivity.java` e apportare le modifiche seguenti:
  
    ```Java
       //TODO: set your protected resource url
        private static final String protectedResUrl = "https://graph.microsoft.com/v1.0/me/";
    ```

In questo caso una semplice chiamata all'API Graph restituisce le informazioni.

Si tratta di tutte le modifiche da apportare. Eseguire l'applicazione `oidlib-sample` e fare clic su **Accedi**.

Una volta eseguita l'autenticazione, premere il pulsante **Request Protected Resource** (Richiedi risorsa protetta) per testare la chiamata all'API Graph.

## <a name="get-security-updates-for-our-product"></a>Ottenere aggiornamenti della sicurezza per il prodotto
È consigliabile ricevere notifiche sui problemi di sicurezza. A tale scopo, visitare [Security TechCenter](https://technet.microsoft.com/security/dd252948) e sottoscrivere gli avvisi di sicurezza.

