---
title: Autenticazione e autorizzazione | Azure
titleSuffix: Microsoft identity platform
description: Informazioni sulle nozioni di base sull'autenticazione e sull'autorizzazione in Microsoft Identity Platform (v 2.0).
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/28/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 61c0202d12756201d45fe829078d84382e44b54e
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82584298"
---
# <a name="authentication-vs-authorization"></a>Autenticazione e autorizzazione

Questo articolo descrive l'autenticazione e l'autorizzazione e illustra brevemente come usare la piattaforma di identità Microsoft per autenticare e autorizzare gli utenti nelle app Web, nelle API Web o nelle app che chiamano API Web protette. Se viene visualizzato un termine con cui non si ha familiarità, provare il [Glossario](developer-glossary.md) o i [video della piattaforma Microsoft Identity](identity-videos.md) che illustrano i concetti di base.

## <a name="authentication"></a>Authentication

L' **autenticazione** è il processo di dimostrazione dell'utente. In lingua inglese, il termine autenticazione viene talvolta abbreviato in AuthN. Microsoft Identity Platform implementa il protocollo [OpenID Connect](https://openid.net/connect/) per la gestione dell'autenticazione.

## <a name="authorization"></a>Autorizzazione

L' **autorizzazione** è l'atto di concedere a un'entità autenticata l'autorizzazione per eseguire un'operazione. Specifica i dati a cui si è autorizzati ad accedere e le operazioni che è possibile eseguire con tali dati. In lingua inglese, il termine autorizzazione viene talvolta abbreviato in AuthZ. Microsoft Identity Platform implementa il protocollo [OAuth 2,0](https://oauth.net/2/) per la gestione dell'autorizzazione.

## <a name="authentication-and-authorization-using-the-microsoft-identity-platform"></a>Autenticazione e autorizzazione con la piattaforma di identità Microsoft

Anziché creare app che gestiscono le proprie informazioni relative a nome utente e password, il che comporta un carico amministrativo elevato quando è necessario aggiungere o rimuovere utenti in più app, le app possono delegare tale responsabilità a un provider di identità centralizzato.

Azure Active Directory (Azure AD) è un provider di identità centralizzato nel cloud. La delega dell'autenticazione e dell'autorizzazione al servizio IT consente scenari come i criteri di accesso condizionale che richiedono a un utente di trovarsi in una posizione specifica, l'uso dell'autenticazione a più fattori, nonché l'accesso di un utente una sola volta e quindi l'accesso automatico a tutte le app Web che condividono la stessa directory centralizzata. Questa funzionalità è denominata **Single Sign-on (SSO)**.

La piattaforma di identità Microsoft semplifica l'autenticazione e l'autorizzazione per gli sviluppatori di applicazioni fornendo identità come servizio, con il supporto per protocolli standard del settore come OAuth 2,0 e OpenID Connect, nonché librerie open source per piattaforme diverse che consentono di iniziare rapidamente a scrivere codice. Consente agli sviluppatori di creare applicazioni che consentono di accedere a tutte le identità Microsoft, ottenere i token per chiamare [Microsoft Graph](https://developer.microsoft.com/graph/), altre API Microsoft o API create dagli sviluppatori. Per ulteriori informazioni, vedere [Evolution of Microsoft Identity Platform](about-microsoft-identity-platform.md).

## <a name="next-steps"></a>Passaggi successivi

Per altri argomenti relativi alle nozioni di base sull'autenticazione e sull'autorizzazione:

* Vedere [token di sicurezza](security-tokens.md) per informazioni sull'uso di token di accesso, token di aggiornamento e token ID nell'autenticazione e nell'autorizzazione.
* Vedere [modello di applicazione](application-model.md) per informazioni sul processo di registrazione dell'applicazione in modo che possa integrarsi con la piattaforma di identità Microsoft.
* Vedere [flusso di accesso all'app](app-sign-in-flow.md) per informazioni sul flusso di accesso di app Web, desktop e per dispositivi mobili nella piattaforma di identità Microsoft.

Per ulteriori informazioni sui protocolli implementati da Microsoft Identity Platform:

* Per altre informazioni sugli standard OpenID Connect e OAuth 2,0, vedere la pagina relativa ai [protocolli oauth 2,0 e OpenID Connect nella piattaforma di identità Microsoft](active-directory-v2-protocols.md) .
* Vedere [protocollo SAML per l'accesso Single Sign-on](single-sign-on-saml-protocol.md) per ulteriori informazioni sul modo in cui la piattaforma di identità Microsoft supporta Single Sign-on.
