---
title: API Web protetta - Panoramica
titleSuffix: Microsoft identity platform
description: Informazioni su come creare un'API Web protetta (panoramica).
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
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: abe4ecf77e7a65251830ff822b15f79291471202
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76773345"
---
# <a name="scenario-protected-web-api"></a>Scenario: API Web protetta

In questo scenario, si apprenderà come esporre un'API web. Si apprenderà inoltre come proteggere l'API Web in modo che solo gli utenti autenticati possano accedervi.

Per usare l'API Web, è necessario abilitare gli utenti autenticati con account aziendali e dell'istituto di istruzione oppure abilitare gli account personali Microsoft.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="specifics"></a>Specifiche

Ecco le informazioni specifiche che è necessario conoscere per proteggere le API Web:Here is specific information you need to know to protect web APIs:

- La registrazione dell'app deve esporre almeno un ambito. La versione del token accettata dall'API Web dipende dal gruppo di destinatari di accesso.
- La configurazione del codice per l'API Web deve convalidare il token utilizzato quando viene chiamata l'API Web.The code configuration for the web API must validate the token used when the web API is called.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Registrazione delle app](scenario-protected-web-api-app-registration.md)
