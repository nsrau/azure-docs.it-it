---
title: Schemi di rilevamento X12 per i messaggi B2B
description: Creare schemi di rilevamento per monitorare i messaggi X12 per le app per la logica di Azure
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/01/2020
ms.openlocfilehash: 5b2df194761ebc167e67498a985960a4fce35f19
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76905296"
---
# <a name="create-schemas-for-tracking-x12-messages-in-azure-logic-apps"></a>Creare schemi per tenere traccia dei messaggi X12 in app per la logica di Azure

Per monitorare più facilmente il completamento delle operazioni, gli errori e le proprietà dei messaggi per le transazioni business-to-business (B2B) è possibile usare questi schemi di rilevamento X12 nell'account di integrazione:

* Schema di rilevamento X12 per set di transazioni
* Schema di rilevamento del riconoscimento del set di transazioni X12
* Schema di rilevamento X12 per interscambio
* Schema di rilevamento di riconoscimento interscambio X12
* Schema di rilevamento X12 per gruppi funzionali
* Schema di rilevamento dei riconoscimenti del gruppo funzionale X12

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
      "needAk2LoopForValidMessages": "",
      "segmentsCount": ""
   }
}
```

| Proprietà | Obbligatoria | Type | Descrizione |
|----------|----------|------|-------------|
| senderPartnerName | No | Stringa | Nome partner del mittente del messaggio X12 |
| receiverPartnerName | No | Stringa | Nome partner del destinatario del messaggio X12 |
| senderQualifier | Sì | string | Invia qualificatore partner |
| senderIdentifier | Sì | string | Invia identificatore partner |
| receiverQualifier | Sì | string | Ricevi qualificatore partner |
| receiverIdentifier | Sì | string | Ricevi identificatore partner |
| agreementName | No | Stringa | Nome del contratto X12 in base al quale vengono risolti i messaggi |
| direction | Sì | Enum | Direzione del flusso dei messaggi, che può essere `receive` o`send` |
| interchangeControlNumber | No | Stringa | Numero di controllo interscambio |
| functionalGroupControlNumber | No | Stringa | Numero di controllo funzionale |
| transactionSetControlNumber | No | Stringa | Numero di controllo set transazioni |
| CorrelationMessageId | No | Stringa | ID del messaggio di correlazione, ovvero una combinazione di {AgreementName} {*GroupControlNumber*} {TransactionSetControlNumber} |
| messageType | No | Stringa | Set di transazioni o tipo di documento |
| isMessageFailed | Sì | Boolean | Indica se il messaggio X12 ha avuto esito negativo |
| isTechnicalAcknowledgmentExpected | Sì | Boolean | Indica se il riconoscimento tecnico è configurato nel contratto X12 |
| isFunctionalAcknowledgmentExpected | Sì | Boolean | Indica se il riconoscimento funzionale è configurato nel contratto X12 |
| needAk2LoopForValidMessages | Sì | Boolean | Indica se il ciclo AK2 è obbligatorio per un messaggio valido |
| segmentsCount | No | Integer | Numero di segmenti nel set di transazioni X12 |
|||||

## <a name="x12-transaction-set-acknowledgment-tracking-schema"></a>Schema di rilevamento del riconoscimento del set di transazioni X12

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

| Proprietà | Obbligatoria | Type | Descrizione |
|----------|----------|------|-------------|
| senderPartnerName | No | Stringa | Nome partner del mittente del messaggio X12 |
| receiverPartnerName | No | Stringa | Nome partner del destinatario del messaggio X12 |
| senderQualifier | Sì | string | Invia qualificatore partner |
| senderIdentifier | Sì | string | Invia identificatore partner |
| receiverQualifier | Sì | string | Ricevi qualificatore partner |
| receiverIdentifier | Sì | string | Ricevi identificatore partner |
| agreementName | No | Stringa | Nome del contratto X12 in base al quale vengono risolti i messaggi |
| direction | Sì | Enum | Direzione del flusso dei messaggi, che può essere `receive` o`send` |
| interchangeControlNumber | No | Stringa | Numero di controllo interscambio del riconoscimento funzionale. Il valore viene popolato solo per il lato di trasmissione in cui viene ricevuto il riconoscimento funzionale per i messaggi inviati al partner. |
| functionalGroupControlNumber | No | Stringa | Numero di controllo del gruppo funzionale del riconoscimento funzionale. Il valore viene popolato solo per il lato di trasmissione in cui viene ricevuto il riconoscimento funzionale per i messaggi inviati al partner |
| isaSegment | No | Stringa | Segmento ISA del messaggio. Il valore viene popolato solo per il lato di trasmissione in cui viene ricevuto il riconoscimento funzionale per i messaggi inviati al partner |
| gsSegment | No | Stringa | Segmento GS del messaggio. Il valore viene popolato solo per il lato di trasmissione in cui viene ricevuto il riconoscimento funzionale per i messaggi inviati al partner |
| respondingfunctionalGroupControlNumber | No | Stringa | Numero di controllo interscambio risposta |
| respondingFunctionalGroupId | No | Stringa | ID del gruppo funzionale di risposta, che esegue il mapping a AK101 nel riconoscimento |
| respondingtransactionSetControlNumber | No | Stringa | Numero di controllo del set di transazioni di risposta |
| respondingTransactionSetId | No | Stringa | ID del set di transazioni di risposta, che esegue il mapping a AK201 nel riconoscimento |
| statusCode | Sì | Boolean | Codice di stato di riconoscimento set transazioni |
| segmentsCount | Sì | Enum | Codice di stato di riconoscimento con questi valori consentiti: `Accepted`, e `Rejected``AcceptedWithErrors` |
| processingStatus | Sì | Enum | Stato di elaborazione del riconoscimento con questi valori consentiti `Received`: `Generated`, e`Sent` |
| CorrelationMessageId | No | Stringa | ID del messaggio di correlazione, ovvero una combinazione di {AgreementName} {*GroupControlNumber*} {TransactionSetControlNumber} |
| isMessageFailed | Sì | Boolean | Indica se il messaggio X12 ha avuto esito negativo |
| ak2Segment | No | Stringa | Riconoscimento per un set di transazioni all'interno del gruppo funzionale ricevuto |
| ak3Segment | No | Stringa | Segnala gli errori in un segmento di dati |
| ak5Segment | No | Stringa | Indica se il set di transazioni identificato nel segmento AK2 è accettato o rifiutato e perché |
|||||

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

| Proprietà | Obbligatoria | Type | Descrizione |
|----------|----------|------|-------------|
| senderPartnerName | No | Stringa | Nome partner del mittente del messaggio X12 |
| receiverPartnerName | No | Stringa | Nome partner del destinatario del messaggio X12 |
| senderQualifier | Sì | string | Invia qualificatore partner |
| senderIdentifier | Sì | string | Invia identificatore partner |
| receiverQualifier | Sì | string | Ricevi qualificatore partner |
| receiverIdentifier | Sì | string | Ricevi identificatore partner |
| agreementName | No | Stringa | Nome del contratto X12 in base al quale vengono risolti i messaggi |
| direction | Sì | Enum | Direzione del flusso dei messaggi, che può essere `receive` o`send` |
| interchangeControlNumber | No | Stringa | Numero di controllo interscambio |
| isaSegment | No | Stringa | Segmento ISA del messaggio |
| isTechnicalAcknowledgmentExpected | Boolean | Indica se il riconoscimento tecnico è configurato nel contratto X12  |
| isMessageFailed | Sì | Boolean | Indica se il messaggio X12 ha avuto esito negativo |
| isa09 | No | Stringa | Data interscambio documento X12 |
| isa10 | No | Stringa | Ora di interscambio del documento X12 |
| isa11 | No | Stringa | Identificatore standard di controllo interscambio X12 |
| isa12 | No | Stringa | Numero di versione del controllo di interscambio X12 |
| isa14 | No | Stringa | È richiesto il riconoscimento X12 |
| isa15 | No | Stringa | Indicatore per test o produzione |
| isa16 | No | Stringa | Separatore elementi |
|||||

## <a name="x12-interchange-acknowledgment-tracking-schema"></a>Schema di rilevamento di riconoscimento interscambio X12

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

| Proprietà | Obbligatoria | Type | Descrizione |
|----------|----------|------|-------------|
| senderPartnerName | No | Stringa | Nome partner del mittente del messaggio X12 |
| receiverPartnerName | No | Stringa | Nome partner del destinatario del messaggio X12 |
| senderQualifier | Sì | string | Invia qualificatore partner |
| senderIdentifier | Sì | string | Invia identificatore partner |
| receiverQualifier | Sì | string | Ricevi qualificatore partner |
| receiverIdentifier | Sì | string | Ricevi identificatore partner |
| agreementName | No | Stringa | Nome del contratto X12 in base al quale vengono risolti i messaggi |
| direction | Sì | Enum | Direzione del flusso dei messaggi, che può essere `receive` o`send` |
| interchangeControlNumber | No | Stringa | Numero di controllo interscambio del riconoscimento tecnico ricevuto dai partner |
| isaSegment | No | Stringa | Segmento ISA per il riconoscimento tecnico ricevuto dai partner |
| respondingInterchangeControlNumber | No | Stringa | Numero di controllo interscambio per il riconoscimento tecnico ricevuto dai partner |
| isMessageFailed | Sì | Boolean | Indica se il messaggio X12 ha avuto esito negativo |
| statusCode | Sì | Enum | Codice di stato di riconoscimento interscambio con questi valori `Accepted`consentiti:, `Rejected`e`AcceptedWithErrors` |
| processingStatus | Sì | Enum | Stato di riconoscimento con i valori consentiti seguenti: `Received`, `Generated`e`Sent` |
| ta102 | No | Stringa | Data interscambio |
| ta103 | No | Stringa | Ora interscambio |
| ta105 | No | Stringa | Codice nota interscambio |
|||||

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

| Proprietà | Obbligatoria | Type | Descrizione |
|----------|----------|------|-------------|
| senderPartnerName | No | Stringa | Nome partner del mittente del messaggio X12 |
| receiverPartnerName | No | Stringa | Nome partner del destinatario del messaggio X12 |
| senderQualifier | Sì | string | Invia qualificatore partner |
| senderIdentifier | Sì | string | Invia identificatore partner |
| receiverQualifier | Sì | string | Ricevi qualificatore partner |
| receiverIdentifier | Sì | string | Ricevi identificatore partner |
| agreementName | No | Stringa | Nome dell'accordo X12 in cui vengono risolti i messaggi |
| direction | Sì | Enum | Direzione del flusso di messaggi, ovvero ricezione o invio |
| interchangeControlNumber | No | Stringa | Numero di controllo interscambio |
| functionalGroupControlNumber | No | Stringa | Numero di controllo funzionale |
| gsSegment | No | Stringa | Segmento GS del messaggio |
| isTechnicalAcknowledgmentExpected | Sì | Boolean | Indica se il riconoscimento tecnico è configurato nel contratto X12 |
| isFunctionalAcknowledgmentExpected | Sì | Boolean | Indica se il riconoscimento funzionale è configurato nel contratto X12 |
| isMessageFailed | Sì | Boolean | Indica se il messaggio X12 ha avuto esito negativo |
| gs01 | No | Stringa | Codice identificatore funzionale |
| gs02 | No | Stringa | Codice del mittente dell'applicazione |
| gs03 | No | Stringa | Codice ricevitore applicazione |
| gs04 | No | Stringa | Data gruppo funzionale |
| gs05 | No | Stringa | Tempo del gruppo funzionale |
| gs07 | No | Stringa | Codice dell'agenzia responsabile |
| gs08 | No | Stringa | Codice identificatore per la versione, la versione o il settore |
|||||

## <a name="x12-functional-group-acknowledgment-tracking-schema"></a>Schema di rilevamento dei riconoscimenti del gruppo funzionale X12

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

| Proprietà | Obbligatoria | Type | Descrizione |
|----------|----------|------|-------------|
| senderPartnerName | No | Stringa | Nome partner del mittente del messaggio X12 |
| receiverPartnerName | No | Stringa | Nome partner del destinatario del messaggio X12 |
| senderQualifier | Sì | string | Invia qualificatore partner |
| senderIdentifier | Sì | string | Invia identificatore partner |
| receiverQualifier | Sì | string | Ricevi qualificatore partner |
| receiverIdentifier | Sì | string | Ricevi identificatore partner |
| agreementName | No | Stringa | Nome del contratto X12 in base al quale vengono risolti i messaggi |
| direction | Sì | Enum | Direzione del flusso dei messaggi, che può essere `receive` o`send` |
| interchangeControlNumber | No | Stringa | Numero di controllo interscambio, che viene popolato per il lato di invio quando viene ricevuto un riconoscimento tecnico dai partner |
| functionalGroupControlNumber | No | Stringa | Numero di controllo del gruppo funzionale del riconoscimento tecnico, che viene popolato per il lato di invio quando viene ricevuto un riconoscimento tecnico dai partner |
| isaSegment | No | Stringa | Uguale al numero di controllo interscambio, ma popolato solo in casi specifici |
| gsSegment | No | Stringa | Uguale al numero di controllo del gruppo funzionale, ma popolato solo in casi specifici |
| respondingfunctionalGroupControlNumber | No | Stringa | Numero di controllo del gruppo funzionale originale |
| respondingFunctionalGroupId | No | Stringa | Esegue il mapping a AK101 nell'ID del gruppo funzionale di riconoscimento |
| isMessageFailed | Boolean | Indica se il messaggio X12 ha avuto esito negativo |
| statusCode | Sì | Enum | Codice di stato di riconoscimento con questi valori consentiti: `Accepted`, e `Rejected``AcceptedWithErrors` |
| processingStatus | Sì | Enum | Stato di elaborazione del riconoscimento con questi valori consentiti `Received`: `Generated`, e`Sent` |
| ak903 | No | Stringa | Numero di set di transazioni ricevuti |
| ak904 | No | Stringa | Numero di set di transazioni accettati nel gruppo funzionale identificato |
| ak9Segment | No | Stringa | Indica se il gruppo funzionale identificato nel segmento AK1 è accettato o rifiutato e perché |
|||||

## <a name="b2b-protocol-tracking-schemas"></a>Schemi di rilevamento per il protocollo B2B

Per informazioni sugli schemi di rilevamento per il protocollo B2B, vedere:

* [Schemi di rilevamento AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Schemi di rilevamento personalizzati B2B](logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Passaggi successivi

* [Monitorare i messaggi B2B con i log di Monitoraggio di Azure](../logic-apps/monitor-b2b-messages-log-analytics.md)