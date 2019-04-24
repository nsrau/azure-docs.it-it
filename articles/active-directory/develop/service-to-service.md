---
title: App da servizio a servizio in Azure Active Directory
description: Descrive che cosa sono le applicazioni da servizio a servizio e presenta le informazioni di base su flusso del protocollo, registrazione e scadenza del token per questo tipo di app.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: saeeda, jmprieur, andret
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: e0ced89ce97d5f22270d9968fdeb0ddb3fad1e4e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60251987"
---
# <a name="service-to-service-apps"></a>App da servizio a servizio

Le applicazioni da servizio a servizio possono essere un'applicazione daemon o server che deve ottenere risorse da un'API Web. A questa sezione sono applicabili due sottoscenari:

- Un'applicazione daemon che deve chiamare un'API Web, basata sul tipo di concessione di credenziali client OAuth 2.0

    In questo scenario è importante comprendere alcuni aspetti. Prima di tutto, l'interazione utente non è possibile con un'applicazione daemon, che richiede che l'applicazione abbia una propria identità. Un esempio di applicazione daemon è un processo batch oppure un servizio del sistema operativo in esecuzione in background. Questo tipo di applicazione richiede un token di accesso usando la relativa identità applicazione e presentando l'ID applicazione, le credenziali (password o certificato) e l'URI ID dell'applicazione ad Azure AD. Dopo l'autenticazione, il daemon riceve un token di accesso da Azure AD, che viene quindi usato per chiamare l'API Web.

- Un'applicazione server (ad esempio un'API Web) che deve chiamare un'API Web, basata sulla bozza di specifica OAuth 2.0 On-Behalf-Of

    In questo scenario si supponga che un utente abbia eseguito l'autenticazione in un'applicazione nativa e che questa abbia l'esigenza di chiamare un'API Web. Azure AD rilascia un token di accesso JWT per chiamare l'API Web. Se l'API Web deve chiamare un'altra API Web a valle, può usare il flusso "on-behalf-of" per delegare l'identità dell'utente ed eseguire l'autenticazione nell'API Web di secondo livello.

## <a name="diagram"></a>Diagramma

![Diagramma Da daemon o applicazione server ad API Web](./media/authentication-scenarios/daemon_server_app_to_web_api.png)

## <a name="dprotocol-flow"></a>Flusso DProtocol

### <a name="application-identity-with-oauth-20-client-credentials-grant"></a>Identità applicazione con concessione delle credenziali client OAuth 2.0

1. Per prima cosa, l'applicazione server deve eseguire l'autenticazione in Azure AD senza intervento dell'utente, ad esempio senza finestra di dialogo di accesso interattiva. Esegue una richiesta all'endpoint token di Azure AD, fornendo credenziali, ID applicazione e URI ID applicazione.
1. Azure AD autentica l'applicazione e restituisce un token di accesso JWT usato per chiamare l'API Web.
1. Su HTTPS l'applicazione Web usa il token di accesso JWT restituito per aggiungere la stringa JWT con una designazione "Bearer" nell'intestazione dell'autorizzazione della richiesta all'API Web. L'API Web convalida quindi il token JWT e, se la convalida riesce, restituisce la risorsa desiderata.

### <a name="delegated-user-identity-with-oauth-20-on-behalf-of-draft-specification"></a>Identità utente delegato con la bozza di specifica OAuth 2.0 On-Behalf-Of

Il flusso descritto di seguito presuppone che un utente abbia eseguito l'autenticazione in un'altra applicazione (ad esempio un'applicazione nativa) e che la relativa identità utente sia stata usata per acquisire un token di accesso all'API Web di primo livello.

1. L'applicazione nativa invia il token di accesso all'API Web di primo livello.
1. L'API Web di primo livello invia una richiesta all'endpoint token di Azure AD, inviando l'ID applicazione e le credenziali, oltre al token di accesso dell'utente. Inoltre, la richiesta viene inviata con un parametro on_behalf_of che indica che l'API Web richiede nuovi token per chiamare un'API Web a valle per conto dell'utente originale.
1. Azure AD verifica che l'API Web di primo livello sia autorizzata ad accedere all'API Web di secondo livello e convalidi la richiesta, restituendo un token di accesso JWT e un token di aggiornamento JWT all'API Web di primo livello.
1. Su HTTPS, l'API Web di primo livello chiama l'API Web di secondo livello aggiungendo la stringa del token all'intestazione dell'autorizzazione nella richiesta. L'API Web di primo livello può continuare a chiamare l'API Web di secondo livello finché il token di accesso e i token di aggiornamento sono validi.

## <a name="code-samples"></a>Esempi di codice

Vedere gli esempi di codice per gli scenari Da applicazione daemon o server ad API Web. Consultare spesso questa pagina perché vengono aggiunti regolarmente nuovi esempi. [Applicazione server o daemon ad API Web](sample-v1-code.md#daemon-applications-accessing-web-apis-with-the-applications-identity)

## <a name="app-registration"></a>Registrazione delle app

* Tenant singolo: in entrambi i casi (identità di applicazione e identità utente delegato), l'applicazione daemon o server deve essere registrata nella stessa directory in Azure AD. L'API Web può essere configurata per esporre un set di autorizzazioni, che vengono usate per limitare l'accesso del daemon o del server alle relative risorse. Se viene usato il tipo di identità utente delegato, l'applicazione server deve selezionare le autorizzazioni desiderate dal menu a discesa "Autorizzazioni per altre applicazioni" nel portale di Azure. Questo passaggio non è necessario se viene usato il tipo di identità applicazione.
* Multi-tenant: per prima cosa, l'applicazione daemon o server viene configurata per indicare le autorizzazioni necessarie per il funzionamento. Questo elenco di autorizzazioni richieste viene visualizzato in una finestra di dialogo quando un utente o amministratore nella directory di destinazione concede il consenso all'applicazione, rendendola disponibile per la propria organizzazione. Alcune applicazioni richiedono solo autorizzazioni a livello utente, che possono essere concesse da qualsiasi utente dell'organizzazione. Altre applicazioni richiedono autorizzazioni a livello amministratore, che non possono essere concesse dagli utenti dell'organizzazione. Solo un amministratore di directory può concedere il consenso alle applicazioni che richiedono questo livello di autorizzazione. Quando l'utente o l'amministratore acconsente, entrambe le API Web vengono registrate nella directory.

## <a name="token-expiration"></a>Scadenza del token

Quando la prima applicazione usa il proprio codice di autorizzazione per ottenere un token di accesso JWT, riceve anche un token di aggiornamento JWT. Quando il token di accesso scade, è possibile usare il token di aggiornamento per autenticare di nuovo l'utente senza richiedere l'immissione delle credenziali. Questo token di aggiornamento viene quindi usato per autenticare l'utente, con conseguente emissione di un nuovo token di accesso e un nuovo token di aggiornamento.

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni su altri [tipi di applicazione e scenari](app-types.md)
- [Nozioni di base sull'autenticazione](authentication-scenarios.md) in Azure AD
