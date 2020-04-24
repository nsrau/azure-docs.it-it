---
title: Aggiungere l'autenticazione alle app per dispositivi mobili con Visual Studio App Center e i servizi di Azure
description: Informazioni sui servizi, ad esempio Visual Studio App Center che consentono di configurare l'autenticazione utente e consentire alle applicazioni per dispositivi mobili di eseguire l'autenticazione con gli account di social networking, Azure Active Directory e l'autenticazione personalizzata.
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
# <a name="add-authentication-and-manage-user-identities-in-your-mobile-apps"></a>Aggiungere l'autenticazione e gestire le identità degli utenti nelle app per dispositivi mobili

La visualizzazione dell'utente e il relativo comportamento nell'applicazione consentono agli sviluppatori di coinvolgere meglio gli utenti creando esperienze personalizzate. Che tu sia uno sviluppatore di applicazioni che sta creando un'applicazione di collaborazione per gli utenti all'interno dell'organizzazione o stai creando la prossima piattaforma di social network, ti serve un modo per autenticare gli utenti e gestire le identità degli utenti. Un servizio di gestione delle identità è una delle funzionalità più importanti di un servizio back-end per dispositivi mobili.

Usare i servizi seguenti per abilitare l'autenticazione utente nelle app per dispositivi mobili.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center auth](/appcenter/auth/) è un servizio di gestione delle identità basato sul cloud che gli sviluppatori possono usare per autenticare gli utenti e gestire le identità utente. App Center autenticazione si integra anche con altre parti di Visual Studio App Center. Gli sviluppatori possono usare l'identità utente per [visualizzare i dati utente](/appcenter/data/index) in altri servizi e persino [inviare notifiche push agli utenti anziché ai singoli dispositivi](/appcenter/push/push-to-user#setting-user-identity). 

**Funzionalità principali**
- Con tecnologia Azure Active Directory B2C (Azure AD B2C). 
    - Classe Enterprise.
    - Disponibilità elevata.
    - Servizio sicuro e globale.
- Usa la tua identità e l'opzione per usare altri provider di gestione delle identità e degli accessi più diffusi, ad esempio Auth0 e Firebase.
- Supporto Azure Active Directory.
    - Connettere i tenant di Azure AD esistenti. 
    - Abilitare l'autenticazione in un dominio aziendale.
    - Gestire l'accesso ai dati sensibili.
- Esperienza utente semplice e magica SDK eseguendo il wrapping di Microsoft Authentication Library con SDK Visual Studio App Center.
- Supporto della piattaforma per iOS, Android, Novell e React native.

**Riferimenti**
- [Iscriversi con Visual Studio App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs) 
- [Introduzione all'autenticazione App Center](/appcenter/auth/)

## <a name="azure-active-directory-b2c"></a>Azure Active Directory B2C
[Azure ad B2C](https://azure.microsoft.com/services/active-directory-b2c/) è un servizio di gestione delle identità business-to-consumer (B2C) che gli sviluppatori possono usare per autenticare i clienti. Questo servizio con etichette bianche consente agli sviluppatori di personalizzare e controllare il modo in cui gli utenti interagiscono in modo sicuro con le applicazioni Web, desktop, per dispositivi mobili o a singola pagina. Con Azure AD B2C, gli utenti possono eseguire l'iscrizione, l'accesso, la reimpostazione delle password e la modifica dei profili. Azure AD B2C implementa una forma dei protocolli OAuth 2.0 e OpenID Connect. 

**Funzionalità principali**
- Autenticare i clienti in modo sicuro con il provider di identità preferito.
- Gestisci l'identità e l'accesso dei clienti.
- Ottieni supporto per l'accesso a social media come Facebook, GitHub, Google, LinkedIn, Twitter, WeChat e Weibo.
- È possibile connettersi agli account utente usando protocolli standard di settore, ad esempio OpenID Connect o SAML, per rendere possibile la gestione delle identità in un'ampia gamma di piattaforme.
- Fornire un'esperienza di registrazione e accesso personalizzata.
- Facile integrazione con database CRM, strumenti di analisi di marketing e sistemi di verifica degli account.
- Acquisisci i dati di accesso, preferenza e conversione per i clienti.

**Riferimenti**
- [Azure portal](https://portal.azure.com/)
- [Documentazione di Azure AD B2C](/azure/active-directory-b2c/)
- [Guide introduttive](/azure/active-directory-b2c/active-directory-b2c-quickstarts-web-app)
- [Esempi](/azure/active-directory-b2c/code-samples)

## <a name="azure-active-directory"></a>Azure Active Directory
[Azure Active Directory](https://azure.microsoft.com/services/active-directory/) è il servizio Microsoft di gestione delle identità e degli accessi basato sul cloud, che consente ai dipendenti di accedere e ottenere l'accesso a:
- Risorse esterne, ad esempio Microsoft Office 365, portale di Azure e migliaia di altre applicazioni SaaS (Software as a Service).
- Risorse interne, tra cui app nella rete e nella Intranet aziendale, oltre a eventuali app cloud sviluppate dall'organizzazione.

**Funzionalità principali**
- Accesso trasparente e altamente sicuro grazie alla connessione degli utenti alle applicazioni necessarie.
- Protezione delle identità completa e sicurezza avanzata per le identità e l'accesso in base all'utente, alla posizione, al dispositivo, ai dati e al contesto dell'applicazione.
- Migliaia di app preintegrate per applicazioni commerciali e personalizzate, ad esempio Office 365, Salesforce.com e box.
- Possibilità di gestire l'accesso su larga scala.

**Riferimenti**
- [Azure portal](https://portal.azure.com/)
- [Informazioni su Azure AD](/azure/active-directory/fundamentals/active-directory-whatis)
- [Introduzione ad Azure Active Directory](/azure/active-directory/fundamentals/active-directory-whatis)
- [Guide introduttive](/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)