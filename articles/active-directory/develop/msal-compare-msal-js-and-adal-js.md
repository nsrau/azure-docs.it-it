---
title: Differenze tra MSAL. js e ADAL. js
titleSuffix: Microsoft identity platform
description: Informazioni sulle differenze tra Microsoft Authentication Library per JavaScript (MSAL.js) e Azure AD Authentication Library per JavaScript (ADAL.js) e sulla scelta della libreria da usare.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/10/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: e3696eb90abbb35f4d989649b4a1198aec69b9a5
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74916911"
---
# <a name="differences-between-msal-js-and-adal-js"></a>Differenze tra MSAL.js e ADAL.js

Sia Microsoft Authentication Library per JavaScript (MSAL.js) che Azure AD Authentication Library per JavaScript (ADAL.js) vengono usate per eseguire l'autenticazione delle entità di Azure AD e richiedere token da Azure AD. Finora, la maggior parte degli sviluppatori ha usato Azure AD per sviluppatori (v1.0) per autenticare le identità di Azure AD (account aziendali e dell'istituto di istruzione) richiedendo token con ADAL. Ora, con MSAL.js, è possibile eseguire l'autenticazione di un set più ampio di identità Microsoft (identità di Azure AD, account Microsoft e account social e locali tramite Azure AD B2C) usando Microsoft Identity Platform (versione 2.0).

In questo articolo viene descritto come scegliere tra Microsoft Authentication Library per JavaScript (MSAL.js) e Azure AD Authentication Library per JavaScript (ADAL.js) e vengono confrontate le due librerie.

## <a name="choosing-between-adaljs-and-msaljs"></a>Scelta tra ADAL.js e MSAL.js

Nella maggior parte dei casi è consigliabile usare Microsoft Identity Platform e MSAL.js, che rappresenta l'ultima generazione delle librerie di autenticazione di Microsoft. Tramite MSAL.js è possibile acquisire i token per gli utenti che accedono all'applicazione con Azure AD (account aziendali e dell'istituto di istruzione), account Microsoft (personali) o Azure AD B2C.

Se si ha già familiarità con l'endpoint della versione 1.0 (e ADAL.js), è possibile leggere l'articolo sulle [differenze rispetto all'endpoint di Microsoft Identity Platform versione 2.0](active-directory-v2-compare.md).

È comunque necessario usare ADAL.js se l'applicazione deve consentire l'accesso degli utenti con versioni precedenti di [Active Directory Federation Services (AD FS)](/windows-server/identity/active-directory-federation-services).

## <a name="key-differences-in-authentication-with-msaljs"></a>Differenze principali di autenticazione con MSAL.js

### <a name="core-api"></a>API Core

* ADAL.js usa [AuthenticationContext](https://github.com/AzureAD/azure-activedirectory-library-for-js/wiki/Config-authentication-context#authenticationcontext) come rappresentazione di un'istanza della connessione dell'applicazione al server di autorizzazione o al provider di identità tramite un URL dell'autorità. L'API MSAL.js, al contrario, è stata progettata sulla base di un'applicazione client di agente utente, un tipo di applicazione client pubblica in cui il codice client viene eseguito in un agente utente, ad esempio un Web browser. Fornisce la classe `UserAgentApplication` per rappresentare un'istanza del contesto di autenticazione dell'applicazione con il server di autorizzazione. Per altre informazioni, vedere [Inizializzare le applicazioni client con MSAL.js](msal-js-initializing-client-applications.md).

* In ADAL.js i metodi per acquisire i token sono associati a un'unica autorità impostata in `AuthenticationContext`. In MSAL.js le richieste di token di acquisizione possono accettare valori di autorità diversi rispetto a quello impostato in `UserAgentApplication`. In questo modo, MSAL.js può acquisire e memorizzare nella cache token separati per più tenant e account utente nella stessa applicazione.

* In ADAL.js il metodo per acquisire e rinnovare automaticamente i token senza chiedere agli utenti è denominato `acquireToken`. In MSAL.js questo metodo è denominato `acquireTokenSilent` per descrivere più chiaramente questa funzionalità.

### <a name="authority-value-common"></a>Valore di autorità `common`

Nella versione 1.0, l'uso dell'autorità `https://login.microsoftonline.com/common` consente agli utenti di accedere con qualsiasi account Azure AD (per qualsiasi organizzazione).

Nella versione 2.0, l'uso dell'autorità `https://login.microsoftonline.com/common` consente agli utenti di accedere con qualsiasi account Azure AD dell'organizzazione o con un account Microsoft personale. Per limitare l'accesso agli account di Azure AD (stesso comportamento adottato con ADAL.js), è necessario usare `https://login.microsoftonline.com/organizations`. Per informazioni dettagliate, vedere l'opzione di configurazione `authority` in [Inizializzare le applicazioni client con MSAL.js](msal-js-initializing-client-applications.md).

### <a name="scopes-for-acquiring-tokens"></a>Ambiti per l'acquisizione dei token
* Ambito in sostituzione del parametro della risorsa nelle richieste di autenticazione per l'acquisizione di token

    Il protocollo della versione 2.0 usa gli ambiti invece delle risorse nelle richieste. In altre parole, quando l'applicazione deve richiedere i token con autorizzazioni per una risorsa, ad esempio Microsoft Graph, la differenza tra i valori passati ai metodi della libreria è la seguente:

    v1.0: resource = https\://graph.microsoft.com

    v2.0: scope = https\://graph.microsoft.com/User.Read

    È possibile richiedere gli ambiti per qualsiasi API di risorsa usando l'URI dell'API in questo formato: URIidapp/ambito, ad esempio: https:\//mytenant.onmicrosoft.com/myapi/api.read

    Solo per l'API Graph MS, un valore di ambito `user.read` viene mappato a https:\//graph.microsoft.com/User.Read e può essere usato in modo intercambiabile.

    ```javascript
    var request = {
        scopes = ["User.Read"];
    };

    acquireTokenPopup(request);   
    ```

* Ambiti dinamici per il consenso incrementale.

    Quando si creano applicazioni tramite la versione 1.0, è necessario registrare il set completo di autorizzazioni (ambiti statici) richiesto dall'applicazione per ottenere il consenso dell'utente al momento dell'accesso. Nella versione 2.0 è possibile usare il parametro di ambito per richiedere le autorizzazioni nel momento desiderato. Gli ambiti di questo tipo sono definiti dinamici. In questo modo l'utente può fornire il proprio consenso incrementale agli ambiti. Pertanto, se all'inizio si vuole semplicemente che l'utente acceda all'applicazione e non è necessario alcun tipo di accesso, è possibile impostare questo tipo di autorizzazione. Se, in un secondo momento, si vuole avere la possibilità di leggere il calendario dell'utente, è possibile richiedere l'ambito del calendario nei metodi di acquisizione dei token e ottenere il consenso dell'utente. ad esempio:

    ```javascript
    var request = {
        scopes = ["https://graph.microsoft.com/User.Read", "https://graph.microsoft.com/Calendar.Read"];
    };

    acquireTokenPopup(request);   
    ```

* Ambiti per le API V1.0

    Quando si recuperano i token per le API V1.0 usando MSAL.js, è possibile richiedere tutti gli ambiti statici registrati sull'API aggiungendo `.default` come ambito nell'URI ID app dell'API. ad esempio:

    ```javascript
    var request = {
        scopes = [ appidURI + "/.default"];
    };

    acquireTokenPopup(request);
    ```

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni, vedere il [confronto tra la versione 1.0 e la versione 2.0](active-directory-v2-compare.md).
