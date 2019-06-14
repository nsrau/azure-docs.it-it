---
title: 'Web API che chiama le API web (passare alla produzione): piattaforma delle identità Microsoft'
description: Informazioni su come creare una web API che le chiamate a valle web API (passare alla produzione).
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
ms.openlocfilehash: 7de892a0851e556c5046fe4b3691f39b42a9d237
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65074756"
---
# <a name="web-api-that-calls-web-apis---move-to-production"></a>API Web che chiama le API - web passare alla produzione

Dopo aver acquisito un token per chiamare le API web, è possibile spostare l'app nell'ambiente di produzione.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="learn-more"></a>Altre informazioni

Dopo avere appreso le nozioni di base di come chiamare le API web da un'API web, potrebbe essere interessante questa esercitazione, che descrive il codice che ha usato per compilare un'API web protetta chiama le API web.

| Esempio | Piattaforma | Descrizione |
|--------|----------|-------------|
| [active-directory-aspnetcore-webapi-tutorial-v2](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph) | 2\.2 API Web ASP.NET Core, Desktop (WPF) | API Web ASP.NET Core 2.2 chiamare Microsoft Graph, stessa chiamata da un'applicazione WPF con la piattaforma delle identità Microsoft (v2.0) |
