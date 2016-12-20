---
title: Schema di rilevamento as2 | Documentazione Microsoft
description: Altre informazioni sullo schema di rilevamento as2
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
ms.sourcegitcommit: 63ba6e8fec8df1cdbc9928b011130f47c5facf89
ms.openlocfilehash: 1fd5bd1038be6c35cf5fc05c9cfa000d818c5e70


---

# <a name="as2-tracking-schemas"></a>Schemi di rilevamento AS2
Ecco gli schemi di rilevamento AS2 supportati:

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

| Proprietà | Descrizione |
| --- | --- |
| senderPartnerName |Facoltativa, stringa.  Indica il nome del partner del mittente del messaggio AS2. |
| receiverPartnerName |Facoltativa, stringa.  Indica il nome del partner del destinatario del messaggio AS2. |
| as2To |Obbligatoria, stringa.  Indica il nome del destinatario del messaggio AS2 incluso nelle intestazioni del messaggio AS2. |
| as2From |Obbligatoria, stringa. Indica il nome del mittente del messaggio AS2 incluso nelle intestazioni del messaggio AS2. |
| agreementName |Facoltativa, stringa.  Nome del contratto AS2 in base al quale vengono risolti i messaggi. |
| direction |Obbligatoria, stringa.  Indica la direzione del flusso di messaggi, ovvero ricezione o invio. |
| messageId |Facoltativa, stringa.  Indica l'ID del messaggio AS2 incluso nelle intestazioni del messaggio AS2. |
| dispositionType |Facoltativa, stringa. Indica il valore del tipo di disposizione MDN. |
| fileName |Facoltativa, stringa.  Indica il nome file incluso nell'intestazione del messaggio AS2. |
| isMessageFailed |Obbligatoria, valore booleano.  Indica se il messaggio AS2 ha avuto esito positivo o negativo. |
| isMessageSigned |Obbligatoria, valore booleano.  Indica se il messaggio AS2 è firmato o no. |
| isMessageEncrypted |Obbligatoria, valore booleano. Indica se il messaggio AS2 è crittografato o no. |
| isMessageCompressed |Obbligatoria, valore booleano. Indica se il messaggio AS2 è compresso o no. |
| correlationMessageId |Facoltativa, stringa. Indica il valore AS2messageid per la correlazione dei messaggi con MDN. |
| incomingHeaders |Facoltativa, dizionario di JToken.  Indica i dettagli dell'intestazione del messaggio AS2 in arrivo. |
| outgoingHeaders |Facoltativa, dizionario di JToken.  Indica i dettagli dell'intestazione del messaggio AS2 in uscita. |
| isNrrEnabled |Obbligatoria, valore booleano.  Usare il valore predefinito se questo valore è sconosciuto. |
| isMdnExpected |Obbligatoria, valore booleano. Usare il valore predefinito se questo valore è sconosciuto. |
| mdnType |Obbligatoria, enumerazione. I valori consentiti sono NotConfigured, Sync o Async. |

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

| Proprietà | Descrizione |
| --- | --- |
| senderPartnerName |Facoltativa, stringa. Indica il nome del partner del mittente del messaggio AS2. |
| receiverPartnerName |Facoltativa, stringa. Indica il nome del partner del destinatario del messaggio AS2. |
| as2To |Obbligatoria, stringa. Indica il nome del partner che riceve il messaggio AS2. |
| as2From |Obbligatoria, stringa. Indica il nome del partner che invia il messaggio AS2. |
| agreementName |Facoltativa, stringa. Nome del contratto AS2 in base al quale vengono risolti i messaggi. |
| direction |Obbligatoria, stringa. Indica la direzione del flusso di messaggi, ovvero ricezione o invio. |
| messageId |Facoltativa, stringa. Indica l'ID del messaggio AS2. |
| originalMessageId |Facoltativa, stringa. Indica l'ID del messaggio AS2 originale. |
| dispositionType |Facoltativa, stringa. Indica il valore del tipo di disposizione MDN. |
| isMessageFailed |Obbligatoria, valore booleano. Indica se il messaggio AS2 ha avuto esito positivo o negativo. |
| isMessageSigned |Obbligatoria, valore booleano. Indica se il messaggio AS2 è firmato o no. |
| isNrrEnabled |Obbligatoria, valore booleano.  Usare il valore predefinito se questo valore è sconosciuto. |
| statusCode |Obbligatoria, enumerazione.  I valori consentiti sono Accepted, Rejected o AcceptedWithErrros. |
| micVerificationStatus |Obbligatoria, enumerazione.  I valori consentito sono NotApplicable, Succeeded o Failed. |
| correlationMessageId |Facoltativa, stringa.  Indica l'ID di correlazione.  L'ID di correlazione è l'ID del messaggio originale, ovvero l'ID del messaggio per cui è configurato MDN. |
| incomingHeaders |Facoltativa, dizionario di JToken.  Indica i dettagli dell'intestazione del messaggio in arrivo. |
| outgoingHeaders |Facoltativa, dizionario di JToken.  Indica i dettagli dell'intestazione del messaggio in uscita. |

## <a name="next-steps"></a>Passaggi successivi
[Altre informazioni su Enterprise Integration Pack](app-service-logic-enterprise-integration-overview.md "Learn about Enterprise Integration Pack")    
[Altre informazioni sul monitoraggio dei messaggi B2B](app-service-logic-monitor-b2b-message.md "Learn more about tracking B2B messages")   
[Rilevamento dei messaggi B2B nel portale di OMS](app-service-logic-track-b2b-messages-omsportal.md "Tracking B2B messages")   
[Altre informazioni sullo schema di rilevamento B2B personalizzato](app-service-logic-track-integration-account-custom-tracking-shema.md "Learn about Custom Schema")   
[Altre informazioni sullo schema di rilevamento X12](app-service-logic-track-integration-account-x12-tracking-shemas.md "Learn about X12 Tracking Schema")   
[Altre informazioni su Enterprise Integration Pack](app-service-logic-enterprise-integration-overview.md "Learn about Enterprise Integration Pack")  




<!--HONumber=Nov16_HO3-->


