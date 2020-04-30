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
ms.openlocfilehash: 078ed3e5f3a19bfa4350f9edea858b717c69e3f8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81537152"
---
# <a name="a-web-api-that-calls-web-apis-move-to-production"></a>Un'API Web che chiama le API Web: passa all'ambiente di produzione

Dopo aver acquisito un token per chiamare le API Web, è possibile spostare l'app nell'ambiente di produzione.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="learn-more"></a>Altre informazioni

Ora che si conoscono le nozioni di base su come chiamare le API Web dalla propria API Web, si potrebbe essere interessati all'esercitazione seguente, in cui viene descritto il codice usato per creare un'API Web protetta che chiama le API Web.

| Esempio | Piattaforma | Descrizione |
|--------|----------|-------------|
| [Active-Directory-aspnetcore-WebAPI-tutorial-v2](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph) | API Web ASP.NET Core 2,2, desktop (WPF) | ASP.NET Core 2,2 chiama API Web Microsoft Graph, che viene chiamata da un'applicazione WPF tramite la piattaforma di identità Microsoft (v 2.0). |
