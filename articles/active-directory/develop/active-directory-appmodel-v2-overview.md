---
title: Endpoint v2.0 di Azure Active Directory | Microsoft Docs
description: Introduzione alla compilazione di applicazioni con accesso sia tramite account Microsoft che tramite Azure Active Directory.
services: active-directory
documentationcenter: 
author: dstrockis
manager: mtillman
editor: 
ms.assetid: 2dee579f-fdf6-474b-bc2c-016c931eaa27
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: 446e39f57448cbc0cd9a6f93a388159d7eca5863
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="sign-in-microsoft-account-and-azure-active-directory-users-in-a-single-application"></a>Accesso di utenti con account Microsoft e con account Azure Active Directory a un'unica applicazione
In passato, gli sviluppatori di applicazioni che intendevano supportare sia gli account Microsoft personali che quelli aziendali da Azure Active Directory dovevano eseguire l'integrazione con due sistemi separati. L'endpoint di Azure Active Directory (Azure AD) versione 2.0 introduce una nuova versione dell'API di autenticazione che semplifica questo processo. L'endpoint consente di accedere con entrambi i tipi di account tramite un'unica integrazione. Le applicazioni che usano l'endpoint di Azure AD versione 2.0 possono anche usare le API REST dalle [API Microsoft Graph](https://graph.microsoft.io) tramite entrambi i tipi di account.

## <a name="getting-started"></a>Introduzione
Scegliere dall'elenco seguente la piattaforma preferita per compilare un'applicazione usando le librerie e i framework open source Microsoft. È anche possibile usare i protocolli OAuth 2.0 e OpenID Connect per inviare e ricevere messaggi di protocollo direttamente senza usare una libreria di autenticazione.
<br />

[!INCLUDE [Azure AD v2.0 endpoint platforms](../../../includes/active-directory-v2-quickstart-table.md)]

## <a name="learn-more-about-the-azure-ad-v20-endpoint"></a>Altre informazioni sull'endpoint di Azure AD versione 2.0
Altre informazioni sulle possibilità di utilizzo dell'endpoint di Azure AD versione 2.0:

* Individuare i [tipi di applicazioni che è possibile compilare con l'endpoint di Azure AD versione 2.0](active-directory-v2-flows.md).
* Comprendere [le limitazioni, le restrizioni e i vincoli](active-directory-v2-limitations.md) dell'endpoint di Azure AD versione 2.0.
* Per una panoramica dell'endpoint di Azure AD versione 2.0, guardare questo video:

>[!VIDEO https://channel9.msdn.com/Events/Build/2017/P4031/player]

## <a name="additional-resources"></a>Risorse aggiuntive
Per approfondire la conoscenza della piattaforma endpoint di Azure AD versione 2.0:

* [Informazioni di riferimento sui protocolli di Azure AD versione 2.0](active-directory-v2-protocols.md)
* [Informazioni di riferimento sui token di Azure AD versione 2.0](active-directory-v2-tokens.md)
* [Informazioni di riferimento sulle librerie di autenticazione di Azure AD versione 2.0](active-directory-v2-libraries.md)
* [Ambiti e consenso nell'endpoint di Azure AD versione 2.0](active-directory-v2-scopes.md)
* [API Microsoft Graph](https://graph.microsoft.io)

> [!NOTE]
> Se occorre effettuare l'accesso con un account aziendale o dell'istituto di istruzione da Azure Active Directory, iniziare con la [Guida di Azure AD per sviluppatori](active-directory-developers-guide.md). L'uso dell'endpoint di Azure AD versione 2.0 è destinato agli sviluppatori che devono eseguire l'accesso ad account personali di Microsoft in modo esplicito.

[!INCLUDE  [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
