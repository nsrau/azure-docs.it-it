---
title: Creare un'app iOS integrata con Azure AD per l'accesso con OAuth 2.0 | Microsoft Docs
description: Informazioni sull'accesso utenti e chiamata dell'API Microsoft Graph da un'app iOS.
services: active-directory
documentationcenter: ios
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 42303177-9566-48ed-8abb-279fcf1e6ddb
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: quickstart
ms.date: 10/25/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: brandwe
ms.collection: M365-identity-device-management
ms.openlocfilehash: 58b78461c9e585cbba4902799543dadb0f013e85
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73149674"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-ios-app-v10"></a>Guida introduttiva: Accesso utenti e chiamata dell'API Microsoft Graph da un'app iOS (v1.0)

[Microsoft Identity Platform](v2-overview.md) è un'evoluzione della piattaforma per sviluppatori di Azure Active Directory (Azure AD). Consente agli sviluppatori di creare applicazioni che supportano l'accesso per tutte le identità Microsoft e il recupero di token per chiamare API Microsoft, come Microsoft Graph o API create dagli sviluppatori.

[Microsoft Authentication Library (MSAL)](msal-overview.md) consente agli sviluppatori di acquisire token dall'endpoint di Microsoft Identity Platform per accedere ad API Web protette. Active Directory Authentication Library (ADAL) si integra con l'endpoint di Azure AD per sviluppatori (v1.0), dove MSAL si integra con l'endpoint di Microsoft Identity Platform (v2.0).

Per le nuove applicazioni macOS e iOS, è consigliabile usare Microsoft Identity Platform (v 2.0) e MSAL per acquisire i token e accedere alle API Web protette: [Guida introduttiva: Accesso utenti e chiamata dell'API Microsoft Graph da un'app iOS o macOS](quickstart-v2-ios.md).
