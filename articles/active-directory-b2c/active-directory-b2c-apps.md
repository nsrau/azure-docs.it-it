---
title: Tipi di applicazioni che possono essere usate in Azure Active Directory B2C | Microsoft Docs
description: Di seguito sono descritti i tipi di applicazioni che è possibile usare in Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 116abd259d11e66be2dc158d833c569f06aaa923
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/20/2019
ms.locfileid: "56446376"
---
# <a name="applications-types-that-can-be-used-in-active-directory-b2c"></a>Tipi di applicazioni che possono essere usate in Active Directory B2C

Azure Active Directory (Azure AD) B2C supporta l'autenticazione per un'ampia gamma di architetture di applicazioni moderne. Tutte le architetture sono basate sui protocolli standard [OAuth 2.0](active-directory-b2c-reference-protocols.md) o [OpenID Connect](active-directory-b2c-reference-protocols.md). Questo documento descrive i tipi di applicazione che è possibile compilare, indipendente dal linguaggio o dalla piattaforma preferita. Illustra anche gli scenari generali per iniziare a compilare applicazioni.

Ogni app che usa Azure AD B2C deve essere registrata in un tenant di [Azure AD B2C](active-directory-b2c-get-started.md) tramite il [portale di Azure](https://portal.azure.com/). Il processo di registrazione dell’applicazione raccoglie e assegna alcuni valori, tra cui:

* Un **ID applicazione** che identifica l'applicazione in modo univoco.
* Un **URL di risposta** che può essere usato per indirizzare le risposte all'applicazione.

Ogni richiesta inviata ad Azure AD B2C specifica un **flusso utente**, che è un criterio che controlla il comportamento di Azure AD. Gli endpoint possono anche essere usati per creare un set personalizzabile di esperienze utente. Viene fornito un set di flussi utente che consentono di configurare criteri comuni, tra cui criteri di iscrizione, accesso e modifica del profilo, ma è anche possibile creare criteri personalizzati. Se non si ha familiarità con i criteri, prima di continuare, vedere [Azure Active Directory B2C: framework di criteri estendibile](active-directory-b2c-reference-policies.md).

Ogni interazione di un'applicazione segue un modello generale simile al seguente:

1. L'applicazione indirizza l'utente all'endpoint 2.0 per l'esecuzione dei [criteri](active-directory-b2c-reference-policies.md).
2. L'utente completa i criteri in base alla relativa definizione.
3. L'applicazione riceve un token di sicurezza dall’endpoint v2.0.
4. L'applicazione usa il token di sicurezza per accedere a informazioni protette o a una risorsa protetta.
5. Il server delle risorse convalida il token di sicurezza per verificare che sia possibile concedere l'accesso.
6. L'applicazione aggiorna periodicamente il token di sicurezza.

Questi passaggi possono presentare lievi differenze a seconda del tipo di applicazione che si sta compilando.

## <a name="web-applications"></a>Applicazioni Web

Per applicazioni Web (ad esempio .NET, PHP, Java, Ruby, Python e Node.js) ospitate in un server e accessibili tramite un browser, Azure AD B2C supporta [OpenID Connect](active-directory-b2c-reference-protocols.md) per tutte le esperienze utente, Nell'implementazione di OpenID Connect in Azure AD B2C l'applicazione Web avvia esperienze utente inviando richieste di autenticazione ad Azure AD. Il risultato della richiesta è un `id_token`. Questo token di sicurezza rappresenta l'identità dell'utente e fornisce informazioni sull'utente sotto forma di attestazioni:

```
// Partial raw id_token
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImtyaU1QZG1Cd...

// Partial content of a decoded id_token
{
    "name": "John Smith",
    "email": "john.smith@gmail.com",
    "oid": "d9674823-dffc-4e3f-a6eb-62fe4bd48a58"
    ...
}
```

Per altre informazioni sui tipi di token e attestazioni disponibili per un'applicazione, vedere [Azure AD B2C: informazioni di riferimento sui token](active-directory-b2c-reference-tokens.md).

Nelle applicazioni Web, ogni esecuzione di [criteri](active-directory-b2c-reference-policies.md) include i passaggi generali seguenti:

1. L'utente passa all'applicazione Web.
2. L'applicazione Web reindirizza l'utente ad Azure AD B2C indicando i criteri da eseguire.
3. L'utente completa i criteri.
4. Azure AD B2C restituisce un oggetto `id_token` al browser.
5. `id_token` viene inserito nell'URI di reindirizzamento.
6. `id_token` viene convalidato e viene impostato un cookie di sessione.
7. All'utente viene restituita una pagina sicura.

La convalida di `id_token` con una chiave di firma pubblica ricevuta da Azure AD è sufficiente per verificare l'identità dell'utente. Questo processo imposta anche un cookie di sessione che può essere usato per identificare l'utente nelle richieste di pagina successive.

Per osservare il funzionamento di questo scenario, provare uno degli esempi di codice di accesso per applicazioni Web nella sezione [Introduzione](active-directory-b2c-overview.md).

Oltre al semplice accesso, un'applicazione per server Web può anche avere bisogno di accedere a un servizio Web back-end. In questo caso, l'applicazione Web può seguire un [flusso di OpenID Connect](active-directory-b2c-reference-oidc.md) leggermente diverso e acquisire i token usando codici di autorizzazione e token di aggiornamento. Questo scenario è illustrato di seguito nella sezione [API Web](#web-apis).

## <a name="web-apis"></a>API Web

È possibile usare Azure AD B2C per proteggere i servizi Web, ad esempio l'API Web RESTful dell'applicazione. Le API Web possono usare OAuth 2.0 per proteggere i propri dati, autenticando le richieste HTTP in ingresso con token. Il chiamante di un'API Web aggiunge un token all'intestazione dell'autorizzazione di una richiesta HTTP:

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

L'API Web può quindi usare il token per verificare l'identità del chiamante dell'API ed estrarre informazioni su quest'ultimo dalle attestazioni codificate nel token. Per altre informazioni sui tipi di token e attestazioni disponibili per un'app, vedere [Azure AD B2C: informazioni di riferimento sui token](active-directory-b2c-reference-tokens.md).

Un'API Web può ricevere token da molti tipi di client, tra cui applicazioni Web, applicazioni desktop e per dispositivi mobili, applicazioni a singola pagina, daemon sul lato server e altre API Web. Di seguito è riportato un esempio di flusso completo di un'applicazione Web che chiama un'API Web:

1. L'applicazione Web esegue criteri e l'utente completa l'esperienza utente.
2. Azure AD B2C restituisce un oggetto `id_token` (OpenID Connect) e un codice di autorizzazione al browser.
3. Il browser inserisce `id_token` e il codice di autenticazione nell'URI di reindirizzamento.
4. Il server Web convalida `id_token` e imposta un cookie di sessione.
5. Il server Web chiede ad Azure AD B2C un oggetto `access_token` fornendogli il codice di autenticazione, l'ID client applicazione e le credenziali client.
6. Vengono restituiti `access_token` e `refresh_token` al server Web.
7. Viene chiamata l'API Web con `access_token` in un'intestazione dell'autorizzazione.
8. L'API Web convalida il token.
9. Al server Web vengono restituiti dati sicuri.

Per altre informazioni su codici di autorizzazione, token di aggiornamento e procedure di recupero dei token, vedere l'articolo relativo al [protocollo OAuth 2.0](active-directory-b2c-reference-oauth-code.md).

Per informazioni su come proteggere un'API Web con Azure AD B2C, vedere le esercitazioni relative alle API Web nella sezione [Introduzione](active-directory-b2c-overview.md).

## <a name="mobile-and-native-applications"></a>Applicazioni per dispositivi mobili e native

Le applicazioni installate nei dispositivi, ad esempio le applicazioni desktop e per dispositivi mobili, devono spesso accedere a servizi back-end o ad API Web per conto degli utenti. È possibile aggiungere esperienze personalizzate di gestione delle identità alle applicazioni native ed eseguire chiamate sicure ai servizi back-end usando Azure AD B2C e il [flusso del codice di autorizzazione OAuth 2.0](active-directory-b2c-reference-oauth-code.md).  

In questo flusso l'applicazione esegue i [criteri](active-directory-b2c-reference-policies.md) e riceve un `authorization_code` da Azure AD dopo che l'utente ha completato i criteri. L'oggetto `authorization_code` rappresenta l'autorizzazione dell'applicazione a chiamare servizi back-end per conto dell'utente che ha eseguito l'accesso. L'applicazione può quindi scambiare l'oggetto `authorization_code` in background con un `id_token` e un `refresh_token`.  L'applicazione può usare l'oggetto `id_token` per eseguire l'autenticazione a un'API Web back-end nelle richieste HTTP. Può anche usare `refresh_token` per ottenere un nuovo `id_token` alla scadenza di quello precedente.

## <a name="current-limitations"></a>Limitazioni correnti

### <a name="application-not-supported"></a>Applicazione non supportata 

#### <a name="daemonsserver-side-applications"></a>Applicazioni sul lato server o daemon

Anche le applicazioni che contengono processi a esecuzione prolungata o che funzionano senza la presenza di un utente necessitano di un modo per accedere alle risorse protette, ad esempio le API Web. Tali applicazioni possono autenticarsi e ottenere i token usando la propria identità, invece di un'identità delegata dell'utente, e mediante il flusso di credenziali client OAuth 2.0. Il flusso di credenziali client non è lo stesso del flusso per conto dell’utente e quest’ultimo non deve essere utilizzato per l'autenticazione da server a server.

Anche se il flusso di credenziali client non è attualmente supportato da Azure AD B2C, è possibile impostare il flusso di credenziali client tramite Azure AD. Un tenant di Azure AD B2C condivide alcune funzionalità con i tenant enterprise di Azure AD.  Il flusso delle credenziali client è supportato tramite la funzionalità di Azure AD del tenant di Azure AD B2C. 

Per impostare il flusso di credenziali client, vedere [Azure Active Directory v2.0 e il flusso di credenziali client OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-protocols-oauth-client-creds). Un'autenticazione riuscita comporta la ricezione di un token formattato in modo che possa essere utilizzato da Azure AD come descritto in [Riferimento al token di Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims).

#### <a name="web-api-chains-on-behalf-of-flow"></a>Catene di API Web (flusso On-Behalf-Of)

Molte architetture includono un'API Web che deve chiamare un'altra API Web downstream, entrambe protette da Azure AD B2C. Questo scenario è comune nei client nativi che hanno un back-end dell'API Web e chiama un servizio online Microsoft come l'API Graph di Azure AD.

Questo scenario di API Web concatenata può essere supportato usando la concessione delle credenziali di connessione JWT di OAuth 2.0, nota anche come flusso On-Behalf-Of.  Il flusso On-Behalf-Of, tuttavia, non è attualmente implementato in Azure AD B2C.

### <a name="faulted-apps"></a>App con errori

Non modificare le app di Azure AD B2C in questi modi:

- In altri portali di gestione delle applicazioni, come il  [portale di registrazione delle applicazioni](https://apps.dev.microsoft.com/).
- Usando l'API Graph o PowerShell.

Se si modifica l'applicazione Azure AD B2C all'esterno del portale di Azure, l'applicazione viene danneggiata e non può più essere usata con Azure AD B2C. Eliminare l'app e crearla di nuovo.

Per eliminare l'applicazione, passare al [portale di registrazione delle applicazioni](https://apps.dev.microsoft.com/) ed eliminarla nel portale. Per rendere l'applicazione visibile, è necessario essere il proprietario dell'applicazione (e non solo un amministratore del tenant).

