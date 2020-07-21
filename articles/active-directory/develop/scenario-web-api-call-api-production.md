---
title: Spostare l'API Web che chiama le API Web nell'ambiente di produzione-piattaforma di identità Microsoft | Azure
description: Informazioni su come spostare un'API Web che chiama API Web nell'ambiente di produzione.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: aea3a173fd07b66021d35142a84499ae9c66c014
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86518199"
---
# <a name="a-web-api-that-calls-web-apis-move-to-production"></a>Un'API Web che chiama le API Web: passa all'ambiente di produzione

Dopo aver acquisito un token per chiamare le API Web, è possibile spostare l'app nell'ambiente di produzione.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="learn-more"></a>Altre informazioni

Ora che si conoscono le nozioni di base su come chiamare le API Web dalla propria API Web, si potrebbe essere interessati all'esercitazione seguente, in cui viene descritto il codice usato per creare un'API Web protetta che chiama le API Web.

| Esempio | Piattaforma | Descrizione |
|--------|----------|-------------|
| [Active-Directory-aspnetcore-WebAPI-tutorial-v2](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph) capitolo 1 | API Web di ASP.NET Core, desktop (WPF) | ASP.NET Core le chiamate all'API Web Microsoft Graph, che viene chiamata da un'applicazione WPF tramite la piattaforma Microsoft Identity (v 2.0). |
