---
title: Creare un'API Web .NET con Azure AD per l'autenticazione e l'autorizzazione| Microsoft Docs
description: Come compilare un'API Web MVC per Node.js che si integra con Azure AD per l'autenticazione e l'autorizzazione.
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 67e74774-1748-43ea-8130-55275a18320f
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 07/17/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 5b080ce3869bcdbbde9724a33433e5f0659b37e4
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76703831"
---
# <a name="quickstart-build-a-net-web-api-that-integrates-with-azure-ad-for-authentication-and-authorization"></a>Avvio rapido: Compilare un'API Web .NET che si integra con Azure AD per l'autenticazione e l'autorizzazione

[Microsoft Identity Platform](v2-overview.md) è un'evoluzione della piattaforma per sviluppatori di Azure Active Directory (Azure AD). Consente agli sviluppatori di creare applicazioni che supportano l'accesso per tutte le identità Microsoft e il recupero di token per chiamare API Microsoft, come Microsoft Graph o API create dagli sviluppatori.

[Microsoft Authentication Library (MSAL)](msal-overview.md) consente agli sviluppatori di acquisire token dall'endpoint di Microsoft Identity Platform per accedere ad API Web protette. Active Directory Authentication Library (ADAL) si integra con l'endpoint di Azure AD per sviluppatori (v1.0), dove MSAL si integra con l'endpoint di Microsoft Identity Platform (v2.0).

Per le nuove API Web, è consigliabile usare Microsoft Identity Platform (v 2.0) e MSAL per acquisire i token e accedere alle API Web protette: [Avvio rapido: Aggiungere l'accesso con Microsoft a un'app Web ASP.NET](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2#calling-an-aspnet-core-web-api-from-a-wpf-application-using-azure-ad-v2)
