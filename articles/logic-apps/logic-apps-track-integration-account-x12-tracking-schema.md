---
title: Schemi di rilevamento X12 per il monitoraggio B2B - App per la logica di Azure | Documentazione Microsoft
description: Utilizzare gli schemi di rilevamento X12 per il monitoraggio dei messaggi B2B dalle transazioni nell'account di integrazione di Azure.
author: padmavc
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: a5413f80-eaad-4bcf-b371-2ad0ef629c3d
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: LADocs; padmavc
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3782c0a76ea8728a146b3d73774f74c31187cbfd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="start-or-enable-tracking-of-x12-messages-to-monitor-success-errors-and-message-properties"></a>Avviare o attivare il rilevamento dei messaggi X12 per monitorare il completamento dell'operazione, gli errori e le proprietà dei messaggi
È possibile usare questi schemi di rilevamento X12 nell'account di integrazione di Azure per monitorare le transazioni business-to-business (B2B):

* Schema di rilevamento X12 per set di transazioni
* Schema di rilevamento X12 per acknowledgement di set di transazioni
* Schema di rilevamento X12 per interscambio
* Schema di rilevamento X12 per acknowledgement di interscambio
* Schema di rilevamento X12 per gruppi funzionali
* Schema di rilevamento X12 per acknowledgement di gruppi funzionali

## <a name="x12-transaction-set-tracking-schema"></a>Schema di rilevamento X12 per set di transazioni
````java

    {
            "agreementProperties": {
                "senderPartnerName": "",
                "receiverPartnerName": "",
                "senderQualifier": "",
                "senderIdentifier": "",
                "receiverQualifier": "",
                "receiverIdentifier": "",
                "agreementName": ""
            },
            "messageProperties": {
                "direction": "",
                "interchangeControlNumber": "",
                "functionalGroupControlNumber": "",
                "transactionSetControlNumber": "",
                "CorrelationMessageId": "",
                "messageType": "",
                "isMessageFailed": "",
                "isTechnicalAcknowledgmentExpected": "",
                "isFunctionalAcknowledgmentExpected": "",
                "needAk2LoopForValidMessages":  "",
                "segmentsCount": ""
            }
    }
````

| Proprietà | type | DESCRIZIONE |
| --- | --- | --- |
| senderPartnerName | string | Nome partner del mittente del messaggio X12. Facoltativa |
| receiverPartnerName | string | Nome partner del destinatario del messaggio X12. Facoltativa |
| senderQualifier | string | Invia il qualificatore del partner. Obbligatoria |
| senderIdentifier | string | Invia l'identificatore del partner. Obbligatoria |
| receiverQualifier | string | Riceve il qualificatore del partner. (Obbligatoria) |
| receiverIdentifier | string | Riceve l'identificatore del partner. Obbligatoria |
| agreementName | string | Nome del contratto X12 in base al quale vengono risolti i messaggi. Facoltativa |
| direction | Enum | Direzione del flusso dei messaggi, ricezione o invio. Obbligatoria |
| interchangeControlNumber | string | Numero di controllo interscambio. Facoltativa |
| functionalGroupControlNumber | string | Numero di controllo funzionale. Facoltativa |
| transactionSetControlNumber | string | Numero di controllo set transazioni. Facoltativa |
| CorrelationMessageId | string | ID messaggio di correlazione. Una combinazione di {AgreementName}{*GroupControlNumber*}{TransactionSetControlNumber}. Facoltativa |
| messageType | string | Set di transazioni o tipo di documento. Facoltativa |
| isMessageFailed | boolean | Indica se il messaggio X12 non è riuscito. Obbligatoria |
| isTechnicalAcknowledgmentExpected | boolean | Indica se l'acknowledgement tecnico è configurato nel contratto X12. Obbligatoria |
| isFunctionalAcknowledgmentExpected | boolean | Indica se l'acknowledgement funzionale è configurato nel contratto X12. Obbligatoria |
| needAk2LoopForValidMessages | boolean | Indica se il ciclo AK2 è necessario per un messaggio valido. Obbligatoria |
| segmentsCount | Integer | Numero di segmenti nel set di transazioni X12. Facoltativa |

## <a name="x12-transaction-set-acknowledgement-tracking-schema"></a>Schema di rilevamento X12 per acknowledgement di set di transazioni
````java

    {
            "agreementProperties": {
                "senderPartnerName": "",
                "receiverPartnerName": "",
                "senderQualifier": "",
                "senderIdentifier": "",
                "receiverQualifier": "",
                "receiverIdentifier": "",
                "agreementName": ""
            },
            "messageProperties": {
                "direction": "",
                "interchangeControlNumber": "",
                "functionalGroupControlNumber": "",
                "isaSegment": "",
                "gsSegment": "",
                "respondingfunctionalGroupControlNumber": "",
                "respondingFunctionalGroupId": "",
                "respondingtransactionSetControlNumber": "",
                "respondingTransactionSetId": "",
                "statusCode": "",
                "processingStatus": "",
                "CorrelationMessageId": ""
                "isMessageFailed": "",
                "ak2Segment": "",
                "ak3Segment": "",
                "ak5Segment": ""
            }
    }
````

| Proprietà | type | DESCRIZIONE |
| --- | --- | --- |
| senderPartnerName | string | Nome partner del mittente del messaggio X12. Facoltativa |
| receiverPartnerName | string | Nome partner del destinatario del messaggio X12. Facoltativa |
| senderQualifier | string | Invia il qualificatore del partner. Obbligatoria |
| senderIdentifier | string | Invia l'identificatore del partner. Obbligatoria |
| receiverQualifier | string | Riceve il qualificatore del partner. (Obbligatoria) |
| receiverIdentifier | string | Riceve l'identificatore del partner. Obbligatoria |
| agreementName | string | Nome del contratto X12 in base al quale vengono risolti i messaggi. Facoltativa |
| direction | Enum | Direzione del flusso dei messaggi, ricezione o invio. Obbligatoria |
| interchangeControlNumber | string | Numero di controllo interscambio dell'acknowledgement funzionale. Il valore viene popolato solo per il lato di invio che ha ricevuto un acknowledgement funzionale per il messaggio inviato al partner. Facoltativa |
| functionalGroupControlNumber | string | Numero di controllo del gruppo funzionale dell'acknowledgement funzionale. Il valore viene popolato solo per il lato di invio che ha ricevuto un acknowledgement funzionale per il messaggio inviato al partner. Facoltativa |
| isaSegment | string | Segmento ISA del messaggio. Il valore viene popolato solo per il lato di invio che ha ricevuto un acknowledgement funzionale per il messaggio inviato al partner. Facoltativa |
| gsSegment | string | Segmento GS del messaggio. Il valore viene popolato solo per il lato di invio che ha ricevuto un acknowledgement funzionale per il messaggio inviato al partner. Facoltativa |
| respondingfunctionalGroupControlNumber | string | Numero di controllo interscambio di risposta. Facoltativa |
| respondingFunctionalGroupId | string | ID del gruppo funzionale di risposta, che esegue il mapping ad AK101 nell'acknowledgement. Facoltativa |
| respondingtransactionSetControlNumber | string | Numero di controllo set transazioni di risposta. Facoltativa |
| respondingTransactionSetId | string | ID del set di transazioni di risposta, che esegue il mapping ad AK201 nell'acknowledgement. Facoltativa |
| statusCode | boolean | Codice di stato dell'acknowledgement del set di transazioni. Obbligatoria |
| segmentsCount | Enum | Codice di stato dell'acknowledgement. I valori consentiti sono **Accepted**, **Rejected** e **AcceptedWithErrors**. Obbligatoria |
| processingStatus | Enum | Stato di elaborazione dell'acknowledgement. I valori consentiti sono **Received**, **Generated** e **Sent**. Obbligatoria |
| CorrelationMessageId | string | ID messaggio di correlazione. Una combinazione di {AgreementName}{*GroupControlNumber*}{TransactionSetControlNumber}. Facoltativa |
| isMessageFailed | boolean | Indica se il messaggio X12 non è riuscito. Obbligatoria |
| ak2Segment | string | Acknowledgement per un set di transazioni nel gruppo funzionale ricevuto. Facoltativa |
| ak3Segment | string | Segnala gli errori in un segmento di dati. Facoltativa |
| ak5Segment | string | Indica se il set di transazioni identificato nel segmento AK2 è accettato o rifiutato e il motivo. Facoltativa |

## <a name="x12-interchange-tracking-schema"></a>Schema di rilevamento X12 per interscambio
````java

    {
            "agreementProperties": {
                "senderPartnerName": "",
                "receiverPartnerName": "",
                "senderQualifier": "",
                "senderIdentifier": "",
                "receiverQualifier": "",
                "receiverIdentifier": "",
                "agreementName": ""
            },
            "messageProperties": {
                "direction": "",
                "interchangeControlNumber": "",
                "isaSegment": "",
                "isTechnicalAcknowledgmentExpected": "",
                "isMessageFailed": "",
                "isa09": "",
                "isa10": "",
                "isa11": "",
                "isa12": "",
                "isa14": "",
                "isa15": "",
                "isa16": ""
            }
    }
````

| Proprietà | type | DESCRIZIONE |
| --- | --- | --- |
| senderPartnerName | string | Nome partner del mittente del messaggio X12. Facoltativa |
| receiverPartnerName | string | Nome partner del destinatario del messaggio X12. Facoltativa |
| senderQualifier | string | Invia il qualificatore del partner. Obbligatoria |
| senderIdentifier | string | Invia l'identificatore del partner. Obbligatoria |
| receiverQualifier | string | Riceve il qualificatore del partner. (Obbligatoria) |
| receiverIdentifier | string | Riceve l'identificatore del partner. Obbligatoria |
| agreementName | string | Nome del contratto X12 in base al quale vengono risolti i messaggi. Facoltativa |
| direction | Enum | Direzione del flusso dei messaggi, ricezione o invio. Obbligatoria |
| interchangeControlNumber | string | Numero di controllo interscambio. Facoltativa |
| isaSegment | string | Segmento ISA del messaggio. Facoltativa |
| isTechnicalAcknowledgmentExpected | boolean | Indica se l'acknowledgement tecnico è configurato nel contratto X12. Obbligatoria |
| isMessageFailed | boolean | Indica se il messaggio X12 non è riuscito. Obbligatoria |
| isa09 | string | Data di interscambio del documento X12. Facoltativa |
| isa10 | string | Ora di interscambio del documento X12. Facoltativa |
| isa11 | string | Identificatore standard del controllo di interscambio X12. Facoltativa |
| isa12 | string | Numero di versione del controllo di interscambio X12. Facoltativa |
| isa14 | string | È necessario l'acknowledgement X12. Facoltativa |
| isa15 | string | Indicatore per test o produzione. Facoltativa |
| isa16 | string | Separatore elementi. Facoltativa |

## <a name="x12-interchange-acknowledgement-tracking-schema"></a>Schema di rilevamento X12 per acknowledgement di interscambio
````java
    {
            "agreementProperties": {
                "senderPartnerName": "",
                "receiverPartnerName": "",
                "senderQualifier": "",
                "senderIdentifier": "",
                "receiverQualifier": "",
                "receiverIdentifier": "",
                "agreementName": ""
            },
            "messageProperties": {
                "direction": "",
                "interchangeControlNumber": "",
                "isaSegment": "",
                "respondingInterchangeControlNumber": "",
                "isMessageFailed": "",
                "statusCode": "",
                "processingStatus": "",
                "ta102": "",
                "ta103": "",
                "ta105": ""
            }
    }
````

| Proprietà | type | DESCRIZIONE |
| --- | --- | --- |
| senderPartnerName | string | Nome partner del mittente del messaggio X12. Facoltativa |
| receiverPartnerName | string | Nome partner del destinatario del messaggio X12. Facoltativa |
| senderQualifier | string | Invia il qualificatore del partner. Obbligatoria |
| senderIdentifier | string | Invia l'identificatore del partner. Obbligatoria |
| receiverQualifier | string | Riceve il qualificatore del partner. (Obbligatoria) |
| receiverIdentifier | string | Riceve l'identificatore del partner. Obbligatoria |
| agreementName | string | Nome del contratto X12 in base al quale vengono risolti i messaggi. Facoltativa |
| direction | Enum | Direzione del flusso dei messaggi, ricezione o invio. Obbligatoria |
| interchangeControlNumber | string | Numero di controllo interscambio dell'acknowledgement tecnico ricevuto dai partner. Facoltativa |
| isaSegment | string | Segmento ISA per l'acknowledgement tecnico ricevuto dai partner. (Facoltativa) |
| respondingInterchangeControlNumber |string | Numero di controllo interscambio per l'acknowledgement tecnico ricevuto dai partner. Facoltativa |
| isMessageFailed | boolean | Indica se il messaggio X12 non è riuscito. Obbligatoria |
| statusCode | Enum | Codice di stato dell'acknowledgement di interscambio. I valori consentiti sono **Accepted**, **Rejected** e **AcceptedWithErrors**. Obbligatoria |
| processingStatus | Enum | Stato dell'acknowledgement. I valori consentiti sono **Received**, **Generated** e **Sent**. Obbligatoria |
| ta102 | string | Data di interscambio. Facoltativa |
| ta103 | string | Ora di interscambio. Facoltativa |
| ta105 | string | Codice della nota di interscambio. Facoltativa |

## <a name="x12-functional-group-tracking-schema"></a>Schema di rilevamento X12 per gruppi funzionali
````java

    {
            "agreementProperties": {
                "senderPartnerName": "",
                "receiverPartnerName": "",
                "senderQualifier": "",
                "senderIdentifier": "",
                "receiverQualifier": "",
                "receiverIdentifier": "",
                "agreementName": ""
            },
            "messageProperties": {
                "direction": "",
                "interchangeControlNumber": "",
                "functionalGroupControlNumber": "",
                "gsSegment": "",
                "isTechnicalAcknowledgmentExpected": "",
                "isFunctionalAcknowledgmentExpected": "",
                "isMessageFailed": "",
                "gs01": "",
                "gs02": "",
                "gs03": "",
                "gs04": "",
                "gs05": "",
                "gs07": "",
                "gs08": ""
            }
    }
````

| Proprietà | type | DESCRIZIONE |
| --- | --- | --- |
| senderPartnerName | string | Nome partner del mittente del messaggio X12. Facoltativa |
| receiverPartnerName | string | Nome partner del destinatario del messaggio X12. Facoltativa |
| senderQualifier | string | Invia il qualificatore del partner. Obbligatoria |
| senderIdentifier | string | Invia l'identificatore del partner. Obbligatoria |
| receiverQualifier | string | Riceve il qualificatore del partner. (Obbligatoria) |
| receiverIdentifier | string | Riceve l'identificatore del partner. Obbligatoria |
| agreementName | string | Nome del contratto X12 in base al quale vengono risolti i messaggi. Facoltativa |
| direction | Enum | Direzione del flusso dei messaggi, ricezione o invio. Obbligatoria |
| interchangeControlNumber | string | Numero di controllo interscambio. Facoltativa |
| functionalGroupControlNumber | string | Numero di controllo funzionale. Facoltativa |
| gsSegment | string | Segmento GS del messaggio. Facoltativa |
| isTechnicalAcknowledgmentExpected | boolean | Indica se l'acknowledgement tecnico è configurato nel contratto X12. Obbligatoria |
| isFunctionalAcknowledgmentExpected | boolean | Indica se l'acknowledgement funzionale è configurato nel contratto X12. Obbligatoria |
| isMessageFailed | boolean | Indica se il messaggio X12 non è riuscito. Obbligatoria|
| gs01 | string | Codice identificatore funzionale. Facoltativa |
| gs02 | string | Codice del mittente dell'applicazione. Facoltativa |
| gs03 | string | Codice del ricevitore dell'applicazione. Facoltativa |
| gs04 | string | Data del gruppo funzionale. Facoltativa |
| gs05 | string | Ora del gruppo funzionale. Facoltativa |
| gs07 | string | Codice agenzia responsabile. Facoltativa |
| gs08 | string | Codice identificatore versione/rilascio/settore. Facoltativa |

## <a name="x12-functional-group-acknowledgement-tracking-schema"></a>Schema di rilevamento X12 per acknowledgement di gruppi funzionali
````java
    {
            "agreementProperties": {
                "senderPartnerName": "",
                "receiverPartnerName": "",
                "senderQualifier": "",
                "senderIdentifier": "",
                "receiverQualifier": "",
                "receiverIdentifier": "",
                "agreementName": ""
            },
            "messageProperties": {
                "direction": "",
                "interchangeControlNumber": "",
                "functionalGroupControlNumber": "",
                "isaSegment": "",
                "gsSegment": "",
                "respondingfunctionalGroupControlNumber": "",
                "respondingFunctionalGroupId": "",
                "isMessageFailed": "",
                "statusCode": "",
                "processingStatus": "",
                "ak903": "",
                "ak904": "",
                "ak9Segment": ""
            }
    }
````

| Proprietà | type | DESCRIZIONE |
| --- | --- | --- |
| senderPartnerName | string | Nome partner del mittente del messaggio X12. Facoltativa |
| receiverPartnerName | string | Nome partner del destinatario del messaggio X12. Facoltativa |
| senderQualifier | string | Invia il qualificatore del partner. Obbligatoria |
| senderIdentifier | string | Invia l'identificatore del partner. Obbligatoria |
| receiverQualifier | string | Riceve il qualificatore del partner. (Obbligatoria) |
| receiverIdentifier | string | Riceve l'identificatore del partner. Obbligatoria |
| agreementName | string | Nome del contratto X12 in base al quale vengono risolti i messaggi. Facoltativa |
| direction | Enum | Direzione del flusso dei messaggi, ricezione o invio. Obbligatoria |
| interchangeControlNumber | string | Numero di controllo interscambio, che viene popolato per il lato di invio quando un acknowledgement tecnico viene ricevuto dai partner. Facoltativa |
| functionalGroupControlNumber | string | Numero di controllo gruppo funzionale dell'acknowledgement tecnico, che viene popolato per il lato di invio quando un acknowledgement tecnico viene ricevuto dai partner. Facoltativa |
| isaSegment | string | Come il numero di controllo interscambio, ma viene popolato solo in casi specifici. Facoltativa |
| gsSegment | string | Come il numero di controllo gruppo funzionale, ma viene popolato solo in casi specifici. Facoltativa |
| respondingfunctionalGroupControlNumber | string | Numero di controllo del gruppo funzionale originale. Facoltativa |
| respondingFunctionalGroupId | string | Esegue il mapping ad AK101 nell'ID del gruppo funzionale dell'acknowledgement. Facoltativa |
| isMessageFailed | boolean | Indica se il messaggio X12 non è riuscito. Obbligatoria |
| statusCode | Enum | Codice di stato dell'acknowledgement. I valori consentiti sono **Accepted**, **Rejected** e **AcceptedWithErrors**. Obbligatoria |
| processingStatus | Enum | Stato di elaborazione dell'acknowledgement. I valori consentiti sono **Received**, **Generated** e **Sent**. Obbligatoria |
| ak903 | string | Numero di set transazioni ricevuti. Facoltativa |
| ak904 | string | Numero di set di transazioni accettati nel gruppo funzionale identificato. Facoltativa |
| ak9Segment | string | Indica se il gruppo funzionale identificato nel segmento AK1 è accettato o rifiutato e il motivo. Facoltativa |

## <a name="next-steps"></a>Passaggi successivi
* Altre informazioni sul [monitoraggio dei messaggi B2B](logic-apps-monitor-b2b-message.md).
* Altre informazioni sugli [schemi di rilevamento AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md).
* Altre informazioni sugli [schemi di rilevamento B2B personalizzati](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md).
* Altre informazioni sul [rilevamento dei messaggi B2B nel portale di Operations Management Suite](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).
* Altre informazioni su [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md).  
