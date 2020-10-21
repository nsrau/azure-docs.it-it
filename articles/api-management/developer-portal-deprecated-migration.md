---
title: Eseguire la migrazione al nuovo portale per sviluppatori dal portale per sviluppatori Legacy
titleSuffix: Azure API Management
description: Informazioni su come eseguire la migrazione dal portale per sviluppatori legacy al nuovo portale per sviluppatori in gestione API.
services: api-management
documentationcenter: API Management
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/15/2020
ms.author: apimpm
ms.openlocfilehash: f5105c685de4b3ccdffe69eec8ee8eeb32976c1f
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92326023"
---
# <a name="migrate-to-the-new-developer-portal"></a>Eseguire la migrazione al nuovo portale per sviluppatori

Questo articolo descrive i passaggi necessari per eseguire la migrazione dal portale legacy deprecato al nuovo portale per sviluppatori in gestione API.

> [!IMPORTANT]
> Il portale per sviluppatori Legacy è ora deprecato e riceverà solo gli aggiornamenti della sicurezza. È possibile continuare a usarlo, come al solito, fino al ritiro entro il 2023 ottobre, quando verrà rimosso da tutti i servizi di gestione API.

![Portale per sviluppatori di Gestione API](media/api-management-howto-developer-portal/cover.png)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="improvements-in-new-developer-portal"></a>Miglioramenti del nuovo portale per sviluppatori

Il nuovo portale per sviluppatori risolve molte limitazioni del portale deprecato. Include un [editor di trascinamento della selezione per la modifica del contenuto](api-management-howto-developer-portal-customize.md) e un pannello dedicato che consente alle finestre di progettazione di applicare uno stile al sito Web. Le pagine, le personalizzazioni e la configurazione vengono salvate come Azure Resource Manager risorse nel servizio gestione API, che consente di [automatizzare le distribuzioni del portale](api-management-howto-developer-portal.md#automate). Infine, la codebase del portale è open source ed è quindi [possibile estenderla con funzionalità personalizzate](api-management-howto-developer-portal.md#managed-vs-self-hosted).

## <a name="how-to-migrate-to-new-developer-portal"></a>Come eseguire la migrazione al nuovo portale per sviluppatori

Il nuovo portale per sviluppatori non è compatibile con il portale deprecato e la migrazione automatica non è possibile. È necessario ricreare manualmente il contenuto (pagine, testo, file multimediali) e personalizzare l'aspetto del nuovo portale. I passaggi precisi variano in base alle personalizzazioni e alla complessità del portale. Per istruzioni, vedere [l'esercitazione sul portale per sviluppatori](api-management-howto-developer-portal-customize.md) . La configurazione rimanente, ad esempio l'elenco di API, prodotti, utenti e provider di identità, viene automaticamente condivisa tra entrambi i portali.

> [!IMPORTANT]
> Se è stato avviato il nuovo portale per sviluppatori, ma non sono state apportate modifiche, [reimpostare il contenuto predefinito](api-management-howto-developer-portal.md#preview-to-ga) per aggiornarlo alla versione più recente.

Quando si esegue la migrazione dal portale deprecato, tenere presenti le seguenti modifiche:

- Se il portale per sviluppatori viene esposto tramite un dominio personalizzato, [assegnare un dominio](configure-custom-domain.md) al nuovo portale per sviluppatori. Usare l'opzione del **portale per sviluppatori** nell'elenco a discesa nella portale di Azure.
- [Applicare un criterio CORS](api-management-howto-developer-portal.md#cors) sulle API per abilitare la console di test interattiva.
- Se si inserisce un CSS personalizzato per applicare uno stile al portale, è necessario [replicare lo stile usando il pannello di progettazione incorporato](api-management-howto-developer-portal-customize.md). L'inserimento CSS non è consentito nel nuovo portale.
- È possibile inserire JavaScript personalizzato solo nella [versione self-hosted del nuovo portale](api-management-howto-developer-portal.md#managed-vs-self-hosted).
- Se gestione API si trova in una rete virtuale ed è esposta a Internet tramite il gateway applicazione, [fare riferimento a questo articolo della documentazione](api-management-howto-integrate-internal-vnet-appgateway.md) per i passaggi di configurazione precisi. È necessario:

    - Abilitare la connettività all'endpoint di gestione di gestione API.
    - Abilitare la connettività al nuovo endpoint del portale.
    - Disabilitare le regole del Web Application Firewall selezionate.

- Se sono stati modificati i modelli di notifica tramite posta elettronica predefiniti per includere un URL del portale deprecato definito in modo esplicito, modificarli in modo da usare il parametro URL del portale o puntare all'URL del nuovo portale. Se i modelli usano invece il parametro URL del portale predefinito, non sono necessarie modifiche.
- *Problemi* e *applicazioni* non sono supportati nel nuovo portale per sviluppatori.
- L'integrazione diretta con Facebook, Microsoft, Twitter e Google come provider di identità non è supportata nel nuovo portale per sviluppatori. È possibile eseguire l'integrazione con tali provider tramite Azure AD B2C.
- Se si usa la delega, modificare l'URL restituito nelle applicazioni e usare l' [endpoint dell'API per *ottenere il token di accesso condiviso* ](/rest/api/apimanagement/2019-12-01/user/getsharedaccesstoken) anziché l'endpoint *genera URL SSO* .
- Se si usa Azure AD come provider di identità:

    - Modificare l'URL restituito nell'applicazione in modo che punti al nuovo dominio del portale per sviluppatori.
    - Modificare il suffisso dell'URL restituito nell'applicazione da `/signin-aad` a `/signin` .

- Se si usa Azure AD B2C come provider di identità:

    - Modificare l'URL restituito nell'applicazione in modo che punti al nuovo dominio del portale per sviluppatori.
    - Modificare il suffisso dell'URL restituito nell'applicazione da `/signin-aad` a `/signin` .
    - Includere il *nome*, il *Cognome*e l' *ID oggetto dell'utente* nelle attestazioni dell'applicazione.

- Se si usa OAuth 2,0 nella console di test interattiva, modificare l'URL restituito nell'applicazione in modo che punti al nuovo dominio del portale per sviluppatori e modificare il suffisso:

    - Da `/docs/services/[serverName]/console/oauth2/authorizationcode/callback` a `/signin-oauth/code/callback/[serverName]` per il flusso di concessione del codice di autorizzazione.
    - Da `/docs/services/[serverName]/console/oauth2/implicit/callback` a `/signin-oauth/implicit/callback` per il flusso di concessione implicita.
- Se si usa OpenID Connect nella console di test interattiva, modificare l'URL restituito nell'applicazione in modo che punti al nuovo dominio del portale per sviluppatori e modificare il suffisso:

    - Da `/docs/services/[serverName]/console/openidconnect/authorizationcode/callback` a `/signin-oauth/code/callback/[serverName]` per il flusso di concessione del codice di autorizzazione.
    - Da `/docs/services/[serverName]/console/openidconnect/implicit/callback` a `/signin-oauth/implicit/callback` per il flusso di concessione implicita.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sul portale per sviluppatori:

- [Panoramica del portale per sviluppatori di Gestione API](api-management-howto-developer-portal.md)
- [Accedere e personalizzare il portale per sviluppatori](api-management-howto-developer-portal-customize.md)