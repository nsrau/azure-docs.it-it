---
title: API Web protetta-Panoramica | Azure
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
ms.openlocfilehash: 02bd4b84cc7542714f6db45c12c4b5b13a7fb449
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68852587"
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
