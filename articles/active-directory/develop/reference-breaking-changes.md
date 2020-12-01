---
title: Riferimento a modifiche di rilievo Azure Active Directory
description: Informazioni sulle modifiche apportate ai protocolli di Azure AD che possono influire sull'applicazione.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: reference
ms.date: 5/4/2020
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 94c34e6f7cb24ff749e5de95f1c28a496700af80
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96348722"
---
# <a name="whats-new-for-authentication"></a>Novità per l'autenticazione

> Per ricevere notifiche degli aggiornamenti a questa pagina, incollare l'URL nel lettore di feed RSS:<br/>`https://docs.microsoft.com/api/search/rss?search=%22whats%20new%20for%20authentication%22&locale=en-us`

Il sistema di autenticazione modifica e aggiunge funzionalità regolarmente per migliorare la sicurezza e la conformità agli standard. Per stare al passo con gli sviluppi più recenti, questo articolo fornisce informazioni sugli argomenti seguenti:

- Funzionalità più recenti
- Problemi noti
- Modifiche al protocollo
- Funzionalità deprecate

> [!TIP]
> Questa pagina viene aggiornata regolarmente, quindi è consigliabile consultarla spesso. Se non specificato diversamente, queste modifiche vengono applicate solo alle applicazioni appena registrate.

## <a name="upcoming-changes"></a>Modifiche imminenti

Nessun aggiornamento pianificato in questo momento.  Vedere di seguito per le modifiche apportate o destinate alla produzione.

## <a name="may-2020"></a>Maggio 2020

### <a name="azure-government-endpoints-are-changing"></a>Gli endpoint di Azure per enti pubblici cambiano

**Data di validità**: 5 maggio (fine giugno 2020) 

Gli **endpoint sono interessati**: tutti

**Influisce sul protocollo**: tutti i flussi

Il 1 giugno 2018, l'autorità ufficiale Azure Active Directory (AAD) per Azure Government è cambiata da `https://login-us.microsoftonline.com` a `https://login.microsoftonline.us` . Questa modifica è stata applicata anche a Microsoft 365 GCC High e DoD, che Azure Government AAD ha anche servizi. Se si è proprietari di un'applicazione in un tenant del governo degli Stati Uniti, è necessario aggiornare l'applicazione per consentire agli utenti di accedere all' `.us` endpoint.  

A partire dal 5 maggio, Azure AD inizierà a applicare la modifica dell'endpoint, bloccando l'accesso degli utenti del governo alle app ospitate nei tenant del governo degli Stati Uniti usando l'endpoint pubblico ( `microsoftonline.com` ).  Le app interessate inizieranno a visualizzare un errore `AADSTS900439`  -  `USGClientNotSupportedOnPublicEndpoint` . Questo errore indica che l'app sta tentando di accedere a un utente del governo degli Stati Uniti nell'endpoint del cloud pubblico. Se l'app si trova in un tenant cloud pubblico ed è destinata a supportare gli utenti del governo degli Stati Uniti, sarà necessario [aggiornare l'app per supportarli in modo esplicito](./authentication-national-cloud.md). Questa operazione potrebbe richiedere la creazione di una nuova registrazione dell'app nel cloud per il governo degli Stati Uniti. 

L'imposizione di questa modifica verrà eseguita usando un'implementazione graduale in base alla frequenza con cui gli utenti del cloud degli Stati Uniti possono accedere alle app per la firma degli utenti del governo degli Stati Uniti raramente vedranno l'imposizione e le app usate di frequente dagli utenti del governo degli Stati Uniti avranno infine l'applicazione dell'applicazione. Si prevede che l'applicazione venga completata in tutte le app del 2020 giugno. 

Per altri dettagli, vedere il [post del Blog di Azure per enti pubblici sulla migrazione](https://devblogs.microsoft.com/azuregov/azure-government-aad-authority-endpoint-update/). 

## <a name="march-2020"></a>Marzo 2020

### <a name="user-passwords-will-be-restricted-to-256-characters"></a>Le password utente saranno limitate a 256 caratteri.

**Data di validità**: 13 marzo 2020

Gli **endpoint sono interessati**: tutti

**Influisce sul protocollo**: tutti i flussi utente.

Gli utenti con password con una lunghezza superiore a 256 caratteri che effettuano l'accesso direttamente a Azure AD (in contrapposizione a un IDP federato come ad FS) non saranno in grado di eseguire l'accesso a partire dal 13 marzo 2020 e verrà richiesto di reimpostare la password.  Gli amministratori possono ricevere richieste di assistenza per la reimpostazione della password degli utenti.

L'errore nei log di accesso sarà AADSTS 50052: InvalidPasswordExceedsMaxLength

Messaggio: `The password entered exceeds the maximum length of 256. Please reach out to your admin to reset the password.`

Correzione:

L'utente non è in grado di eseguire l'accesso perché la password supera la lunghezza massima consentita. È necessario contattare l'amministratore per reimpostare la password. Se SSPR è abilitato per il tenant, è possibile reimpostare la password seguendo il collegamento "password dimenticata".



## <a name="february-2020"></a>Febbraio 2020

### <a name="empty-fragments-will-be-appended-to-every-http-redirect-from-the-login-endpoint"></a>I frammenti vuoti verranno aggiunti a ogni Reindirizzamento HTTP dall'endpoint di accesso.

**Data di validità**: 8 febbraio 2020

**Endpoint interessati**: sia la versione 1.0 che la versione 2.0

Con **effetti sul protocollo**: flussi OAuth e OIDC che usano response_type = query. questa operazione riguarda il flusso del codice di [autorizzazione](v2-oauth2-auth-code-flow.md) in alcuni casi e il [flusso implicito](v2-oauth2-implicit-grant-flow.md).

Quando viene inviata una risposta di autenticazione da login.microsoftonline.com a un'applicazione tramite Reindirizzamento HTTP, il servizio aggiunge un frammento vuoto all'URL di risposta.  Ciò impedisce a una classe di attacchi di reindirizzamento assicurando che il browser cancelli tutti i frammenti esistenti nella richiesta di autenticazione.  Nessuna app deve avere una dipendenza da questo comportamento.


## <a name="august-2019"></a>Agosto 2019

### <a name="post-form-semantics-will-be-enforced-more-strictly---spaces-and-quotes-will-be-ignored"></a>La semantica POST-form verrà applicata in modo più rigoroso e le virgolette verranno ignorate

**Data di validità**: 2 settembre 2019

**Endpoint interessati**: sia la versione 1.0 che la versione 2.0

**Con effetti sul protocollo**: viene usato Anywhere post ([credenziali client](./v2-oauth2-client-creds-grant-flow.md), [riscatto del codice di autorizzazione](./v2-oauth2-auth-code-flow.md), [ROPC](./v2-oauth-ropc.md), [OBO](./v2-oauth2-on-behalf-of-flow.md)e [riscatto del token di aggiornamento](./v2-oauth2-auth-code-flow.md#refresh-the-access-token))

A partire dalla settimana 9/2, le richieste di autenticazione che utilizzano il metodo POST verranno convalidate utilizzando standard HTTP più restrittivi.  In particolare, gli spazi e le virgolette doppie (") non verranno più rimossi dai valori del modulo della richiesta. Queste modifiche non devono interrompere i client esistenti e garantire che le richieste inviate a Azure AD siano gestite in modo affidabile ogni volta. In futuro (vedere sopra) si prevede di rifiutare anche i parametri duplicati e ignorare il BOM all'interno delle richieste.

Esempio:

Attualmente, `?e=    "f"&g=h` viene analizzato in modo identico `?e=f&g=h` `e`  ==  `f` .  Con questa modifica, verrà analizzato in modo da non `e`  ==  `    "f"` essere un argomento valido e la richiesta avrà ora esito negativo.


## <a name="july-2019"></a>Luglio 2019

### <a name="app-only-tokens-for-single-tenant-applications-are-only-issued-if-the-client-app-exists-in-the-resource-tenant"></a>I token solo app per le applicazioni a tenant singolo vengono emessi solo se l'app client esiste nel tenant delle risorse

**Data di validità**: 26 luglio 2019

Sono stati **interessati endpoint**: [v 1.0](../azuread-dev/v1-oauth2-client-creds-grant-flow.md) e [v 2.0](./v2-oauth2-client-creds-grant-flow.md)

Con **effetti sul protocollo**: [credenziali client (token solo app)](../azuread-dev/v1-oauth2-client-creds-grant-flow.md)

Una modifica alla sicurezza è passata in diretta il 26 luglio che modifica il modo in cui vengono emessi i token solo app (tramite la concessione delle credenziali client). In precedenza, le applicazioni potevano ottenere i token per chiamare qualsiasi altra app, indipendentemente dalla presenza nel tenant o dai ruoli consentiti per l'applicazione.  Questo comportamento è stato aggiornato in modo che per le risorse (talvolta chiamate API Web) sia impostato come single-tenant (impostazione predefinita), l'applicazione client deve esistere all'interno del tenant delle risorse.  Si noti che il consenso esistente tra il client e l'API non è ancora necessario e le app devono ancora eseguire i propri controlli di autorizzazione per assicurarsi che `roles` sia presente un'attestazione e che contenga il valore previsto per l'API.

Il messaggio di errore per questo scenario attualmente indica:

`The service principal named <appName> was not found in the tenant named <tenant_name>. This can happen if the application has not been installed by the administrator of the tenant.`

Per risolvere questo problema, usare l'esperienza di consenso dell'amministratore per creare l'entità servizio dell'applicazione client nel tenant oppure crearla manualmente.  Questo requisito garantisce che il tenant abbia concesso all'applicazione l'autorizzazione per operare nel tenant.

#### <a name="example-request"></a>Richiesta di esempio

`https://login.microsoftonline.com/contoso.com/oauth2/authorize?resource=https://gateway.contoso.com/api&response_type=token&client_id=14c88eee-b3e2-4bb0-9233-f5e3053b3a28&...` In questo esempio, il tenant di risorse (Authority) è contoso.com, l'app per le risorse è un'app a tenant singolo chiamata `gateway.contoso.com/api` per il tenant Contoso e l'app client è `14c88eee-b3e2-4bb0-9233-f5e3053b3a28` .  Se l'app client dispone di un'entità servizio all'interno di Contoso.com, questa richiesta può continuare.  In caso contrario, la richiesta avrà esito negativo con l'errore precedente.

Se l'app del gateway contoso era un'applicazione multi-tenant, tuttavia, la richiesta continuerà indipendentemente dall'app client con un'entità servizio all'interno di Contoso.com.

### <a name="redirect-uris-can-now-contain-query-string-parameters"></a>Gli URI di reindirizzamento possono ora contenere parametri della stringa di query

**Data di validità**: 22 luglio 2019

**Endpoint interessati**: sia la versione 1.0 che la versione 2.0

**Influisce sul protocollo**: tutti i flussi

Per [RFC 6749](https://tools.ietf.org/html/rfc6749#section-3.1.2), Azure ad applicazioni possono ora registrare e usare gli URI di reindirizzamento (risposta) con parametri di query statici (ad esempio `https://contoso.com/oauth2?idp=microsoft` ) per le richieste OAuth 2,0.  Gli URI di reindirizzamento dinamici sono ancora proibiti perché rappresentano un rischio per la sicurezza e non possono essere usati per mantenere le informazioni sullo stato attraverso una richiesta di autenticazione. per questo, usare il `state` parametro.

Il parametro della query statica è soggetto alla corrispondenza di stringa per gli URI di reindirizzamento come qualsiasi altra parte dell'URI di reindirizzamento: se nessuna stringa è registrata che corrisponde alla redirect_uri decodificata dall'URI, la richiesta verrà rifiutata.  Se l'URI si trova nella registrazione dell'app, l'intera stringa verrà usata per reindirizzare l'utente, incluso il parametro di query statica.

Si noti che al momento (fine del luglio 2019), la registrazione dell'app UX in portale di Azure ancora blocca i parametri della query.  Tuttavia, è possibile modificare manualmente il manifesto dell'applicazione per aggiungere parametri di query e testarlo nell'app.


## <a name="march-2019"></a>Marzo 2019

### <a name="looping-clients-will-be-interrupted"></a>I client di ciclo verranno interrotti

**Data di validità**: 25 marzo 2019

**Endpoint interessati**: sia la versione 1.0 che la versione 2.0

**Influisce sul protocollo**: tutti i flussi

Le applicazioni client possono talvolta comportarsi in maniera non concreta, emettendo centinaia della stessa richiesta di accesso in un breve periodo di tempo.  Queste richieste possono avere esito positivo o negativo, ma contribuiscono all'esperienza utente e ai carichi di lavoro aumentati per l'IDP, aumentando la latenza per tutti gli utenti e riducendo la disponibilità dell'IDP.  Queste applicazioni funzionano al di fuori dei limiti di utilizzo normale e devono essere aggiornate per comportarsi correttamente.

Ai client che inviano richieste duplicate più volte verrà inviato un `invalid_grant` errore: `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request` .

Per evitare questo errore, la maggior parte dei client non dovrà modificare il comportamento.  Questo errore verrà influenzato solo dai client non configurati correttamente (quelli senza memorizzazione nella cache dei token o che presentano già i cicli di richiesta).  I client vengono rilevati in base alle singole istanze in locale (tramite cookie) sui fattori seguenti:

* Hint utente, se presente

* Ambiti o risorse richieste

* ID client

* URI di reindirizzamento

* Tipo di risposta e modalità

Le app che eseguono più richieste (15 +) in un breve periodo di tempo (5 minuti) riceveranno un `invalid_grant` errore che indica che sono in ciclo.  I token richiesti hanno una durata sufficientemente lunga (almeno 10 minuti, 60 minuti per impostazione predefinita), quindi le richieste ripetute per questo periodo di tempo non sono necessarie.

Tutte le app devono essere gestite `invalid_grant` visualizzando un prompt interattivo anziché richiedere un token in modo invisibile all'utente.  Per evitare questo errore, è necessario che i client garantiscano la corretta memorizzazione nella cache dei token ricevuti.


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

**Protocolli interessati**: flusso implicito e [flusso per conto di](v2-oauth2-on-behalf-of-flow.md)

Dopo il 1° maggio 2018, i token ID non possono essere usati come asserzione in un flusso OBO per le nuove applicazioni. È invece necessario usare token di accesso per proteggere le API, anche tra un client e il livello intermedio della stessa applicazione. Le app registrate prima del 1° maggio 2018 continueranno a funzionare e a poter scambiare token ID per un token di accesso, ma questa non è una procedura consigliata.

Per ovviare a questa modifica, è possibile eseguire le operazioni seguenti:

1. Creare un'API Web per l'applicazione, con uno o più ambiti. Questo punto di ingresso esplicito permetterà di ottenere il controllo e la sicurezza con granularità maggiore.
1. Nel manifesto dell'app, nel [portale di Azure](https://portal.azure.com) o nel [portale di registrazione delle app](https://apps.dev.microsoft.com) assicurarsi che all'app sia consentito rilasciare token di accesso usando il flusso implicito. Questa funzionalità è controllata attraverso la chiave `oauth2AllowImplicitFlow`.
1. Quando l'applicazione client richiede un id_token via `response_type=id_token` , richiedere anche un token di accesso ( `response_type=token` ) per l'API Web creata in precedenza. Di conseguenza, quando si usa l'endpoint 2.0, il parametro `scope` dovrebbe essere simile a `api://GUID/SCOPE`. Nell'endpoint v1.0 il parametro `resource` deve essere l'URI dell'app dell'API Web.
1. Passare questo token di accesso al livello intermedio al posto del token ID.
