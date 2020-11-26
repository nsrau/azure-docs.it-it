---
title: Supportare Single Sign-On e i criteri di protezione delle app nelle app per dispositivi mobili sviluppate | Azure
titleSuffix: Microsoft identity platform
description: Spiegazione e panoramica della creazione di applicazioni per dispositivi mobili che supportano Single Sign-On e criteri di protezione delle app usando la piattaforma di identità Microsoft e integrando con Azure Active Directory.
services: active-directory
author: knicholasa
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/14/2020
ms.author: nichola
ms.openlocfilehash: 59924c1f876d08aa504f19c5d6c86dca32fbd1e2
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96173465"
---
# <a name="support-single-sign-on-and-app-protection-policies-in-mobile-apps-you-develop"></a>Supportare Single Sign-On e i criteri di protezione delle app nelle app per dispositivi mobili sviluppate

Single Sign-on (SSO) è un'offerta chiave della piattaforma di identità Microsoft e Azure Active Directory, che consente di accedere in modo semplice e sicuro agli utenti dell'app. I criteri di protezione delle app (APP), inoltre, abilitano il supporto dei criteri di sicurezza principali che proteggono i dati dell'utente. Insieme, queste funzionalità consentono di proteggere gli accessi utente e la gestione dei dati dell'app.

Questo articolo illustra il motivo per cui SSO e APP sono importanti e fornisce indicazioni generali per la creazione di applicazioni per dispositivi mobili che supportano queste funzionalità. Questo vale per le app per telefoni e tablet. Se si è un amministratore IT che vuole distribuire SSO nel tenant Azure Active Directory dell'organizzazione, consultare le [linee guida per la pianificazione di una distribuzione di Single Sign-on](../manage-apps/plan-sso-deployment.md)

## <a name="about-single-sign-on-and-app-protection-policies"></a>Informazioni sui criteri di Single Sign-On e di protezione delle app

[Single Sign-on (SSO)](../manage-apps/plan-sso-deployment.md) consente a un utente di accedere una sola volta e ottenere l'accesso ad altre applicazioni senza immettere di nuovo le credenziali. In questo modo si semplifica l'accesso alle app e si elimina la necessità per gli utenti di ricordare lunghi elenchi di nomi utente e password. L'implementazione nell'app semplifica l'accesso e l'uso dell'app.

Inoltre, l'abilitazione di Single Sign-On nell'app Sblocca i nuovi meccanismi di autenticazione disponibili con l'autenticazione moderna, ad esempio gli [account di accesso senza password](../authentication/concept-authentication-passwordless.md). I nomi utente e le password sono uno dei vettori di attacco più diffusi per le applicazioni e l'abilitazione di SSO consente di attenuare questo rischio imponendo l'accesso condizionale o gli account di accesso senza password che aggiungono ulteriore sicurezza oppure si basano su meccanismi di autenticazione più sicuri. Infine, l'abilitazione di Single Sign-On consente anche l' [accesso Single Sign-out](v2-protocols-oidc.md#single-sign-out). Questa operazione è utile in situazioni come le applicazioni di lavoro che verranno usate nei dispositivi condivisi.

I [criteri di protezione delle app (app)](/mem/intune/apps/app-protection-policy) assicurano che i dati di un'organizzazione rimangano sicuri e contenuti. Consentono alle aziende di gestire e proteggere i dati all'interno di un'app e consentono di controllare chi può accedere all'app e ai relativi dati. L'implementazione dei criteri di protezione delle app consente all'app di connettere gli utenti alle risorse protette dai criteri di accesso condizionale e di trasferire in modo sicuro i dati da e verso altre app protette. Gli scenari sbloccati dai criteri di protezione delle app includono richiedere un PIN per aprire un'app, controllare la condivisione di dati tra le app e impedire il salvataggio dei dati delle app aziendali nei percorsi di archiviazione personali.

## <a name="implementing-single-sign-on"></a>Implementazione di Single Sign-On

Per consentire all'app di sfruttare i vantaggi di Single Sign-On, è consigliabile eseguire le operazioni seguenti.

### <a name="use-microsoft-authentication-library-msal"></a>USA Microsoft Authentication Library (MSAL)

La scelta migliore per implementare Single Sign-On nell'applicazione consiste nell'utilizzare [Microsoft Authentication Library (MSAL)](msal-overview.md). Con MSAL è possibile aggiungere l'autenticazione all'app con codice minimo e chiamate API, ottenere le funzionalità complete della [piattaforma Microsoft Identity](./index.yml)e consentire a Microsoft di gestire la manutenzione di una soluzione di autenticazione sicura. Per impostazione predefinita, MSAL aggiunge il supporto SSO per l'applicazione. Inoltre, l'uso di MSAL è un requisito se si prevede anche di implementare i criteri di protezione delle app.

> [!NOTE]
> È possibile configurare MSAL per l'utilizzo di una visualizzazione Web incorporata. In questo modo si eviterà Single Sign-On. Utilizzare il comportamento predefinito, ovvero il Web browser di sistema, per garantire il corretto funzionamento di SSO.

Se attualmente si usa la [libreria adal](../azuread-dev/active-directory-authentication-libraries.md) nell'applicazione, è consigliabile [eseguire la migrazione a MSAL](msal-migration.md), perché [adal è deprecato](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/update-your-applications-to-use-microsoft-authentication-library/ba-p/1257363).

Per le applicazioni iOS, è presente una [Guida introduttiva](quickstart-v2-ios.md) che illustra come configurare gli accessi tramite MSAL, oltre a [indicazioni per la configurazione di MSAL per diversi scenari SSO](single-sign-on-macos-ios.md).

Per le applicazioni Android, abbiamo una [Guida introduttiva](quickstart-v2-android.md) che illustra come configurare gli accessi con MSAL e istruzioni [su come abilitare l'accesso Single Sign-on tra app in Android usando MSAL](msal-android-single-sign-on.md).

### <a name="use-the-system-web-browser"></a>Usare il Web browser di sistema

Per l'autenticazione interattiva è necessario un Web browser. Per le app per dispositivi mobili che usano librerie di autenticazione moderne diverse da MSAL, ovvero altre librerie OpenID Connect o SAML, oppure se si implementa il proprio codice di autenticazione, è necessario usare il browser di sistema come superficie di autenticazione per abilitare l'accesso SSO.

Google offre istruzioni per eseguire questa operazione nelle applicazioni Android: [schede personalizzate Chrome-Google Chrome](https://developer.chrome.com/multidevice/android/customtabs).

Apple offre istruzioni per eseguire questa operazione nelle applicazioni iOS: [autenticazione di un utente tramite un servizio Web | Documentazione per sviluppatori Apple](https://developer.apple.com/documentation/authenticationservices/authenticating_a_user_through_a_web_service).

> [!TIP]
> Il [plug-in SSO per i dispositivi Apple](apple-sso-plugin.md) consente l'accesso SSO per le app iOS che usano visualizzazioni Web incorporate nei dispositivi gestiti con Intune. Per lo sviluppo di app che abilitano l'accesso SSO per tutti gli utenti è consigliabile usare MSAL e browser di sistema, ma ciò consentirà l'accesso SSO in alcuni scenari in cui altrimenti non è possibile.

## <a name="enable-app-protection-policies"></a>Abilitare i criteri di protezione delle app

Per abilitare i criteri di protezione delle app, usare [Microsoft Authentication Library (MSAL)](msal-overview.md). MSAL è la libreria di autenticazione e autorizzazione della piattaforma di gestione delle identità Microsoft e Intune SDK è stato sviluppato per funzionare in tandem.

Inoltre, è necessario usare un'app Broker per l'autenticazione. Il broker richiede che l'app fornisca informazioni sull'applicazione e sul dispositivo per garantire la conformità dell'app. Gli utenti iOS useranno l'app [Microsoft Authenticator](../user-help/user-help-auth-app-sign-in.md) e gli utenti Android useranno l'app Microsoft Authenticator o l'app [portale aziendale](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal) per l' [autenticazione negoziata](./msal-android-single-sign-on.md). Per impostazione predefinita, MSAL usa un broker come prima scelta per soddisfare una richiesta di autenticazione, quindi l'uso di broker per l'autenticazione verrà abilitato automaticamente per l'app quando si usa MSAL.

Infine, [aggiungere INTUNE SDK](/mem/intune/developer/app-sdk-get-started) all'app per abilitare i criteri di protezione delle app. L'SDK nella maggior parte dei casi segue un modello di intercettazione e applica automaticamente i criteri di protezione delle app per determinare se le azioni eseguite dall'app sono consentite o meno. Esistono anche API che è possibile chiamare manualmente per indicare all'app se sono presenti restrizioni per determinate azioni.

## <a name="additional-resources"></a>Risorse aggiuntive

- [Pianificare una distribuzione di Azure Active Directory Single Sign-On](../manage-apps/plan-sso-deployment.md)
- [Procedura: configurare l'accesso SSO in macOS e iOS](single-sign-on-macos-ios.md)
- [Plug-in Microsoft Enterprise SSO per i dispositivi Apple (anteprima)](apple-sso-plugin.md)
- [Autenticazione negoziata in Android](./msal-android-single-sign-on.md)
- [Agenti di autorizzazione e come abilitarli](./msal-android-single-sign-on.md)
- [Introduzione a Microsoft Intune App SDK](/mem/intune/developer/app-sdk-get-started)
- [Configurare le impostazioni per Intune App SDK](/mem/intune/developer/app-sdk-ios#configure-settings-for-the-intune-app-sdk)
- [App protette di Microsoft Intune](/mem/intune/apps/apps-supported-intune-apps)