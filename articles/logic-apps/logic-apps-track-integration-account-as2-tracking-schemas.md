---
title: Schemi di rilevamento AS2 per i messaggi B2B
description: Creare schemi di rilevamento per monitorare i messaggi AS2 in app per la logica di Azure
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/01/2020
ms.openlocfilehash: bccf69362279afd9e8148b20b61ff3ea9b472a03
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "76906966"
---
# <a name="create-schemas-for-tracking-as2-messages-in-azure-logic-apps"></a>Creare schemi per tenere traccia dei messaggi AS2 in app per la logica di Azure

Per monitorare più facilmente il completamento delle operazioni, gli errori e le proprietà dei messaggi per le transazioni business-to-business (B2B) è possibile usare questi schemi di rilevamento AS2 nell'account di integrazione:

* Schema di rilevamento messaggi AS2
* Schema di rilevamento MDN (Message Disposition Notification) AS2

## <a name="as2-message-tracking-schema"></a>Schema di rilevamento messaggi AS2

```json
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
      "incomingHeaders": {},
      "outgoingHeaders": {},
      "isNrrEnabled": "",
      "isMdnExpected": "",
      "mdnType": ""
    }
}
```

| Proprietà | Obbligatoria | Type | Descrizione |
|----------|----------|------|-------------|
| senderPartnerName | No | string | Nome partner del mittente del messaggio AS2 |
| receiverPartnerName | No | string | Nome partner del destinatario del messaggio AS2 |
| as2To | Sì | string | Nome del destinatario del messaggio AS2 dalle intestazioni del messaggio AS2 |
| as2From | Sì | string | Nome del mittente del messaggio AS2 dalle intestazioni del messaggio AS2 |
| agreementName | No | string | Nome del contratto AS2 in base al quale vengono risolti i messaggi. |
| direction | Sì | string | Direzione del flusso dei messaggi, che può essere `receive` o`send` |
| messageId | No | string | ID messaggio AS2 dalle intestazioni del messaggio AS2 |
| dispositionType | No | string | Valore del tipo di disposizione Message Disposition Notification (MDN) |
| fileName | No | string | Nome file dall'intestazione del messaggio AS2 |
| isMessageFailed | Sì | Boolean | Indica se il messaggio AS2 ha avuto esito negativo |
| isMessageSigned | Sì | Boolean | Indica se il messaggio AS2 è stato firmato |
| isMessageEncrypted | Sì | Boolean | Indica se il messaggio AS2 è stato crittografato |
| isMessageCompressed | Sì | Boolean | Indica se il messaggio AS2 è stato compresso |
| correlationMessageId | No | string | ID messaggio AS2, per correlare i messaggi con MDN |
| incomingHeaders | No | Dizionario di JToken | Dettagli intestazione messaggio AS2 in arrivo |
| outgoingHeaders | No | Dizionario di JToken | Dettagli intestazione messaggio AS2 in uscita |
| isNrrEnabled | Sì | Boolean | Indica se utilizzare il valore predefinito se il valore non è noto. |
| isMdnExpected | Sì | Boolean | Indica se utilizzare il valore predefinito se il valore non è noto. |
| mdnType | Sì | Enumerazione | Valori consentiti: `NotConfigured` , `Sync` e`Async` |
|||||

## <a name="as2-mdn-tracking-schema"></a>Schema di rilevamento MDN AS2

```json
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
```

| Proprietà | Obbligatoria | Type | Descrizione |
|----------|----------|------|-------------|
| senderPartnerName | No | string | Nome partner del mittente del messaggio AS2 |
| receiverPartnerName | No | string | Nome partner del destinatario del messaggio AS2 |
| as2To | Sì | string | Nome del partner che riceve il messaggio AS2 |
| as2From | Sì | string | Nome del partner che invia il messaggio AS2 |
| agreementName | No | string | Nome del contratto AS2 in base al quale vengono risolti i messaggi. |
| direction | Sì | string | Direzione del flusso dei messaggi, che può essere `receive` o`send` |
| messageId | No | string | ID messaggio AS2 |
| originalMessageId | No | string | ID messaggio originale AS2 |
| dispositionType | No | string | Valore tipo di disposizione MDN |
| isMessageFailed | Sì | Boolean | Indica se il messaggio AS2 ha avuto esito negativo |
| isMessageSigned | Sì | Boolean | Indica se il messaggio AS2 è stato firmato |
| isNrrEnabled | Sì | Boolean | Indica se utilizzare il valore predefinito se il valore non è noto. |
| statusCode | Sì | Enumerazione | Valori consentiti: `Accepted` , `Rejected` e`AcceptedWithErrors` |
| micVerificationStatus | Sì | Enumerazione | Valori consentiti: `NotApplicable` , `Succeeded` e`Failed` |
| correlationMessageId | No | string | ID di correlazione, ovvero l'ID del messaggio originale configurato per l'MDN |
| incomingHeaders | No | Dizionario di JToken | Dettagli intestazione messaggio in ingresso |
| outgoingHeaders | No | Dizionario di JToken | Dettagli dell'intestazione del messaggio in uscita |
|||||

## <a name="b2b-protocol-tracking-schemas"></a>Schemi di rilevamento per il protocollo B2B

Per informazioni sugli schemi di rilevamento per il protocollo B2B, vedere:

* [Schemi di rilevamento X12](logic-apps-track-integration-account-x12-tracking-schema.md)
* [Schemi di rilevamento personalizzati B2B](logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Passaggi successivi

* [Monitorare i messaggi B2B con i log di Monitoraggio di Azure](../logic-apps/monitor-b2b-messages-log-analytics.md)