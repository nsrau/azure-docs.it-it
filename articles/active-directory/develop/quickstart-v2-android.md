---
title: Guida introduttiva per Android di Azure AD v2 | Microsoft Docs
description: Informazioni su come le applicazioni Android possono chiamare un'API che richiede token di accesso dall'endpoint di Azure Active Directory v2.0
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/01/2019
ms.author: dadobali
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: cd78e6acd801f3b973cc45609b72f86b257f4d43
ms.sourcegitcommit: d83fa82d6fec451c0cb957a76cfba8d072b72f4f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2019
ms.locfileid: "58862761"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-android-app"></a>Guida introduttiva: Accesso utenti e chiamata dell'API Microsoft Graph da un'app di Android

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

Questa guida introduttiva contiene un esempio di codice che illustra come un'applicazione Android può concedere l'accesso ad account personali, di lavoro o dell'istituto di istruzione, ottenere un token di accesso e chiamare l'API Graph di Microsoft.

![Mostra come funziona l'app di esempio generata da questo avvio rapido](media/quickstart-v2-android/android-intro-updated.png)

> [!NOTE]
> **Prerequisiti**
> * Android Studio 3+
> * Android 21+ (obbligatorio) 


> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Registrare e scaricare l'app della guida introduttiva
> Per avviare l'applicazione della guida introduttiva sono disponibili due opzioni:
> * [Rapida] [Opzione 1: Registrare e configurare automaticamente l'app e quindi scaricare l'esempio di codice](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Manuale] [Opzione 2: Registrare e configurare manualmente l'applicazione e il codice di esempio](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opzione 1: Registrare e configurare automaticamente l'app e quindi scaricare l'esempio di codice
> #### <a name="step-1-register-your-application"></a>Passaggio 1: Registrare l'applicazione
> Per registrare l'app
> 1. Passare alla [registrazione delle applicazioni (anteprima) nel portale di Azure](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AndroidQuickstartPage/sourceType/docs).
> 1. Immettere un nome per l'applicazione e fare clic su **Registra**.
> 1. Seguire le istruzioni per scaricare e configurare automaticamente la nuova applicazione con un clic.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Opzione 2: Registrare e configurare manualmente l'applicazione e il codice di esempio
>
> #### <a name="step-1-register-your-application"></a>Passaggio 1: Registrare l'applicazione
> Per registrare l'applicazione e aggiungere manualmente le informazioni di registrazione dell'app alla soluzione, seguire questa procedura:
>
> 1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
> 1. Se l'account consente di accedere a più tenant, selezionare l'account nell'angolo in alto a destra e impostare la sessione del portale sul tenant di Azure Active Directory desiderato.
> 1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory** e quindi **Registrazioni app (anteprima)** > **Nuova registrazione**.
> 1. Nella pagina **Registra un'applicazione** visualizzata immettere le informazioni di registrazione dell'applicazione:
>      - Nella sezione **Nome** immettere un nome di applicazione significativo che verrà visualizzato agli utenti dell'app, ad esempio `Android-Quickstart`.
>      - Premere il pulsante `Register`.
> 1. Passare a `Authentication` > `Redirect URIs` > `Suggested Redirect URIs for public clients` e selezionare l'URI di reindirizzamento nel formato **msal{AppId}://auth**. Salvare la modifica.


> [!div renderon="portal" class="sxs-lookup"]
> #### <a name="step-1-configure-your-application"></a>Passaggio 1: Configurare l'applicazione
> Affinché il codice di esempio di questa guida introduttiva funzioni, è necessario aggiungere un URL di risposta come **msal{AppId}://auth** (dove {AppId} è l'ID applicazione dell'app).
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Apporta questa modifica per me]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Già configurata](media/quickstart-v2-android/green-check.png) L'applicazione è configurata con questi attributi

#### <a name="step-2-download-the-project"></a>Passaggio 2: Scaricare il progetto

* [Scaricare il progetto di Android Studio](https://github.com/Azure-Samples/active-directory-android-native-v2/archive/master.zip)

#### <a name="step-3-configure-your-project"></a>Passaggio 3: Configurare il progetto

> [!div renderon="docs"]
> Se in precedenza è stata selezionata l'opzione 1, è possibile ignorare questi passaggi. Aprire il progetto in Android Studio ed eseguire l'app. 

> [!div renderon="portal" class="sxs-lookup"]
> 1. Estrarre e aprire il progetto in Android Studio.
> 1. In **app** > **res** > **raw** aprire **auth_config.json**.
> 1. Modificare **auth_config.json** e sostituire `client_id` e `tenant_id`:
>    ```javascript
>    "client_id" : "Enter_the_Application_Id_Here",
>    "type": "Enter_the_Audience_Info_Here",
>    "tenant_id" : "Enter_the_Tenant_Info_Here"
>    ```
> 1. In **app** > **manifests** aprire **AndroidManifest.xml**.
> 1. Aggiungere l'attività seguente al nodo **manifest\application**. Questo codice consente a Microsoft di eseguire il callback nell'app:   
>    ```xml
>    <!--Intent filter to catch Microsoft's callback after Sign In-->
>    <activity
>        android:name="com.microsoft.identity.client.BrowserTabActivity">
>        <intent-filter>
>            <action android:name="android.intent.action.VIEW" />
>            <category android:name="android.intent.category.DEFAULT" />
>            <category android:name="android.intent.category.BROWSABLE" />
> 
>            <!--Add in your scheme/host from registered redirect URI-->
>            <!--By default, the scheme should be similar to 'msal[appId]' -->
>            <data android:scheme="msalEnter_The_Application_Id_Here"
>                android:host="auth" />
>        </intent-filter>
>    </activity>
>    ```

> [!div renderon="docs"]
> 1. Estrarre e aprire il progetto in Android Studio.
> 1. In **app** > **res** > **raw** aprire **auth_config.json**.
> 1. Modificare **auth_config.json** e sostituire `client_id` e `redirect_uri`:
>    ```javascript
>    "client_id" : "ENTER_YOUR_APPLICATION_ID",
>    "redirect_uri": "ENTER_YOUR_REDIRECT_URI", 
>     ```
> 1. In **app** > **manifests** aprire **AndroidManifest.xml**.
> 1. Aggiungere l'attività seguente al nodo **manifest\application**. Questo frammento di codice registra un'attività **BrowserTabActivity** per consentire al sistema operativo di riavviare l'applicazione dopo il completamento dell'autenticazione:
>    ```xml
>    <!--Intent filter to catch Microsoft's callback after Sign In-->
>    <activity
>        android:name="com.microsoft.identity.client.BrowserTabActivity">
>        <intent-filter>
>            <action android:name="android.intent.action.VIEW" />
>            <category android:name="android.intent.category.DEFAULT" />
>            <category android:name="android.intent.category.BROWSABLE" />
> 
>            <!--Add in your scheme/host from registered redirect URI-->
>            <!--By default, the scheme should be similar to 'msal[appId]' -->
>            <data android:scheme="msal<ENTER_YOUR_APPLICATION_ID>"
>                android:host="auth" />
>        </intent-filter>
>    </activity>
>    ```
> 1. Sostituire `<ENTER_THE_APPLICATION_ID_HERE>` con l'*ID della propria applicazione*. Se è necessario trovare l'*ID applicazione*, andare alla pagina *Panoramica*.

## <a name="more-information"></a>Altre informazioni

Leggere le sezioni seguenti per altre informazioni su questa guida introduttiva.

### <a name="msal"></a>MSAL

MSAL ([com.microsoft.identity.client](https://javadoc.io/doc/com.microsoft.identity.client/msal)) è la libreria usata per concedere l'accesso agli utenti e richiedere i token usati per accedere a un'API protetta da Microsoft Azure Active Directory (Azure AD). È possibile usare Gradle per l'installazione aggiungendo quanto segue in **Gradle Scripts** > **build.gradle (Module: app)** in **Dependencies**:

```gradle  
implementation 'com.android.volley:volley:1.1.1'
implementation 'com.microsoft.identity.client:msal:0.2.+'
```

### <a name="msal-initialization"></a>Inizializzazione della libreria MSAL

È possibile aggiungere il riferimento per la libreria MSAL aggiungendo il codice seguente:

```java
import com.microsoft.identity.client.*;
```

Inizializzare quindi la libreria MSAL usando il codice seguente:

```java
    sampleApp = new PublicClientApplication(
        this.getApplicationContext(),
        R.raw.auth_config);
```

> |Dove: ||
> |---------|---------|
> |`R.raw.auth_config` | questo file contiene le configurazioni per l'applicazione, tra cui l'ID app/client, il gruppo di destinatari dell'accesso e diverse altre opzioni di personalizzazione. |

### <a name="requesting-tokens"></a>Richiesta di token

In MSAL sono disponibili due metodi per acquisire i token: `acquireToken` e `acquireTokenSilentAsync`

#### <a name="getting-a-user-token-interactively"></a>Acquisizione di un token utente in modo interattivo

Alcune situazioni richiedono di forzare gli utenti a interagire con l'endpoint v2.0 di Azure AD, determinando un cambio di contesto per il browser del sistema per convalidare le credenziali dell'utente o per il consenso. Di seguito sono riportati alcuni esempi:

* La prima volta che gli utenti accedono all'applicazione
* Quando gli utenti devono immettere nuovamente le credenziali perché la password è scaduta
* Quando l'applicazione richiede l'accesso a una risorsa per cui è necessario il consenso dell'utente
* Quando è necessaria l'autenticazione a due fattori

```java
sampleApp.acquireToken(this, SCOPES, getAuthInteractiveCallback());
```

> |Dove:||
> |---------|---------|
> | `SCOPES` | Contiene gli ambiti richiesti, vale a dire `{ "user.read" }` per Microsoft Graph o `{ "<Application ID URL>/scope" }` per le API Web personalizzate (ad esempio, `api://<Application ID>/access_as_user`) |
> | `getAuthInteractiveCallback` | Callback eseguito quando il controllo passa nuovamente all'applicazione dopo l'autenticazione |

#### <a name="getting-a-user-token-silently"></a>Acquisizione di un token utente in modo invisibile

Non si desidera richiedere all'utente di convalidare le proprie credenziali ogni volta che deve accedere a una risorsa. La maggior parte delle volte si desidera che le acquisizioni e i rinnovi dei token avvengano senza alcuna interazione da parte dell'utente. È possibile usare il metodo `AcquireTokenSilentAsync` per ottenere i token per accedere alle risorse protette dopo il metodo `acquireToken` iniziale:

```java
List<IAccount> accounts = sampleApp.getAccounts();
if (sample.size() == 1) {
    sampleApp.acquireTokenSilentAsync(SCOPES, accounts.get(0), getAuthSilentCallback());
} else {
    // No or multiple accounts
}
```

> |Dove:||
> |---------|---------|
> | `SCOPES` | Contiene gli ambiti richiesti, vale a dire `{ "user.read" }` per Microsoft Graph o `{ "<Application ID URL>/scope" }` per le API Web personalizzate (ad esempio, `api://<Application ID>/access_as_user`) |
> | `accounts.get(0)` | Contiene l'account per cui si sta provando a ottenere i token automaticamente |
> | `getAuthInteractiveCallback` | Callback eseguito quando il controllo passa nuovamente all'applicazione dopo l'autenticazione |

## <a name="next-steps"></a>Passaggi successivi

### <a name="learn-the-steps-to-create-the-application-used-in-this-quickstart"></a>Informazioni sulla procedura per creare l'applicazione usata in questa guida introduttiva

Provare l'esercitazione Android per una guida dettagliata completa sulla creazione di applicazioni e di nuove funzionalità, tra cui una spiegazione completa di questa guida introduttiva.

> [!div class="nextstepaction"]
> [Esercitazione su Android per le chiamate API Graph](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-android)

### <a name="msal-for-android-library-wiki"></a>MSAL per wiki della libreria Android

Leggere altre informazioni sulla libreria MSAL per Android:

> [!div class="nextstepaction"]
> [MSAL per wiki della libreria Android](https://github.com/AzureAD/microsoft-authentication-library-for-android/wiki)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
