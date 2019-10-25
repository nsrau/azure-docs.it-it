---
title: Aggiungere l'autenticazione alle app per dispositivi mobili con Visual Studio App Center e i servizi di Azure
description: Informazioni sui servizi, ad esempio App Center che consentono di configurare l'autenticazione utente e consentire alle applicazioni per dispositivi mobili di eseguire l'autenticazione con gli account di social networking, Azure Active Directory e l'autenticazione personalizzata.
author: elamalani
ms.assetid: 34a8a070-2222-4faf-9090-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 8885f6cd91179f4dee6456277d47581f68c21723
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795757"
---
# <a name="add-authentication-and-manage-user-identities-in-your-mobile-apps"></a>Aggiungere l'autenticazione e gestire le identità degli utenti nelle app per dispositivi mobili

La visualizzazione dell'utente e il relativo comportamento nell'applicazione consentono agli sviluppatori di coinvolgere meglio gli utenti creando esperienze personalizzate. Per gli sviluppatori di applicazioni che creano un'applicazione di collaborazione per gli utenti all'interno dell'organizzazione o per la creazione della piattaforma di social networking successiva, sarà necessario un modo per autenticare gli utenti e gestire le identità utente. L'uso di un servizio di gestione delle identità è una delle funzionalità più importanti di un servizio back-end per dispositivi mobili.

Usare i servizi seguenti per abilitare l'autenticazione utente nelle app per dispositivi mobili.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center auth](/appcenter/auth/) è un servizio di gestione delle identità basato sul cloud che consente agli sviluppatori di autenticare gli utenti e gestire le identità degli utenti. App Center auth si integra anche con altre parti di App Center, consentendo agli sviluppatori di sfruttare l'identità utente per [visualizzare i dati degli](/appcenter/data/index) utenti in altri servizi e persino [inviare notifiche push agli utenti anziché ai singoli dispositivi](/appcenter/push/push-to-user#app-center-auth-set-identity). 

**Funzionalità principali**
- **Con tecnologia Azure Active Directory B2C (Azure AD B2C)** 
    - Di livello aziendale.
    - Disponibilità elevata.
    - Servizio sicuro e globale.
- USA **la tua identità** e usa altri noti provider di gestione delle identità e degli accessi, come Auth0 e Firebase.
- **Supporto di AAD** 
    - Connettere i tenant di AAD esistenti. 
    - Abilitare l'autenticazione in un dominio aziendale.
    - Gestire l'accesso ai dati sensibili.
- Esperienza **utente semplice** e magica SDK eseguendo il wrapping della libreria MSAL con App Center SDK.
- **Supporto della piattaforma** : iOS, Android, Novell, React native.

**Riferimenti**
- [Iscriversi con App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs) 
- [Introduzione all'autenticazione App Center](/appcenter/auth/)

## <a name="azure-active-directory-b2c"></a>Azure Active Directory B2C
[Azure ad B2C](https://azure.microsoft.com/services/active-directory-b2c/) è un servizio di gestione delle identità business-to-consumer che consente agli sviluppatori di autenticare i clienti. Questo servizio con etichette bianche consente agli sviluppatori di personalizzare e controllare il modo in cui gli utenti interagiscono in modo sicuro con le applicazioni Web, desktop, per dispositivi mobili o a singola pagina. Con Azure AD B2C, gli utenti possono eseguire l'iscrizione, l'accesso, la reimpostazione delle password e la modifica dei profili. Azure AD B2C implementa una forma dei protocolli OAuth 2.0 e OpenID Connect. 

**Funzionalità principali**
- Autenticare i clienti in modo sicuro con il provider di identità preferito.
- **Gestione delle identità e degli accessi dei clienti**.
- Account di accesso di social networking supportati come Facebook, GitHub, Google, LinkedIn, Twitter, WeChat, Weibo.
- È possibile connettersi agli account utente usando **protocolli standard di settore** come OpenID Connect o SAML per rendere possibile la gestione delle identità in un'ampia gamma di piattaforme.
- Fornire esperienze di registrazione e accesso personalizzate.
- Facile integrazione con database CRM, strumenti di analisi di marketing e sistemi di verifica degli account.
- Acquisisci i dati di accesso, preferenza e conversione per i clienti.

**Riferimenti**
- [Azure portal](https://portal.azure.com/)
- [Documentazione di Azure AD B2C](/azure/active-directory-b2c/)
- [Guide introduttive](/azure/active-directory-b2c/active-directory-b2c-quickstarts-web-app)
- [Esempi](/azure/active-directory-b2c/code-samples)

## <a name="azure-active-directory"></a>Azure Active Directory
[Azure Active Directory (Azure ad)](https://azure.microsoft.com/services/active-directory/) è il servizio Microsoft di gestione delle identità e degli accessi basato sul cloud, che consente all'utente di accedere e accedere ai dipendenti.
- Risorse esterne, tra cui Microsoft Office 365, il portale di Azure e migliaia di altre applicazioni SaaS.
- Risorse interne, tra cui app nella rete e nella Intranet aziendale, oltre a eventuali app cloud sviluppate dall'organizzazione.

**Funzionalità principali**
- **Accesso trasparente e altamente sicuro** grazie alla connessione degli utenti alle applicazioni necessarie.
- **Protezione delle identità completa** e sicurezza avanzata per le identità e l'accesso in base all'utente, alla posizione, al dispositivo, ai dati e al contesto dell'applicazione.
- **Migliaia di app preintegrate** , sia applicazioni commerciali che personalizzate, ad esempio Office 365, Salesforce.com e box.
- **Gestire l'accesso su larga scala**.

**Riferimenti**
- [Azure portal](https://portal.azure.com/)
- [Informazioni su Azure AD](/azure/active-directory/fundamentals/active-directory-whatis)
- [Introduzione ad Azure Active Directory](/azure/active-directory/fundamentals/active-directory-whatis)
- [Guide introduttive](/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)