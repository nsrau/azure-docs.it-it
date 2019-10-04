---
title: Schemi di rilevamento AS2 per i messaggi B2B - App per la logica di Azure | Microsoft Docs
description: Creare schemi di rilevamento AS2 che consentono di monitorare i messaggi B2B negli account di integrazione per App per la logica di Azure con Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: f169c411-1bd7-4554-80c1-84351247bf94
ms.date: 01/27/2017
ms.openlocfilehash: 180d90450497b38f107f3601944385a003f50282
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60845784"
---
# <a name="create-schemas-for-tracking-as2-messages-and-mdns-in-integration-accounts-for-azure-logic-apps"></a>Creare schemi per il rilevamento di messaggi AS2 e notifiche sulla ricezione del messaggio negli account di integrazione per App per la logica di Azure

Per monitorare più facilmente il completamento delle operazioni, gli errori e le proprietà dei messaggi per le transazioni business-to-business (B2B) è possibile usare questi schemi di rilevamento AS2 nell'account di integrazione:

* Schema di rilevamento messaggi AS2
* Schema di rilevamento MDN AS2

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
      "incomingHeaders": {
       },
      "outgoingHeaders": {
       },
      "isNrrEnabled": "",
      "isMdnExpected": "",
      "mdnType": ""
    }
}
```

| Proprietà | Type | Descrizione |
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
||||

## <a name="as2-mdn-tracking-schema"></a>Schema di rilevamento MDN AS2

```json
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
```

| Proprietà | Type | Descrizione |
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
||||

## <a name="b2b-protocol-tracking-schemas"></a>Schemi di rilevamento per il protocollo B2B

Per informazioni sugli schemi di rilevamento per il protocollo B2B, vedere:

* [Schemi di rilevamento X12](logic-apps-track-integration-account-x12-tracking-schema.md)
* [Schemi di rilevamento personalizzati B2B](logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sul [monitoraggio dei messaggi B2B](logic-apps-monitor-b2b-message.md)
* Informazioni su [rilevamento dei messaggi B2B in log di monitoraggio di Azure](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)