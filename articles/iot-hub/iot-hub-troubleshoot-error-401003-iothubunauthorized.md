---
title: Risoluzione dei problemi dell'hub Azure Internet Error 401003 IoTHubUnauthorized
description: Informazioni su come correggere l'errore 401003 IoTHubUnauthorized
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: f46d41c8287d03cbe9582ed560244cbd85cdeeaa
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81759585"
---
# <a name="401003-iothubunauthorized"></a>401003 IoTHubUnauthorized

Questo articolo descrive le cause e le soluzioni per gli errori di **401003 IoTHubUnauthorized** .

## <a name="symptoms"></a>Sintomi

### <a name="symptom-1"></a>Sintomo 1

Nei log di diagnostica viene visualizzato un modello di dispositivi che si disconnettono con **401003 IoTHubUnauthorized**, seguiti da **404104 DeviceConnectionClosedRemotely**e che si connettono subito dopo.

### <a name="symptom-2"></a>Sintomo 2

Le richieste all'hub Internet non riescono con uno dei messaggi di errore seguenti:

* Intestazione dell'autorizzazione mancante
* IotHub '\*' non contiene il dispositivo specificato '\*'
* La regola di\*autorizzazione '' non consente l'accesso\*per ''
* Autenticazione non riuscita per il dispositivo, rinnovo del token o del certificato e riconnessione
* L'identificazione personale non corrisponde alla configurazione: identificazione personale\*: SHA1Hash =\*, SHA2Hash =; Configurazione: PrimaryThumbprint =\*, SecondaryThumbprint =\*

## <a name="cause"></a>Causa

### <a name="cause-1"></a>Causa 1

Per MQTT, alcuni SDK si basano sull'hub degli oggetti Internet per emettere la disconnessione quando il token di firma di accesso condiviso scade per stabilire quando aggiornarlo. Allora 

1. Il token SAS scade
1. L'hub Internet rileva la scadenza e disconnette il dispositivo con **401003 IoTHubUnauthorized**
1. Il dispositivo completa la disconnessione con **404104 DeviceConnectionClosedRemotely**
1. L'SDK di Internet delle cose genera un nuovo token SAS
1. Il dispositivo si riconnette con l'hub Internet con successo

### <a name="cause-2"></a>Causa 2

L'hub Internet delle cose non è riuscito ad autenticare l'intestazione, la regola o la chiave di autenticazione.

## <a name="solution"></a>Soluzione

### <a name="solution-1"></a>Soluzione 1

Non è necessaria alcuna azione se si usa l'SDK per la connessione usando la stringa di connessione del dispositivo. Per la riconnessione alla scadenza del token SAS, il nuovo token SDK viene rigenerato da Internet. 

Se il volume degli errori rappresenta un problema, passare a C SDK, che rinnova il token di firma di accesso condiviso prima della scadenza. Per AMQP, inoltre, il token SAS può essere aggiornato senza disconnessione.

### <a name="solution-2"></a>Soluzione 2

In generale, il messaggio di errore presentato dovrebbe spiegare come correggere l'errore. Se per qualche motivo non è possibile accedere ai dettagli del messaggio di errore, verificare che:

- La firma di accesso condiviso o altri token di sicurezza usati non sono scaduti. 
- Le credenziali di autorizzazione sono ben formate per il protocollo usato. Per altre informazioni, vedere [controllo di accesso dell'hub Internet](iot-hub-devguide-security.md).
- La regola di autorizzazione utilizzata dispone dell'autorizzazione per l'operazione richiesta.

## <a name="next-steps"></a>Passaggi successivi

Per semplificare l'autenticazione all'hub molto, è consigliabile usare gli [SDK di Azure](iot-hub-devguide-sdks.md).