---
title: Microsoft Identity Platform | Azure
description: Informazioni su Microsoft Identity Platform, un'evoluzione della piattaforma per sviluppatori e del servizio di gestione delle identità di Azure Active Directory.
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: c7b3eee08c036862e6ce9f0c590a596f7b1d3fb0
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59258692"
---
# <a name="about-microsoft-identity-platform"></a>Microsoft Identity Platform

Microsoft Identity Platform è un'evoluzione della piattaforma per sviluppatori e del servizio di gestione delle identità di Azure Active Directory (Azure AD). Consente agli sviluppatori di creare applicazioni che supportano l'accesso per tutte le identità Microsoft e il recupero di token per chiamare Microsoft Graph, altre API Microsoft o API create dagli sviluppatori. È una piattaforma completa che include un servizio di autenticazione, librerie open source, la configurazione e la registrazione delle applicazioni (attraverso un portale per sviluppatori e l'API dell'applicazione), una documentazione esaustiva, esempi di codice e altri contenuti per gli sviluppatori. Microsoft Identity Platform supporta protocolli standard di settore come OAuth 2.0 e OpenID Connect.

La maggior parte degli sviluppatori ha finora usato la piattaforma di Azure AD v1.0 per l'autenticazione delle identità di Azure AD (account aziendali e degli istituti di istruzione) richiedendo i token dall'endpoint di Azure AD v1.0, usando Azure AD Authentication Library (ADAL), il portale di Azure per la registrazione e la configurazione delle applicazioni e l'API Graph di Azure AD per la configurazione a livello di codice delle applicazioni. Azure AD v1.0 è una piattaforma avanzata che continuerà a essere usata per le applicazioni aziendali.

Per espandere e sviluppare le funzionalità di Microsoft Identity Platform, è ora possibile eseguire l'autenticazione di un set più ampio di identità Microsoft (identità di Azure AD, account Microsoft come outlook.com e hotmail.com e gli account di social e locali con Azure AD B2C) tramite l'endpoint di Azure AD v2.0. In questa sede si userà Microsoft Authentication Library (MSAL) o una qualsiasi libreria open source di OAuth 2.0 oppure OpenID Connect, il portale di Azure per la registrazione e la configurazione dell'applicazione e l'API Microsoft Graph per la configurazione a livello di codice dell'applicazione. Microsoft Identity Platform (in particolare le librerie MSAL e la nuova esperienza di registrazione delle app nel portale di Azure) si è evoluto notevolmente nell'ultimo anno. Per finalizzare questa versione, si invitano gli sviluppatori a sviluppare e testare le proprie applicazioni usando la versione più recente di Microsoft Identity Platform.

Le applicazioni che usano le librerie ADAL e MSAL più recenti eseguiranno il Single Sign-On reciprocamente. Le applicazioni aggiornate dalle librerie ADAL alle librerie MSAL manterranno lo stato di accesso dell'utente. Gli sviluppatori possono scegliere di aggiornare le proprie applicazioni alle librerie MSAL se le ritengono idonee, mentre le applicazioni con le librerie ADAL continueranno a essere usate e supportate.

## <a name="microsoft-identity-platform-experience"></a>Esperienza con Microsoft Identity Platform

Il diagramma seguente illustra l'esperienza con Microsoft Identity a un alto livello, inclusi l'esperienza di registrazione delle app, gli SDK, gli endpoint e le identità supportate.

![Microsoft Identity Platform oggi](./media/about-microsoft-identity-platform/about-microsoft-identity-platform.svg)

Microsoft Identity Platform dispone di due endpoint (v1.0 e v2.0) e di due set di librerie client per gestire questi endpoint. Quando si sviluppa una nuova applicazione, prendere in considerazione i vantaggi e lo stato corrente degli endpoint e delle librerie di autenticazione. Tenere presente anche questi aspetti:

* Piattaforme supportate

    * [ADAL](active-directory-authentication-libraries.md) supporta .NET, JavaScript, iOS, Android, Java e Python
    * L'[anteprima di MSAL](reference-v2-libraries.md) supporta .NET, JavaScript, iOS e Android
    * Entrambi gli endpoint supportano il middleware del server .NET e Node.js per la protezione delle API e dell'accesso. 

* La maggior parte dell'innovazione, ad esempio i consensi dinamico e incrementale, riguarda l'endpoint v2.0 e le librerie MSAL mentre si continua a supportare l'endpoint v1.0 e le librerie ADAL.

    Nel portale di Azure in precedenza era necessario identificare in modo statico tutti gli ambiti di cui necessitava l'app. Con l'endpoint v2.0 e i portali ad esso associati, è possibile definire in modo statico gli ambiti esattamente come nella versione precedente oppure è possibile richiederli in modo dinamico quando l'app necessità dell'autorizzazione. Il modo dinamico offre una funzionalità opzionale aggiuntiva, vale a dire il consenso incrementale. Il consenso incrementale consente di richiedere un subset di ambiti che sono necessari quando un utente esegue l'autenticazione per la prima volta e richiede ambiti aggiuntivi man mano che si rendono necessari. 
    
    Quando si usa ad esempio un'app per fotocamera in un dispositivo mobile, all'utente viene richiesto di consentire all'app di accedere alla fotocamera e solo dopo che l'utente ha dato il consenso l'app potrà accedere alla fotocamera e scattare una foto.  Quando è pronta per salvare la nuova foto, l'app deve richiedere l'autorizzazione di lettura/scrittura delle foto. 

* Possibili modifiche che causano un'interruzione

    La libreria MSAL è adatta all'uso in un ambiente di produzione. Per questa libreria viene offerto lo stesso supporto a livello di produzione offerto per le librerie di produzione attuali. Nel periodo in cui è disponibile l'anteprima è possibile che vengano apportate modifiche all'API, al formato della cache interna e ad altri meccanismi di questa libreria. Queste modifiche verranno incluse con le correzioni di bug o i miglioramenti delle funzionalità. Possono infatti incidere sull'applicazione. Una modifica al formato della cache, ad esempio, può incidere sugli utenti chiedendo loro di accedere di nuovo. Per aggiornare il codice è possibile che sia necessario modificare l'API. Con la distribuzione della versione a disponibilità generale (GA) verrà richiesto di eseguire l'aggiornamento alla versione GA entro sei mesi, in quanto le applicazioni scritte usando la versione di anteprima della libreria potrebbero non funzionare più.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su v1.0 e v2.0.

* [Informazioni sulla versione 1.0](v1-overview.md)
* [Informazioni su v2.0](v2-overview.md)
