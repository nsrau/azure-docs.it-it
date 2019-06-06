---
title: Acquisire un token mediante un'applicazione Android in Azure Active Directory B2C | Microsoft Docs
description: Questo articolo spiega come creare un'app per Android che usa AppAuth con Azure Active Directory B2C per gestire le identità utente e l'autenticazione degli utenti.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 4db4806b6be018bfc53a155627de825bf62d8395
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66510100"
---
# <a name="sign-in-using-an-android-application-in-azure-active-directory-b2c"></a>Accedere mediante un'applicazione Android in Azure Active Directory B2C

La piattaforma delle identità Microsoft usa standard aperti, ad esempio OAuth2 e OpenID Connect. Questi standard consentono di usare qualsiasi libreria che si vuole integrare con Azure Active Directory B2C. Per aiutare gli sviluppatori a usare altre librerie, sono state scritte alcune procedure dettagliate come questa, che illustrano come configurare le librerie di terze parti per connettersi alla piattaforma delle identità Microsoft. La maggior parte delle librerie che implementano [la specifica OAuth2 RFC6749](https://tools.ietf.org/html/rfc6749) può connettersi alla piattaforma delle identità Microsoft.

> [!WARNING]
> Microsoft non fornisce correzioni per queste librerie di terze parti e non ha eseguito una verifica su esse. In questo esempio si usa una libreria di terze parti chiamata AppAuth che è stata testata per la compatibilità in scenari di base con Azure AD B2C. Le richieste relative a problemi e funzionalità devono essere indirizzate al progetto open source della libreria. Per altre informazioni, vedere [questo articolo](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries).  
>
>

Se non si ha familiarità con OAuth2 o OpenID, gran parte di questo esempio risulterà poco chiara. È consigliabile vedere la breve [panoramica del protocollo documentata qui](active-directory-b2c-reference-protocols.md).

## <a name="get-an-azure-ad-b2c-directory"></a>Ottenere una directory di Azure AD B2C

Prima di poter usare Azure AD B2C, è necessario creare una directory, o tenant. Una directory è un contenitore per utenti, app, gruppi e così via. Se non ne è già disponibile una, prima di continuare [creare una directory B2C](tutorial-create-tenant.md) .

## <a name="create-an-application"></a>Creare un'applicazione

Successivamente, è necessario creare un'app nella directory B2C. In questo modo Azure AD acquisisce le informazioni necessarie per comunicare in modo sicuro con l'app. Per creare un'app per dispositivi mobili, [seguire questa procedura](active-directory-b2c-app-registration.md). Assicurarsi di:

* Includere un **client nativo** nell'applicazione.
* Copiare l' **ID applicazione** assegnato all'app. che sarà necessario più avanti.
* Configurare un **URI di reindirizzamento** client nativo (ad esempio com.onmicrosoft.fabrikamb2c.exampleapp://oauth/redirect). Sarà necessario più avanti.

## <a name="create-your-user-flows"></a>Creare i flussi utente

In Azure AD B2C ogni esperienza utente viene definita da un [flusso utente](active-directory-b2c-reference-policies.md), che è un set di criteri che controllano il comportamento di Azure AD. Questa applicazione richiede un flusso utente di accesso e iscrizione. Durante la creazione del flusso utente, assicurarsi di:

* Scegliere **Nome visualizzato** come attributo di iscrizione nel flusso utente.
* Scegliere le attestazioni dell'applicazione **Nome visualizzato** e **ID oggetto** ogni flusso utente. È consentito scegliere anche altre attestazioni.
* Copiare il **nome** di ogni flusso utente dopo averlo creato. Dovrebbero mostrare il prefisso `b2c_1_`.  Il nome del flusso utente sarà necessario in un secondo momento.

Dopo aver creato i flussi utente, è possibile passare alla creazione dell'app.

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
* Nome del flusso utente (ad esempio B2C\_1\_SignUpIn)

Se si sceglie di individuare automaticamente gli URI degli endpoint di autorizzazione e di token, è necessario recuperare le informazioni dall'URI di individuazione. L'URI di individuazione può essere generato mediante la sostituzione dell'ID\_tenant e del nome\_criterio nell'URL seguente:

```java
String mDiscoveryURI = "https://<Tenant_name>.b2clogin.com/<Tenant_ID>/v2.0/.well-known/openid-configuration?p=<Policy_Name>";
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
String mAuthEndpoint = "https://<Tenant_name>.b2clogin.com/<Tenant_ID>/oauth2/v2.0/authorize?p=<Policy_Name>";

String mTokenEndpoint = "https://<Tenant_name>.b2clogin.com/<Tenant_ID>/oauth2/v2.0/token?p=<Policy_Name>";
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

