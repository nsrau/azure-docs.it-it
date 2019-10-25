---
title: API Web protetta-Panoramica
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 64418138f9eff61cc94a57be4dc5e2625e5fdebb
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72803678"
---
# <a name="scenario-protected-web-api"></a>Scenario: API Web protetta

In questo scenario verrà illustrato come è possibile esporre un'API Web e come proteggerla in modo che solo gli utenti autenticati possano accedere all'API. Per usare l'API Web, è possibile abilitare gli utenti autenticati con account aziendali o dell'Istituto di istruzione o con account personali Microsoft personali.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="specifics"></a>Specifiche

Di seguito sono riportate alcune specifiche che è necessario tenere presente per proteggere le API Web:

- La registrazione dell'app deve esporre almeno un ambito. La versione del token accettata dall'API Web dipende dal pubblico di accesso.
- La configurazione del codice per l'API Web deve convalidare il token usato quando si chiama l'API Web.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Registrazione delle app](scenario-protected-web-api-app-registration.md)
