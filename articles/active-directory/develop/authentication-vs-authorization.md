---
title: Confronto tra autenticazione e autorizzazione | Azure
titleSuffix: Microsoft identity platform
description: Informazioni sulle nozioni di base di autenticazione e autorizzazione in Microsoft Identity Platform (2.0).
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 095bc5594cf9b6eaf0df7929ea83c25a401793e2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86252114"
---
# <a name="authentication-vs-authorization"></a>Confronto tra autenticazione e autorizzazione

Questo articolo descrive l'autenticazione e l'autorizzazione e illustra brevemente in che modo è possibile usare Microsoft Identity Platform per autenticare e autorizzare gli utenti nelle proprie app Web, API Web o app che chiamano API Web protette. Se si nota un termine che non si conosce, consultare il [glossario](developer-glossary.md) o i [video di Microsoft Identity Platform](identity-videos.md) che trattano i concetti di base.

## <a name="authentication"></a>Authentication

L'**autenticazione** è un processo che consente di verificare che l'utente è chi dice di essere. In lingua inglese, il termine autenticazione viene talvolta abbreviato in AuthN. Per gestire l'autenticazione, Microsoft Identity Platform implementa il protocollo [OpenID Connect](https://openid.net/connect/).

## <a name="authorization"></a>Autorizzazione

L'**autorizzazione** è la concessione del permesso di eseguire determinate operazioni a un'entità autenticata. Indica a quali dati può accedere e cosa può fare con tali dati. In lingua inglese, il termine autorizzazione viene talvolta abbreviato in AuthZ. Microsoft Identity Platform implementa il protocollo [OAuth 2.0](https://oauth.net/2/) per gestire le autorizzazioni.

## <a name="authentication-and-authorization-using-microsoft-identity-platform"></a>Autenticazione e autorizzazione con Microsoft Identity Platform

Invece di creare app che gestiscono le proprie informazioni relative a nome utente e password, il che comporta un carico amministrativo quando è necessario aggiungere o rimuovere utenti in più app, le app possono delegare tale responsabilità a un provider di identità centralizzato.

Azure Active Directory (Azure AD) è un provider di identità centralizzato su cloud. Scegliere di delegare autenticazione e autorizzazione al provider di identità consente di usare criteri di accesso condizionale in base alle quali l'utente deve trovarsi in una posizione specifica, di usare l'[autenticazione a più fattori](../authentication/concept-mfa-howitworks.md) (a volte chiamata autenticazione a due fattori o 2FA) e di consentire agli utenti di accedere una sola volta e poi sempre automaticamente alle app Web che condividono la stessa directory centralizzata. Questa funzionalità si chiama **Single Sign On (SSO)** .

Microsoft Identity Platform semplifica l'autorizzazione e l'autenticazione per gli sviluppatori di applicazioni offrendo le identità come servizio con il supporto per protocolli standard del settore come OAuth 2.0 e OpenID Connect, nonché librerie open source per diverse piattaforme che consentono di iniziare rapidamente a creare codice. Consente agli sviluppatori di creare applicazioni che supportano l'accesso per tutte le identità Microsoft e il recupero di token per chiamare [Microsoft Graph](https://developer.microsoft.com/graph/), altre API Microsoft o API create dagli sviluppatori.

Di seguito è riportato un breve confronto dei diversi protocolli usati da Microsoft Identity Platform:

* **Confronto tra OAuth e OpenID Connect**: OAuth viene usato per l'autorizzazione, mentre OpenID Connect (OIDC) viene usato per l'autenticazione. OpenID Connect si basa su OAuth 2.0, quindi la terminologia e il flusso di entrambi sono simili. È anche possibile autenticare un utente (usando OpenID Connect) e ottenere l'autorizzazione per accedere a una risorsa protetta di proprietà dell'utente (usando OAuth 2.0) in un'unica richiesta. Per altre informazioni vedere [Protocolli OAuth 2.0 e OpenID Connect](active-directory-v2-protocols.md) e [Protocollo OpenID Connect](v2-protocols-oidc.md).
* **Confronto tra OAuth e SAML**: OAuth viene usato per l'autorizzazione, mentre SAML viene usato per l'autenticazione. Per altre informazioni sulla modalità di uso combinato dei due protocolli per autenticare un utente (con SAML) e ottenere l'autorizzazione per accedere a una risorsa protetta (con OAuth 2.0), vedere [Microsoft Identity Platform e il flusso di asserzione SAML Bearer di OAuth 2.0](v2-saml-bearer-assertion.md).
* **Confronto tra OpenID Connect e SAML**: Sia OpenID Connect che SAML vengono usati per autenticare gli utenti e per abilitare il Single Sign-On. L'autenticazione SAML è solitamente usata con provider di identità come Active Directory Federation Services (ADFS), associato a Azure AD ed è pertanto usata spesso nelle applicazioni aziendali. OpenID Connect è solitamente usato per le app che si trovano esclusivamente sul cloud, ad esempio app per dispositivi mobili, siti Web e API Web.

## <a name="next-steps"></a>Passaggi successivi

Per altri argomenti relativi alle informazioni di base su autenticazione e autorizzazione:

* Vedere [Token di sicurezza](security-tokens.md) per informazioni su come accedere e aggiornare i token e su come vengono usati per autorizzazione e autenticazione.
* Vedere [Modello applicativo](application-model.md) per informazioni sul processo di registrazione dell'applicazione in modo che possa integrarsi con Microsoft Identity Platform.
* Per informazioni sul flusso di accesso alle app Web, desktop e per dispositivi mobili con Microsoft Identity Platform, vedere [Flusso di accesso alle app](app-sign-in-flow.md).

* Per altre informazioni sui protocolli implementati da Microsoft Identity Platform, vedere [Protocolli OAuth 2.0 e OpenID Connect su Microsoft Identity Platform](active-directory-v2-protocols.md).
* Per altre informazioni sul modo in cui Microsoft Identity Platform supporta il Single Sign-On, vedere [Protocollo SAML per Single Sign-On](single-sign-on-saml-protocol.md).
* Per altre informazioni sui diversi modi in cui è possibile implementare il Single Sign-On nelle proprie app, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
