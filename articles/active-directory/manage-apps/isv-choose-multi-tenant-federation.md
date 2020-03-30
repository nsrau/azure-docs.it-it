---
title: Scegliere il protocollo di federazione corretto per l'applicazione multi-tenantChoose right federation protocol for multi-tenant application
description: Linee guida per i fornitori di software indipendenti sull'integrazione con Azure Active Directory
services: active-directory
author: barbaraselden
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: baselden
ms.reviewer: jeeds
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3b3edbbe037c3874d639476e516b3732b7573d9b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443383"
---
# <a name="choose-the-right-federation-protocol-for-your-multi-tenant-application"></a>Scegliere il protocollo di federazione corretto per l'applicazione multi-tenant

Quando si sviluppa il software come applicazione SaaS (Service), è necessario selezionare il protocollo di federazione più adatto alle proprie esigenze e ai clienti. Questa decisione si basa sulla piattaforma di sviluppo e sul desiderio di integrarsi con i dati disponibili nell'ecosistema Office 365 e Azure AD dei clienti.

Vedere l'elenco completo dei [protocolli disponibili per le integrazioni SSO](what-is-single-sign-on.md) con Azure Active Directory.See the complete list of protocols available for SSO integrations with Azure Active Directory.
La tabella seguente confronta 
* Autenticazione aperta 2.0 (OAuth 2.0)
* Open ID Connect (OIDC)
* SAML (Security Assertion Markup Language)
* Federazione dei servizi Web (WSFed)

| Funzionalità| OAuth / OIDC| SAML / WSFed |
| - |-|-|
| Single Sign-On basato sul Web| √| √ |
| Disconnessione singola basata sul Web| √| √ |
| Single Sign-On basato su dispositivi mobili| √| √* |
| Disconnessione singola basata su dispositivi mobili| √| √* |
| Criteri di accesso condizionale per le applicazioni per dispositivi mobiliConditional Access policies for mobile applications| √| X |
| Esperienza Senza soluzione di continuità dell'autenticazione a più fattori per le applicazioni mobili| √| X |
| Accedere a Microsoft Graph| √| X |

- Possibile, ma Microsoft non fornisce esempi o indicazioni.

## <a name="oauth-20-and-open-id-connect"></a>OAuth 2.0 e Open ID Connect

OAuth 2.0 è un protocollo standard del [settore](https://oauth.net/2/) per l'autorizzazione. OIDC (OpenID Connect) è un livello di autenticazione dell'identità standard del [settore](https://openid.net/connect/) basato sul protocollo OAuth 2.0.

### <a name="benefits"></a>Vantaggi

Microsoft consiglia di utilizzare OIDC/OAuth 2.0 in quanto dispongono dell'autenticazione e dell'autorizzazione incorporate nei protocolli. Con SAML, è inoltre necessario implementare l'autorizzazione.

L'autorizzazione insita in questi protocolli consente all'applicazione di accedere e integrarsi con i dati utente e organizzazione avanzati tramite l'API Microsoft Graph.

L'uso di OAuth 2.0 e OIDC semplifica l'esperienza dell'utente finale dei clienti quando adottano SSO per l'applicazione. È possibile definire facilmente i set di autorizzazioni necessari, che vengono quindi rappresentati automaticamente all'amministratore o all'utente finale.

Inoltre, l'utilizzo di questi protocolli consente ai clienti di utilizzare l'accesso condizionale e i criteri di autenticazione a più fattori per controllare l'accesso alle applicazioni. Microsoft fornisce librerie ed esempi di [codice su più piattaforme tecnologiche](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Samples) per facilitare lo sviluppo.  

### <a name="implementation"></a>Implementazione

Registrare l'applicazione con Microsoft Identity, che è un provider OAuth 2.0. È quindi possibile registrare l'applicazione basata su OAuth 2.0 con qualsiasi altro provider di identità con cui si desidera eseguire l'integrazione. 

Per informazioni su come registrare l'applicazione e implementare questi protocolli per SSO alle app Web, vedere [Autorizzare l'accesso alle applicazioni Web usando OpenID Connect e Azure Active Directory.](../develop/sample-v2-code.md)  Per informazioni su come implementare questi protocolli per SSO nelle app per dispositivi mobili, vedere gli elementi seguenti:For information on how to implement these protocols for SSO in mobile apps, see the following: 

* [Android](../develop/quickstart-v2-android.md)

* [iOS](../develop/quickstart-v2-ios.md)

* [Piattaforma UWP (Universal Windows Platform)](../develop/quickstart-v2-uwp.md)

## <a name="saml-20-and-wsfed"></a>SAML 2.0 e WSFed

Security Assertion Markup Language (SAML) viene in genere utilizzato per le applicazioni Web. Per una panoramica, vedere [Come Azure usa il protocollo SAML.](../develop/active-directory-saml-protocol-reference.md) 

Web Services Federation (WSFed) è [uno standard](https://docs.oasis-open.org/wsfed/federation/v1.2/ws-federation.html) di settore generalmente utilizzato per le applicazioni Web sviluppate utilizzando la piattaforma .Net.

### <a name="benefits"></a>Vantaggi

SAML 2.0 è uno standard maturo e la maggior parte delle piattaforme tecnologiche supporta librerie open source per SAML 2.0. È possibile fornire ai clienti un'interfaccia di amministrazione per configurare SAML SSO. Possono configurare SAML SSO per Microsoft Azure AD e qualsiasi altro provider di identità che supporti SAML 2

### <a name="trade-offs"></a>Compromessi

Quando si utilizzano protocolli SAML 2.0 o WSFed per le applicazioni mobili, alcuni criteri di accesso condizionale, tra cui Multi-Factor Authentication (MFA), avranno un'esperienza degradata. Inoltre, se si desidera accedere a Microsoft Graph, è necessario implementare l'autorizzazione tramite OAuth 2.0 per generare i token necessari. 

### <a name="implementation"></a>Implementazione

Microsoft non fornisce librerie per l'implementazione SAML né consiglia librerie specifiche. Sono disponibili molte librerie open source.

## <a name="sso-and-using-microsoft-graph-rest-api"></a>SSO e utilizzo dell'API di riposo di Microsoft Graph 

Microsoft Graph è l'infrastruttura di dati in tutti i microsoft 365, inclusi Office 365, Windows 10 e Enterprise Mobility and Security, e prodotti aggiuntivi come Dynamics 365. Sono inclusi gli schemi principali delle entità, ad esempio Users, Groups, Calendar, Mail, Files e altro ancora, che aumentano la produttività degli utenti. Microsoft Graph offre tre interfacce per gli sviluppatori un'API basata su REST, la connessione dati di Microsoft Graph e i connettori che consentono agli sviluppatori di aggiungere i propri dati in Microsoft Graph.  

L'uso di uno dei protocolli precedenti per SSO consente all'applicazione di accedere ai dati avanzati disponibili tramite l'API REST di Microsoft Graph. Ciò consente ai clienti di ottenere più valore dal proprio investimento in Microsoft 365. Ad esempio, l'applicazione può chiamare l'API Microsoft Graph per l'integrazione con gli elementi di Microsoft Office 365 dell'istanza e di Surface degli utenti dei clienti all'interno dell'applicazione. 

Se si utilizza Open ID Connect per l'autenticazione, l'esperienza di sviluppo è senza soluzione di continuità perché si utilizzerà OAuth2, la base di Open ID Connect, per acquisire i token può essere utilizzata per richiamare le API di Microsoft Graph. Se l'applicazione utilizza SAML o WSFed, è necessario aggiungere codice aggiuntivo all'interno dell'applicazione per ottenere questi OAuth2 per acquisire i token necessari per richiamare le API di Microsoft Graph. 

## <a name="next-steps"></a>Passaggi successivi

[Abilitare l'accesso SSO per l'applicazione multi-tenant](isv-sso-content.md)

[Creare la documentazione per l'applicazione multi-tenantCreate documentation for your multi-tenant application](isv-create-sso-documentation.md)
