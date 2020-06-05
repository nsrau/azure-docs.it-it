---
title: Aggiungere l'autenticazione alle app per dispositivi mobili con Visual Studio App Center e i servizi di Azure
description: Informazioni sui servizi, ad esempio Visual Studio App Center, che consentono di configurare l'autenticazione degli utenti e abilitare l'autenticazione delle applicazioni per dispositivi mobili con account di social networking, Azure Active Directory e metodi personalizzati.
author: codemillmatt
ms.assetid: 34a8a070-2222-4faf-9090-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: a20ec1039dc8fc477410891495762395b408f2d6
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2020
ms.locfileid: "83870581"
---
# <a name="add-authentication-and-manage-user-identities-in-your-mobile-apps"></a>Aggiungere l'autenticazione e gestire le identità degli utenti nelle app per dispositivi mobili

Con una visibilità completa degli utenti e del loro comportamento nei confronti delle applicazioni, gli sviluppatori possono creare esperienze personalizzate e più coinvolgenti. Sia che si sviluppino applicazioni per la collaborazione degli utenti all'interno dell'organizzazione o che si crei la piattaforma di social networking di nuova generazione, è necessario un modo per autenticare gli utenti e gestirne le identità. La gestione delle identità è una delle funzionalità più importanti dei servizi back-end per dispositivi mobili.

Usare i servizi seguenti per abilitare l'autenticazione degli utenti nelle app per dispositivi mobili.

## <a name="azure-active-directory-b2c"></a>Azure Active Directory B2C
[Azure AD B2C](https://azure.microsoft.com/services/active-directory-b2c/) è un servizio di gestione delle identità B2C (business-to-consumer) che gli sviluppatori possono usare per autenticare i loro clienti. Questo servizio white-label consente agli sviluppatori di personalizzare e controllare l'interazione sicura degli utenti con applicazioni Web, desktop, per dispositivi mobili o a pagina singola. Con Azure AD B2C, gli utenti possono eseguire l'iscrizione, l'accesso, la reimpostazione delle password e la modifica dei profili. Azure AD B2C implementa una forma dei protocolli OAuth 2.0 e OpenID Connect. 

**Funzionalità principali**
- Autenticazione sicura degli utenti con il loro provider di identità preferito.
- Gestione di identità e accesso dei clienti.
- Supporto dell'accesso per social media, come Facebook, GitHub, Google, LinkedIn, Twitter, WeChat e Weibo.
- Connessione agli account utente tramite protocolli standard di settore, come OpenID Connect o SAML, per rendere possibile la gestione delle identità in un'ampia gamma di piattaforme.
- Possibilità di offrire esperienze personalizzate di registrazione e accesso.
- Facile integrazione con database CRM, strumenti di analisi di marketing e sistemi di verifica degli account.
- Acquisizione di dati su accesso, preferenze e conversione per i clienti.

**Riferimenti**
- [Azure portal](https://portal.azure.com/)
- [Documentazione di Azure AD B2C](/azure/active-directory-b2c/)
- [Guide introduttive](/azure/active-directory-b2c/active-directory-b2c-quickstarts-web-app)
- [Esempi](/azure/active-directory-b2c/code-samples)

## <a name="azure-active-directory"></a>Azure Active Directory
[Azure Active Directory](https://azure.microsoft.com/services/active-directory/) è il servizio di gestione di identità e accessi basato sul cloud di Microsoft, che consente ai dipendenti di accedere e usare:
- Risorse esterne, come Microsoft Office 365, il portale di Azure e migliaia di altre applicazioni SaaS (software distribuito come servizio).
- Risorse interne, tra cui app nella rete e nella Intranet aziendale, oltre a eventuali app cloud sviluppate dall'organizzazione.

**Funzionalità principali**
- Accesso trasparente e altamente sicuro connettendo gli utenti alle applicazioni necessarie.
- Protezione completa e sicurezza avanzata per le identità e accesso in base a utente, località, dispositivo, dati e contesto dell'applicazione.
- Migliaia di app commerciali e personalizzate preintegrate, come Office 365, Salesforce.com e Box.
- Possibilità di gestire l'accesso su larga scala.

**Riferimenti**
- [Azure portal](https://portal.azure.com/)
- [Informazioni su Azure AD](/azure/active-directory/fundamentals/active-directory-whatis)
- [Introduzione ad Azure Active Directory](/azure/active-directory/fundamentals/active-directory-whatis)
- [Guide introduttive](/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)