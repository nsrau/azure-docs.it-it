---
title: Protected Web API - Panoramica | Azure
description: Informazioni su come creare un'API (panoramica) web protetta.
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
ms.openlocfilehash: c5bc0075e6604bd1f94531040f2e4a0628e70667
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65074891"
---
# <a name="scenario-protected-web-api"></a>Scenario: API Web protetta

In questo scenario verrà illustrato come esporre un'API web e come è possibile proteggerli in modo che solo gli utenti autenticati possono accedere all'API. È opportuno consentire agli utenti autenticati con lavoro e dell'istituto di istruzione o account personali Microsoft a usare l'API web.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="specifics"></a>Specifiche

Ecco alcuni aspetti specifici che è necessario conoscere per proteggere le API web:

- La registrazione dell'app deve esporre almeno un ambito. La versione del token accettata dall'API web dipende dal segno nel gruppo di destinatari.
- La configurazione del codice per l'API web deve convalidare il token viene usato quando si chiama l'API web.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Registrazione delle app](scenario-protected-web-api-app-registration.md)
