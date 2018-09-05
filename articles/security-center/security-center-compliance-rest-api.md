---
title: Monitoraggio della conformità del Centro sicurezza di Azure tramite l'API REST | Microsoft Docs
description: Esaminare i risultati delle analisi di conformità automatizzate tramite l'API REST del Centro sicurezza di Azure.
services: security-center
documentationcenter: na
author: rloutlaw
manager: angerobe
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/23/2018
ms.author: rloutlaw
ms.openlocfilehash: 651d7737258f1b1b17c8392a09882388ddf95635
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/24/2018
ms.locfileid: "42819196"
---
# <a name="review-security-center-compliance-results-using-the-azure-rest-apis"></a>Esaminare i risultati di conformità del Centro sicurezza tramite le API REST di Azure

Questo articolo illustra come recuperare le informazioni sulla conformità della sicurezza per un elenco di sottoscrizioni tramite le API REST di Azure.

## <a name="review-compliance-for-each-subscription"></a>Esaminare la conformità per ogni sottoscrizione

Nell'esempio seguente vengono ottenute le informazioni della valutazione della sicurezza per una conformità e una sottoscrizione specifiche usando l'operazione [Recuperare conformità](/rest/api/securitycenter/compliances/get).

```http
GET https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Security/compliances/{complianceName}?api-version=2017-08-01-preview
```

Il parametro `{complianceName}` è obbligatorio e deve contenere il nome della conformità valutata per `{subscription-id}`. L'output conterrà i risultati della valutazione, ad esempio:

```json
{
...
  "properties": {
    "assessmentResult": [
      {
        "segmentType": "Compliant",
        "percentage": 77.777777777777786
      }
    ],
    "resourceCount": 18,
    "assessmentTimestampUtcDate": "2018-01-01T00:00:00Z"
  }
}
```

## <a name="review-compliance-for-multiple-subscriptions"></a>Verificare la conformità di più sottoscrizioni

Per ottenere i dati per più sottoscrizioni, eseguire la chiamata seguente per ottenere prima un elenco delle sottoscrizioni tramite l'operazione [Elencare sottoscrizioni](/rest/api/resources/subscriptions/list). Quindi richiamare l'operazione [Recuperare conformità](/rest/api/securitycenter/compliances/get) per ogni ID sottoscrizione restituito.

La chiamata all'API è:

```http
GET https://management.azure.com/subscriptions?api-version=2016-06-01
```

che restituisce una matrice di valori come il seguente. Usare i valori subscriptionId nella chiamata riportata sopra per rivedere le informazioni di conformità per tutte le sottoscrizioni.

```json
"value": [
    {
      "id": "/subscriptions/{subscription-id}",
      "subscriptionId": "{subscription-id}",
      "displayName": "Demo subscription",
      ...
    }
```






