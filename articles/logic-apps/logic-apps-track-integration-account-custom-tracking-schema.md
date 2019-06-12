---
title: Schemi di rilevamento personalizzati per i messaggi B2B - App per la logica di Azure | Microsoft Docs
description: Creare schemi di rilevamento personalizzati che consentono di monitorare i messaggi B2B negli account di integrazione per App per la logica di Azure con Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: 433ae852-a833-44d3-a3c3-14cca33403a2
ms.date: 01/27/2017
ms.openlocfilehash: f919e9a7cca210fa5920bcc6bed05a9a41fba8bf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60847187"
---
# <a name="create-custom-tracking-schemas-that-monitor-end-to-end-workflows-in-azure-logic-apps"></a>Creare schemi di rilevamento personalizzati che consentono di monitorare i flussi di lavoro end-to-end in App per la logica di Azure

È disponibile un rilevamento predefinito che è possibile abilitare per diverse parti del flusso di lavoro Business to Business, ad esempio il rilevamento dei messaggi AS2 o X12. Quando si creano flussi di lavoro che includono app per la logica, BizTalk Server, SQL Server o qualsiasi altro livello, è possibile abilitare il rilevamento personalizzato che registra gli eventi dall'inizio alla fine del flusso di lavoro. 

Questo articolo fornisce un codice personalizzato che è possibile usare nei livelli all'esterno di app per la logica. 

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
         "repeatItemIndex": "",
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
         "record": {                
         }
      }
   ]
}
```

| Proprietà | Type | DESCRIZIONE |
| --- | --- | --- |
| sourceType |   | Tipo dell'origine dell'esecuzione. I valori consentiti sono **Microsoft.Logic/workflows** e **custom**. Obbligatoria |
| source |   | Se il tipo di origine è **Microsoft.Logic/workflows**, le informazioni dell'origine devono seguire questo schema. Se il tipo di origine è **custom**, lo schema è JToken. Obbligatoria |
| systemId | string | ID di sistema dell'app per la logica. Obbligatoria |
| runId | string | ID di esecuzione dell'app per la logica. Obbligatoria |
| operationName | string | Nome dell'operazione, ad esempio azione o trigger. Obbligatoria |
| repeatItemScopeName | string | Ripetere il nome dell'elemento se l'azione è all'interno di un loop `foreach` / `until`. Obbligatoria |
| repeatItemIndex | Integer | Se l'azione è all'interno di un loop `foreach`/`until`. Indica l'indice dell'elemento ripetuto. Obbligatoria |
| trackingId | string | Indica l'ID di rilevamento per la correlazione dei messaggi. Facoltativa |
| correlationId | string | ID di correlazione, per la correlazione dei messaggi. Facoltativa |
| clientRequestId | string | Il client può popolare questo valore per la correlazione dei messaggi. Facoltativa |
| eventLevel |   | Livello dell'evento. Obbligatoria |
| eventTime |   | Ora dell'evento in formato UTC, ovvero AAAA-MM-GGTHH:MM:SS.00000Z Obbligatoria |
| recordType |   | Tipo di record di rilevamento. Il valore consentito è **custom**. Obbligatoria |
| record |   | Tipo di record personalizzato. Il formato consentito è JToken. Obbligatoria |
||||

## <a name="b2b-protocol-tracking-schemas"></a>Schemi di rilevamento per il protocollo B2B

Per informazioni sugli schemi di rilevamento per il protocollo B2B, vedere:

* [Schemi di rilevamento AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)   
* [Schemi di rilevamento X12](logic-apps-track-integration-account-x12-tracking-schema.md)

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sul [monitoraggio dei messaggi B2B](logic-apps-monitor-b2b-message.md)
* Informazioni su [rilevamento dei messaggi B2B in log di monitoraggio di Azure](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)
