---
title: Concedere l'accesso agli utenti e chiamare le API Microsoft Graph da Desktop .NET (WPF) | Azure
description: Informazioni su come compilare un'applicazione Windows Desktop .NET che si integra con Azure AD per l'accesso e chiama le API protette di Azure AD usando OAuth 2.0.
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: ed33574f-6fa3-402c-b030-fae76fba84e1
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 07/17/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: c35e44dff321c4a2f568855dacd827001a57f64a
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76703814"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-a-net-desktop-wpf-app"></a>Avvio rapido: Accesso utenti e chiamata dell'API Microsoft Graph da un'app Desktop .NET (WPF)

[Microsoft Identity Platform](v2-overview.md) è un'evoluzione della piattaforma per sviluppatori di Azure Active Directory (Azure AD). Consente agli sviluppatori di creare applicazioni che supportano l'accesso per tutte le identità Microsoft e il recupero di token per chiamare API Microsoft, come Microsoft Graph o API create dagli sviluppatori.

[Microsoft Authentication Library (MSAL)](msal-overview.md) consente agli sviluppatori di acquisire token dall'endpoint di Microsoft Identity Platform per accedere ad API Web protette. Active Directory Authentication Library (ADAL) si integra con l'endpoint di Azure AD per sviluppatori (v1.0), dove MSAL si integra con l'endpoint di Microsoft Identity Platform (v2.0).

Per le nuove applicazioni desktop, è consigliabile usare Microsoft Identity Platform (v 2.0) e MSAL per acquisire i token e accedere alle API Web protette: [Avvio rapido: Acquisire un token e chiamare l'API Microsoft Graph da un'app desktop Windows](quickstart-v2-windows-desktop.md)
