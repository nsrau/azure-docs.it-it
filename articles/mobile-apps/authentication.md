---
title: Aggiungere l'autenticazione alle app per dispositivi mobili con Visual Studio App Center e i servizi di AzureAdd authentication to your mobile apps with Visual Studio App Center and Azure services
description: Informazioni sui servizi, ad esempio Visual Studio App Center, che consentono di configurare l'autenticazione utente e abilitare le applicazioni per dispositivi mobili per l'autenticazione con account social, Azure Active Directory e l'autenticazione personalizzata.
author: codemillmatt
ms.assetid: 34a8a070-2222-4faf-9090-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 820005bca008ea3bdb59659c815da3ec36beb0d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241043"
---
# <a name="add-authentication-and-manage-user-identities-in-your-mobile-apps"></a>Aggiungere l'autenticazione e gestire le identità utente nelle app per dispositivi mobili

Avere una visione dell'utente e del suo comportamento in tutta l'applicazione consente agli sviluppatori di coinvolgere meglio gli utenti creando esperienze su misura per loro. Che tu sia uno sviluppatore di applicazioni che sta creando un'applicazione di collaborazione per gli utenti all'interno dell'organizzazione o che stia creando la prossima piattaforma di social network, hai bisogno di un modo per autenticare gli utenti e gestire le identità degli utenti. Un servizio di gestione delle identità è una delle funzionalità più importanti di un servizio back-end mobile.

Usa i servizi seguenti per abilitare l'autenticazione utente nelle tue app per dispositivi mobili.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center Auth](/appcenter/auth/) è un servizio di gestione delle identità basato su cloud che gli sviluppatori possono usare per autenticare gli utenti e gestire le identità degli utenti. App Center Auth si integra anche con altre parti di Visual Studio App Center. Gli sviluppatori possono utilizzare l'identità utente per visualizzare i [dati utente](/appcenter/data/index) in altri servizi e persino [inviare notifiche push agli utenti anziché ai singoli dispositivi.](/appcenter/push/push-to-user#setting-user-identity) 

**Caratteristiche principali**
- Alimentato da Azure Active Directory B2C (Azure AD B2C). 
    - Di livello enterprise.
    - Disponibilità elevata.
    - Servizio sicuro e globale.
- Porta la tua identità e la possibilità di utilizzare altri provider di gestione di identità e accessi popolari, come Auth0 e Firebase.
- Supporto di Azure Active Directory.Azure Active Directory support.
    - Connettere tenant di Azure AD esistenti. 
    - Abilitare l'autenticazione in un dominio aziendale.
    - Gestire l'accesso ai dati sensibili.
- Esperienza utente semplice ed esperienza SDK magica eseguendo il wrapping della libreria di autenticazione Microsoft con Visual Studio App Center SDK.
- Supporto della piattaforma per iOS, Android, Xamarin e React Native.

**Riferimenti**
- [Iscriversi a Visual Studio App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs) 
- [Introduzione all'autenticazione di App Center](/appcenter/auth/)

## <a name="azure-active-directory-b2c"></a>Azure Active Directory B2C
[Azure AD B2C](https://azure.microsoft.com/services/active-directory-b2c/) è un servizio di gestione delle identità business-to-consumer (B2C) che gli sviluppatori possono usare per autenticare i clienti. Questo servizio white-label consente agli sviluppatori di personalizzare e controllare il modo in cui gli utenti interagiscono in modo sicuro con le loro applicazioni Web, desktop, mobile o a pagina singola. Con Azure AD B2C, gli utenti possono eseguire l'iscrizione, l'accesso, la reimpostazione delle password e la modifica dei profili. Azure AD B2C implementa una forma dei protocolli OAuth 2.0 e OpenID Connect. 

**Caratteristiche principali**
- Autentica in modo sicuro i clienti con il loro provider di identità preferito.
- Gestire l'identità e l'accesso dei clienti.
- Ottieni il supporto per l'accesso per i social media come Facebook, GitHub, Google, LinkedIn, Twitter, WeChat e Weibo.
- Connettersi agli account utente utilizzando protocolli standard del settore, ad esempio OpenID Connect o SAML, per rendere possibile la gestione delle identità su un'ampia gamma di piattaforme.
- Fornire esperienze di registrazione e di accesso personalizzate.
- Integrazione semplificata con database CRM, strumenti di analisi di marketing e sistemi di verifica dell'account.
- Acquisisci i dati di accesso, preferenza e conversione per i clienti.

**Riferimenti**
- [Portale di Azure](https://portal.azure.com/)
- [Documentazione di Azure AD B2CAzure AD B2C documentation](/azure/active-directory-b2c/)
- [Avvi rapidi](/azure/active-directory-b2c/active-directory-b2c-quickstarts-web-app)
- [Esempi](/azure/active-directory-b2c/code-samples)

## <a name="azure-active-directory"></a>Azure Active Directory
[Azure Active Directory](https://azure.microsoft.com/services/active-directory/) è il servizio di gestione delle identità e degli accessi basato su cloud di Microsoft, che consente ai dipendenti di accedere e accedere a:Azure Active Directory is Microsoft's cloud-based identity and access management service, which helps your employees to sign in and get access to:
- Risorse esterne, ad esempio Microsoft Office 365, il portale di Azure e migliaia di altre applicazioni di software come servizio (SaaS).
- Risorse interne, tra cui app nella rete e nella Intranet aziendale, oltre a eventuali app cloud sviluppate dall'organizzazione.

**Caratteristiche principali**
- Accesso trasparente e altamente sicuro collegando gli utenti alle applicazioni di cui hanno bisogno.
- Protezione completa delle identità e sicurezza avanzata per le identità e l'accesso in base a utente, posizione, dispositivo, dati e contesto dell'applicazione.
- Migliaia di app preintegrate per applicazioni commerciali e personalizzate, ad esempio Office 365, Salesforce.com e Box.
- Possibilità di gestire l'accesso su larga scala.

**Riferimenti**
- [Portale di Azure](https://portal.azure.com/)
- [Informazioni su Azure AD](/azure/active-directory/fundamentals/active-directory-whatis)
- [Introduzione ad Azure Active Directory](/azure/active-directory/fundamentals/active-directory-whatis)
- [Avvi rapidi](/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)