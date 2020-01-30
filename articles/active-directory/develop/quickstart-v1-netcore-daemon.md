---
title: 'Acquisire un token e chiamare Microsoft Graph: console .NET Core (v1.0) | Azure'
description: Creare un'applicazione daemon .NET che si integra con Azure AD e chiama le API protette di Azure AD usando OAuth 2.0
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 07/17/2019
ms.author: jmprieur
ms.reviewer: ryanwi
ms.custom: aaddev
ms.openlocfilehash: bdcf0632110df23cbe91040f41535f51fb06ba44
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76703797"
---
# <a name="quickstart-acquire-token-and-call-microsoft-graph-using-console-apps-identity-v10"></a>Avvio rapido: Acquisire un token e chiamare Microsoft Graph con l'identità dell'app console (v1.0)

[Microsoft Identity Platform](v2-overview.md) è un'evoluzione della piattaforma per sviluppatori di Azure Active Directory (Azure AD). Consente agli sviluppatori di creare applicazioni che supportano l'accesso per tutte le identità Microsoft e il recupero di token per chiamare API Microsoft, come Microsoft Graph o API create dagli sviluppatori.

[Microsoft Authentication Library (MSAL)](msal-overview.md) consente agli sviluppatori di acquisire token dall'endpoint di Microsoft Identity Platform per accedere ad API Web protette. Active Directory Authentication Library (ADAL) si integra con l'endpoint di Azure AD per sviluppatori (v1.0), dove MSAL si integra con l'endpoint di Microsoft Identity Platform (v2.0).

## <a name="next-steps"></a>Passaggi successivi

Per le nuove applicazioni daemon .NET, è consigliabile usare Microsoft Identity Platform (v 2.0) e MSAL per acquisire i token e accedere alle API Web protette: [Avvio rapido: Acquisire un token e chiamare l'API Microsoft Graph da un'app console usando l'identità di un'app](quickstart-v2-netcore-daemon.md).
