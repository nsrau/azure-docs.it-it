---
title: Schemi di rilevamento X12 per i messaggi B2B
description: Creare schemi di rilevamento X12 che consentono di monitorare i messaggi B2B negli account di integrazione per App per la logica di Azure con Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/27/2017
ms.openlocfilehash: 6f2356600f5b6a637da731c650b26d968092e2f6
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74791721"
---
# <a name="create-schemas-for-tracking-x12-messages-in-integration-accounts-for-azure-logic-apps"></a>Creare schemi per il rilevamento di messaggi X12 negli account di integrazione per App per la logica di Azure

Per monitorare più facilmente il completamento delle operazioni, gli errori e le proprietà dei messaggi per le transazioni business-to-business (B2B) è possibile usare questi schemi di rilevamento X12 nell'account di integrazione:

* Schema di rilevamento X12 per set di transazioni
* Schema di rilevamento X12 per acknowledgement di set di transazioni
* Schema di rilevamento X12 per interscambio
* Schema di rilevamento X12 per acknowledgement di interscambio
* Schema di rilevamento X12 per gruppi funzionali
* Schema di rilevamento X12 per acknowledgement di gruppi funzionali

## <a name="x12-transaction-set-tracking-schema"></a>Schema di rilevamento X12 per set di transazioni

```json
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
```

| Proprietà | Type | Description |
| --- | --- | --- |
| senderPartnerName | Stringa | Nome partner del mittente del messaggio X12. (Facoltativa) |
| receiverPartnerName | Stringa | Nome partner del destinatario del messaggio X12. (Facoltativa) |
| senderQualifier | Stringa | Invia il qualificatore del partner. (Obbligatoria) |
| senderIdentifier | Stringa | Invia l'identificatore del partner. (Obbligatoria) |
| receiverQualifier | Stringa | Riceve il qualificatore del partner. (Obbligatoria) |
| receiverIdentifier | Stringa | Riceve l'identificatore del partner. (Obbligatoria) |
| agreementName | Stringa | Nome del contratto X12 in base al quale vengono risolti i messaggi. (Facoltativa) |
| direction | Enum | Direzione del flusso dei messaggi, ricezione o invio. (Obbligatoria) |
| interchangeControlNumber | Stringa | Numero di controllo interscambio. (Facoltativa) |
| functionalGroupControlNumber | Stringa | Numero di controllo funzionale. (Facoltativa) |
| transactionSetControlNumber | Stringa | Numero di controllo set transazioni. (Facoltativa) |
| CorrelationMessageId | Stringa | ID messaggio di correlazione. Una combinazione di {AgreementName}{*GroupControlNumber*}{TransactionSetControlNumber}. (Facoltativa) |
| messageType | Stringa | Set di transazioni o tipo di documento. (Facoltativa) |
| isMessageFailed | boolean | Indica se il messaggio X12 non è riuscito. (Obbligatoria) |
| isTechnicalAcknowledgmentExpected | boolean | Indica se l'acknowledgement tecnico è configurato nel contratto X12. (Obbligatoria) |
| isFunctionalAcknowledgmentExpected | boolean | Indica se l'acknowledgement funzionale è configurato nel contratto X12. (Obbligatoria) |
| needAk2LoopForValidMessages | boolean | Indica se il ciclo AK2 è necessario per un messaggio valido. (Obbligatoria) |
| segmentsCount | Integer | Numero di segmenti nel set di transazioni X12. (Facoltativa) |
||||

## <a name="x12-transaction-set-acknowledgement-tracking-schema"></a>Schema di rilevamento X12 per acknowledgement di set di transazioni

```json
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
      "CorrelationMessageId": "",
      "isMessageFailed": "",
      "ak2Segment": "",
      "ak3Segment": "",
      "ak5Segment": ""
   }
}
```

| Proprietà | Type | Description |
| --- | --- | --- |
| senderPartnerName | Stringa | Nome partner del mittente del messaggio X12. (Facoltativa) |
| receiverPartnerName | Stringa | Nome partner del destinatario del messaggio X12. (Facoltativa) |
| senderQualifier | Stringa | Invia il qualificatore del partner. (Obbligatoria) |
| senderIdentifier | Stringa | Invia l'identificatore del partner. (Obbligatoria) |
| receiverQualifier | Stringa | Riceve il qualificatore del partner. (Obbligatoria) |
| receiverIdentifier | Stringa | Riceve l'identificatore del partner. (Obbligatoria) |
| agreementName | Stringa | Nome del contratto X12 in base al quale vengono risolti i messaggi. (Facoltativa) |
| direction | Enum | Direzione del flusso dei messaggi, ricezione o invio. (Obbligatoria) |
| interchangeControlNumber | Stringa | Numero di controllo interscambio dell'acknowledgement funzionale. Il valore viene popolato solo per il lato di invio che ha ricevuto un acknowledgement funzionale per il messaggio inviato al partner. (Facoltativa) |
| functionalGroupControlNumber | Stringa | Numero di controllo del gruppo funzionale dell'acknowledgement funzionale. Il valore viene popolato solo per il lato di invio che ha ricevuto un acknowledgement funzionale per il messaggio inviato al partner. (Facoltativa) |
| isaSegment | Stringa | Segmento ISA del messaggio. Il valore viene popolato solo per il lato di invio che ha ricevuto un acknowledgement funzionale per il messaggio inviato al partner. (Facoltativa) |
| gsSegment | Stringa | Segmento GS del messaggio. Il valore viene popolato solo per il lato di invio che ha ricevuto un acknowledgement funzionale per il messaggio inviato al partner. (Facoltativa) |
| respondingfunctionalGroupControlNumber | Stringa | Numero di controllo interscambio di risposta. (Facoltativa) |
| respondingFunctionalGroupId | Stringa | ID del gruppo funzionale di risposta, che esegue il mapping ad AK101 nell'acknowledgement. (Facoltativa) |
| respondingtransactionSetControlNumber | Stringa | Numero di controllo set transazioni di risposta. (Facoltativa) |
| respondingTransactionSetId | Stringa | ID del set di transazioni di risposta, che esegue il mapping ad AK201 nell'acknowledgement. (Facoltativa) |
| statusCode | boolean | Codice di stato dell'acknowledgement del set di transazioni. (Obbligatoria) |
| segmentsCount | Enum | Codice di stato dell'acknowledgement. I valori consentiti sono **Accepted**, **Rejected** e **AcceptedWithErrors**. (Obbligatoria) |
| processingStatus | Enum | Stato di elaborazione dell'acknowledgement. I valori consentiti sono **Received**, **Generated** e **Sent**. (Obbligatoria) |
| CorrelationMessageId | Stringa | ID messaggio di correlazione. Una combinazione di {AgreementName}{*GroupControlNumber*}{TransactionSetControlNumber}. (Facoltativa) |
| isMessageFailed | boolean | Indica se il messaggio X12 non è riuscito. (Obbligatoria) |
| ak2Segment | Stringa | Acknowledgement per un set di transazioni nel gruppo funzionale ricevuto. (Facoltativa) |
| ak3Segment | Stringa | Segnala gli errori in un segmento di dati. (Facoltativa) |
| ak5Segment | Stringa | Indica se il set di transazioni identificato nel segmento AK2 è accettato o rifiutato e il motivo. (Facoltativa) |
||||

## <a name="x12-interchange-tracking-schema"></a>Schema di rilevamento X12 per interscambio

```json
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
```

| Proprietà | Type | Description |
| --- | --- | --- |
| senderPartnerName | Stringa | Nome partner del mittente del messaggio X12. (Facoltativa) |
| receiverPartnerName | Stringa | Nome partner del destinatario del messaggio X12. (Facoltativa) |
| senderQualifier | Stringa | Invia il qualificatore del partner. (Obbligatoria) |
| senderIdentifier | Stringa | Invia l'identificatore del partner. (Obbligatoria) |
| receiverQualifier | Stringa | Riceve il qualificatore del partner. (Obbligatoria) |
| receiverIdentifier | Stringa | Riceve l'identificatore del partner. (Obbligatoria) |
| agreementName | Stringa | Nome del contratto X12 in base al quale vengono risolti i messaggi. (Facoltativa) |
| direction | Enum | Direzione del flusso dei messaggi, ricezione o invio. (Obbligatoria) |
| interchangeControlNumber | Stringa | Numero di controllo interscambio. (Facoltativa) |
| isaSegment | Stringa | Segmento ISA del messaggio. (Facoltativa) |
| isTechnicalAcknowledgmentExpected | boolean | Indica se l'acknowledgement tecnico è configurato nel contratto X12. (Obbligatoria) |
| isMessageFailed | boolean | Indica se il messaggio X12 non è riuscito. (Obbligatoria) |
| isa09 | Stringa | Data di interscambio del documento X12. (Facoltativa) |
| isa10 | Stringa | Ora di interscambio del documento X12. (Facoltativa) |
| isa11 | Stringa | Identificatore standard del controllo di interscambio X12. (Facoltativa) |
| isa12 | Stringa | Numero di versione del controllo di interscambio X12. (Facoltativa) |
| isa14 | Stringa | È necessario l'acknowledgement X12. (Facoltativa) |
| isa15 | Stringa | Indicatore per test o produzione. (Facoltativa) |
| isa16 | Stringa | Separatore elementi. (Facoltativa) |
||||

## <a name="x12-interchange-acknowledgement-tracking-schema"></a>Schema di rilevamento X12 per acknowledgement di interscambio

```json
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
```

| Proprietà | Type | Description |
| --- | --- | --- |
| senderPartnerName | Stringa | Nome partner del mittente del messaggio X12. (Facoltativa) |
| receiverPartnerName | Stringa | Nome partner del destinatario del messaggio X12. (Facoltativa) |
| senderQualifier | Stringa | Invia il qualificatore del partner. (Obbligatoria) |
| senderIdentifier | Stringa | Invia l'identificatore del partner. (Obbligatoria) |
| receiverQualifier | Stringa | Riceve il qualificatore del partner. (Obbligatoria) |
| receiverIdentifier | Stringa | Riceve l'identificatore del partner. (Obbligatoria) |
| agreementName | Stringa | Nome del contratto X12 in base al quale vengono risolti i messaggi. (Facoltativa) |
| direction | Enum | Direzione del flusso dei messaggi, ricezione o invio. (Obbligatoria) |
| interchangeControlNumber | Stringa | Numero di controllo interscambio dell'acknowledgement tecnico ricevuto dai partner. (Facoltativa) |
| isaSegment | Stringa | Segmento ISA per l'acknowledgement tecnico ricevuto dai partner. (Facoltativa) |
| respondingInterchangeControlNumber |Stringa | Numero di controllo interscambio per l'acknowledgement tecnico ricevuto dai partner. (Facoltativa) |
| isMessageFailed | boolean | Indica se il messaggio X12 non è riuscito. (Obbligatoria) |
| statusCode | Enum | Codice di stato dell'acknowledgement di interscambio. I valori consentiti sono **Accepted**, **Rejected** e **AcceptedWithErrors**. (Obbligatoria) |
| processingStatus | Enum | Stato dell'acknowledgement. I valori consentiti sono **Received**, **Generated** e **Sent**. (Obbligatoria) |
| ta102 | Stringa | Data di interscambio. (Facoltativa) |
| ta103 | Stringa | Ora di interscambio. (Facoltativa) |
| ta105 | Stringa | Codice della nota di interscambio. (Facoltativa) |
||||

## <a name="x12-functional-group-tracking-schema"></a>Schema di rilevamento X12 per gruppi funzionali

```json
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
```

| Proprietà | Type | Description |
| --- | --- | --- |
| senderPartnerName | Stringa | Nome partner del mittente del messaggio X12. (Facoltativa) |
| receiverPartnerName | Stringa | Nome partner del destinatario del messaggio X12. (Facoltativa) |
| senderQualifier | Stringa | Invia il qualificatore del partner. (Obbligatoria) |
| senderIdentifier | Stringa | Invia l'identificatore del partner. (Obbligatoria) |
| receiverQualifier | Stringa | Riceve il qualificatore del partner. (Obbligatoria) |
| receiverIdentifier | Stringa | Riceve l'identificatore del partner. (Obbligatoria) |
| agreementName | Stringa | Nome del contratto X12 in base al quale vengono risolti i messaggi. (Facoltativa) |
| direction | Enum | Direzione del flusso dei messaggi, ricezione o invio. (Obbligatoria) |
| interchangeControlNumber | Stringa | Numero di controllo interscambio. (Facoltativa) |
| functionalGroupControlNumber | Stringa | Numero di controllo funzionale. (Facoltativa) |
| gsSegment | Stringa | Segmento GS del messaggio. (Facoltativa) |
| isTechnicalAcknowledgmentExpected | boolean | Indica se l'acknowledgement tecnico è configurato nel contratto X12. (Obbligatoria) |
| isFunctionalAcknowledgmentExpected | boolean | Indica se l'acknowledgement funzionale è configurato nel contratto X12. (Obbligatoria) |
| isMessageFailed | boolean | Indica se il messaggio X12 non è riuscito. (Obbligatoria)|
| gs01 | Stringa | Codice identificatore funzionale. (Facoltativa) |
| gs02 | Stringa | Codice del mittente dell'applicazione. (Facoltativa) |
| gs03 | Stringa | Codice del ricevitore dell'applicazione. (Facoltativa) |
| gs04 | Stringa | Data del gruppo funzionale. (Facoltativa) |
| gs05 | Stringa | Ora del gruppo funzionale. (Facoltativa) |
| gs07 | Stringa | Codice agenzia responsabile. (Facoltativa) |
| gs08 | Stringa | Codice identificatore versione/rilascio/settore. (Facoltativa) |
||||

## <a name="x12-functional-group-acknowledgement-tracking-schema"></a>Schema di rilevamento X12 per acknowledgement di gruppi funzionali

```json
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
```

| Proprietà | Type | Description |
| --- | --- | --- |
| senderPartnerName | Stringa | Nome partner del mittente del messaggio X12. (Facoltativa) |
| receiverPartnerName | Stringa | Nome partner del destinatario del messaggio X12. (Facoltativa) |
| senderQualifier | Stringa | Invia il qualificatore del partner. (Obbligatoria) |
| senderIdentifier | Stringa | Invia l'identificatore del partner. (Obbligatoria) |
| receiverQualifier | Stringa | Riceve il qualificatore del partner. (Obbligatoria) |
| receiverIdentifier | Stringa | Riceve l'identificatore del partner. (Obbligatoria) |
| agreementName | Stringa | Nome del contratto X12 in base al quale vengono risolti i messaggi. (Facoltativa) |
| direction | Enum | Direzione del flusso dei messaggi, ricezione o invio. (Obbligatoria) |
| interchangeControlNumber | Stringa | Numero di controllo interscambio, che viene popolato per il lato di invio quando un acknowledgement tecnico viene ricevuto dai partner. (Facoltativa) |
| functionalGroupControlNumber | Stringa | Numero di controllo gruppo funzionale dell'acknowledgement tecnico, che viene popolato per il lato di invio quando un acknowledgement tecnico viene ricevuto dai partner. (Facoltativa) |
| isaSegment | Stringa | Come il numero di controllo interscambio, ma viene popolato solo in casi specifici. (Facoltativa) |
| gsSegment | Stringa | Come il numero di controllo gruppo funzionale, ma viene popolato solo in casi specifici. (Facoltativa) |
| respondingfunctionalGroupControlNumber | Stringa | Numero di controllo del gruppo funzionale originale. (Facoltativa) |
| respondingFunctionalGroupId | Stringa | Esegue il mapping ad AK101 nell'ID del gruppo funzionale dell'acknowledgement. (Facoltativa) |
| isMessageFailed | boolean | Indica se il messaggio X12 non è riuscito. (Obbligatoria) |
| statusCode | Enum | Codice di stato dell'acknowledgement. I valori consentiti sono **Accepted**, **Rejected** e **AcceptedWithErrors**. (Obbligatoria) |
| processingStatus | Enum | Stato di elaborazione dell'acknowledgement. I valori consentiti sono **Received**, **Generated** e **Sent**. (Obbligatoria) |
| ak903 | Stringa | Numero di set transazioni ricevuti. (Facoltativa) |
| ak904 | Stringa | Numero di set di transazioni accettati nel gruppo funzionale identificato. (Facoltativa) |
| ak9Segment | Stringa | Indica se il gruppo funzionale identificato nel segmento AK1 è accettato o rifiutato e il motivo. (Facoltativa) |
|||| 

## <a name="b2b-protocol-tracking-schemas"></a>Schemi di rilevamento per il protocollo B2B

Per informazioni sugli schemi di rilevamento per il protocollo B2B, vedere:

* [Schemi di rilevamento AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)   
* [Schemi di rilevamento personalizzati B2B](logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sul [monitoraggio dei messaggi B2B](logic-apps-monitor-b2b-message.md).
* Informazioni sul [rilevamento dei messaggi B2B nei log di monitoraggio di Azure](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).
