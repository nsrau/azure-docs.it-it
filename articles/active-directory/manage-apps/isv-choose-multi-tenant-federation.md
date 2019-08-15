---
title: Scegliere il protocollo federativo appropriato per l'applicazione multi-tenant
description: Linee guida per i fornitori di software indipendenti per l'integrazione con Azure Active Directory
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
ms.openlocfilehash: 3c5975b57b6f960badf747e33deb238adf260199
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68967204"
---
# <a name="choose-the-right-federation-protocol-for-your-multi-tenant-application"></a>Scegliere il protocollo federativo appropriato per l'applicazione multi-tenant

Quando si sviluppa l'applicazione SaaS (Software as a Service), è necessario selezionare il protocollo federativo che meglio soddisfa le esigenze dell'utente e dei clienti. Questa decisione si basa sulla piattaforma di sviluppo e sul desiderio di integrarsi con i dati disponibili all'interno di Office 365 e Azure AD ecosistema dei clienti.

Vedere l'elenco completo dei [protocolli disponibili per le integrazioni SSO](what-is-single-sign-on.md) con Azure Active Directory.
La tabella seguente confronta 
* Aprire l'autenticazione 2,0 (OAuth 2,0)
* Connessione Open ID (OIDC)
* SAML (Security Assertion Markup Language)
* Web Services Federation (WSFed)

| Capacità| OAuth/OIDC| SAML/WSFed |
| - |-|-|
| Single Sign-on basato sul Web| √| √ |
| Single Sign-out basato sul Web| √| √ |
| Single Sign-on basato su dispositivi mobili| √| √* |
| Single Sign-out basato su dispositivi mobili| √| √* |
| Criteri di accesso condizionale per le applicazioni per dispositivi mobili| √| X |
| Esperienza multi-factor authentication per applicazioni per dispositivi mobili| √| X |
| Microsoft Graph di accesso| √| X |

\* Possibile, ma Microsoft non fornisce esempi o indicazioni.

## <a name="oauth-20-and-open-id-connect"></a>OAuth 2,0 e Open ID Connect

OAuth 2,0 è un protocollo [standard del settore](https://oauth.net/2/) per l'autorizzazione. OIDC (OpenID Connect) è un livello di autenticazione delle identità [standard del settore](https://openid.net/connect/) basato sul protocollo del giuramento 2,0.

### <a name="benefits"></a>Vantaggi

Microsoft consiglia di utilizzare OIDC/OAuth 2,0 perché i protocolli dispongono di autenticazione e autorizzazione incorporati. Con SAML è inoltre necessario implementare l'autorizzazione.

L'autorizzazione inerente a questi protocolli consente all'applicazione di accedere e di integrarsi con dati utente e aziendali avanzati tramite l'API Microsoft Graph.

L'uso di OAuth 2,0 e OIDC semplifica l'esperienza dell'utente finale dei clienti quando si adotta SSO per l'applicazione. È possibile definire facilmente i set di autorizzazioni necessari, che vengono quindi rappresentati automaticamente all'amministratore o al consenso dell'utente finale.

Inoltre, l'uso di questi protocolli consente ai clienti di usare i criteri di accesso condizionale e di autenticazione a più fattori per controllare l'accesso alle applicazioni. Microsoft fornisce librerie ed [esempi di codice in più piattaforme tecnologiche](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Samples) per favorire lo sviluppo.  

### <a name="implementation"></a>Implementazione

Registrare l'applicazione con Microsoft Identity, ovvero un provider OAuth 2,0. È anche possibile registrare l'applicazione basata su OAuth 2,0 con qualsiasi altro provider di identità con cui si vuole eseguire l'integrazione. 

Per informazioni su come registrare l'applicazione e implementare questi protocolli per l'accesso SSO alle app Web, vedere [autorizzare l'accesso ad applicazioni Web con OpenID Connect e Azure Active Directory](../develop/sample-v2-code.md).  Per informazioni su come implementare questi protocolli per SSO nelle app per dispositivi mobili, vedere gli argomenti seguenti: 

* [Android](../develop/quickstart-v2-android.md)

* [iOS](../develop/quickstart-v2-ios.md)

* [Piattaforma UWP (Universal Windows Platform)](../develop/quickstart-v2-uwp.md)

## <a name="saml-20-and-wsfed"></a>SAML 2,0 e WSFed

Security Assertion Markup Language (SAML) viene in genere utilizzato per le applicazioni Web. Per una panoramica [, vedere come Azure usa il protocollo SAML](../develop/active-directory-saml-protocol-reference.md) . 

Web Services Federation (WSFed) è uno [standard di settore](https://docs.oasis-open.org/wsfed/federation/v1.2/ws-federation.html) generalmente usato per le applicazioni Web sviluppate con la piattaforma .NET.

### <a name="benefits"></a>Vantaggi

SAML 2,0 è una piattaforma standard e la maggior parte delle tecnologie supporta le librerie open source per SAML 2,0. È possibile fornire ai clienti un'interfaccia di amministrazione per configurare SAML SSO. Possono configurare SAML SSO per Microsoft Azure AD e qualsiasi altro provider di identità che supporta SAML 2

### <a name="trade-offs"></a>Compromessi

Quando si usano protocolli SAML 2,0 o WSFed per le applicazioni per dispositivi mobili, alcuni criteri di accesso condizionale che includono l'autenticazione a più fattori (multi-factor authentication) avranno una riduzione dell'esperienza. Se inoltre si desidera accedere al Microsoft Graph, sarà necessario implementare l'autorizzazione tramite OAuth 2,0 per generare i token necessari. 

### <a name="implementation"></a>Implementazione

Microsoft non fornisce librerie per l'implementazione SAML o suggerisce librerie specifiche. Sono disponibili molte librerie open source.

## <a name="sso-and-using-microsoft-graph-rest-api"></a>SSO e uso dell'API REST di Microsoft Graph 

Microsoft Graph è l'infrastruttura di dati in tutti i Microsoft 365, tra cui Office 365, Windows 10 e Enterprise Mobility and Security e altri prodotti come Dynamics 365. Sono inclusi gli schemi principali delle entità, ad esempio utenti, gruppi, calendari, posta elettronica, file e altro ancora, che determinano la produttività degli utenti. Microsoft Graph offre tre interfacce per gli sviluppatori di un'API basata su REST, Microsoft Graph connessione dati e connettori che consentono agli sviluppatori di aggiungere i propri dati nel Microsoft Graph.  

L'uso di uno dei protocolli precedenti per SSO consente all'applicazione di accedere ai dati avanzati disponibili tramite l'API REST di Microsoft Graph. In questo modo i clienti possono ottenere un valore maggiore dagli investimenti in Microsoft 365. Ad esempio, l'applicazione può chiamare l'API di Microsoft Graph per l'integrazione con i Microsoft Office dell'istanza di Office 365 e degli utenti della superficie dei clienti e degli elementi di SharePoint all'interno dell'applicazione. 

Se si usa Open ID Connect per l'autenticazione, l'esperienza di sviluppo è semplice perché si userà OAuth2, la base di Open ID Connect, per acquisire i token per richiamare le API Microsoft Graph. Se l'applicazione usa SAML o WSFed, è necessario aggiungere altro codice all'interno dell'applicazione per ottenere questi OAuth2 per acquisire i token necessari per richiamare le API Microsoft Graph. 

## <a name="next-steps"></a>Fasi successive

[Abilitare l'accesso SSO per l'applicazione multi-tenant](isv-sso-content.md)

[Creare la documentazione per l'applicazione multi-tenant](isv-create-sso-documentation.md)
