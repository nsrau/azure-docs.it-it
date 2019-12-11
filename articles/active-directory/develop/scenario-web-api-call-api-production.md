---
title: Spostare l'API Web che chiama le API Web nell'ambiente di produzione-piattaforma di identità Microsoft | Azure
description: Informazioni su come spostare un'API Web che chiama API Web nell'ambiente di produzione.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0d59a5b2a74c10e36103713725113cbe8c9cc412
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74965170"
---
# <a name="web-api-that-calls-web-apis---move-to-production"></a>API Web che chiama le API Web-passa all'ambiente di produzione

Dopo aver acquisito un token per chiamare le API Web, è possibile spostare l'app nell'ambiente di produzione.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="learn-more"></a>Altre informazioni.

Ora che si conoscono le nozioni di base su come chiamare le API Web dalla propria API Web, si potrebbe essere interessati a questa esercitazione, che descrive il codice usato per creare un'API Web protetta che chiama API Web.

| Esempio | Piattaforma | Description |
|--------|----------|-------------|
| [active-directory-aspnetcore-webapi-tutorial-v2](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph) | API Web ASP.NET Core 2,2, desktop (WPF) | ASP.NET Core 2,2 API Web chiamata Microsoft Graph, chiamata da un'applicazione WPF con la piattaforma di identità Microsoft (v 2.0) |
