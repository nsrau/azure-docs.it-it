---
title: Azure AD V2 Introduzione desktop di Windows-config
description: Come un'applicazione .NET per Windows Desktop (XAML) può ottenere un token di accesso e chiamare un'API protetta dall'endpoint di Azure Active Directory v2.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/29/2020
ms.author: ryanwi
ms.custom: aaddev
ms.openlocfilehash: d82f9beecb1b558fca094c31f8c6718c990debd1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "80885770"
---
# <a name="add-the-applications-registration-information-to-your-app"></a>Aggiungere le informazioni di registrazione dell'applicazione all'app
In questo passaggio è necessario aggiungere l'ID applicazione al progetto.

1.  Aprire `App.xaml.cs` e sostituire la riga contenente il `ClientId` con:

```csharp
private static string ClientId = "[Enter the application Id here]";
```

### <a name="what-is-next"></a>Passaggi successivi

[!INCLUDE [Test and Validate](../../../../includes/active-directory-develop-guidedsetup-windesktop-test.md)]
