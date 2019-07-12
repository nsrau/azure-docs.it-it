---
title: Scegliere il protocollo di federazione corretto per l'applicazione multi-tenant
description: Linee guida per fornitori di software indipendenti per l'integrazione con Azure Active Directory
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
ms.openlocfilehash: 53b315b87200b37cda215a29a65be9babaf54f43
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67795191"
---
# <a name="choose-the-right-federation-protocol-for-your-multi-tenant-application"></a>Scegliere il protocollo di federazione corretto per l'applicazione multi-tenant

Quando si sviluppa l'applicazione software come servizio (SaaS), è necessario selezionare il protocollo di federazione adatta alle esigenze dei tuoi e dei tuoi clienti. Questa decisione si basa sulla piattaforma di sviluppo e come desiderato per l'integrazione con i dati disponibili nell'ecosistema di Office 365 e Azure AD dei clienti.

Vedere l'elenco completo dei [protocolli disponibili per le integrazioni di SSO](what-is-single-sign-on.md) con Azure Active Directory.
La tabella seguente vengono confrontate 
* Autenticazione aperta 2.0 (OAuth 2.0)
* Openid Connect (OIDC)
* SAML (Security Assertion Markup Language)
* Web Services Federation (WSFed)

| Funzionalità| OAuth / OIDC| SAML / WSFed |
| - |-|-|
| Basata sul Web Single sign-on| √| √ |
| Basata sul Web Single Sign-Out| √| √ |
| Basato su dispositivi mobili Single sign-on| √| √* |
| Basato su dispositivi mobili Single Sign-Out| √| √* |
| Criteri di accesso condizionale per applicazioni per dispositivi mobili| √| X |
| Esperienza di autenticazione a più fattori trasparente per le applicazioni per dispositivi mobili| √| X |
| Access Microsoft Graph| √| X |

\* Possibili, ma Microsoft non fornisce gli esempi o indicazioni.

## <a name="oauth-20-and-open-id-connect"></a>OAuth 2.0 e Openid Connect

OAuth 2.0 è un [standard del settore](https://oauth.net/2/) protocollo per l'autorizzazione. OIDC (OpenID Connect) è un' [standard di settore](https://openid.net/connect/) livello autenticazione identità basata su protocollo Oath 2.0.

### <a name="benefits"></a>Vantaggi

Microsoft consiglia di usare OIDC/OAuth 2.0 con l'autenticazione e autorizzazione predefiniti per i protocolli. Con SAML, è inoltre necessario implementare l'autorizzazione.

L'autorizzazione intrinseca in questi protocolli consente all'applicazione di accedere e integrare con utente avanzata e ai dati aziendali tramite l'API Microsoft Graph.

Con OAuth 2.0 e OIDC semplifica l'esperienza degli utenti finali dei tuoi clienti ad adottare l'accesso SSO per l'applicazione. È possibile definire facilmente gli oggetti necessari set di autorizzazioni che vengono quindi automaticamente rappresentati per l'amministratore o utente finale consenso.

Inoltre, l'uso di questi protocolli consente ai clienti di usare l'accesso condizionale e i criteri di autenticazione a più fattori per controllare l'accesso alle applicazioni. Microsoft fornisce librerie e [esempi di codice su più piattaforme di tecnologia](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Samples) per semplificare lo sviluppo.  

### <a name="implementation"></a>Implementazione

Si registra l'applicazione con Microsoft Identity, che rappresenta un provider OAuth 2.0. Quindi è possibile inoltre registrare l'applicazione basata su OAuth 2.0 con qualsiasi altro Provider di identità che si vuole integrare con. 

Per informazioni su come registrare l'applicazione e implementare questi protocolli per l'accesso SSO alle App web, vedere [autorizzare l'accesso ad applicazioni web con OpenID Connect e Azure Active Directory](../develop/sample-v2-code.md).  Per informazioni su come implementare questi protocolli per l'accesso SSO nell'App per dispositivi mobili, vedere gli argomenti seguenti: 

* [Android](../develop/quickstart-v2-android.md)

* [iOS](../develop/quickstart-v2-ios.md)

* [Piattaforma UWP (Universal Windows Platform)](../develop/quickstart-v2-uwp.md)

## <a name="saml-20-and-wsfed"></a>SAML 2.0 e WSFed

Security Assertion Markup Language (SAML) viene in genere usato per le applicazioni web. Visualizzare [modo in cui Azure Usa il protocollo SAML](../develop/active-directory-saml-protocol-reference.md) per una panoramica. 

Web Services Federation (WSFed) è un' [standard di settore](http://docs.oasis-open.org/wsfed/federation/v1.2/ws-federation.html) in genere usato per le applicazioni web sviluppate con la piattaforma .net.

### <a name="benefits"></a>Vantaggi

SAML 2.0 è uno standard consolidato e la maggior parte delle tecnologie piattaforme supporto librerie open source per SAML 2.0. È possibile fornire ai clienti un'interfaccia di amministrazione per configurare SSO SAML. Possono configurare SSO SAML per Microsoft Azure AD e qualsiasi altro provider di identità che supporta SAML 2

### <a name="trade-offs"></a>Vantaggi e svantaggi

Quando si usano i protocolli SAML 2.0 o WSFed per applicazioni per dispositivi mobili, tra cui multi-factor Authentication (MFA) di determinati criteri di accesso condizionale avrà un'esperienza con funzionalità ridotta. Inoltre, se si desidera accedere a Microsoft Graph, è necessario implementare l'autorizzazione tramite OAuth 2.0 per generare i token necessari. 

### <a name="implementation"></a>Implementazione

Microsoft non forniscono librerie per l'implementazione di SAML o consigliabile librerie specifiche. Sono disponibili molte librerie open source.

## <a name="sso-and-using-microsoft-graph-rest-api"></a>L'accesso SSO e tramite Microsoft Graph Rest API 

Microsoft Graph sia l'infrastruttura di dati all'interno di Microsoft 365, tra cui Office 365, Windows 10 ed Enterprise Mobility e sicurezza e altri prodotti, ad esempio Dynamics 365. Sono inclusi gli schemi di core delle entità, ad esempio utenti, gruppi, calendario, posta elettronica, file e altro ancora, la produttività degli utenti tale unità. Microsoft Graph sono disponibili tre interfacce per gli sviluppatori un REST basato su API, i dati di Microsoft Graph per connettersi e connettori che consentono agli sviluppatori di aggiungere i propri dati in Microsoft Graph.  

Usando i protocolli sopra per l'accesso SSO consente l'accesso dell'applicazione per i dati dettagliati disponibili tramite l'API REST di Microsoft Graph. In questo modo i clienti a ottenere maggior valore dai propri investimenti in Microsoft 365. Ad esempio, l'applicazione può chiamare l'API Microsoft Graph per l'integrazione con l'istanza di Office 365 dei clienti e Microsoft Office degli utenti superfici e gli elementi di SharePoint all'interno dell'applicazione. 

Se si usa Openid Connect per l'autenticazione, l'esperienza di sviluppo è facile, perché verrà usato OAuth2, la base di Openid Connect, per acquisire i token è utilizzabile per richiamare le API Microsoft Graph. Se l'applicazione usa SAML o WSFed, è necessario aggiungere ulteriore codice all'interno dell'applicazione per ottenere questi OAuth2 per acquisire il token necessario per richiamare le API Microsoft Graph. 

## <a name="next-steps"></a>Fasi successive

[Abilitare l'accesso SSO per l'applicazione multi-tenant](isv-sso-content.md)

[Creare la documentazione per l'applicazione multi-tenant](isv-create-sso-documentation.md)
