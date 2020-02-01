---
title: Schemi di rilevamento personalizzati per i messaggi B2B
description: Creare schemi di rilevamento personalizzati per monitorare i messaggi B2B in app per la logica di Azure
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/01/2020
ms.openlocfilehash: c82f9cbfaf2e23ddaa5e4b05f4aac4795d3e16a9
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76903061"
---
# <a name="create-custom-tracking-schemas-that-monitor-end-to-end-workflows-in-azure-logic-a"></a>Creare schemi di rilevamento personalizzati che monitorano i flussi di lavoro end-to-end nella logica di Azure A

App per la logica di Azure include il rilevamento incorporato che è possibile abilitare per parti del flusso di lavoro. Tuttavia, è possibile configurare il rilevamento personalizzato che registra gli eventi dall'inizio alla fine dei flussi di lavoro, ad esempio i flussi di lavoro che includono un'app per la logica, BizTalk Server, SQL Server o qualsiasi altro livello. Questo articolo fornisce un codice personalizzato che è possibile usare nei livelli all'esterno di app per la logica.

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

| Proprietà | Obbligatorio | Tipo | Description |
|----------|----------|------|-------------|
| sourceType | Sì | string | Tipo di origine dell'esecuzione con questi valori consentiti: `Microsoft.Logic/workflows`, `custom` |
| source | Sì | String o JToken | Se il tipo di origine è `Microsoft.Logic/workflows`, le informazioni sull'origine devono seguire questo schema. Se il tipo di origine è `custom`, lo schema è JToken. |
| systemId | Sì | string | ID di sistema dell'app per la logica |
| runId | Sì | string | ID esecuzione App per la logica |
| operationName | Sì | string | Nome dell'operazione, ad esempio azione o trigger |
| repeatItemScopeName | Sì | string | Ripetere il nome dell'elemento se l'azione si trova all'interno di un ciclo `foreach`o `until` |
| repeatItemIndex | Sì | Integer | Indica che l'azione si trova all'interno di un ciclo `foreach` o `until` e è il numero di indice dell'elemento ripetuto. |
| trackingId | No | string | ID di traccia per la correlazione dei messaggi |
| correlationId | No | string | ID di correlazione per correlare i messaggi |
| clientRequestId | No | string | Il client può popolare questa proprietà per correlare i messaggi |
| eventLevel | Sì | string | Livello dell'evento |
| eventTime | Sì | Data e ora | Ora dell'evento in formato UTC: *aaaa-mm-ggThh: mm: SS. 00000Z* |
| recordType | Sì | string | Tipo di record di rilevamento solo con questo valore consentito: `custom` |
| record | Sì | JToken | Tipo di record personalizzato con solo formato JToken |
|||||

## <a name="b2b-protocol-tracking-schemas"></a>Schemi di rilevamento per il protocollo B2B

Per informazioni sugli schemi di rilevamento per il protocollo B2B, vedere:

* [Schemi di rilevamento AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Schemi di rilevamento X12](logic-apps-track-integration-account-x12-tracking-schema.md)

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sul [monitoraggio dei messaggi B2B con i log di monitoraggio di Azure](../logic-apps/monitor-b2b-messages-log-analytics.md)