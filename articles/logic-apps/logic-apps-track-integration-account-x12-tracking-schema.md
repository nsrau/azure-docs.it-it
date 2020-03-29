---
title: Schemi di rilevamento X12 per i messaggi B2BX12 tracking schemas for B2B messages
description: Creare schemi di rilevamento per monitorare i messaggi X12 per le app per la logica di AzureCreate tracking schemas to monitor X12 messages for Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/01/2020
ms.openlocfilehash: 5b2df194761ebc167e67498a985960a4fce35f19
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905296"
---
# <a name="create-schemas-for-tracking-x12-messages-in-azure-logic-apps"></a>Creare schemi per tenere traccia dei messaggi X12 nelle app per la logica di AzureCreate schemas for tracking X12 messages in Azure Logic Apps

Per monitorare più facilmente il completamento delle operazioni, gli errori e le proprietà dei messaggi per le transazioni business-to-business (B2B) è possibile usare questi schemi di rilevamento X12 nell'account di integrazione:

* Schema di rilevamento X12 per set di transazioni
* Schema di rilevamento del set di transazioni X12X12 transaction set acknowledgment tracking schema
* Schema di rilevamento X12 per interscambio
* Schema di rilevamento del riconoscimento interscambio X12X12 interchange acknowledgment tracking schema
* Schema di rilevamento X12 per gruppi funzionali
* Schema di rilevamento del riconoscimento del gruppo funzionale X12X12 functional group acknowledgment tracking schema

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
| senderPartnerName | No | string | Nome partner del mittente del messaggio X12 |
| receiverPartnerName | No | string | Nome partner del destinatario del messaggio X12 |
| senderQualifier | Sì | string | Invia qualificatore partner |
| senderIdentifier | Sì | string | Invia identificatore partner |
| receiverQualifier | Sì | string | Qualificatore partner di ricezione |
| receiverIdentifier | Sì | string | Identificatore partner di ricezione |
| agreementName | No | string | Nome del contratto X12 in base al quale vengono risolti i messaggi |
| direction | Sì | Enum | Direzione del flusso di `receive` messaggi, ovvero`send` |
| interchangeControlNumber | No | string | Numero di controllo interscambio |
| functionalGroupControlNumber | No | string | Numero di controllo funzionale |
| transactionSetControlNumber | No | string | Numero di controllo set transazioni |
| CorrelationMessageId | No | string | ID del messaggio di correlazione, che è una combinazione di "AgreementName"*GroupControlNumber*"TransactionSetControlNumber" |
| messageType | No | string | Set di transazioni o tipo di documento |
| isMessageFailed | Sì | Boolean | Se il messaggio X12 non è riuscito |
| isTechnicalAcknowledgmentExpected | Sì | Boolean | Se il riconoscimento tecnico è configurato nell'accordo X12 |
| isFunctionalAcknowledgmentExpected | Sì | Boolean | Se il riconoscimento funzionale è configurato nell'accordo X12 |
| needAk2LoopForValidMessages | Sì | Boolean | Se il ciclo AK2 è obbligatorio per un messaggio valido |
| segmentsCount | No | Integer | Numero di segmenti nel set di transazioni X12 |
|||||

## <a name="x12-transaction-set-acknowledgment-tracking-schema"></a>Schema di rilevamento del set di transazioni X12X12 transaction set acknowledgment tracking schema

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
| senderPartnerName | No | string | Nome partner del mittente del messaggio X12 |
| receiverPartnerName | No | string | Nome partner del destinatario del messaggio X12 |
| senderQualifier | Sì | string | Invia qualificatore partner |
| senderIdentifier | Sì | string | Invia identificatore partner |
| receiverQualifier | Sì | string | Qualificatore partner di ricezione |
| receiverIdentifier | Sì | string | Identificatore partner di ricezione |
| agreementName | No | string | Nome del contratto X12 in base al quale vengono risolti i messaggi |
| direction | Sì | Enum | Direzione del flusso di `receive` messaggi, ovvero`send` |
| interchangeControlNumber | No | string | Numero di controllo dell'interscambio del riconoscimento funzionale. Il valore viene popolato solo per il lato di invio in cui viene ricevuto il riconoscimento funzionale per i messaggi inviati al partner. |
| functionalGroupControlNumber | No | string | Numero di controllo del gruppo funzionale del riconoscimento funzionale. Il valore viene popolato solo per il lato di invio in cui viene ricevuto il riconoscimento funzionale per i messaggi inviati al partner |
| isaSegment | No | string | Segmento ISA del messaggio. Il valore viene popolato solo per il lato di invio in cui viene ricevuto il riconoscimento funzionale per i messaggi inviati al partner |
| gsSegment | No | string | Segmento GS del messaggio. Il valore viene popolato solo per il lato di invio in cui viene ricevuto il riconoscimento funzionale per i messaggi inviati al partner |
| respondingfunctionalGroupControlNumber | No | string | Il numero di controllo dell'interscambio che risponde |
| respondingFunctionalGroupId | No | string | L'ID del gruppo funzionale di risposta, che esegue il mapping ad AK101 nel |
| respondingtransactionSetControlNumber | No | string | Il numero di controllo del set di transazioni di risposta |
| respondingTransactionSetId | No | string | ID del set di transazioni di risposta, che esegue il mapping ad AK201 nel |
| statusCode | Sì | Boolean | Codice di stato di riconoscimento del set di transazioniTransaction set acknowledgment status code |
| segmentsCount | Sì | Enum | Codice di stato di riconoscimento con i seguenti valori consentiti: `Accepted`, `Rejected`e`AcceptedWithErrors` |
| processingStatus | Sì | Enum | Stato di elaborazione del riconoscimento `Received` `Generated`con i seguenti valori consentiti: , e`Sent` |
| CorrelationMessageId | No | string | ID del messaggio di correlazione, che è una combinazione di "AgreementName"*GroupControlNumber*"TransactionSetControlNumber" |
| isMessageFailed | Sì | Boolean | Se il messaggio X12 non è riuscito |
| ak2Segment | No | string | Riconoscimento per un set di transazioni all'interno del gruppo funzionale ricevuto |
| ak3Segment | No | string | Segnala errori in un segmento di dati |
| ak5Segment | No | string | Indica se il set di transazioni identificato nel segmento AK2 viene accettato o rifiutato e se |
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
| senderPartnerName | No | string | Nome partner del mittente del messaggio X12 |
| receiverPartnerName | No | string | Nome partner del destinatario del messaggio X12 |
| senderQualifier | Sì | string | Invia qualificatore partner |
| senderIdentifier | Sì | string | Invia identificatore partner |
| receiverQualifier | Sì | string | Qualificatore partner di ricezione |
| receiverIdentifier | Sì | string | Identificatore partner di ricezione |
| agreementName | No | string | Nome del contratto X12 in base al quale vengono risolti i messaggi |
| direction | Sì | Enum | Direzione del flusso di `receive` messaggi, ovvero`send` |
| interchangeControlNumber | No | string | Numero di controllo interscambio |
| isaSegment | No | string | Segmento ISA messaggio |
| isTechnicalAcknowledgmentExpected | Boolean | Se il riconoscimento tecnico è configurato nell'accordo X12  |
| isMessageFailed | Sì | Boolean | Se il messaggio X12 non è riuscito |
| isa09 | No | string | Data di interscambio documenti X12 |
| isa10 | No | string | Tempo di interscambio documenti X12 |
| isa11 | No | string | Identificatore degli standard di controllo interscambio X12 |
| isa12 | No | string | Numero di versione del controllo interscambio X12 |
| isa14 | No | string | È richiesto il riconoscimento X12 |
| isa15 | No | string | Indicatore per la prova o la produzione |
| isa16 | No | string | Separatore elementi |
|||||

## <a name="x12-interchange-acknowledgment-tracking-schema"></a>Schema di rilevamento del riconoscimento interscambio X12X12 interchange acknowledgment tracking schema

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
| senderPartnerName | No | string | Nome partner del mittente del messaggio X12 |
| receiverPartnerName | No | string | Nome partner del destinatario del messaggio X12 |
| senderQualifier | Sì | string | Invia qualificatore partner |
| senderIdentifier | Sì | string | Invia identificatore partner |
| receiverQualifier | Sì | string | Qualificatore partner di ricezione |
| receiverIdentifier | Sì | string | Identificatore partner di ricezione |
| agreementName | No | string | Nome del contratto X12 in base al quale vengono risolti i messaggi |
| direction | Sì | Enum | Direzione del flusso di `receive` messaggi, ovvero`send` |
| interchangeControlNumber | No | string | Numero di controllo interscambio del riconoscimento tecnico ricevuto dai partner |
| isaSegment | No | string | Segmento ISA per il riconoscimento tecnico ricevuto dai partner |
| respondingInterchangeControlNumber | No | string | Numero di controllo interscambio per il riconoscimento tecnico ricevuto dai partner |
| isMessageFailed | Sì | Boolean | Se il messaggio X12 non è riuscito |
| statusCode | Sì | Enum | Codice di stato del riconoscimento `Accepted`interscambio con i seguenti valori consentiti: , `Rejected`e`AcceptedWithErrors` |
| processingStatus | Sì | Enum | Lo stato di riconoscimento `Received` `Generated`con i seguenti valori consentiti: , e`Sent` |
| ta102 | No | string | Data di interscambio |
| ta103 | No | string | Tempo di interscambio |
| ta105 | No | string | Codice nota di interscambio |
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
| senderPartnerName | No | string | Nome partner del mittente del messaggio X12 |
| receiverPartnerName | No | string | Nome partner del destinatario del messaggio X12 |
| senderQualifier | Sì | string | Invia qualificatore partner |
| senderIdentifier | Sì | string | Invia identificatore partner |
| receiverQualifier | Sì | string | Qualificatore partner di ricezione |
| receiverIdentifier | Sì | string | Identificatore partner di ricezione |
| agreementName | No | string | Il nome dell'accordo X12 in cui vengono risolti i messaggi |
| direction | Sì | Enum | Direzione del flusso di messaggi, ricezione o invio |
| interchangeControlNumber | No | string | Numero di controllo interscambio |
| functionalGroupControlNumber | No | string | Numero di controllo funzionale |
| gsSegment | No | string | Segmento GS messaggio |
| isTechnicalAcknowledgmentExpected | Sì | Boolean | Se il riconoscimento tecnico è configurato nell'accordo X12 |
| isFunctionalAcknowledgmentExpected | Sì | Boolean | Se il riconoscimento funzionale è configurato nell'accordo X12 |
| isMessageFailed | Sì | Boolean | Se il messaggio X12 non è riuscito |
| gs01 | No | string | Codice identificativo funzionale |
| gs02 | No | string | Codice del mittente dell'applicazione |
| gs03 | No | string | Codice del destinatario dell'applicazione |
| gs04 | No | string | Data del gruppo funzionale |
| gs05 | No | string | Tempo di gruppo funzionale |
| gs07 | No | string | Codice agenzia responsabile |
| gs08 | No | string | Codice identificativo per la versione, la versione o il settore |
|||||

## <a name="x12-functional-group-acknowledgment-tracking-schema"></a>Schema di rilevamento del riconoscimento del gruppo funzionale X12X12 functional group acknowledgment tracking schema

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
| senderPartnerName | No | string | Nome partner del mittente del messaggio X12 |
| receiverPartnerName | No | string | Nome partner del destinatario del messaggio X12 |
| senderQualifier | Sì | string | Invia qualificatore partner |
| senderIdentifier | Sì | string | Invia identificatore partner |
| receiverQualifier | Sì | string | Qualificatore partner di ricezione |
| receiverIdentifier | Sì | string | Identificatore partner di ricezione |
| agreementName | No | string | Nome del contratto X12 in base al quale vengono risolti i messaggi |
| direction | Sì | Enum | Direzione del flusso di `receive` messaggi, ovvero`send` |
| interchangeControlNumber | No | string | Numero di controllo dell'interscambio, che viene popolato per il lato di invio quando viene ricevuto un riconoscimento tecnico dai partner |
| functionalGroupControlNumber | No | string | Numero di controllo del gruppo funzionale del riconoscimento tecnico, che viene popolato per il lato di invio quando viene ricevuto un riconoscimento tecnico dai partner |
| isaSegment | No | string | Uguale al numero di controllo interscambio, ma popolato solo in casi specifici |
| gsSegment | No | string | Uguale al numero di controllo del gruppo funzionale, ma popolato solo in casi specifici |
| respondingfunctionalGroupControlNumber | No | string | Numero di controllo del gruppo funzionale originale |
| respondingFunctionalGroupId | No | string | Esegue il mapping ad AK101 nell'ID del gruppo funzionale di riconoscimento |
| isMessageFailed | Boolean | Se il messaggio X12 non è riuscito |
| statusCode | Sì | Enum | Codice di stato di riconoscimento con i seguenti valori consentiti: `Accepted`, `Rejected`e`AcceptedWithErrors` |
| processingStatus | Sì | Enum | Stato di elaborazione del riconoscimento `Received` `Generated`con i seguenti valori consentiti: , e`Sent` |
| ak903 | No | string | Numero di set di transazioni ricevute |
| ak904 | No | string | Numero di set di transazioni accettati nel gruppo funzionale identificato |
| ak9Segment | No | string | Se il gruppo funzionale identificato nel segmento AK1 viene accettato o rifiutato e perché |
|||||

## <a name="b2b-protocol-tracking-schemas"></a>Schemi di rilevamento per il protocollo B2B

Per informazioni sugli schemi di rilevamento per il protocollo B2B, vedere:

* [Schemi di rilevamento AS2AS2 tracking schemas](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Schemi di rilevamento personalizzati B2B](logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Passaggi successivi

* [Monitorare i messaggi B2B con i log di Monitoraggio di Azure](../logic-apps/monitor-b2b-messages-log-analytics.md)