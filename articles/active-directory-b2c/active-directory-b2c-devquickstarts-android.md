---
title: 'Azure Active Directory B2C: acquisire un token mediante un&quot;applicazione Android | Documentazione Microsoft'
description: "Questo articolo spiega come creare un&quot;app per Android che usa AppAuth con Azure Active Directory B2C per gestire le identità utente e l&quot;autenticazione degli utenti."
services: active-directory-b2c
documentationcenter: android
author: parakhj
manager: krassk
editor: 
ms.assetid: d00947c3-dcaa-4cb3-8c2e-d94e0746d8b2
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 03/06/2017
ms.author: parakhj
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: fdf9f494f30f11a00831b6c56a3d6ac40582c0ad
ms.lasthandoff: 03/14/2017


---
# <a name="azure-ad-b2c-sign-in-using-an-android-application"></a>Azure AD B2C: accedere mediante un'applicazione Android

La piattaforma delle identità Microsoft usa standard aperti, ad esempio OAuth2 e OpenID Connect. Questo consente agli sviluppatori di sfruttare le librerie che vogliono integrare con i servizi. Per aiutare gli sviluppatori a usare la piattaforma con altre librerie, sono state scritte alcune procedure dettagliate come questa, che illustrano come configurare le librerie di terze parti per connettersi alla piattaforma delle identità Microsoft. La maggior parte delle librerie che implementano la [specifica OAuth2 RFC6749](https://tools.ietf.org/html/rfc6749) potrà connettersi alla piattaforma delle identità Microsoft.

> [!WARNING]
> Microsoft non fornisce correzioni per queste librerie di terze parti e non ha eseguito una verifica su esse. In questo esempio si usa una libreria di terze parti chiamata AppAuth che è stata testata per la compatibilità in scenari di base con Azure AD B2C. Le richieste relative a problemi e funzionalità devono essere indirizzate al progetto open source della libreria. Per altre informazioni, vedere [questo articolo](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries).  
>
>

Se non si ha familiarità con OAuth2 o OpenID, gran parte di questo esempio risulterà poco chiara. È consigliabile vedere la breve [panoramica del protocollo documentata qui](active-directory-b2c-reference-protocols.md).

Non tutti gli scenari e le funzionalità di Azure Active Directory sono supportati dalla piattaforma B2C.  Per determinare se è consigliabile usare la piattaforma B2C, vedere l'articolo relativo alle [limitazioni di B2C](active-directory-b2c-limitations.md).

## <a name="get-an-azure-ad-b2c-directory"></a>Ottenere una directory di Azure AD B2C

Prima di poter usare Azure AD B2C, è necessario creare una directory, o tenant. Una directory è un contenitore per utenti, app, gruppi e così via. Se non ne è già disponibile una, prima di continuare [creare una directory B2C](active-directory-b2c-get-started.md) .

## <a name="create-an-application"></a>Creare un'applicazione

Successivamente, è necessario creare un'app nella directory B2C. In questo modo Azure AD acquisisce le informazioni necessarie per comunicare in modo sicuro con l'app. Per creare un'app per dispositivi mobili, [seguire questa procedura](active-directory-b2c-app-registration.md). Assicurarsi di:

* Includere un **client nativo** nell'applicazione.
* Copiare l' **ID applicazione** assegnato all'app. che sarà necessario più avanti.
* Configurare un **URI di reindirizzamento** client nativo (ad esempio com.onmicrosoft.fabrikamb2c.exampleapp://oauth/redirect). Sarà necessario più avanti.

[!INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>Creare i criteri

In Azure AD B2C ogni esperienza utente è definita da [criteri](active-directory-b2c-reference-policies.md)specifici. Questa app contiene un'esperienza di identità che combina accesso e iscrizione. È necessario creare i criteri, come descritto nell'[articolo di riferimento per i criteri](active-directory-b2c-reference-policies.md#create-a-sign-up-policy). Durante la creazione dei criteri, assicurarsi di:

* Scegliere **Nome visualizzato** come attributo di iscrizione nei criteri.
* Scegliere le attestazioni dell'applicazione **Nome visualizzato** e **ID oggetto** in tutti i criteri. È consentito scegliere anche altre attestazioni.
* Copiare il **Nome** di ogni criterio dopo averlo creato. Dovrebbero mostrare il prefisso `b2c_1_`.  Il nome dei criteri sarà necessario più avanti.

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Dopo aver creato i criteri, è possibile passare alla creazione dell'app.

## <a name="download-the-sample-code"></a>Scaricare il codice di esempio

È fornito un esempio funzionante che usa AppAuth con Azure Active Directory B2C [in GitHub](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c). È possibile scaricare ed eseguire il codice. Si può iniziare rapidamente con la propria app usando la configurazione di Azure Active Directory B2C con le istruzioni riportare in [README.md](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c/blob/master/README.md).

L'esempio è una versione modificata di quello fornito da [AppAuth](https://openid.github.io/AppAuth-Android/). Visitare la pagina per altre informazioni su AppAuth e le relative funzionalità.

## <a name="modifying-your-app-to-use-azure-ad-b2c-with-appauth"></a>Modifica dell'app per usare Azure Active Directory B2C con AppAuth

> [!NOTE]
> AppAuth supporta l'API 16 (Jellybean) di Android e versioni successive. Si consiglia di usare l'API 23 e versioni successive.
>

### <a name="configuration"></a>Configurazione

È possibile configurare la comunicazione con Azure AD B2C specificando l'URI di individuazione o specificando sia l'URI dell'endpoint di autorizzazione che quello dell'endpoint di token. In ogni caso sarà necessario specificare le informazioni seguenti:

* ID tenant (ad esempio contoso.onmicrosoft.com)
* Nome del criterio (ad esempio B2C\_1\_SignUpIn)

Se si sceglie di individuare automaticamente gli URI degli endpoint di autorizzazione e di token, è necessario recuperare le informazioni dall'URI di individuazione. L'URI di individuazione può essere generato mediante la sostituzione dell'ID\_tenant e del nome\_criterio nell'URL seguente:

```java
String mDiscoveryURI = "https://login.microsoftonline.com/<Tenant_ID>/v2.0/.well-known/openid-configuration?p=<Policy_Name>";
```

È quindi possibile acquisire gli URI degli endpoint di autorizzazione e di token e creare un oggetto AuthorizationServiceConfiguration eseguendo le operazioni seguenti:

```java
final Uri issuerUri = Uri.parse(mDiscoveryURI);
AuthorizationServiceConfiguration config;

AuthorizationServiceConfiguration.fetchFromIssuer(
    issuerUri,
    new RetrieveConfigurationCallback() {
      @Override public void onFetchConfigurationCompleted(
          @Nullable AuthorizationServiceConfiguration serviceConfiguration,
          @Nullable AuthorizationException ex) {
        if (ex != null) {
            Log.w(TAG, "Failed to retrieve configuration for " + issuerUri, ex);
        } else {
            // service configuration retrieved, proceed to authorization...
        }
      }
  });
```

Anziché usare l'individuazione per ottenere gli URI degli endpoint di autorizzazione e di token, è possibile anche specificarli in modo esplicito mediante la sostituzione dell'ID\_tenant e del nome\_criterio negli URL seguenti:

```java
String mAuthEndpoint = "https://login.microsoftonline.com/<Tenant_ID>/oauth2/v2.0/authorize?p=<Policy_Name>";

String mTokenEndpoint = "https://login.microsoftonline.com/<Tenant_ID>/oauth2/v2.0/token?p=<Policy_Name>";
```

Eseguire il codice seguente per creare l'oggetto AuthorizationServiceConfiguration:

```java
AuthorizationServiceConfiguration config =
        new AuthorizationServiceConfiguration(name, mAuthEndpoint, mTokenEndpoint);

// perform the auth request...
```

### <a name="authorizing"></a>Autorizzazione

Dopo la configurazione o il recupero di una configurazione del servizio di autorizzazione, è possibile costruire una richiesta di autorizzazione. Per creare la richiesta sono necessarie le informazioni seguenti:

* ID client (ad esempio 00000000-0000-0000-0000-000000000000)
* URI di reindirizzamento con schema personalizzato (ad esempio com.onmicrosoft.fabrikamb2c.exampleapp://oauthredirect)

Entrambi gli elementi sono stati salvati durante la [registrazione dell'app](#create-an-application).

```java
AuthorizationRequest req = new AuthorizationRequest.Builder(
    config,
    clientId,
    ResponseTypeValues.CODE,
    redirectUri)
    .build();
```

Per informazioni su come completare il resto della procedura, consultare la [guida di AppAuth](https://openid.github.io/AppAuth-Android/). Se è necessario iniziare subito con un'app funzionante, vedere [il nostro esempio](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c). Seguire i passaggi indicati in [README.md](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c/blob/master/README.md) per immettere la configurazione di Azure AD B2C.

Commenti e suggerimenti sono sempre graditi. In caso di difficoltà con questo argomento o di suggerimenti per migliorarne il contenuto, è possibile lasciare un commento nella parte inferiore della pagina. Le richieste di funzionalità possono essere aggiunte in [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c).


