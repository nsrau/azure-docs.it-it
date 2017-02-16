---
title: Schemi di rilevamento AS2 | Documentazione Microsoft
description: Altre informazioni sugli schemi di rilevamento AS2
author: padmavc
manager: erikre
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: f169c411-1bd7-4554-80c1-84351247bf94
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2016
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: e79f93c94d729a604944fc065b7016e9dbfcb9b6
ms.openlocfilehash: 65a1da23b1fdd671035f99a2a66da7c948573eb9


---
# <a name="as2-tracking-schemas"></a>Schemi di rilevamento AS2
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

| Proprietà | Tipo | Descrizione |
| --- | --- | --- |
| senderPartnerName | String | Nome partner del mittente del messaggio AS2. Facoltativa |
| receiverPartnerName | String | Nome partner del destinatario del messaggio AS2. Facoltativa |
| as2To | String | Nome del destinatario del messaggio AS2, dalle intestazioni del messaggio AS2. Obbligatoria |
| as2From | String | Nome del mittente del messaggio AS2, dalle intestazioni del messaggio AS2. Obbligatoria |
| agreementName | String | Nome del contratto AS2 in base al quale vengono risolti i messaggi. Facoltativa |
| direction | String | Direzione del flusso dei messaggi, ricezione o invio. Obbligatoria |
| messageId | String | ID del messaggio AS2, dalle intestazioni del messaggio AS2. Facoltativa |
| dispositionType |String | Valore del tipo di notifica sulla ricezione del messaggio (MDN). Facoltativa |
| fileName | String | Nome di file, dall'intestazione del messaggio AS2. Facoltativa |
| isMessageFailed |Boolean | Se il messaggio AS2 non è riuscito. Obbligatoria |
| isMessageSigned | Boolean | Se il messaggio AS2 era firmato. Obbligatoria |
| isMessageEncrypted | Boolean | Se il messaggio AS2 era crittografato. Obbligatoria |
| isMessageCompressed |Boolean | Se il messaggio AS2 era compresso. Obbligatoria |
| correlationMessageId | String | ID del messaggio AS2 per la correlazione dei messaggi con le notifiche MDN. Facoltativa |
| incomingHeaders |Dizionario di JToken | Dettagli dell'intestazione del messaggio AS2 in arrivo. Facoltativa |
| outgoingHeaders |Dizionario di JToken | Dettagli dell'intestazione del messaggio AS2 in uscita. Facoltativa |
| isNrrEnabled | Boolean | Usare il valore predefinito se questo valore è sconosciuto. Obbligatoria |
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

| Proprietà | Tipo | Descrizione |
| --- | --- | --- |
| senderPartnerName | String | Nome partner del mittente del messaggio AS2. Facoltativa |
| receiverPartnerName | String | Nome partner del destinatario del messaggio AS2. Facoltativa |
| as2To | String | Nome partner che riceve il messaggio AS2. Obbligatoria |
| as2From | String | Nome partner che invia il messaggio AS2. Obbligatoria |
| agreementName | String | Nome del contratto AS2 in base al quale vengono risolti i messaggi. Facoltativa |
| direction |String | Direzione del flusso dei messaggi, ricezione o invio. Obbligatoria |
| messageId | String | ID del messaggio AS2. Facoltativa |
| originalMessageId |String | ID del messaggio originale AS2. Facoltativa |
| dispositionType | String | Valore del tipo di gestione MDN. Facoltativa |
| isMessageFailed |Boolean | Se il messaggio AS2 non è riuscito. Obbligatoria |
| isMessageSigned |Boolean | Se il messaggio AS2 era firmato. Obbligatoria |
| isNrrEnabled | Boolean | Usare il valore predefinito se questo valore è sconosciuto. Obbligatoria |
| statusCode | Enum | I valori consentiti sono **Accepted**, **Rejected** e **AcceptedWithErrors**. Obbligatoria |
| micVerificationStatus | Enum | I valori consentiti sono **NotApplicable**, **Succeeded** e **Failed**. Obbligatoria |
| correlationMessageId | String | ID correlazione. ID del messaggio originale, ovvero ID del messaggio per cui è configurata la notifica MDN. Facoltativa |
| incomingHeaders | Dizionario di JToken | Indica i dettagli dell'intestazione del messaggio in arrivo. Facoltativa |
| outgoingHeaders |Dizionario di JToken | Indica i dettagli dell'intestazione del messaggio in uscita. Facoltativa |

## <a name="next-steps"></a>Passaggi successivi
* Altre informazioni su [Enterprise Integration Pack](app-service-logic-enterprise-integration-overview.md).    
* Altre informazioni sul [monitoraggio dei messaggi B2B](app-service-logic-monitor-b2b-message.md).   
* Altre informazioni sugli [schemi di rilevamento B2B personalizzati](app-service-logic-track-integration-account-custom-tracking-shema.md).   
* Altre informazioni sugli [schemi di rilevamento X12](app-service-logic-track-integration-account-x12-tracking-shemas.md).   
* Altre informazioni sul [rilevamento dei messaggi B2B nel portale di Operations Management Suite](app-service-logic-track-b2b-messages-omsportal.md).



<!--HONumber=Dec16_HO3-->


