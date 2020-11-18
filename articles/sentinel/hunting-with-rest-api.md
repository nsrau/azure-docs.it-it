---
title: Gestire le query di caccia e livestream in Sentinel di Azure con l'API REST | Microsoft Docs
description: Questo articolo descrive in che modo le funzionalità di ricerca di Azure Sentinel consentono di sfruttare Log Analytics ' API REST per gestire le query di caccia e livestream.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/06/2020
ms.author: yelevin
ms.openlocfilehash: cd52f22004bf72f3328d1e6a0d1ec988c2406317
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94660849"
---
# <a name="manage-hunting-and-livestream-queries-in-azure-sentinel-using-rest-api"></a>Gestire le query di caccia e livestream in Sentinel di Azure con l'API REST

Azure Sentinel, che è integrato in Log Analytics di monitoraggio di Azure, consente di usare Log Analytics API REST per gestire le query di caccia e livestream. Questo documento illustra come creare e gestire le query di caccia usando l'API REST.  Le query create in questo modo verranno visualizzate nell'interfaccia utente di Sentinel di Azure.

Per altri dettagli sull' [API delle ricerche salvate](/rest/api/loganalytics/savedsearches), vedere il riferimento all'API REST definitivo.

## <a name="api-examples"></a>Esempi di API

Negli esempi seguenti sostituire questi segnaposto con la sostituzione prescritta nella tabella seguente:

| Segnaposto | Sostituire con |
|-|-|
| **SubscriptionId** | nome della sottoscrizione a cui si sta applicando la query di caccia o livestream. |
| **ResourceGroupName** | nome del gruppo di risorse a cui si sta applicando la query di caccia o livestream. |
| **{savedSearchId}** | ID univoco (GUID) per ogni query di ricerca. |
| **WorkspaceName** | nome dell'area di lavoro Log Analytics che rappresenta la destinazione della query. |
| **DisplayName** | nome visualizzato di propria scelta per la query. |
| **Descrizione** | Descrizione della query di caccia o livestream. |
| **Tattiche** | la controparte dell'ATT pertinente&le tattiche CK che si applicano alla query. |
| **Query** | espressione di query per la query. |
|  

### <a name="example-1"></a>Esempio 1

Questo esempio illustra come creare o aggiornare una query di ricerca per un'area di lavoro di Azure Sentinel specificata.  Per una query Livestream, sostituire *"Category": "Hunting queries"* con *"Category": "livestream queries"* nel **corpo della richiesta**: 

#### <a name="request-header"></a>Intestazione della richiesta

```http
PUT https://management.azure.com/subscriptions/{subscriptionId} _
    /resourcegroups/{resourceGroupName} _
    /providers/Microsoft.OperationalInsights/workspaces/{workspaceName} _
    /savedSearches/{savedSearchId}?api-version=2020-03-01-preview
```

#### <a name="request-body"></a>Corpo della richiesta

```json
{
"properties": {
    “Category”: “Hunting Queries”,
    "DisplayName": "HuntingRule02",
    "Query": "SecurityEvent | where EventID == \"4688\" | where CommandLine contains \"-noni -ep bypass $\"",
    “Tags”: [
        { 
        “Name”: “Description”,
        “Value”: “Test Hunting Query”
        },
        { 
        “Name”: “Tactics”,
        “Value”: “Execution, Discovery”
        }
        ]        
    }
}
```

### <a name="example-2"></a>Esempio 2

Questo esempio illustra come eliminare una query di caccia o livestream per un'area di lavoro di Azure Sentinel specificata:

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId} _
       /resourcegroups/{resourceGroupName} _
       /providers/Microsoft.OperationalInsights/workspaces/{workspaceName} _
       /savedSearches/{savedSearchId}?api-version=2020-03-01-preview
```

### <a name="example-3"></a>Esempio 3

Questo esempio Mostra come recuperare una query di caccia o livestream per una determinata area di lavoro:

```http
GET https://management.azure.com/subscriptions/{subscriptionId} _
    /resourcegroups/{resourceGroupName} _
    /providers/Microsoft.OperationalInsights/workspaces/{workspaceName} _
    /savedSearches/{savedSearchId}?api-version=2020-03-01-preview
```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come gestire le query di caccia e livestream in Azure Sentinel usando l'API Log Analytics. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:

- [Cercare le minacce in modo proattivo](hunting.md)
- [Usare i notebook per eseguire campagne di caccia automatiche](notebooks.md)