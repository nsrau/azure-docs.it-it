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
ms.date: 07/26/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 747b279b38ec3b73f19194825195f1b3450d4514
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68834866"
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

2019 agosto: Applicare la semantica POST in base alle regole di analisi dell'URL: i parametri duplicati generano un errore, le virgolette tra i parametri non verranno più ignorate e il [BOM](https://www.w3.org/International/questions/qa-byte-order-mark) verrà ignorato.

## <a name="july-2019"></a>2019 luglio

### <a name="app-only-tokens-for-single-tenant-applications-are-only-issued-if-the-client-app-exists-in-the-resource-tenant"></a>I token solo app per le applicazioni a tenant singolo vengono emessi solo se l'app client esiste nel tenant delle risorse

**Data di validità**: 26 luglio 2019

**Endpoint interessati**: [V 1.0](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow) e [v 2.0](https://docs.microsoft.com/en-us/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow)

**Protocollo interessato**: [Credenziali client (token solo app)](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow)

Una modifica alla sicurezza è passata in diretta il 26 luglio che modifica il modo in cui vengono emessi i token solo app (tramite la concessione delle credenziali client). In precedenza, le applicazioni potevano ottenere i token per chiamare qualsiasi altra app, indipendentemente dalla presenza nel tenant o dai ruoli consentiti per l'applicazione.  Questo comportamento è stato aggiornato in modo che per le risorse (talvolta chiamate API Web) sia impostato come single-tenant (impostazione predefinita), l'applicazione client deve esistere all'interno del tenant delle risorse.  Si noti che il consenso esistente tra il client e l'API non è ancora necessario e le app devono ancora eseguire i propri controlli di autorizzazione per assicurarsi `roles` che sia presente un'attestazione e che contenga il valore previsto per l'API.

Il messaggio di errore per questo scenario attualmente indica: 

`The service principal named <appName> was not found in the tenant named <tenant_name>. This can happen if the application has not been installed by the administrator of the tenant.`

Per risolvere questo problema, usare l'esperienza di consenso dell'amministratore per creare l'entità servizio dell'applicazione client nel tenant oppure crearla manualmente.  Questo requisito garantisce che il tenant abbia concesso all'applicazione l'autorizzazione per operare nel tenant.  

#### <a name="example-request"></a>Richiesta di esempio

`https://login.microsoftonline.com/contoso.com/oauth2/authorize?resource=https://gateway.contoso.com/api&response_type=token&client_id=14c88eee-b3e2-4bb0-9233-f5e3053b3a28&...`In questo esempio, il tenant di risorse (Authority) è contoso.com, l'app per le risorse è un'app a `gateway.contoso.com/api` tenant singolo chiamata per il tenant Contoso e l'app `14c88eee-b3e2-4bb0-9233-f5e3053b3a28`client è.  Se l'app client dispone di un'entità servizio all'interno di Contoso.com, questa richiesta può continuare.  In caso contrario, la richiesta avrà esito negativo con l'errore precedente.  

Se l'app del gateway contoso era un'applicazione multi-tenant, tuttavia, la richiesta continuerà indipendentemente dall'app client con un'entità servizio all'interno di Contoso.com.  

### <a name="redirect-uris-can-now-contain-query-string-parameters"></a>Gli URI di reindirizzamento possono ora contenere parametri della stringa di query

**Data di validità**: 22 luglio 2019

**Endpoint interessati**: sia la versione 1.0 che la versione 2.0

**Protocollo interessato**: Tutti i flussi

Per [RFC 6749](https://tools.ietf.org/html/rfc6749#section-3.1.2), Azure ad applicazioni possono ora registrare e usare gli URI di reindirizzamento (risposta) con parametri di query statici https://contoso.com/oauth2?idp=microsoft) , ad esempio per le richieste OAuth 2,0.  Gli URI di reindirizzamento dinamici sono ancora proibiti perché rappresentano un rischio per la sicurezza e non possono essere usati per mantenere le informazioni sullo stato attraverso una richiesta di autenticazione `state` . per questo, usare il parametro.

Il parametro della query statica è soggetto alla corrispondenza di stringa per gli URI di reindirizzamento come qualsiasi altra parte dell'URI di reindirizzamento: se nessuna stringa è registrata che corrisponde al redirect_uri decodificato dall'URI, la richiesta verrà rifiutata.  Se l'URI si trova nella registrazione dell'app, l'intera stringa verrà usata per reindirizzare l'utente, incluso il parametro di query statica. 

Si noti che al momento (fine del luglio 2019), la registrazione dell'app UX in portale di Azure ancora blocca i parametri della query.  Tuttavia, è possibile modificare manualmente il manifesto dell'applicazione per aggiungere parametri di query e testarlo nell'app.  


## <a name="march-2019"></a>2019 marzo

### <a name="looping-clients-will-be-interrupted"></a>I client di ciclo verranno interrotti

**Data di validità**: 25 marzo 2019

**Endpoint interessati**: sia la versione 1.0 che la versione 2.0

**Protocollo interessato**: Tutti i flussi

Le applicazioni client possono talvolta comportarsi in maniera non concreta, emettendo centinaia della stessa richiesta di accesso in un breve periodo di tempo.  Queste richieste possono avere esito positivo o negativo, ma contribuiscono all'esperienza utente e ai carichi di lavoro aumentati per l'IDP, aumentando la latenza per tutti gli utenti e riducendo la disponibilità dell'IDP.  Queste applicazioni funzionano al di fuori dei limiti di utilizzo normale e devono essere aggiornate per comportarsi correttamente.  

Ai client che inviano richieste duplicate più volte verrà `invalid_grant` inviato un `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request`errore:. 

Per evitare questo errore, la maggior parte dei client non dovrà modificare il comportamento.  Questo errore verrà influenzato solo dai client non configurati correttamente (quelli senza memorizzazione nella cache dei token o che presentano già i cicli di richiesta).  I client vengono rilevati in base alle singole istanze in locale (tramite cookie) sui fattori seguenti:

* Hint utente, se presente

* Ambiti o risorse richieste

* ID client

* URI di reindirizzamento

* Tipo di risposta e modalità

Le app che eseguono più richieste (15 +) in un breve periodo di tempo (5 minuti) riceveranno un `invalid_grant` errore che indica che sono in ciclo.  I token richiesti hanno una durata sufficientemente lunga (almeno 10 minuti, 60 minuti per impostazione predefinita), quindi le richieste ripetute per questo periodo di tempo non sono necessarie.  

Tutte le app devono `invalid_grant` essere gestite visualizzando un prompt interattivo anziché richiedere un token in modo invisibile all'utente.  Per evitare questo errore, è necessario che i client garantiscano la corretta memorizzazione nella cache dei token ricevuti.


## <a name="october-2018"></a>Ottobre 2018

### <a name="authorization-codes-can-no-longer-be-reused"></a>I codici di autorizzazione non possono più essere riutilizzati

**Data di validità**: 15 novembre 2018

**Endpoint interessati**: sia la versione 1.0 che la versione 2.0

**Protocollo interessato**: [flusso del codice](v2-oauth2-auth-code-flow.md)

A partire dal 15 novembre 2018, Azure AD non accetterà più i codici di autenticazione usati in precedenza per le app. Questa modifica di sicurezza consente di allineare Azure AD con la specifica OAuth e verrà applicata a entrambi gli endpoint v1 e v2.

Se l'app usa nuovamente i codici di autorizzazione per ottenere token per più risorse, è consigliabile usare il codice per ottenere un token di aggiornamento che consenta di acquisire token aggiuntivi per altre risorse. I codici di autorizzazione possono essere usati solo una volta, mentre quelli di aggiornamento possono essere usati più volte su più risorse. Qualsiasi nuova app che tenti di usare di nuovo un codice di autenticazione durante il flusso del codice OAuth genererà un errore invalid_grant.

Per altre informazioni sui token di aggiornamento, vedere [Aggiornamento dei token di accesso](v1-protocols-oauth-code.md#refreshing-the-access-tokens).  Se si usa ADAL o MSAL, questa operazione viene gestita automaticamente dalla libreria. Sostituire la seconda istanza di 'AcquireTokenByAuthorizationCodeAsync' con 'AcquireTokenSilentAsync'. 

## <a name="may-2018"></a>Maggio 2018

### <a name="id-tokens-cannot-be-used-for-the-obo-flow"></a>I token ID non possono essere usati per un flusso OBO

**Data**: 1 maggio 2018

**Endpoint interessati**: sia la versione 1.0 che la versione 2.0

**Protocolli interessati**: flusso implicito e [flusso OBO](v1-oauth2-on-behalf-of-flow.md)

Dopo il 1° maggio 2018, i token ID non possono essere usati come asserzione in un flusso OBO per le nuove applicazioni. È invece necessario usare token di accesso per proteggere le API, anche tra un client e il livello intermedio della stessa applicazione. Le app registrate prima del 1° maggio 2018 continueranno a funzionare e a poter scambiare token ID per un token di accesso, ma questa non è una procedura consigliata.

Per ovviare a questa modifica, è possibile eseguire le operazioni seguenti:

1. Creare un'API Web per l'applicazione, con uno o più ambiti. Questo punto di ingresso esplicito permetterà di ottenere il controllo e la sicurezza con granularità maggiore.
1. Nel manifesto dell'app, nel [portale di Azure](https://portal.azure.com) o nel [portale di registrazione delle app](https://apps.dev.microsoft.com) assicurarsi che all'app sia consentito rilasciare token di accesso usando il flusso implicito. Questa funzionalità è controllata attraverso la chiave `oauth2AllowImplicitFlow`.
1. Quando l'applicazione client richiede un token ID tramite `response_type=id_token`, richiede anche un token di accesso (`response_type=token`) per l'API Web creata in precedenza. Di conseguenza, quando si usa l'endpoint 2.0, il parametro `scope` dovrebbe essere simile a `api://GUID/SCOPE`. Nell'endpoint v1.0 il parametro `resource` deve essere l'URI dell'app dell'API Web.
1. Passare questo token di accesso al livello intermedio al posto del token ID.  
