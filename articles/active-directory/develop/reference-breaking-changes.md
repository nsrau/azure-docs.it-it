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
ms.topic: article
ms.date: 10/02/2018
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ea3ec9024e4ea6a254fb6fe80f93886dc31a0ff
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65545796"
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

Nessun aggiornamento pianificato in questo momento. 

## <a name="march-2019"></a>Marzo 2019

### <a name="looping-clients-will-be-interrupted"></a>I client di ciclo verrà interrotto

**Data di validità**: 25 marzo 2019

**Endpoint interessati**: sia la versione 1.0 che la versione 2.0

**Protocollo interessato**: Tutti i flussi

Le applicazioni client possono talvolta causino il comportamento errato, il rilascio di centinaia della stessa richiesta di accesso in un breve periodo di tempo.  Queste richieste potrebbero o non sia riuscite, ma tutti contribuiscono all'esperienza utente misera e cogliere nuove opportunità di carichi di lavoro per il provider di identità, aumentare la latenza per tutti gli utenti e ridurre la disponibilità del provider di identità.  Queste applicazioni operano esterno ai limiti di utilizzo normale e devono essere aggiornate per funzionare correttamente.  

I client che inviano richieste di duplicazione più volte in cui verranno inviati un `invalid_grant` errori: `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request`. 

La maggior parte dei client non sarà necessario modificare il comportamento per evitare questo errore.  Solo i client non configurato correttamente (quelli senza la memorizzazione nella cache di token o quelle che presentano già cicli dei messaggi di richiesta) saranno interessati da questo errore.  I client vengono registrati in ogni istanza in locale (tramite cookie) dai fattori seguenti:

* Suggerimento di utente, se presente

* Risorsa richiesta o agli ambiti

* ID client

* URI di reindirizzamento

* La modalità e il tipo di risposta

Le app rendendo più richieste (15 +) in un breve periodo di tempo (5 minuti) verranno visualizzato un `invalid_grant` errore che indica che essi viene eseguito alcun ciclo.  I token richiesti sufficientemente lunga durate (minimo di 10 minuti, 60 minuti per impostazione predefinita), quindi ripetere il richieste in questo periodo di tempo non sono necessari.  

Tutte le app devono gestire `invalid_grant` che mostra un prompt interattivo, anziché in modo invisibile richiedendo un token.  Per evitare questo errore, i client necessario accertarsi che in modo corretto la memorizzazione nella cache i token ricevuti.


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
