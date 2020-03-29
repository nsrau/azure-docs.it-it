---
title: Schemi di rilevamento personalizzati per i messaggi B2BCustom tracking schemas for B2B messages
description: Creare schemi di rilevamento personalizzati per monitorare i messaggi B2B nelle app per la logica di AzureCreate custom tracking schemas to monitor B2B messages in Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/01/2020
ms.openlocfilehash: c82f9cbfaf2e23ddaa5e4b05f4aac4795d3e16a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76903061"
---
# <a name="create-custom-tracking-schemas-that-monitor-end-to-end-workflows-in-azure-logic-a"></a>Creare schemi di rilevamento personalizzati che monitorino i flussi di lavoro end-to-end in Azure Logic ACreate custom tracking schemas that monitor end-to-end workflows in Azure Logic A

App per la logica di Azure include il rilevamento incorporato che è possibile abilitare per parti del flusso di lavoro. Tuttavia, è possibile impostare il rilevamento personalizzato che registra gli eventi dall'inizio alla fine dei flussi di lavoro, ad esempio i flussi di lavoro che includono un'app per la logica, BizTalk Server, SQL Server o qualsiasi altro livello. Questo articolo fornisce un codice personalizzato che è possibile usare nei livelli all'esterno di app per la logica.

## <a name="custom-tracking-schema"></a>Schema di rilevamento personalizzato

```json
{
   "sourceType": "",
   "source": {
      "workflow": {
         "systemId": ""
      },
      "runInstance": {
         "runId": ""
      },
      "operation": {
         "operationName": "",
         "repeatItemScopeName": "",
         "repeatItemIndex": ,
         "trackingId": "",
         "correlationId": "",
         "clientRequestId": ""
      }
   },
   "events": [
      {
         "eventLevel": "",
         "eventTime": "",
         "recordType": "",
         "record": {}
      }
   ]
}
```

| Proprietà | Obbligatoria | Type | Descrizione |
|----------|----------|------|-------------|
| sourceType | Sì | string | Tipo di origine di esecuzione `Microsoft.Logic/workflows`con i seguenti valori consentiti: ,`custom` |
| source | Sì | Stringa o JToken | Se il tipo `Microsoft.Logic/workflows`di origine è , le informazioni di origine devono seguire questo schema. Se il tipo `custom`di origine è , lo schema è un JToken. |
| systemId | Sì | string | ID di sistema dell'app per la logica |
| runId | Sì | string | ID di esecuzione dell'app per la logica |
| operationName | Sì | string | Nome dell'operazione, ad esempio azione o trigger |
| repeatItemScopeName | Sì | string | Ripetere il nome dell'elemento `foreach` `until` se l'azione si trova all'interno di un ciclo o |
| repeatItemIndex | Sì | Integer | Indica che l'azione `foreach` si `until` trova all'interno di un ciclo o ed è il numero di indice dell'elemento ripetuto. |
| trackingId | No | string | ID di monitoraggio per correlare i messaggi |
| correlationId | No | string | ID di correlazione per correlare i messaggi |
| clientRequestId | No | string | Il client può popolare questa proprietà per correlare i messaggi |
| eventLevel | Sì | string | Livello dell'evento |
| eventTime | Sì | Datetime | Ora dell'evento nel formato UTC: *AAAA-MM-GGTHH:MM:SS.00000* |
| recordType | Sì | string | Tipo del track record con questo valore consentito solo:`custom` |
| record | Sì | JToken (informazioni in questo gis | Tipo di record personalizzato solo con formato JToken |
|||||

## <a name="b2b-protocol-tracking-schemas"></a>Schemi di rilevamento per il protocollo B2B

Per informazioni sugli schemi di rilevamento per il protocollo B2B, vedere:

* [Schemi di rilevamento AS2AS2 tracking schemas](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Schemi di rilevamento X12X12 tracking schemas](logic-apps-track-integration-account-x12-tracking-schema.md)

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sul [monitoraggio dei messaggi B2B con i log](../logic-apps/monitor-b2b-messages-log-analytics.md) di Monitoraggio di AzureLearn more about monitoring B2B messages with Azure Monitor logs