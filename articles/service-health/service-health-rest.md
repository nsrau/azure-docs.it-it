---
title: Ottenere gli eventi di integrità per le risorse tramite l'API REST di Azure | Microsoft Docs
description: Usare le API REST di Azure per ottenere gli eventi di integrità per le risorse di Azure.
author: stephbaron
ms.author: stbaron
ms.service: service-health
ms.custom: REST
ms.topic: article
ms.date: 06/06/2017
ms.openlocfilehash: 6d83aed6910127ceb34b9a694f48ca9c19ab6d18
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60790913"
---
# <a name="get-resource-health-using-the-rest-api"></a>Ottenere Integrità risorse tramite l'API REST 

Questo articolo di esempio illustra come recuperare un elenco di eventi di integrità per le risorse di Azure nella sottoscrizione tramite l'[API REST di Azure](/rest/api/azure/).

La documentazione di riferimento completa e altri esempi relativi all'API REST sono disponibili nelle [informazioni di riferimento REST di Monitoraggio di Azure](/rest/api/monitor). 

## <a name="build-the-request"></a>Compilare la richiesta

Usare la richiesta HTTP `GET` seguente per elencare gli eventi di integrità per la sottoscrizione per l'intervallo di tempo che intercorre tra `2018-05-16` e `2018-06-20`.

```http
https://management.azure.com/subscriptions/{subscription-id}/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&%24filter=eventTimestamp%20ge%20'2018-05-16T04%3A36%3A37.6407898Z'%20and%20eventTimestamp%20le%20'2018-06-20T04%3A36%3A37.6407898Z'
```

### <a name="request-headers"></a>Intestazioni della richiesta

Gli argomenti seguenti sono obbligatori: 

|Intestazione della richiesta|Descrizione|  
|--------------------|-----------------|  
|*Content-Type:*|Richiesto. Impostare su `application/json`.|  
|*Authorization:*|Richiesto. Impostare su un [token di accesso](/rest/api/azure/#authorization-code-grant-interactive-clients) `Bearer` valido. |  

### <a name="uri-parameters"></a>Parametri URI

| NOME | Descrizione |
| :--- | :---------- |
| subscriptionId | ID sottoscrizione che identifica una sottoscrizione di Azure. Se sono disponibili più sottoscrizioni, vedere [Uso di più sottoscrizioni](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest). |
| api-version | Versione dell'API da usare per la richiesta.<br /><br /> Questo documento illustra la versione api-version `2015-04-01`, inclusa nell'URL precedente.  |
| $filter | L'opzione filtro per ridurre il set di risultati restituiti. I modelli consentiti per questo parametro sono disponibili [nel riferimento per l'operazione Log attività](/rest/api/monitor/activitylogs/list#uri-parameters). Nell'esempio illustrato vengono acquisiti tutti gli eventi nell'intervallo di tempo compreso tra 2018-05-16 e 20-06-2018 |
| &nbsp; | &nbsp; |

### <a name="request-body"></a>Corpo della richiesta

Per questa operazione non è necessario alcun corpo della richiesta.

## <a name="handle-the-response"></a>Gestire la risposta

Il codice di stato 200 viene restituito con un elenco di valori di evento di integrità corrispondente al parametro di filtro, insieme a un `nextlink` URI per recuperare la pagina successiva di risultati.

## <a name="example-response"></a>Risposta di esempio 

```json
{
  "value": [
    {
      "correlationId": "1e121103-0ba6-4300-ac9d-952bb5d0c80f",
      "eventName": {
        "value": "EndRequest",
        "localizedValue": "End request"
      },
      "id": "/subscriptions/{subscription-id}/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841/events/44ade6b4-3813-45e6-ae27-7420a95fa2f8/ticks/635574752669792776",
      "resourceGroupName": "MSSupportGroup",
      "resourceProviderName": {
        "value": "microsoft.support",
        "localizedValue": "microsoft.support"
      },
      "operationName": {
        "value": "microsoft.support/supporttickets/write",
        "localizedValue": "microsoft.support/supporttickets/write"
      },
      "status": {
        "value": "Succeeded",
        "localizedValue": "Succeeded"
      },
      "eventTimestamp": "2015-01-21T22:14:26.9792776Z",
      "submissionTimestamp": "2015-01-21T22:14:39.9936304Z",
      "level": "Informational"
    }
  ],
  "nextLink": "https://management.azure.com/########-####-####-####-############$skiptoken=######"
}
```
