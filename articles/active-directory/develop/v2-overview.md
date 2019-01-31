---
title: Informazioni su v2.0 | Azure
description: Informazioni sulla piattaforma e sull'endpoint v2.0.
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
ms.openlocfilehash: 1f707351f5f5887155148b58d5ed145dde785b40
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/28/2019
ms.locfileid: "55095413"
---
# <a name="about-v20"></a>Informazioni su v2.0

La piattaforma e l'endpoint v2.0 sono stati in fase di anteprima e sono stati continuamente migliorati. Oggi, gli scenari di applicazione a singola pagina (SPA) JavaScript includono funzionalità complete invitiamo l'utente a usare MSAL. js per compilare applicazioni basate su browser e fornire commenti per consentirci di aggiornare lo stato da anteprima a disponibilità generale (GA).

> [!NOTE]
> MSAL Android, iOS e .NET presentano ancora le funzionalità in fase di sviluppo. È possibile utilizzarli per compilare applicazioni e inviare commenti e suggerimenti.

L'esperienza [Registrazioni app (anteprima)](quickstart-register-app.md) del portale di Azure è stata aggiornata in modo significativo e adesso include tutte le applicazioni compilate con ADAL o MSAL e per migliorare l'usabilità.

In passato, gli sviluppatori di applicazioni che intendevano supportare sia gli account Microsoft personali che quelli aziendali da Azure Active Directory (Azure AD) dovevano eseguire l'integrazione con due sistemi separati. L'endpoint 2.0 e la piattaforma offrono una versione di API di autenticazione che semplifica questo processo. Consente di accedere con entrambi i tipi di account tramite un'unica integrazione. Le applicazioni che usano l'endpoint di versione 2.0 possono anche usare le API REST dalle [API Microsoft Graph](https://developer.microsoft.com/graph) tramite entrambi i tipi di account.

## <a name="getting-started"></a>Introduzione

Scegliere dall'elenco seguente la piattaforma preferita per compilare un'applicazione usando le librerie e i framework open source Microsoft:

[!INCLUDE [v2.0 endpoint platforms](../../../includes/active-directory-v2-quickstart-table.md)]

## <a name="learn-more-about-the-v20-endpoint-and-platform"></a>Altre informazioni sull'endpoint e la piattaforma di versione 2.0

Altre informazioni sulle possibilità di utilizzo dell'endpoint di Azure AD versione 2.0:

* Individuare i [tipi di applicazioni che è possibile compilare con l'endpoint di Azure AD versione 2.0](v2-app-types.md).
* Comprendere [le limitazioni, le restrizioni e i vincoli](active-directory-v2-limitations.md) dell'endpoint di Azure AD versione 2.0.

## <a name="additional-resources"></a>Risorse aggiuntive

Approfondimento su v2.0:

* [Informazioni su Microsoft Identity Platform](about-microsoft-identity-platform.md)
* [Informazioni di riferimento sui protocolli della versione 2.0](active-directory-v2-protocols.md)
* [Riferimento ai token di accesso](access-tokens.md)
* [Informazioni di riferimento sui token ID](id-tokens.md)
* [Informazioni di riferimento sulle librerie di autenticazione di v2.0](reference-v2-libraries.md)
* [Autorizzazioni e consenso in v2.0](v2-permissions-and-consent.md)
* [API Microsoft Graph](https://developer.microsoft.com/graph)

> [!NOTE]
> Se occorre effettuare l'accesso con un account aziendale o dell'istituto di istruzione da Azure Active Directory, iniziare con la [Guida di Azure AD per sviluppatori](v1-overview.md). L'uso dell'endpoint v2.0 è destinato agli sviluppatori che devono eseguire l'accesso agli account personali di Microsoft in modo esplicito.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
