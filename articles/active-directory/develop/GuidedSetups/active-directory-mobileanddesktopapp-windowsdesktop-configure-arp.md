---
title: Introduzione a Windows Desktop per Azure AD v2 - Configurazione | Microsoft Docs
description: Come un'applicazione .NET per Windows Desktop (XAML) può ottenere un token di accesso e chiamare un'API protetta dall'endpoint di Azure Active Directory v2.
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/29/2020
ms.author: ryanwi
ms.custom: aaddev
ms.openlocfilehash: 4c757b52e7378bffe4a4954022b131b772a337cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76897677"
---
# <a name="add-the-applications-registration-information-to-your-app"></a>Aggiungere le informazioni di registrazione dell'applicazione all'app
In questo passaggio è necessario aggiungere l'ID applicazione al progetto.

1.  Aprire `App.xaml.cs` e sostituire la riga contenente il `ClientId` con:

```csharp
private static string ClientId = "[Enter the application Id here]";
```

### <a name="what-is-next"></a>Passaggi successivi

[!INCLUDE [Test and Validate](../../../../includes/active-directory-develop-guidedsetup-windesktop-test.md)]
