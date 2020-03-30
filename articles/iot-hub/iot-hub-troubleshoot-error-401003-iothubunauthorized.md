---
title: Risoluzione dei problemi dell'hub IoT di Azure 401003 IoTHubUnauthorized
description: Comprendere come risolvere l'errore 401003 IoTHubUnauthorized
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: d0a9f643516af5945037acb3dd1da24b06944171
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284408"
---
# <a name="401003-iothubunauthorized"></a>401003 IoTHubUnauthorized

In questo articolo vengono descritte le cause e le soluzioni per gli errori **401003 IoTHubUnauthorized.**

## <a name="symptoms"></a>Sintomi

### <a name="symptom-1"></a>Sintomo 1

Nei log di diagnostica viene visualizzato un modello di dispositivi che si disconnettono con **401003 IoTHubUnauthorized**, seguito da **404104 DeviceConnectionClosedRemotely**e quindi si connettono correttamente poco dopo.

### <a name="symptom-2"></a>Sintomo 2

Le richieste all'hub IoT hanno esito negativo con uno dei seguenti messaggi di errore:

* Intestazione di autorizzazione mancante
* IotHub\*' ' non contiene\*il dispositivo specificato ' '
* La regola\*di autorizzazione '\*' non consente l'accesso per ' '
* Autenticazione non riuscita per questo dispositivo, rinnovare il token o il certificato e riconnettersi
* L'identificazione personale non corrisponde alla configurazione:\*Identificazione personale:\*SHA1Hash , SHA2Hash ; Configurazione: PrimaryThumbprint\*, , SecondaryThumbprint\*

## <a name="cause"></a>Causa

### <a name="cause-1"></a>Causa 1

Per MQTT, alcuni SDK si basano sull'hub IoT per emettere la disconnessione alla scadenza del token di firma di accesso condiviso per sapere quando aggiornarlo. Così 

1. Il token di firma di accesso condiviso scade
1. Hub IoT rileva la scadenza e disconnette il dispositivo con **401003 IoTHubUnauthorized**
1. Il dispositivo completa la disconnessione con **404104 DeviceConnectionClosedRemotely**
1. L'SDK IoT genera un nuovo token di firma di accesso condiviso
1. Il dispositivo si riconnette con l'hub IoT

### <a name="cause-2"></a>Causa 2

L'hub IoT non è in stato di autenticare l'intestazione, la regola o la chiave di autenticazione.

## <a name="solution"></a>Soluzione

### <a name="solution-1"></a>Soluzione 1

Non è necessaria alcuna azione se si usa L'SDK IoT per la connessione usando la stringa di connessione del dispositivo. L'SDK IoT rigenera il nuovo token per riconnettersi alla scadenza del token SAS. 

Se il volume di errori è un problema, passare al C SDK, che rinnova il token di firma di accesso condiviso prima della scadenza. Inoltre, per AMQP il token di firma di accesso condiviso può aggiornare senza disconnessione.

### <a name="solution-2"></a>Soluzione 2

In generale, il messaggio di errore presentato dovrebbe spiegare come risolvere l'errore. Se per qualche motivo non hai accesso ai dettagli del messaggio di errore, assicurati:

- La firma di accesso condiviso o un altro token di sicurezza usato non è scaduto. 
- La credenziale di autorizzazione è ben formata per il protocollo utilizzato. Per altre informazioni, vedere Controllo degli [accessi all'hub IoT](iot-hub-devguide-security.md).
- La regola di autorizzazione utilizzata dispone dell'autorizzazione per l'operazione richiesta.

## <a name="next-steps"></a>Passaggi successivi

Per semplificare l'autenticazione all'hub IoT, è consigliabile usare gli SDK di [Azure IoT.](iot-hub-devguide-sdks.md)