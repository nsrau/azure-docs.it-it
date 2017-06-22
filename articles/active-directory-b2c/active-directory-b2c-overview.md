---
title: 'Azure Active Directory B2C: panoramica | Microsoft Docs'
description: Sviluppo di applicazioni di utenti con Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: 
author: saeeda
manager: krassk
editor: parja
ms.assetid: c465dbde-f800-4f2e-8814-0ff5f5dae610
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 12/06/2016
ms.author: saeeda
ms.translationtype: Human Translation
ms.sourcegitcommit: 532ff423ff53567b6ce40c0ea7ec09a689cee1e7
ms.openlocfilehash: f3c2760ec66c0292ebeb53d0acb5f9ee1df388ae
ms.contentlocale: it-it
ms.lasthandoff: 06/05/2017


---
# <a name="azure-ad-b2c-focus-on-your-app-let-us-worry-about-sign-up-and-sign-in"></a>Azure Active Directory B2C permette di concentrarsi sull'app, lasciando ad Azure la gestione di iscrizioni e accessi

Azure Active Directory B2C è una soluzione di gestione delle identità cloud per applicazioni Web e per dispositivi mobili. Si tratta di un servizio globale a disponibilità elevata, ridimensionabile in base a centinaia di milioni di identità. Basato su una piattaforma sicura di livello aziendale, Azure Active Directory B2C contribuisce alla protezione di applicazioni, azienda e clienti.

Con una configurazione minima, Azure Active Directory B2C permette all'applicazione di autenticare gli account seguenti:

* **Account social**, come Facebook, Google, LinkedIn e altri
* **Account aziendali** tramite protocolli standard aperti, OpenID Connect o SAML
* **Account locali**, con indirizzo e-mail e password o nome utente e password

## <a name="get-started"></a>Attività iniziali

Prima di tutto, ottenere il tenant usando la procedura descritta in [Azure Active Directory B2C: Creare un tenant di Azure AD B2C](active-directory-b2c-get-started.md).

Scegliere quindi lo scenario di sviluppo dell'applicazione:

|  |  |  |  |
| --- | --- | --- | --- |
| <center>![App per dispositivi mobili e desktop](../active-directory/develop/media/active-directory-developers-guide/NativeApp_Icon.png)<br />App per dispositivi mobili e desktop</center> | [Panoramica](active-directory-b2c-reference-oauth-code.md)&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<br /><br />[iOS](https://github.com/Azure-Samples/active-directory-b2c-ios-swift-native-msal)<br /><br />[Android](https://github.com/Azure-Samples/active-directory-b2c-android-native-msal) | [.NET](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop)<br /><br />[Xamarin](https://github.com/Azure-Samples/active-directory-b2c-xamarin-native) |  |
| <center>![App Web](../active-directory/develop/media/active-directory-developers-guide/Web_app.png)<br />App Web</center> | [Panoramica](active-directory-b2c-reference-oidc.md)<br /><br />[ASP.NET](active-directory-b2c-devquickstarts-web-dotnet-susi.md)<br /><br />[ASP.NET Core](https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapp) | [Node.JS](active-directory-b2c-devquickstarts-web-node.md) |  |
| <center>![App a singola pagina](../active-directory/develop/media/active-directory-developers-guide/SPA.png)<br />App a singola pagina</center> | [Panoramica](active-directory-b2c-reference-spa.md)<br /><br />[JavaScript](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp)<br /><br /> |  |  |
| <center>![API Web](../active-directory/develop/media/active-directory-developers-guide/Web_API.png)<br />API Web</center> | [ASP.NET](active-directory-b2c-devquickstarts-api-dotnet.md)<br /><br /> [ASP.NET Core](https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi)<br /><br /> [Node.js](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) | [Chiamare un'API Web .NET](active-directory-b2c-devquickstarts-web-api-dotnet.md) |

## <a name="whats-new"></a>Novità

Visitare spesso questa pagina per rimanere aggiornati sulle future modifiche apportate ad Azure Active Directory B2C. Gli aggiornamenti vengono anche resi disponibili su Twitter con @AzureAD.

* Oltre ai "criteri predefiniti" (disponibili a livello generale), è ora disponibile in anteprima pubblica la funzionalità ["Criteri personalizzati"](active-directory-b2c-overview-custom.md).  I criteri personalizzati sono destinati ai professionisti in gestione delle identità che devono poter controllare la struttura della propria esperienza di gestione.
* È ora disponibile in anteprima pubblica la funzionalità relativa ai [token di accesso](https://azure.microsoft.com/en-us/blog/azure-ad-b2c-access-tokens-now-in-public-preview).
* È stata annunciata la [disponibilità generale delle directory Azure Active Directory B2C in Europa](https://azure.microsoft.com/en-us/blog/azuread-b2c-ga-eu/).
* Vedere la libreria degli [esempi di codice su GitHub](https://github.com/Azure-Samples?q=b2c), in continuo aumento.

## <a name="how-to-articles"></a>Procedure

Informazioni su come usare funzionalità specifiche di Azure Active Directory B2C:

* Configurare gli account [Facebook](active-directory-b2c-setup-fb-app.md), [Google+](active-directory-b2c-setup-goog-app.md), [Microsoft](active-directory-b2c-setup-msa-app.md), [Amazon](active-directory-b2c-setup-amzn-app.md) e [LinkedIn](active-directory-b2c-setup-li-app.md) per l'uso nelle applicazioni destinate agli utenti.
* [Utilizzare attributi personalizzati per raccogliere informazioni sugli utenti](active-directory-b2c-reference-custom-attr.md).
* [Abilitare Azure Multi-Factor Authentication nelle applicazioni destinate agli utenti](active-directory-b2c-reference-mfa.md).
* [Configurare la reimpostazione password self-service per gli utenti](active-directory-b2c-reference-sspr.md).
* [Personalizzare l'aspetto delle pagine di iscrizione e di accesso e di altre pagine destinate agli utenti](active-directory-b2c-reference-ui-customization.md) rese disponibili da Azure Active Directory B2C.
* [Usare l'API Graph di Azure Active Directory per creare, leggere, aggiornare ed eliminare utenti a livello di codice](active-directory-b2c-devquickstarts-graph-dotnet.md) nel tenant di Azure Active Directory B2C.

## <a name="next-steps"></a>Passaggi successivi

I collegamenti seguenti sono utili per un'esplorazione più approfondita del servizio:

* Vedere [Prezzi di Azure Active Directory B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/).
* Vedere i [codici di esempio](https://azure.microsoft.com/en-us/resources/samples/?service=active-directory&term=b2c) per Azure Active Directory B2C. 
* Ottenere supporto in Stack Overflow usando il tag [azure-ad-b2c](http://stackoverflow.com/questions/tagged/azure-ad-b2c).
* Inviare commenti e suggerimenti usando [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c).
* Vedere le [informazioni di riferimento sui protocolli di Azure AD B2C](active-directory-b2c-reference-protocols.md).
* Vedere [Azure AD B2C: informazioni di riferimento sui token](active-directory-b2c-reference-tokens.md).
* Vedere [Azure Active Directory B2C: domande frequenti](active-directory-b2c-faqs.md).
* [Inviare richieste di supporto per Azure Active Directory B2C](active-directory-b2c-support.md).

## <a name="get-security-updates-for-our-products"></a>Ottenere aggiornamenti della sicurezza per i prodotti

È consigliabile ricevere notifiche in caso di problemi di sicurezza. A tale scopo, visitare [questa pagina](https://technet.microsoft.com/security/dd252948) e sottoscrivere gli avvisi di sicurezza.


