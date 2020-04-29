---
title: includere il file
description: File di inclusione
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/19/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 9c51ce726545d1c64d69c86c36fc69ea43c3b882
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76279291"
---
Specifica il numero di chiamate di funzione che vengono aggregate quando [si esegue il calcolo della metrica per Application Insights](../articles/azure-functions/functions-monitoring.md#configure-the-aggregator). 

```json
{
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    }
}
```

|Proprietà |Predefinito  | Descrizione |
|---------|---------|---------| 
|batchSize|1000|Numero massimo di richieste da aggregare.| 
|flushTimeout|00:00:30|Intervallo massimo da aggregare.| 

Le chiamate di funzione vengono aggregate quando il primo dei due limiti viene raggiunto.
