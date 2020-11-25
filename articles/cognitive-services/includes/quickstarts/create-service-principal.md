---
title: Creare un'entità servizio di Azure
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/01/2020
ms.author: pafarley
ms.openlocfilehash: 99487fe962228f775582aa2f0a6fb60fe52862ac
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95098613"
---
## <a name="create-an-azure-service-principal"></a>Creare un'entità servizio di Azure

Per fare in modo che l'applicazione interagisca con l'account Azure, è necessaria un'entità servizio di Azure per gestire le autorizzazioni. Seguire le istruzioni riportate in [Creare un'entità servizio di Azure](/powershell/azure/create-azure-service-principal-azureps?view=azps-4.4.0&viewFallbackFrom=azps-3.3.0).

Quando si crea un'entità servizio, si noterà che include un valore segreto, un ID e un ID applicazione. Salvare l'ID applicazione e il segreto in un percorso temporaneo per i passaggi successivi.