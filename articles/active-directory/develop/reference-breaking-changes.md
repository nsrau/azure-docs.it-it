---
title: Informazioni di riferimento sulle modifiche di rilievo di Azure Active Directory | Microsoft Docs
description: Informazioni sulle modifiche apportate ai protocolli di Azure AD che possono influire sull'applicazione.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 68517c83-1279-4cc7-a7c1-c7ccc3dbe146
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 3/13/2020
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 1248063c1b4c1b1e124ff671797450dd5c1b8727
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050179"
---
# <a name="whats-new-for-authentication"></a>Novità per l'autenticazione 

>Per ricevere notifiche sugli aggiornamenti a questa pagina è sufficiente aggiungere [questo URL](https://docs.microsoft.com/api/search/rss?search=%22whats%20new%20for%20authentication%22&locale=en-us) al lettore di feed RSS.

Il sistema di autenticazione modifica e aggiunge funzionalità regolarmente per migliorare la sicurezza e la conformità agli standard. Per stare al passo con gli sviluppi più recenti, questo articolo fornisce informazioni sugli argomenti seguenti:

- Funzionalità più recenti
- Problemi noti
- Modifiche al protocollo
- Funzionalità deprecate

> [!TIP] 
> Questa pagina viene aggiornata regolarmente, quindi è consigliabile consultarla spesso. Se non specificato diversamente, queste modifiche vengono applicate solo alle applicazioni appena registrate.  

## <a name="upcoming-changes"></a>Modifiche imminenti

Nessun aggiornamento pianificato in questo momento.  Si prega di vedere di seguito per i cambiamenti che sono in o stanno arrivando in produzione. 

## <a name="march-2020"></a>Marzo 2020 

### <a name="user-passwords-will-be-restricted-to-256-characters"></a>Le password utente saranno limitate a 256 caratteri.

**Data di validità**: 13 marzo 2020

**Endpoint interessati**: sia la versione 1.0 che la versione 2.0

**Protocollo interessato**: Tutti i flussi utente. 

Gli utenti con password più lunghe di 256 caratteri che accedono direttamente ad Azure AD (a differenza di un IDP federato come ADFS) non potranno accedere a partire dal 13 marzo 2020 e verrà chiesto di reimpostare la password.  Gli amministratori possono ricevere richieste per aiutare a reimpostare la password degli utenti. 

L'errore nei registri di accesso sarà AADSTS 50052: InvalidPasswordExceedsMaxLength

Messaggio:`The password entered exceeds the maximum length of 256. Please reach out to your admin to reset the password.`

Bonifica:

L'utente non è in grado di accedere perché la password supera la lunghezza massima consentita. Devono contattare l'amministratore per reimpostare la password. Se SSPR è abilitato per il tenant, è possibile reimpostare la password seguendo il collegamento "Password dimenticata".



## <a name="february-2020"></a>Febbraio 2020 

### <a name="empty-fragments-will-be-appended-to-every-http-redirect-from-the-login-endpoint"></a>I frammenti vuoti verranno aggiunti a ogni reindirizzamento HTTP dall'endpoint di accesso. 

**Data di validità**: 8 febbraio 2020

**Endpoint interessati**: sia la versione 1.0 che la versione 2.0

**Protocollo interessato**: flussi OAuth e OIDC che utilizzano response_type, che copre il flusso del codice di [autorizzazione](v2-oauth2-auth-code-flow.md) in alcuni casi e il [flusso implicito](v2-oauth2-implicit-grant-flow.md). 

Quando una risposta di autenticazione viene inviata da login.microsoftonline.com a un'applicazione tramite reindirizzamento HTTP, il servizio aggiungerà un frammento vuoto all'URL di risposta.  In questo modo si evita una classe di attacchi di reindirizzamento assicurando che il browser elimini qualsiasi frammento esistente nella richiesta di autenticazione.  Nessuna app deve avere una dipendenza da questo comportamento. 


## <a name="august-2019"></a>Agosto 2019

### <a name="post-form-semantics-will-be-enforced-more-strictly---spaces-and-quotes-will-be-ignored"></a>La semantica del modulo POST verrà applicata in modo più rigoroso- gli spazi e le virgolette verranno ignorati

**Data di validità**: 2 settembre 2019

**Endpoint interessati**: sia la versione 1.0 che la versione 2.0

**Protocollo interessato**: Ovunque venga utilizzato POST[(credenziali client](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow), [riscatto del codice](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-auth-code-flow)di autorizzazione , [ROPC](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth-ropc), [OBO](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow)e [riscatto token](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-auth-code-flow#refresh-the-access-token)di aggiornamento )

A partire dalla settimana 9/2, le richieste di autenticazione che utilizzano il metodo POST verranno convalidate utilizzando standard HTTP più severi.  In particolare, gli spazi e le virgolette doppie (") non verranno più rimossi dai valori del modulo di richiesta. Queste modifiche non devono interrompere i client esistenti e garantiscono che le richieste inviate ad Azure AD vengano gestite in modo affidabile ogni volta. In futuro (vedi sopra) prevediamo di rifiutare ulteriormente i parametri duplicati e ignorare la distinta materiali all'interno delle richieste. 

Esempio:

Oggi, `?e=    "f"&g=h` viene analizzato `?e=f&g=h` in `e`  ==  `f`modo identico come - così .  Con questa modifica, sarebbe ora analizzato in modo che - questo è improbabile che `e`  ==  `    "f"` sia un argomento valido e la richiesta avrebbe ora esito negativo. 


## <a name="july-2019"></a>Luglio 2019

### <a name="app-only-tokens-for-single-tenant-applications-are-only-issued-if-the-client-app-exists-in-the-resource-tenant"></a>I token solo app per le applicazioni single-tenant vengono rilasciati solo se l'app client esiste nel tenant delle risorse

**Data di validità**: 26 luglio 2019

**Endpoint interessati**: sia [v1.0](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow) che [v2.0](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow)

**Protocollo interessato**: [Credenziali client (token solo app)](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow)

Una modifica alla sicurezza è stata attivata il 26 luglio che modifica il modo in cui vengono emessi i token solo per le app (tramite la concessione delle credenziali client). In precedenza, alle applicazioni era consentito ottenere i token per chiamare qualsiasi altra app, indipendentemente dalla presenza nel tenant o dai ruoli autorizzati per l'applicazione.  Questo comportamento è stato aggiornato in modo che per le risorse (talvolta denominate API Web) impostate come single-tenant (impostazione predefinita), l'applicazione client deve esistere all'interno del tenant della risorsa.  Si noti che il consenso esistente tra il client e l'API non è `roles` ancora necessario e le app devono comunque eseguire i propri controlli di autorizzazione per assicurarsi che un'attestazione sia presente e contenga il valore previsto per l'API.

Il messaggio di errore per questo scenario afferma attualmente: 

`The service principal named <appName> was not found in the tenant named <tenant_name>. This can happen if the application has not been installed by the administrator of the tenant.`

Per risolvere questo problema, usare l'esperienza di consenso dell'amministratore per creare l'entità servizio dell'applicazione client nel tenant o crearla manualmente.  Questo requisito garantisce che il tenant abbia concesso all'applicazione l'autorizzazione per operare all'interno del tenant.  

#### <a name="example-request"></a>Richiesta di esempio

`https://login.microsoftonline.com/contoso.com/oauth2/authorize?resource=https://gateway.contoso.com/api&response_type=token&client_id=14c88eee-b3e2-4bb0-9233-f5e3053b3a28&...`In questo esempio, il tenant delle risorse (autorità) è contoso.com, `gateway.contoso.com/api` l'app per le risorse `14c88eee-b3e2-4bb0-9233-f5e3053b3a28`è un'app a tenant singolo chiamata per il tenant Contoso e l'app client è .  Se l'app client dispone di un'entità servizio all'interno di Contoso.com, questa richiesta può continuare.  In caso contrario, tuttavia, la richiesta avrà esito negativo con l'errore precedente.  

Se l'app gateway Contoso è un'applicazione multi-tenant, tuttavia, la richiesta continuerà indipendentemente dal fatto che l'app client abbia un'entità servizio all'interno di Contoso.com.  

### <a name="redirect-uris-can-now-contain-query-string-parameters"></a>Gli URI di reindirizzamento possono ora contenere parametri di stringa di queryRedirect URIs can now contain query string parameters

**Data di validità**: 22 luglio 2019

**Endpoint interessati**: sia la versione 1.0 che la versione 2.0

**Protocollo interessato**: Tutti i flussi

In [base a RFC 6749,](https://tools.ietf.org/html/rfc6749#section-3.1.2)le applicazioni Azure AD possono ora registrare `https://contoso.com/oauth2?idp=microsoft`e utilizzare gli URI di reindirizzamento (risposta) con parametri di query statici (ad esempio ) per le richieste OAuth 2.0.  Gli URI di reindirizzamento dinamico sono ancora proibiti in quanto rappresentano un rischio per la sicurezza `state` e non possono essere utilizzati per mantenere le informazioni sullo stato in una richiesta di autenticazione, per questo, utilizzare il parametro .

Il parametro di query statico è soggetto alla corrispondenza della stringa per gli URI di reindirizzamento come qualsiasi altra parte dell'URI di reindirizzamento: se non viene registrata alcuna stringa corrispondente all'redirect_uri con decodifica URI, la richiesta verrà rifiutata.  Se l'URI viene trovato nella registrazione dell'app, verrà utilizzata l'intera stringa per reindirizzare l'utente, incluso il parametro di query statico. 

Si noti che in questo momento (fine luglio 2019), l'esperienza utente di registrazione dell'app nel portale di Azure blocca ancora i parametri di query.  Tuttavia, è possibile modificare manualmente il manifesto dell'applicazione per aggiungere parametri di query e testarlo nell'app.  


## <a name="march-2019"></a>Marzo 2019

### <a name="looping-clients-will-be-interrupted"></a>I client a ciclo continuo verranno interrotti

**Data di validità**: 25 marzo 2019

**Endpoint interessati**: sia la versione 1.0 che la versione 2.0

**Protocollo interessato**: Tutti i flussi

Le applicazioni client possono talvolta comportarsi male, emettendo centinaia della stessa richiesta di accesso in un breve periodo di tempo.  Queste richieste possono o non possono avere esito positivo, ma tutte contribuiscono a una scarsa esperienza utente e a carichi di lavoro più accresciuti per l'IDP, aumentando la latenza per tutti gli utenti e riducendo la disponibilità dell'IDP.  Queste applicazioni funzionano al di fuori dei limiti dell'utilizzo normale e devono essere aggiornate per funzionare correttamente.  

Ai client che inviano richieste `invalid_grant` duplicate `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request`più volte verrà inviato un errore: . 

La maggior parte dei client non dovrà modificare il comportamento per evitare questo errore.  Solo i client non configurati correttamente (quelli senza la memorizzazione nella cache dei token o quelli già presenti nei cicli prompt) saranno interessati da questo errore.  I client vengono registrati in base alle istanze in locale (tramite cookie) in base ai fattori seguenti:Clients are tracked on a per-instance basis locally (via cookie) on the following factors:

* Suggerimento per l'utente, se presente

* Ambiti o risorse richieste

* ID client

* URI di reindirizzamento

* Tipo di risposta e modalità

Le app che effettuano più richieste (più di 15) `invalid_grant` in un breve periodo di tempo (5 minuti) riceveranno un errore che spiega che stanno crollando.  I token richiesti hanno durate sufficientemente lunghe (10 minuti minimo, 60 minuti per impostazione predefinita), pertanto le richieste ripetute in questo periodo di tempo non sono necessarie.  

Tutte le `invalid_grant` app devono essere gestite mostrando un prompt interattivo, anziché richiedere automaticamente un token.  Per evitare questo errore, i client devono assicurarsi di memorizzare correttamente nella cache i token ricevuti.


## <a name="october-2018"></a>Ottobre 2018

### <a name="authorization-codes-can-no-longer-be-reused"></a>I codici di autorizzazione non possono più essere riutilizzati

**Data di validità**: 15 novembre 2018

**Endpoint interessati**: sia la versione 1.0 che la versione 2.0

**Protocollo interessato**: [flusso del codice](v2-oauth2-auth-code-flow.md)

A partire dal 15 novembre 2018, Azure AD non accetterà più i codici di autenticazione usati in precedenza per le app. Questa modifica di sicurezza consente di allineare Azure AD con la specifica OAuth e verrà applicata a entrambi gli endpoint v1 e v2.

Se l'app usa nuovamente i codici di autorizzazione per ottenere token per più risorse, è consigliabile usare il codice per ottenere un token di aggiornamento che consenta di acquisire token aggiuntivi per altre risorse. I codici di autorizzazione possono essere usati solo una volta, mentre quelli di aggiornamento possono essere usati più volte su più risorse. Qualsiasi nuova app che tenti di usare di nuovo un codice di autenticazione durante il flusso del codice OAuth genererà un errore invalid_grant.

Per altre informazioni sui token di aggiornamento, vedere [Aggiornamento dei token di accesso](v2-oauth2-auth-code-flow.md#refresh-the-access-token).  Se si usa ADAL o MSAL, questa operazione viene gestita automaticamente dalla libreria. Sostituire la seconda istanza di 'AcquireTokenByAuthorizationCodeAsync' con 'AcquireTokenSilentAsync'. 

## <a name="may-2018"></a>Maggio 2018

### <a name="id-tokens-cannot-be-used-for-the-obo-flow"></a>I token ID non possono essere usati per un flusso OBO

**Data**: 1 maggio 2018

**Endpoint interessati**: sia la versione 1.0 che la versione 2.0

**Protocolli interessati**: Flusso implicito e [flusso per conto di](v2-oauth2-on-behalf-of-flow.md)

Dopo il 1° maggio 2018, i token ID non possono essere usati come asserzione in un flusso OBO per le nuove applicazioni. È invece necessario usare token di accesso per proteggere le API, anche tra un client e il livello intermedio della stessa applicazione. Le app registrate prima del 1° maggio 2018 continueranno a funzionare e a poter scambiare token ID per un token di accesso, ma questa non è una procedura consigliata.

Per ovviare a questa modifica, è possibile eseguire le operazioni seguenti:

1. Creare un'API Web per l'applicazione, con uno o più ambiti. Questo punto di ingresso esplicito permetterà di ottenere il controllo e la sicurezza con granularità maggiore.
1. Nel manifesto dell'app, nel [portale di Azure](https://portal.azure.com) o nel [portale di registrazione delle app](https://apps.dev.microsoft.com) assicurarsi che all'app sia consentito rilasciare token di accesso usando il flusso implicito. Questa funzionalità è controllata attraverso la chiave `oauth2AllowImplicitFlow`.
1. Quando l'applicazione client richiede un token ID tramite `response_type=id_token`, richiede anche un token di accesso (`response_type=token`) per l'API Web creata in precedenza. Di conseguenza, quando si usa l'endpoint 2.0, il parametro `scope` dovrebbe essere simile a `api://GUID/SCOPE`. Nell'endpoint v1.0 il parametro `resource` deve essere l'URI dell'app dell'API Web.
1. Passare questo token di accesso al livello intermedio al posto del token ID.  
