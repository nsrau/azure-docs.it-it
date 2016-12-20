---
title: Schema di rilevamento personalizzato | Documentazione Microsoft
description: Altre informazioni sullo schema di rilevamento personalizzato
author: padmavc
manager: erikre
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 433ae852-a833-44d3-a3c3-14cca33403a2
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2016
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: 63ba6e8fec8df1cdbc9928b011130f47c5facf89
ms.openlocfilehash: f846f23bef61e8e772920196b9c81d059546d8a6


---
# <a name="custom-tracking-schema"></a>Schema di rilevamento personalizzato
## <a name="supported-custom-tracking-schema"></a>Schema di rilevamento personalizzato supportato
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

| Proprietà | Descrizione |
| --- | --- |
| sourceType |Obbligatorio.  Indica il tipo di origine dell'esecuzione. I valori consentiti sono Microsoft.Logic/workflows o custom. |
| Source |Obbligatoria. Se il tipo di origine è Microsoft.Logic/workflows, le informazioni di origine devono seguire questo schema. Se il tipo di origine è 'custom', lo schema è un JToken.  |
| systemId |Obbligatoria, stringa.  Indica l'ID di sistema dell'app per la logica. |
| runId |Obbligatoria, stringa.  Indica l'ID di esecuzione dell'app per la logica. |
| operationName |Obbligatoria, stringa.  Indica il nome dell'operazione, ad esempio action o trigger. |
| repeatItemScopeName |Obbligatoria, stringa. Indica il nome dell'elemento ripetuto se l'azione si trova in un ciclo foreach/until. |
| repeatItemIndex |Obbligatoria, numero intero.  Se l'azione si trova in un ciclo foreach/until, indica l'indice di elementi ripetuti.  |
| trackingId |Facoltativa, stringa. Indica l'ID di rilevamento per la correlazione dei messaggi. |
| correlationId |Facoltativa, stringa. Indica l'ID di correlazione per la correlazione dei messaggi. |
| clientRequestId |Facoltativa, stringa.  Il client può popolare questo valore per la correlazione dei messaggi. |
| eventLevel |Obbligatorio. Indica il livello dell'evento. |
| eventTime |Obbligatorio. Indica l'ora dell'evento in formato UTC, ovvero AAAA-MM-GGTHH:MM:SS.00000Z |
| recordType |Obbligatoria, indica il tipo di record di rilevamento. Il valore consentito è Custom. |
| record |Obbligatorio.  Indica il tipo di record personalizzato. Il formato consentito è JToken. |
|  | |

## <a name="supported-b2b-protocol-tracking-schemas"></a>Schemi di rilevamento supportati per il protocollo B2B
* [Schema di rilevamento AS2](app-service-logic-track-integration-account-as2-tracking-shemas.md)   
* [Schema di rilevamento X12](app-service-logic-track-integration-account-x12-tracking-shemas.md) 

## <a name="next-steps"></a>Passaggi successivi
[Altre informazioni sul monitoraggio dei messaggi B2B](app-service-logic-monitor-b2b-message.md "Learn more about tracking B2B messages")   
[Rilevamento dei messaggi B2B nel portale di OMS](app-service-logic-track-b2b-messages-omsportal.md "Tracking B2B messages")   
[Altre informazioni su Enterprise Integration Pack](app-service-logic-enterprise-integration-overview.md "Learn about Enterprise Integration Pack")   




<!--HONumber=Nov16_HO3-->


