---
title: Schemi di rilevamento x12 | Documentazione Microsoft
description: Altre informazioni sullo schema di rilevamento X12
author: padmavc
manager: erikre
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: a5413f80-eaad-4bcf-b371-2ad0ef629c3d
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2016
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: 63ba6e8fec8df1cdbc9928b011130f47c5facf89
ms.openlocfilehash: 48dfc8327b7e2dfb16b0392fd6d44e83bdfb1177


---
# <a name="x12-tracking-schemas"></a>Schemi di rilevamento X12
Gli schemi di rilevamento X12 supportati sono i seguenti:

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

| Proprietà | Descrizione |
| --- | --- |
| senderPartnerName |Facoltativa, stringa.  Indica il nome del partner di trasmissione del messaggio X12 |
| receiverPartnerName |Facoltativa, stringa.  Indica il nome del partner di ricezione del messaggio X12 |
| senderQualifier |Obbligatoria, stringa.  Indica il qualificatore del partner di trasmissione |
| senderIdentifier |Obbligatoria, stringa.  Indica l'identificatore del partner di trasmissione |
| receiverQualifier |Obbligatoria, stringa.  Indica il qualificatore del partner di ricezione |
| receiverIdentifier |Obbligatoria, stringa.  Indica l'identificatore del partner di ricezione |
| agreementName |Facoltativa, stringa.  Nome del contratto X12 in base al quale vengono risolti i messaggi |
| direction |Obbligatoria, enumerazione.  Indica la direzione del flusso di messaggi.  I valori consentiti sono receive o send |
| interchangeControlNumber |Facoltativa, stringa.  Indica il numero di controllo interscambio |
| functionalGroupControlNumber |Facoltativa, stringa.  Indica il numero di controllo funzionale |
| transactionSetControlNumber |Facoltativa, stringa.  Indica il numero di controllo del set di transazioni |
| CorrelationMessageId |Facoltativa, stringa.  Indica l'ID del messaggio di correlazione.  L'ID di correlazione è costituito dalla combinazione di {AgreementName}*{GroupControlNumber}*{TransactionSetControlNumber} |
| messageType |Facoltativa, stringa. Indica il set di transazioni o il tipo di documento |
| isMessageFailed |Obbligatoria, valore booleano.  Indica se il messaggio X12 ha avuto esito positivo o negativo |
| isTechnicalAcknowledgmentExpected |Obbligatoria, valore booleano.  Indica se l'acknowledgement tecnico è configurato o meno nel contratto X12 |
| isFunctionalAcknowledgmentExpected |Obbligatoria, valore booleano.  Indica se l'acknowledgement funzionale è configurato o meno nel contratto X12 |
| needAk2LoopForValidMessages |Obbligatoria, valore booleano.  Indica se il ciclo AK2 è necessario o meno per un messaggio valido. Valore booleano |
| segmentsCount |Facoltativa, valore intero  Indica il numero di segmenti nel set di transazioni X12 |

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

| Proprietà | Descrizione |
| --- | --- |
| senderPartnerName |Facoltativa, stringa.  Indica il nome partner del mittente del messaggio X12 |
| receiverPartnerName |Facoltativa, stringa.  Indica il nome partner del destinatario del messaggio X12 |
| senderQualifier |Obbligatoria, stringa.  Indica il qualificatore del partner di trasmissione |
| senderIdentifier |Obbligatoria, stringa.  Indica l'identificatore del partner di trasmissione |
| receiverQualifier |Obbligatoria, stringa.  Indica il qualificatore del partner di ricezione |
| receiverIdentifier |Obbligatoria, stringa.  Indica l'identificatore del partner di ricezione |
| agreementName |Facoltativa, stringa.  Nome del contratto X12 in base al quale vengono risolti i messaggi |
| direction |Obbligatoria, enumerazione.  Indica la direzione del flusso di messaggi.  I valori consentiti sono receive o send |
| interchangeControlNumber |Facoltativa, stringa.  Indica il numero di controllo interscambio dell'acknowledgement funzionale. Il valore viene popolato solo per il lato di invio che ha ricevuto un acknowledgement funzionale per il messaggio inviato al partner |
| functionalGroupControlNumber |Facoltativa, stringa.  Indica il numero di controllo del gruppo funzionale dell'acknowledgement funzionale. Il valore viene popolato solo per il lato di invio che ha ricevuto un acknowledgement funzionale per il messaggio inviato al partner |
| isaSegment |Facoltativa, stringa.  Indica il segmento ISA del messaggio. Il valore viene popolato solo per il lato di invio che ha ricevuto un acknowledgement funzionale per il messaggio inviato al partner |
| gsSegment |Facoltativa, stringa.  Indica il segmento GS del messaggio. Il valore viene popolato solo per il lato di invio che ha ricevuto un acknowledgement funzionale per il messaggio inviato al partner |
| respondingfunctionalGroupControlNumber |Facoltativa, stringa.  Indica il numero di controllo interscambio di risposta |
| respondingFunctionalGroupId |Facoltativa, stringa. Indica l'ID del gruppo funzionale di risposta, che esegue il mapping ad AK101 nell'acknowledgement |
| respondingtransactionSetControlNumber |Facoltativa, stringa.  Indica il numero di controllo del set di transazioni di risposta |
| respondingTransactionSetId |Facoltativa, stringa.  Indica l'ID del set di transazioni di risposta, che esegue il mapping ad AK201 nell'acknowledgement |
| statusCode |Obbligatoria, valore booleano.  Indica il codice di stato dell'acknowledgement del set di transazioni |
| segmentsCount |Obbligatoria, enumerazione.  Indica il codice di stato dell'acknowledgement.  I valori consentiti sono Accepted, Rejected o AcceptedWithErrros |
| processingStatus |Obbligatoria, enumerazione.  Indica lo stato di elaborazione dell'acknowledgement.  I valori consentiti sono Received, Generated o Sent |
| CorrelationMessageId |Facoltativa, stringa.  Indica l'ID del messaggio di correlazione.  L'ID di correlazione è costituito dalla combinazione di {AgreementName}*{GroupControlNumber}*{TransactionSetControlNumber} |
| isMessageFailed |Obbligatoria, valore booleano.  Indica se il messaggio X12 ha avuto esito positivo o negativo |
| ak2Segment |Facoltativa, stringa. Indica il segmento ak2. Il segmento ak2 indica un acknowledgement per un set di transazioni nel gruppo funzionale ricevuto |
| ak3Segment |Facoltativa, stringa. Indica il segmento ak3.  Il segmento ak3 segnala gli errori in un segmento di dati |
| ak5Segment |Facoltativa, stringa. Indica il segmento ak5.  Il segmento ak5 indica se il set di transazioni identificato nel segmento ak2 è accettato o rifiutato e il motivo |

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

| Proprietà | Descrizione |
| --- | --- |
| senderPartnerName |Facoltativa, stringa.  Indica il nome partner del mittente del messaggio X12 |
| receiverPartnerName |Facoltativa, stringa.  Indica il nome partner del destinatario del messaggio X12 |
| senderQualifier |Obbligatoria, stringa.  Indica il qualificatore del partner di trasmissione |
| senderIdentifier |Obbligatoria, stringa.  Indica l'identificatore del partner di trasmissione |
| receiverQualifier |Obbligatoria, stringa.  Indica il qualificatore del partner di ricezione |
| receiverIdentifier |Obbligatoria, stringa.  Indica l'identificatore del partner di ricezione |
| agreementName |Facoltativa, stringa.  Nome del contratto X12 in base al quale vengono risolti i messaggi |
| direction |Obbligatoria, enumerazione.  Indica la direzione del flusso di messaggi.  I valori consentiti sono receive o send |
| interchangeControlNumber |Facoltativa, stringa.  Indica il numero di controllo interscambio |
| isaSegment |Facoltativa, stringa.  Indica il segmento ISA del messaggio |
| isTechnicalAcknowledgmentExpected |Obbligatoria, valore booleano.  Indica se l'acknowledgement tecnico è configurato o meno nel contratto X12 |
| isMessageFailed |Obbligatoria, valore booleano.  Indica se il messaggio X12 ha avuto esito positivo o negativo |
| isa09 |Facoltativa, stringa.  Indica la data di interscambio del documento X12 |
| isa10 |Facoltativa, stringa. Indica l'ora di interscambio del documento X12 |
| isa11 |Facoltativa, stringa. Indica l'identificatore standard del controllo di interscambio X12 |
| isa12 |Facoltativa, stringa.  Indica il numero di versione del controllo di interscambio X12 |
| isa14 |Facoltativa, stringa.  Indica che è necessario l'acknowledgement X12 |
| isa15 |Facoltativa, stringa.  Indicatore per test o produzione |
| isa16 |Facoltativa, stringa. Indica il separatore degli elementi |

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

| Proprietà | Descrizione |
| --- | --- |
| senderPartnerName |Facoltativa, stringa.  Indica il nome partner del mittente del messaggio X12 |
| receiverPartnerName |Facoltativa, stringa.  Indica il nome partner del destinatario del messaggio X12 |
| senderQualifier |Obbligatoria, stringa.  Indica il qualificatore del partner di trasmissione |
| senderIdentifier |Obbligatoria, stringa.  Indica l'identificatore del partner di trasmissione |
| receiverQualifier |Obbligatoria, stringa.  Indica il qualificatore del partner di ricezione |
| receiverIdentifier |Obbligatoria, stringa.  Indica l'identificatore del partner di ricezione |
| agreementName |Facoltativa, stringa.  Nome del contratto X12 in base al quale vengono risolti i messaggi |
| direction |Obbligatoria, enumerazione.  Indica la direzione del flusso di messaggi.  I valori consentiti sono receive o send |
| interchangeControlNumber |Facoltativa, stringa.  Indica il numero di controllo interscambio dell'acknowledgement tecnico. L'acknowledgment tecnico ricevuto dai partner ha questo valore |
| isaSegment |Facoltativa, stringa.  Indica il segmento ISA per l'acknowledgement tecnico. L'acknowledgment tecnico ricevuto dai partner ha questo valore |
| respondingInterchangeControlNumber |Facoltativa, stringa.  Indica il numero di controllo interscambio dell'acknowledgement tecnico. L'acknowledgment tecnico ricevuto dai partner ha questo valore |
| isMessageFailed |Obbligatoria, valore booleano.  Indica se il messaggio X12 ha avuto esito positivo o negativo |
| statusCode |Obbligatoria, enumerazione.  Indica il codice di stato dell'acknowledgement di interscambio.  I valori consentiti sono Accepted, Rejected o AcceptedWithErrros |
| processingStatus |Obbligatoria, enumerazione.  Indica lo stato dell'acknowledgement.  I valori consentiti sono Received, Generated o Sent |
| ta102 |Facoltativa, stringa. Indica la data di interscambio |
| ta103 |Facoltativa, stringa. Indica l'ora di interscambio |
| ta105 |Facoltativa, stringa. Indica il codice nota di interscambio |

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

| Proprietà | Descrizione |
| --- | --- |
| senderPartnerName |Facoltativa, stringa.  Indica il nome partner del mittente del messaggio X12 |
| receiverPartnerName |Facoltativa, stringa.  Indica il nome partner del destinatario del messaggio X12 |
| senderQualifier |Obbligatoria, stringa.  Indica il qualificatore del partner di trasmissione |
| senderIdentifier |Obbligatoria, stringa.  Indica l'identificatore del partner di trasmissione |
| receiverQualifier |Obbligatoria, stringa.  Indica il qualificatore del partner di ricezione |
| receiverIdentifier |Obbligatoria, stringa.  Indica l'identificatore del partner di ricezione |
| agreementName |Facoltativa, stringa.  Nome del contratto X12 in base al quale vengono risolti i messaggi |
| direction |Obbligatoria, enumerazione.  Indica la direzione del flusso di messaggi.  I valori consentiti sono receive o send |
| interchangeControlNumber |Facoltativa, stringa. Indica il numero di controllo interscambio |
| functionalGroupControlNumber |Facoltativa, indica il numero di controllo funzionale, tipo stringa |
| gsSegment |Facoltativa, stringa.  Indica il segmento GS del messaggio |
| isTechnicalAcknowledgmentExpected |Obbligatoria, valore booleano.  Indica se l'acknowledgement tecnico è configurato o meno nel contratto X12 |
| isFunctionalAcknowledgmentExpected |Obbligatoria, valore booleano.  Indica se l'acknowledgement funzionale è configurato o meno nel contratto X12 |
| isMessageFailed |Obbligatoria, valore booleano.  Indica se il messaggio X12 ha avuto esito positivo o negativo |
| gs01 |Facoltativa, stringa. Indica il codice identificatore funzionale |
| gs02 |Facoltativa, stringa. Indica il codice mittente applicazione |
| gs03 |Facoltativa, stringa. Indica il codice ricevitore applicazione |
| gs04 |Facoltativa, stringa. Indica la data del gruppo funzionale |
| gs05 |Facoltativa, stringa. Indica l'ora del gruppo funzionale |
| gs07 |Facoltativa, stringa. Indica il codice dell'agenzia responsabile |
| gs08 |Facoltativa, stringa. Indica versione/rilascio/codice identificativo del settore; tipo stringa |

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

| Proprietà | Descrizione |
| --- | --- |
| senderPartnerName |Facoltativa, stringa.  Indica il nome partner del mittente del messaggio X12 |
| receiverPartnerName |Facoltativa, stringa.  Indica il nome partner del destinatario del messaggio X12 |
| senderQualifier |Obbligatoria, stringa.  Indica il qualificatore del partner di trasmissione |
| senderIdentifier |Obbligatoria, stringa.  Indica l'identificatore del partner di trasmissione |
| receiverQualifier |Obbligatoria, stringa.  Indica il qualificatore del partner di ricezione |
| receiverIdentifier |Obbligatoria, stringa.  Indica l'identificatore del partner di ricezione |
| agreementName |Facoltativa, stringa.  Nome del contratto X12 in base al quale vengono risolti i messaggi |
| direction |Obbligatoria, enumerazione.  Indica la direzione del flusso di messaggi.  I valori consentiti sono receive o send |
| interchangeControlNumber |Facoltativa, stringa. Indica il numero di controllo interscambio. Il valore viene compilato per il lato di invio quando si riceve un acknowledgement tecnico dai partner |
| functionalGroupControlNumber |Facoltativa, stringa. Indica il numero di controllo del gruppo funzionale dell'acknowledgement tecnico. Il valore viene compilato per il lato di invio quando si riceve un acknowledgement tecnico dai partner |
| isaSegment |Facoltativa. Come sopra: il numero di controllo interscambio viene popolato solo in casi specifici. Tipo stringa |
| gsSegment |Facoltativa. Come sopra: il numero di controllo del gruppo funzionale viene popolato solo in casi specifici. Tipo stringa |
| respondingfunctionalGroupControlNumber |Facoltativa, indica il numero di controllo del gruppo funzionale originale, tipo stringa |
| respondingFunctionalGroupId |Facoltativa. Esegue il mapping ad AK101 nell'acknowledgement; ID del gruppo funzionale, tipo stringa |
| isMessageFailed |Obbligatoria, valore booleano.  Indica se il messaggio X12 ha avuto esito positivo o negativo |
| statusCode |Obbligatoria, enumerazione.  Indica il codice di stato dell'acknowledgement. I valori consentiti sono Accepted, Rejected o AcceptedWithErrros |
| processingStatus |Obbligatoria, enumerazione.  Indica lo stato di elaborazione dell'acknowledgement. I valori consentiti sono Received, Generated o Sent |
| ak903 |Facoltativa, stringa. Indica il numero di set di transazioni ricevuti |
| ak904 |Facoltativa, stringa. Indica il numero di set di transazioni accettati nel gruppo funzionale identificato |
| ak9Segment |Facoltativa, stringa.  Il segmento Ak9 indica se il gruppo funzionale identificato nel segmento ak1 è accettato o rifiutato e il motivo |

## <a name="next-steps"></a>Passaggi successivi
[Altre informazioni sul monitoraggio dei messaggi B2B](app-service-logic-monitor-b2b-message.md "Learn more about tracking B2B messages")   
[Rilevamento dei messaggi B2B nel portale di OMS](app-service-logic-track-b2b-messages-omsportal.md "Tracking B2B messages")   
[Altre informazioni sullo schema di rilevamento personalizzato](app-service-logic-track-integration-account-custom-tracking-shema.md "Learn about Custom Schema")   
[Altre informazioni sullo schema di rilevamento as2](app-service-logic-track-integration-account-as2-tracking-shemas.md "Learn about AS2 Schema")   
[Altre informazioni su Enterprise Integration Pack](app-service-logic-enterprise-integration-overview.md "Learn about Enterprise Integration Pack")  




<!--HONumber=Nov16_HO3-->


