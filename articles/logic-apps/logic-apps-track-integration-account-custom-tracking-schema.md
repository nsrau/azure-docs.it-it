---
title: Schemi di rilevamento personalizzati per i messaggi B2B
description: Creare schemi di rilevamento personalizzati che consentono di monitorare i messaggi B2B negli account di integrazione per App per la logica di Azure con Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/27/2017
ms.openlocfilehash: 7d7c5ef9e9a86c8b061a56fe41c0c8bbfc5ddbb3
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792787"
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

| Proprietà | Obbligatoria | Type | Description |
| --- | --- | --- | --- |
| sourceType | SÌ |   | Tipo dell'origine dell'esecuzione. I valori consentiti sono **Microsoft.Logic/workflows** e **custom**. |
| una sezione source | SÌ |   | Se il tipo di origine è **Microsoft.Logic/workflows**, le informazioni dell'origine devono seguire questo schema. Se il tipo di origine è **custom**, lo schema è JToken. |
| systemId | SÌ | Stringa | ID di sistema dell'app per la logica. |
| runId | SÌ | Stringa | ID di esecuzione dell'app per la logica. |
| operationName | SÌ | Stringa | Nome dell'operazione, ad esempio azione o trigger. |
| repeatItemScopeName | SÌ | Stringa | Ripetere il nome dell'elemento se l'azione è all'interno di un loop `foreach` / `until`. |
| repeatItemIndex | SÌ | Integer | Se l'azione è all'interno di un loop `foreach`/`until`. Indica l'indice dell'elemento ripetuto. |
| trackingId | No | Stringa | Indica l'ID di rilevamento per la correlazione dei messaggi. |
| correlationId | No | Stringa | ID di correlazione, per la correlazione dei messaggi. |
| clientRequestId | No | Stringa | Il client può popolare questo valore per la correlazione dei messaggi. |
| eventLevel | SÌ |   | Livello dell'evento. |
| eventTime | SÌ |   | Ora dell'evento in formato UTC, ovvero AAAA-MM-GGTHH:MM:SS.00000Z |
| recordType | SÌ |   | Tipo di record di rilevamento. Il valore consentito è **custom**. |
| record | SÌ |   | Tipo di record personalizzato. Il formato consentito è JToken. |
||||

## <a name="b2b-protocol-tracking-schemas"></a>Schemi di rilevamento per il protocollo B2B

Per informazioni sugli schemi di rilevamento per il protocollo B2B, vedere:

* [Schemi di rilevamento AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)   
* [Schemi di rilevamento X12](logic-apps-track-integration-account-x12-tracking-schema.md)

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sul [monitoraggio dei messaggi B2B](logic-apps-monitor-b2b-message.md)
* Informazioni sul [rilevamento dei messaggi B2B nei log di monitoraggio di Azure](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)
