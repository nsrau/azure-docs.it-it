---
title: Acquisire il token e chiamare l'API Microsoft Graph con l'identità dell'app console (v1.0) | Microsoft Docs
description: Informazioni su come creare un'applicazione daemon .NET che si integra con Azure AD e chiama le API protette di Azure AD usando OAuth 2.0
services: active-directory
documentationcenter: .net
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 07/17/2019
ms.author: jmprieur
ms.reviewer: ryanwi
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: d79d448f10c0d404516371be19d561bb21c8c0c7
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73149615"
---
# <a name="quickstart-acquire-token-and-call-microsoft-graph-api-with-console-apps-identity-v10"></a>Guida introduttiva: Acquisire il token e chiamare l'API Microsoft Graph con l'identità dell'app console (v1.0) 

[Microsoft Identity Platform](v2-overview.md) è un'evoluzione della piattaforma per sviluppatori di Azure Active Directory (Azure AD). Consente agli sviluppatori di creare applicazioni che supportano l'accesso per tutte le identità Microsoft e il recupero di token per chiamare API Microsoft, come Microsoft Graph o API create dagli sviluppatori.

[Microsoft Authentication Library (MSAL)](msal-overview.md) consente agli sviluppatori di acquisire token dall'endpoint di Microsoft Identity Platform per accedere ad API Web protette. Active Directory Authentication Library (ADAL) si integra con l'endpoint di Azure AD per sviluppatori (v1.0), dove MSAL si integra con l'endpoint di Microsoft Identity Platform (v2.0).

## <a name="next-steps"></a>Passaggi successivi

Per le nuove applicazioni daemon .NET, è consigliabile usare Microsoft Identity Platform (v 2.0) e MSAL per acquisire i token e accedere alle API Web protette: [Guida introduttiva: Acquisire un token e chiamare l'API Microsoft Graph da un'app console usando l'identità di un'app](quickstart-v2-netcore-daemon.md).
