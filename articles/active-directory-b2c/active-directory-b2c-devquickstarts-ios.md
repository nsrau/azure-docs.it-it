---
title: 'Azure Active Directory B2C: acquisire un token mediante un&quot;applicazione iOS | Microsoft Docs'
description: "Questo articolo spiega come creare un&quot;app per iOS che usa AppAuth con Azure Active Directory B2C per gestire le identità utente e l&quot;autenticazione degli utenti."
services: active-directory-b2c
documentationcenter: ios
author: saeeda
manager: krassk
editor: 
ms.assetid: d818a634-42c2-4cbd-bf73-32fa0c8c69d3
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: mobile-ios
ms.devlang: objectivec
ms.topic: article
ms.date: 03/07/2017
ms.author: saeeda
translationtype: Human Translation
ms.sourcegitcommit: afe143848fae473d08dd33a3df4ab4ed92b731fa
ms.openlocfilehash: 992bbf513ac87b0d955f9dc4b27984ef03050b83
ms.lasthandoff: 03/17/2017


---
# <a name="azure-ad-b2c-sign-in-using-an-ios-application"></a>Azure AD B2C: accedere mediante un'applicazione iOS

La piattaforma delle identità Microsoft usa standard aperti, ad esempio OAuth2 e OpenID Connect. L'uso di un protocollo a standard aperto offre più scelta allo sviluppatore nella selezione della libreria da integrare con i nostri servizi. Abbiamo fornito questa procedura dettagliata e altre simili per aiutare gli sviluppatori a scrivere applicazioni che si connettono alla piattaforma Microsoft Identity. La maggior parte delle librerie che implementano la [specifica OAuth2 RFC6749](https://tools.ietf.org/html/rfc6749) può connettersi alla piattaforma delle identità Microsoft.

> [!WARNING]
> Microsoft non fornisce correzioni per queste librerie di terze parti e non ha eseguito una verifica su esse. In questo esempio si usa una libreria di terze parti chiamata AppAuth che è stata testata per la compatibilità in scenari di base con Azure AD B2C. Le richieste relative a problemi e funzionalità devono essere indirizzate al progetto open source della libreria. Per altre informazioni, vedere [questo articolo](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries).
>
>

Se non si ha familiarità con OAuth2 o OpenID, gran parte di questo esempio risulterà poco chiara. È consigliabile vedere la breve [panoramica del protocollo documentata qui](active-directory-b2c-reference-protocols.md).

Non tutti gli scenari e le funzionalità di Azure Active Directory sono supportati dalla piattaforma B2C.  Per determinare se è consigliabile usare la piattaforma B2C, vedere l'articolo relativo alle [limitazioni di B2C](active-directory-b2c-limitations.md).

## <a name="get-an-azure-ad-b2c-directory"></a>Ottenere una directory di Azure AD B2C
Prima di poter usare Azure AD B2C, è necessario creare una directory, o tenant. Una directory è un contenitore per utenti, app, gruppi e altro ancora. Se non ne è già disponibile una, [creare una directory B2C](active-directory-b2c-get-started.md) prima di continuare.

## <a name="create-an-application"></a>Creare un'applicazione
Successivamente, è necessario creare un'app nella directory B2C. La registrazione dell'app fornisce ad Azure AD le informazioni necessarie per comunicare in modo sicuro con l'app. Per creare un'app per dispositivi mobili, [seguire questa procedura](active-directory-b2c-app-registration.md). Assicurarsi di:

* Includere un **client nativo** nell'applicazione.
* Copiare l' **ID applicazione** assegnato all'app. Questo GUID sarà necessario in seguito.
* Configurare un **URI di reindirizzamento** con schema personalizzato (ad esempio com.onmicrosoft.fabrikamb2c.exampleapp://oauth/redirect). Questo URI sarà necessario in seguito.

[!INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>Creare i criteri
In Azure AD B2C ogni esperienza utente è definita da [criteri](active-directory-b2c-reference-policies.md)specifici. Questa app contiene un'esperienza di identità che combina accesso e iscrizione. Creare i criteri come descritto nell'[articolo relativo ai criteri](active-directory-b2c-reference-policies.md#create-a-sign-up-policy). Durante la creazione dei criteri, assicurarsi di:

* In **Attributi iscrizione** selezionare l'attributo **Nome visualizzato**.  È possibile selezionare anche altri attributi.
* In **Attestazioni applicazione** selezionare **Nome visualizzato** e **ID oggetto dell'utente**. Sono disponibili anche altre attestazioni.
* Copiare il **Nome** di ogni criterio dopo averlo creato. Quando si salva il criterio, al relativo nome viene aggiunto un prefisso `b2c_1_`.  Il nome dei criteri sarà necessario in seguito.

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Dopo aver creato i criteri, è possibile passare alla creazione dell'app.

## <a name="download-the-sample-code"></a>Scaricare il codice di esempio
È fornito un esempio funzionante che usa AppAuth con Azure Active Directory B2C [in GitHub](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c). È possibile scaricare ed eseguire il codice. Per usare il proprio tenant Azure AD B2C, seguire le istruzioni riportate in [README.md](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c/blob/master/README.md).

Questo esempio è stato creato seguendo le istruzioni del file README del [progetto iOS AppAuth in GitHub](https://github.com/openid/AppAuth-iOS). Per altre informazioni sul funzionamento dell'esempio e della libreria, fare riferimento al file README di AppAuth in GitHub.

## <a name="modifying-your-app-to-use-azure-ad-b2c-with-appauth"></a>Modifica dell'app per usare Azure Active Directory B2C con AppAuth

> [!NOTE]
> AppAuth supporta iOS 7 e versioni successive.  Tuttavia, per supportare gli accessi da social network su Google, è necessario SFSafariViewController che richiede iOS 9 o versione successiva.
>

### <a name="configuration"></a>Configurazione

È possibile configurare la comunicazione con Azure AD B2C specificando sia l'URI dell'endpoint di autorizzazione che quello dell'endpoint di token.  Per generare questi URI sono necessarie le informazioni seguenti:
* ID tenant (ad esempio contoso.onmicrosoft.com)
* Nome del criterio (ad esempio B2C\_1\_SignUpIn)

L'URI dell'endpoint di token può essere generato mediante la sostituzione dell'ID\_tenant e del nome\_criterio nell'URL seguente:

```objc
static NSString *const tokenEndpoint = @"https://login.microsoftonline.com/te/<Tenant_ID>/<Policy_Name>/oauth2/v2.0/token";
```

L'URI dell'endpoint di autorizzazione può essere generato mediante la sostituzione dell'ID\_tenant e del nome\_criterio nell'URL seguente:

```objc
static NSString *const authorizationEndpoint = @"https://login.microsoftonline.com/te/<Tenant_ID>/<Policy_Name>/oauth2/v2.0/authorize";
```

Eseguire il codice seguente per creare l'oggetto AuthorizationServiceConfiguration:

```objc
OIDServiceConfiguration *configuration = 
    [[OIDServiceConfiguration alloc] initWithAuthorizationEndpoint:authorizationEndpoint tokenEndpoint:tokenEndpoint];
// now we are ready to perform the auth request...
```

### <a name="authorizing"></a>Autorizzazione

Dopo la configurazione o il recupero di una configurazione del servizio di autorizzazione, è possibile costruire una richiesta di autorizzazione. Per creare la richiesta sono necessarie le informazioni seguenti:  
* ID client (ad esempio 00000000-0000-0000-0000-000000000000)
* URI di reindirizzamento con schema personalizzato (ad esempio com.onmicrosoft.fabrikamb2c.exampleapp://oauth/redirect)

Entrambi gli elementi sono stati salvati durante la [registrazione dell'app](#create-an-application).

```objc
OIDAuthorizationRequest *request = 
    [[OIDAuthorizationRequest alloc] initWithConfiguration:configuration
                                                  clientId:kClientId
                                                    scopes:@[OIDScopeOpenID, OIDScopeProfile]
                                               redirectURL:[NSURL URLWithString:kRedirectUri]
                                              responseType:OIDResponseTypeCode
                                      additionalParameters:nil];

AppDelegate *appDelegate = (AppDelegate *)[UIApplication sharedApplication].delegate;
appDelegate.currentAuthorizationFlow = 
    [OIDAuthState authStateByPresentingAuthorizationRequest:request
                                   presentingViewController:self
                                                   callback:^(OIDAuthState *_Nullable authState, NSError *_Nullable error) {
        if (authState) {
            NSLog(@"Got authorization tokens. Access token: %@", authState.lastTokenResponse.accessToken);
            [self setAuthState:authState];
        } else {
            NSLog(@"Authorization error: %@", [error localizedDescription]);
            [self setAuthState:nil];
        }
    }];
```

Per configurare l'applicazione per la gestione del reindirizzamento all'URI con lo schema personalizzato, è necessario aggiornare l'elenco degli "Schemi URL" in Info.pList:
* Aprire Info.pList.
* Passare il mouse su una riga come "Codice del tipo di sistema operativo del creatore del bundle" e fare clic sul simbolo \+.
* Rinominare la nuova riga come "Tipi di URL".
* Fare clic sulla freccia a sinistra di "Tipi di URL" per aprire l'albero.
* Fare clic sulla freccia a sinistra di "Elemento 0" per aprire l'albero.
* Rinominare il primo elemento sotto l'elemento 0 in "Schemi URL".
* Fare clic sulla freccia a sinistra di "Schemi URL" per aprire l'albero.
* Nella colonna "Valore" è presente un campo vuoto a sinistra di "Elemento 0" sotto "Schemi URL".  Impostare il valore sullo schema univoco dell'applicazione.  Il valore deve corrispondere allo schema usato in redirectURL quando si crea l'oggetto OIDAuthorizationRequest.  Nel nostro esempio abbiamo usato lo schema "com.onmicrosoft.fabrikamb2c.exampleapp".

Per informazioni su come completare il resto della procedura, consultare la [guida di AppAuth](https://openid.github.io/AppAuth-iOS/). Se è necessario iniziare subito con un'app funzionante, vedere [il nostro esempio](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c). Seguire i passaggi indicati in [README.md](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c/blob/master/README.md) per immettere la configurazione di Azure AD B2C.

Commenti e suggerimenti sono sempre graditi. In caso di difficoltà con questo argomento o di suggerimenti per migliorarne il contenuto, è possibile lasciare un commento nella parte inferiore della pagina. Le richieste di funzionalità possono essere aggiunte in [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c).

