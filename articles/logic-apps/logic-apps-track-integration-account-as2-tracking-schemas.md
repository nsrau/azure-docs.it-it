---
title: Schemi di rilevamento AS2 per i messaggi B2B
description: Creare schemi di rilevamento AS2 che consentono di monitorare i messaggi B2B negli account di integrazione per App per la logica di Azure con Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/27/2017
ms.openlocfilehash: 515d7cfc985ee9929f70de2c862170ff79ae4d60
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792819"
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

| Proprietà | Type | Description |
| --- | --- | --- |
| senderPartnerName | Stringa | Nome partner del mittente del messaggio AS2. (Facoltativa) |
| receiverPartnerName | Stringa | Nome partner del destinatario del messaggio AS2. (Facoltativa) |
| as2To | Stringa | Nome del destinatario del messaggio AS2, dalle intestazioni del messaggio AS2. (Obbligatoria) |
| as2From | Stringa | Nome del mittente del messaggio AS2, dalle intestazioni del messaggio AS2. (Obbligatoria) |
| agreementName | Stringa | Nome del contratto AS2 in base al quale vengono risolti i messaggi. (Facoltativa) |
| direction | Stringa | Direzione del flusso dei messaggi, ricezione o invio. (Obbligatoria) |
| messageId | Stringa | ID del messaggio AS2, dalle intestazioni del messaggio AS2. Facoltativa |
| dispositionType |Stringa | Valore del tipo di notifica sulla ricezione del messaggio (MDN). (Facoltativa) |
| fileName | Stringa | Nome di file, dall'intestazione del messaggio AS2. (Facoltativa) |
| isMessageFailed |boolean | Se il messaggio AS2 non è riuscito. (Obbligatoria) |
| isMessageSigned | boolean | Se il messaggio AS2 era firmato. (Obbligatoria) |
| isMessageEncrypted | boolean | Se il messaggio AS2 era crittografato. (Obbligatoria) |
| isMessageCompressed |boolean | Se il messaggio AS2 era compresso. (Obbligatoria) |
| correlationMessageId | Stringa | ID del messaggio AS2 per la correlazione dei messaggi con le notifiche MDN. (Facoltativa) |
| incomingHeaders |Dizionario di JToken | Dettagli dell'intestazione del messaggio AS2 in arrivo. (Facoltativa) |
| outgoingHeaders |Dizionario di JToken | Dettagli dell'intestazione del messaggio AS2 in uscita. (Facoltativa) |
| isNrrEnabled | boolean | Usare il valore predefinito se questo valore è sconosciuto. (Obbligatoria) |
| isMdnExpected | boolean | Usare il valore predefinito se questo valore è sconosciuto. (Obbligatoria) |
| mdnType | Enum | I valori consentiti sono **NotConfigured**, **Sync** e **Async**. (Obbligatoria) |
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

| Proprietà | Type | Description |
| --- | --- | --- |
| senderPartnerName | Stringa | Nome partner del mittente del messaggio AS2. (Facoltativa) |
| receiverPartnerName | Stringa | Nome partner del destinatario del messaggio AS2. (Facoltativa) |
| as2To | Stringa | Nome partner che riceve il messaggio AS2. (Obbligatoria) |
| as2From | Stringa | Nome partner che invia il messaggio AS2. (Obbligatoria) |
| agreementName | Stringa | Nome del contratto AS2 in base al quale vengono risolti i messaggi. (Facoltativa) |
| direction |Stringa | Direzione del flusso dei messaggi, ricezione o invio. (Obbligatoria) |
| messageId | Stringa | ID del messaggio AS2. (Facoltativa) |
| originalMessageId |Stringa | ID del messaggio originale AS2. (Facoltativa) |
| dispositionType | Stringa | Valore del tipo di gestione MDN. (Facoltativa) |
| isMessageFailed |boolean | Se il messaggio AS2 non è riuscito. (Obbligatoria) |
| isMessageSigned |boolean | Se il messaggio AS2 era firmato. (Obbligatoria) |
| isNrrEnabled | boolean | Usare il valore predefinito se questo valore è sconosciuto. (Obbligatoria) |
| statusCode | Enum | I valori consentiti sono **Accepted**, **Rejected** e **AcceptedWithErrors**. (Obbligatoria) |
| micVerificationStatus | Enum | I valori consentiti sono **NotApplicable**, **Succeeded** e **Failed**. (Obbligatoria) |
| correlationMessageId | Stringa | ID correlazione. ID del messaggio originale, ovvero ID del messaggio per cui è configurata la notifica MDN. (Facoltativa) |
| incomingHeaders | Dizionario di JToken | Indica i dettagli dell'intestazione del messaggio in arrivo. (Facoltativa) |
| outgoingHeaders |Dizionario di JToken | Indica i dettagli dell'intestazione del messaggio in uscita. (Facoltativa) |
||||

## <a name="b2b-protocol-tracking-schemas"></a>Schemi di rilevamento per il protocollo B2B

Per informazioni sugli schemi di rilevamento per il protocollo B2B, vedere:

* [Schemi di rilevamento X12](logic-apps-track-integration-account-x12-tracking-schema.md)
* [Schemi di rilevamento personalizzati B2B](logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sul [monitoraggio dei messaggi B2B](logic-apps-monitor-b2b-message.md)
* Informazioni sul [rilevamento dei messaggi B2B nei log di monitoraggio di Azure](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)