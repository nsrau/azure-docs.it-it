---
title: Informazioni di riferimento sulle modifiche di rilievo di Azure Active Directory | Microsoft Docs
description: Modifiche apportate ai protocolli di Azure AD che possono influire sull'applicazione.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 68517c83-1279-4cc7-a7c1-c7ccc3dbe146
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 14217d03cdb56c5c641ab8f8c3fc0748e8e815a2
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46987769"
---
# <a name="whats-new-for-authentication"></a>Novità per l'autenticazione 

|
>Per ricevere notifiche quando questa pagina viene aggiornata ed è consigliabile visitarla di nuovo, è possibile aggiungere questo [URL](https://docs.microsoft.com/api/search/rss?search=%22whats%20new%20for%20authentication%22&locale=en-us) al lettore di feed RSS.

Il sistema di autenticazione modifica e aggiunge funzionalità regolarmente per migliorare la sicurezza e la conformità agli standard. Per stare al passo con gli sviluppi più recenti, questo articolo fornisce informazioni sugli argomenti seguenti:

- Funzionalità più recenti
- Problemi noti
- Modifiche al protocollo
- Funzionalità deprecate

> [!TIP] 
> Poiché questa pagina viene aggiornata regolarmente, è consigliabile consultarla spesso. Se non specificato diversamente, queste modifiche vengono applicate solo alle applicazioni appena registrate.  

## <a name="upcoming-changes"></a>Modifiche imminenti

### <a name="authorization-codes-can-no-longer-be-reused"></a>I codici di autorizzazione non possono più essere riutilizzati

**Data di validità**: 10 ottobre 2018 **Endpoint interessati**: v1.0 e v2.0 **Protocollo interessato**: [flusso del codice](v2-oauth2-auth-code-flow.md)

A partire dal 10 ottobre 2018, Azure AD non accetterà più i codici di autenticazione usati in precedenza per le nuove app. Qualsiasi app creata prima del 10 ottobre 2018 sarà comunque in grado di riusare i codici di autenticazione. Questa modifica di sicurezza consente di allineare Azure AD con la specifica OAuth e verrà applicata a entrambi gli endpoint v1 e v2.

Se l'app usa nuovamente i codici di autorizzazione per ottenere token per più risorse, è consigliabile usare il codice per ottenere un token di aggiornamento che consenta di acquisire token aggiuntivi per altre risorse. I codici di autorizzazione possono essere usati solo una volta, mentre quelli di aggiornamento possono essere usati più volte su più risorse. Qualsiasi nuova app che tenti di usare di nuovo un codice di autenticazione durante il flusso del codice OAuth genererà un errore invalid_grant.

Per altre informazioni sui token di aggiornamento, vedere [Aggiornamento dei token di accesso](v1-protocols-oauth-code.md#refreshing-the-access-tokens).

## <a name="may-2018"></a>Maggio 2018

### <a name="id-tokens-cannot-be-used-for-the-obo-flow"></a>I token ID non possono essere usati per un flusso OBO

**Data**: 1° maggio 2018 **Endpoint interessati**: v1.0 e v2.0 **Protocolli interessati**: flusso implicito e [flusso OBO](v1-oauth2-on-behalf-of-flow.md)

Dopo il 1° maggio 2018, i token ID non possono essere usati come asserzione in un flusso OBO per le nuove applicazioni.  È invece necessario usare token di accesso per proteggere le API, anche tra un client e il livello intermedio della stessa applicazione.  Le app registrate prima del 1° maggio 2018 continueranno a funzionare e a essere in grado di scambiare token ID per un token di accesso, ma questa non è una procedura consigliata.

Per ovviare a questa modifica, è possibile eseguire le operazioni seguenti:

1. Creare un'API Web per l'applicazione di livello intermedio, con uno o più ambiti.  In questo modo, sarà possibile ottenere controllo e sicurezza con granularità maggiore.
1. Nel manifesto dell'app, nel [portale di Azure](https://portal.azure.com) o nel [portale di registrazione delle app](https://apps.dev.microsoft.com) assicurarsi che all'app sia consentito rilasciare token di accesso tramite il flusso implicito. Questa funzionalità è controllata tramite la chiave `oauth2AllowImplicitFlow`.
1. Quando l'applicazione client richiede un token ID tramite `response_type=id_token`, richiede anche un token di accesso (`response_type=token`) per l'API Web creata in precedenza.  Di conseguenza, quando si usa l'endpoint 2.0, il parametro `scope` dovrebbe essere simile a `api://GUID/SCOPE`.  Nell'endpoint v1.0 il parametro `resource` deve essere l'URI dell'app dell'API Web.
1. Passare questo token di accesso al livello intermedio al posto del token ID.  