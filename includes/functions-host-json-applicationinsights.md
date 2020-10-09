---
title: includere file
description: includere file
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/19/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: a925076dfccd30c73febb2aadc8692667ea01525
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "76279415"
---
Controlla le [funzionalità di campionamento in Application Insights](../articles/azure-functions/functions-monitoring.md#configure-sampling).

```json
{
    "applicationInsights": {
        "sampling": {
          "isEnabled": true,
          "maxTelemetryItemsPerSecond" : 5
        }
    }
}
```

|Proprietà  |Predefinito | Descrizione |
|---------|---------|---------| 
|isEnabled|true|Abilita o disabilita il campionamento.| 
|maxTelemetryItemsPerSecond|5|La soglia oltre la quale viene avviato il campionamento.| 
