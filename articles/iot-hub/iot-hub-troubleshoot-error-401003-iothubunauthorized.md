---
title: Risoluzione dei problemi dell'hub Azure Internet Error 401003 IoTHubUnauthorized
description: Informazioni su come correggere l'errore 401003 IoTHubUnauthorized
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 11/06/2020
ms.author: jlian
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 8fb891d5a47203c9905a7def9d04199d24327f70
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2020
ms.locfileid: "94357250"
---
# <a name="401003-iothubunauthorized"></a>401003 IoTHubUnauthorized

Questo articolo descrive le cause e le soluzioni per gli errori di **401003 IoTHubUnauthorized** .

## <a name="symptoms"></a>Sintomi

### <a name="symptom-1"></a>Sintomo 1

Nei log viene visualizzato un modello di dispositivi che si disconnettono con **401003 IoTHubUnauthorized** , seguiti da **404104 DeviceConnectionClosedRemotely** e che si connettono subito dopo.

### <a name="symptom-2"></a>Sintomo 2

Le richieste all'hub Internet non riescono con uno dei messaggi di errore seguenti:

* Intestazione dell'autorizzazione mancante
* IotHub ' \* ' non contiene il dispositivo specificato ' \* '
* La regola di autorizzazione ' \* ' non consente l'accesso per ' \* '
* Autenticazione non riuscita per il dispositivo, rinnovo del token o del certificato e riconnessione
* L'identificazione personale non corrisponde alla configurazione: identificazione personale: SHA1Hash = \* , SHA2Hash = \* ; Configurazione: PrimaryThumbprint = \* , SecondaryThumbprint =\*

## <a name="cause"></a>Causa

### <a name="cause-1"></a>Causa 1

Per MQTT, alcuni SDK si basano sull'hub degli oggetti Internet per emettere la disconnessione quando il token di firma di accesso condiviso scade per stabilire quando aggiornarlo. Quindi

1. Il token SAS scade
1. L'hub Internet rileva la scadenza e disconnette il dispositivo con **401003 IoTHubUnauthorized**
1. Il dispositivo completa la disconnessione con **404104 DeviceConnectionClosedRemotely**
1. L'SDK di Internet delle cose genera un nuovo token SAS
1. Il dispositivo si riconnette con l'hub Internet con successo

### <a name="cause-2"></a>Causa 2

L'hub Internet delle cose non è riuscito ad autenticare l'intestazione, la regola o la chiave di autenticazione. Questo problema può essere dovuto a uno dei motivi citati nei sintomi.

## <a name="solution"></a>Soluzione

### <a name="solution-1"></a>Soluzione 1

Non è necessaria alcuna azione se si usa l'SDK per la connessione usando la stringa di connessione del dispositivo. Per la riconnessione alla scadenza del token SAS, il nuovo token SDK viene rigenerato da Internet.

La durata del token predefinita è di 60 minuti negli SDK. Tuttavia, per alcuni SDK è possibile configurare la durata dei token e la soglia di rinnovo del token. Inoltre, gli errori generati quando un dispositivo si disconnette e si riconnette in caso di rinnovo del token è diverso per ogni SDK. Per altre informazioni e per informazioni su come determinare l'SDK usato dal dispositivo nei log, vedere [comportamento di disconnessione del dispositivo MQTT con gli SDK di Azure](iot-hub-troubleshoot-connectivity.md#mqtt-device-disconnect-behavior-with-azure-iot-sdks).

Per gli sviluppatori di dispositivi, se il volume degli errori rappresenta un problema, passare a C SDK, che rinnova il token di firma di accesso condiviso prima della scadenza. Per AMQP, il token SAS può essere aggiornato senza disconnessione.

### <a name="solution-2"></a>Soluzione 2

In generale, il messaggio di errore presentato dovrebbe spiegare come correggere l'errore. Se per qualche motivo non è possibile accedere ai dettagli del messaggio di errore, verificare che:

- La firma di accesso condiviso o altri token di sicurezza usati non sono scaduti.
- Per l'autenticazione del certificato X. 509, il certificato del dispositivo o il certificato della CA associato al dispositivo non è scaduto. Per informazioni su come registrare i certificati della CA X. 509 con l'hub Internet, vedere [configurare la sicurezza x. 509 nell'hub Azure](iot-hub-security-x509-get-started.md).
- Per l'autenticazione dell'identificazione personale del certificato X. 509, l'identificazione personale del certificato del dispositivo è registrata con l'hub Internet.
- Le credenziali di autorizzazione sono ben formate per il protocollo usato. Per altre informazioni, vedere [controllare l'accesso all'hub](iot-hub-devguide-security.md).
- La regola di autorizzazione utilizzata dispone dell'autorizzazione per l'operazione richiesta.

## <a name="next-steps"></a>Passaggi successivi

- Per semplificare l'autenticazione all'hub molto, è consigliabile usare gli [SDK di Azure](iot-hub-devguide-sdks.md).
- Per informazioni dettagliate sull'autenticazione con l'hub Internet, vedere [controllare l'accesso all'hub](iot-hub-devguide-security.md)Internet.
