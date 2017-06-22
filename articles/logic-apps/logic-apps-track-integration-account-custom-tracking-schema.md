---
title: Schemi di rilevamento personalizzati per il monitoraggio B2B - App per la logica di Azure | Documenti di Microsoft
description: Creare schemi di rilevamento personalizzati per il monitoraggio dei messaggi B2B dalle transazioni nell&quot;account di integrazione di Azure.
author: padmavc
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 433ae852-a833-44d3-a3c3-14cca33403a2
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: LADocs; padmavc
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 5913c81088724ef946ae147f4f3154fa6aefd22e
ms.openlocfilehash: 9a663a4e79c30b97e6390b7ff7f83deec131384a
ms.contentlocale: it-it
ms.lasthandoff: 03/01/2017


---
# <a name="enable-tracking-to-monitor-your-complete-workflow-end-to-end"></a>Abilitare il rilevamento per monitorare il flusso di lavoro completo, end-to-end
È disponibile un rilevamento predefinito che è possibile abilitare per diverse parti del flusso di lavoro Business to Business, ad esempio il rilevamento dei messaggi AS2 o X12. Quando si creano flussi di lavoro che includono app per la logica, BizTalk Server, SQL Server o qualsiasi altro livello, è possibile abilitare il rilevamento personalizzato che registra gli eventi dall'inizio alla fine del flusso di lavoro. 

Questo argomento fornisce un codice personalizzato che è possibile usare nei livelli all'esterno di app per la logica. 

## <a name="custom-tracking-schema"></a>Schema di rilevamento personalizzato
````java

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

````

| Proprietà | Tipo | Descrizione |
| --- | --- | --- |
| sourceType |   | Tipo dell'origine dell'esecuzione. I valori consentiti sono **Microsoft.Logic/workflows** e **custom**. Obbligatoria |
| Source |   | Se il tipo di origine è **Microsoft.Logic/workflows**, le informazioni dell'origine devono seguire questo schema. Se il tipo di origine è **custom**, lo schema è JToken. Obbligatoria |
| systemId | String | ID di sistema dell'app per la logica. Obbligatoria |
| runId | String | ID di esecuzione dell'app per la logica. Obbligatoria |
| operationName | String | Nome dell'operazione, ad esempio azione o trigger. Obbligatoria |
| repeatItemScopeName | String | Ripetere il nome dell'elemento se l'azione è all'interno di un loop `foreach` / `until`. Obbligatoria |
| repeatItemIndex | Integer | Se l'azione è all'interno di un loop `foreach`/`until`. Indica l'indice dell'elemento ripetuto. Obbligatoria |
| trackingId | String | Indica l'ID di rilevamento per la correlazione dei messaggi. Facoltativa |
| correlationId | String | ID di correlazione, per la correlazione dei messaggi. Facoltativa |
| clientRequestId | String | Il client può popolare questo valore per la correlazione dei messaggi. Facoltativa |
| eventLevel |   | Livello dell'evento. Obbligatoria |
| eventTime |   | Ora dell'evento in formato UTC, ovvero AAAA-MM-GGTHH:MM:SS.00000Z Obbligatoria |
| recordType |   | Tipo di record di rilevamento. Il valore consentito è **custom**. Obbligatoria |
| record |   | Tipo di record personalizzato. Il formato consentito è JToken. Obbligatoria |

## <a name="b2b-protocol-tracking-schemas"></a>Schemi di rilevamento per il protocollo B2B
Per informazioni sugli schemi di rilevamento per il protocollo B2B, vedere:
* [Schemi di rilevamento AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)   
* [Schemi di rilevamento X12](logic-apps-track-integration-account-x12-tracking-schema.md)

## <a name="next-steps"></a>Passaggi successivi
* Altre informazioni sul [monitoraggio dei messaggi B2B](logic-apps-monitor-b2b-message.md).   
* Altre informazioni sul [rilevamento dei messaggi B2B nel portale di Operations Management Suite](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).
* Altre informazioni su [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md).

