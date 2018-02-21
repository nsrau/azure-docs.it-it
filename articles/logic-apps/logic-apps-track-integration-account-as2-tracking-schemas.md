---
title: Schemi di rilevamento AS2 per il monitoraggio B2B - App per la logica di Azure | Documenti di Microsoft
description: Utilizzare gli schemi di rilevamento AS2 per il monitoraggio dei messaggi B2B dalle transazioni nell'account di integrazione di Azure.
author: padmavc
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: f169c411-1bd7-4554-80c1-84351247bf94
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: LADocs; padmavc
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 31bd296dc5ed5ac6998a6c05ee80fd38b12d662c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="start-or-enable-tracking-of-as2-messages-and-mdns-to-monitor-success-errors-and-message-properties"></a>Avviare o attivare il rilevamento dei messaggi AS2 e di MDN per monitorare il completamento dell'operazione, gli errori e le proprietà dei messaggi
È possibile utilizzare questi schemi di rilevamento AS2 nell'account di integrazione di Azure per monitorare le transazioni business-to-business (B2B):

* Schema di rilevamento messaggi AS2
* Schema di rilevamento MDN AS2

## <a name="as2-message-tracking-schema"></a>Schema di rilevamento messaggi AS2
````java

    {
       "agreementProperties": {  
            "senderPartnerName": "",  
            "receiverPartnerName": "",  
            "as2To": "",  
            "as2From": "",  
            "agreementName": ""  
        },  
        "messageProperties": {
            "direction": "",
            "messageId": "",
            "dispositionType": "",
            "fileName": "",
            "isMessageFailed": "",
            "isMessageSigned": "",
            "isMessageEncrypted": "",
            "isMessageCompressed": "",
            "correlationMessageId": "",
            "incomingHeaders": {
            },
            "outgoingHeaders": {
            },
        "isNrrEnabled": "",
        "isMdnExpected": "",
        "mdnType": ""
        }
    }
````

| Proprietà | type | DESCRIZIONE |
| --- | --- | --- |
| senderPartnerName | string | Nome partner del mittente del messaggio AS2. Facoltativa |
| receiverPartnerName | string | Nome partner del destinatario del messaggio AS2. Facoltativa |
| as2To | string | Nome del destinatario del messaggio AS2, dalle intestazioni del messaggio AS2. Obbligatoria |
| as2From | string | Nome del mittente del messaggio AS2, dalle intestazioni del messaggio AS2. Obbligatoria |
| agreementName | string | Nome del contratto AS2 in base al quale vengono risolti i messaggi. Facoltativa |
| direction | string | Direzione del flusso dei messaggi, ricezione o invio. Obbligatoria |
| messageId | string | ID del messaggio AS2, dalle intestazioni del messaggio AS2. Facoltativa |
| dispositionType |string | Valore del tipo di notifica sulla ricezione del messaggio (MDN). Facoltativa |
| fileName | string | Nome di file, dall'intestazione del messaggio AS2. Facoltativa |
| isMessageFailed |boolean | Se il messaggio AS2 non è riuscito. Obbligatoria |
| isMessageSigned | boolean | Se il messaggio AS2 era firmato. Obbligatoria |
| isMessageEncrypted | boolean | Se il messaggio AS2 era crittografato. Obbligatoria |
| isMessageCompressed |boolean | Se il messaggio AS2 era compresso. Obbligatoria |
| correlationMessageId | string | ID del messaggio AS2 per la correlazione dei messaggi con le notifiche MDN. Facoltativa |
| incomingHeaders |Dizionario di JToken | Dettagli dell'intestazione del messaggio AS2 in arrivo. Facoltativa |
| outgoingHeaders |Dizionario di JToken | Dettagli dell'intestazione del messaggio AS2 in uscita. Facoltativa |
| isNrrEnabled | boolean | Usare il valore predefinito se questo valore è sconosciuto. Obbligatoria |
| isMdnExpected | Boolean | Usare il valore predefinito se questo valore è sconosciuto. Obbligatoria |
| mdnType | Enum | I valori consentiti sono **NotConfigured**, **Sync** e **Async**. Obbligatoria |

## <a name="as2-mdn-tracking-schema"></a>Schema di rilevamento MDN AS2
````java

    {
        "agreementProperties": {
                "senderPartnerName": "",
                "receiverPartnerName": "",
                "as2To": "",
                "as2From": "",
                "agreementName": "g"
            },
            "messageProperties": {
                "direction": "",
                "messageId": "",
                "originalMessageId": "",
                "dispositionType": "",
                "isMessageFailed": "",
                "isMessageSigned": "",
                "isNrrEnabled": "",
                "statusCode": "",
                "micVerificationStatus": "",
                "correlationMessageId": "",
                "incomingHeaders": {
                },
                "outgoingHeaders": {
                }
            }
    }
````

| Proprietà | type | DESCRIZIONE |
| --- | --- | --- |
| senderPartnerName | string | Nome partner del mittente del messaggio AS2. Facoltativa |
| receiverPartnerName | string | Nome partner del destinatario del messaggio AS2. Facoltativa |
| as2To | string | Nome partner che riceve il messaggio AS2. Obbligatoria |
| as2From | string | Nome partner che invia il messaggio AS2. Obbligatoria |
| agreementName | string | Nome del contratto AS2 in base al quale vengono risolti i messaggi. Facoltativa |
| direction |string | Direzione del flusso dei messaggi, ricezione o invio. Obbligatoria |
| messageId | string | ID del messaggio AS2. Facoltativa |
| originalMessageId |string | ID del messaggio originale AS2. Facoltativa |
| dispositionType | string | Valore del tipo di gestione MDN. Facoltativa |
| isMessageFailed |boolean | Se il messaggio AS2 non è riuscito. Obbligatoria |
| isMessageSigned |boolean | Se il messaggio AS2 era firmato. Obbligatoria |
| isNrrEnabled | boolean | Usare il valore predefinito se questo valore è sconosciuto. Obbligatoria |
| statusCode | Enum | I valori consentiti sono **Accepted**, **Rejected** e **AcceptedWithErrors**. Obbligatoria |
| micVerificationStatus | Enum | I valori consentiti sono **NotApplicable**, **Succeeded** e **Failed**. Obbligatoria |
| correlationMessageId | string | ID correlazione. ID del messaggio originale, ovvero ID del messaggio per cui è configurata la notifica MDN. Facoltativa |
| incomingHeaders | Dizionario di JToken | Indica i dettagli dell'intestazione del messaggio in arrivo. Facoltativa |
| outgoingHeaders |Dizionario di JToken | Indica i dettagli dell'intestazione del messaggio in uscita. Facoltativa |

## <a name="next-steps"></a>Passaggi successivi
* Altre informazioni su [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md).    
* Altre informazioni sul [monitoraggio dei messaggi B2B](logic-apps-monitor-b2b-message.md).   
* Altre informazioni sugli [schemi di rilevamento B2B personalizzati](logic-apps-track-integration-account-custom-tracking-schema.md).   
* Altre informazioni sugli [schemi di rilevamento X12](logic-apps-track-integration-account-x12-tracking-schema.md).   
* Altre informazioni sul [rilevamento dei messaggi B2B nel portale di Operations Management Suite](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).
