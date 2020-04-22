---
title: Informazioni sul supporto MQTT del servizio di provisioning dei dispositivi IoT di Azure Documenti Microsoft
description: "Guida per gli sviluppatori: supporto per i dispositivi che si connettono all'endpoint del dispositivo DPS (Device Provisioning Service) di Azure IoT usando il protocollo MQTT.Developer guide - support for devices connecting to the Azure IoT Device Provisioning Service (DPS) device-facing endpoint using the MQTT protocol."
author: rajeevmv
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: ravokkar
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 213fc3412a2dfad77946e52a355a30774d6860c7
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680683"
---
# <a name="communicate-with-your-dps-using-the-mqtt-protocol"></a>Comunicare con il DPS utilizzando il protocollo MQTT

DPS consente ai dispositivi di comunicare con l'endpoint del dispositivo DPS utilizzando:

* [MQTT v3.1.1](https://mqtt.org/) sulla porta 8883
* [MQTT v3.1.1](http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html#_Toc398718127) su WebSocket sulla porta 443.

DPS non è un broker MQTT completo e non supporta tutti i comportamenti specificati nello standard MQTT v3.1.1. In questo articolo viene descritto come i dispositivi possono utilizzare i comportamenti MQTT supportati per comunicare con DPS.

Tutte le comunicazioni del dispositivo con DPS devono essere protette tramite TLS/SSL. Pertanto, DPS non supporta le connessioni non protette sulla porta 1883.

 > [!NOTE] 
 > DPS attualmente non supporta i dispositivi che utilizzano il meccanismo di [attestazione](https://docs.microsoft.com/azure/iot-dps/concepts-device#attestation-mechanism) TPM tramite il protocollo MQTT.

## <a name="connecting-to-dps"></a>Connessione a DPS

Un dispositivo può utilizzare il protocollo MQTT per connettersi a un DPS utilizzando una delle seguenti opzioni.

* Librerie negli SDK di provisioning di [Azure IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks#microsoft-azure-provisioning-sdks).
* Il protocollo MQTT direttamente.

## <a name="using-the-mqtt-protocol-directly-as-a-device"></a>Uso diretto del protocollo MQTT (come dispositivo)

Se un dispositivo non può usare gli SDK per dispositivi, può comunque connettersi agli endpoint pubblici del dispositivo tramite il protocollo MQTT sulla porta 8883. Nel pacchetto **CONNECT,** il dispositivo deve utilizzare i seguenti valori:

* Per il campo **ClientId** , utilizzare **registrationId**.

* Per il campo `{idScope}/registrations/{registration_id}/api-version=2019-03-31`Nome `{idScope}` **utente,** utilizzare , dove è l'idScope del DPS. [idScope](https://docs.microsoft.com/azure/iot-dps/concepts-device#id-scope)

* Per il campo **Password** usare un token di firma di accesso condiviso. Il formato del token di firma di accesso condiviso è identico a quello per i protocolli HTTPS e AMQP:

  `SharedAccessSignature sr={URL-encoded-resourceURI}&sig={signature-string}&se={expiry}&skn=registration`Il resourceURI deve essere `{idScope}/registrations/{registration_id}`nel formato . Il nome del `registration`criterio deve essere .

  > [!NOTE]
  > Le password di token SAS non sono obbligatorie se si usa l'autenticazione dei certificati X.509.

  Per ulteriori informazioni su come generare token di firma di accesso condiviso, vedere la sezione relativa ai token di sicurezza in [Controllare l'accesso a DPS](how-to-control-access.md#security-tokens).

Di seguito è riportato un elenco dei comportamenti specifici dell'implementazione DPS:The following is a list of DPS implementation-specific behaviors:

 * DPS non supporta la funzionalità del flag **CleanSession** impostato su **0**.

 * Quando un'app per dispositivi sottoscrive un argomento con **QoS 2,** DPS concede il livello QoS massimo 1 nel pacchetto **SUBACK.** Dopo di che, DPS recapita i messaggi al dispositivo utilizzando QoS 1.

## <a name="tlsssl-configuration"></a>Configurazione TLS/SSL

Per utilizzare direttamente il protocollo MQTT, il client *deve* connettersi tramite TLS 1.2. altrimenti si verificheranno errori di connessione.


## <a name="registering-a-device"></a>Registrazione di un dispositivo

Per registrare un dispositivo tramite DPS, un dispositivo deve iscriversi utilizzando `$dps/registrations/res/#` come filtro **argomento**. Il carattere jolly a più livelli `#` nel filtro argomento viene usato solo per consentire al dispositivo di ricevere proprietà aggiuntive nel nome dell'argomento. DPS non consente l'utilizzo `?` dei caratteri jolly o per il `#` filtraggio degli argomenti secondari. Poiché DPS non è un broker di messaggistica pub-sub generico, supporta solo i nomi degli argomenti documentati e i filtri degli argomenti.

Il dispositivo deve pubblicare un `$dps/registrations/PUT/iotdps-register/?$rid={request_id}` messaggio di registrazione in DPS utilizzando come **nome dell'argomento**. Il payload deve contenere l'oggetto [Device Registration](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice#deviceregistration) in formato JSON.
In uno scenario riuscito, il dispositivo `$dps/registrations/res/202/?$rid={request_id}&retry-after=x` riceverà una risposta sul nome dell'argomento, dove x è il valore retry-after in secondi. Il payload della risposta conterrà l'oggetto [RegistrationOperationStatus](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice#registrationoperationstatus) in formato JSON.

## <a name="polling-for-registration-operation-status"></a>Polling per lo stato dell'operazione di registrazione

Il dispositivo deve eseguire periodicamente il polling del servizio per ricevere il risultato dell'operazione di registrazione del dispositivo. Supponendo che il dispositivo abbia `$dps/registrations/res/#` già sottoscritto l'argomento come indicato in precedenza, può pubblicare un messaggio get operationstatus per il nome dell'argomento. `$dps/registrations/GET/iotdps-get-operationstatus/?$rid={request_id}&operationId={operationId}` L'ID operazione in questo messaggio deve essere il valore ricevuto nel messaggio di risposta RegistrationOperationStatus nel passaggio precedente. Nel caso di esito positivo, `$dps/registrations/res/200/?$rid={request_id}` il servizio risponderà sull'argomento. Il payload della risposta conterrà l'oggetto RegistrationOperationStatus. Il dispositivo deve continuare a eseguire il polling del servizio se il codice di risposta è 202 dopo un ritardo uguale al periodo di tentativi dopo. L'operazione di registrazione del dispositivo ha esito positivo se il servizio restituisce un codice di stato 200.The device registration operation is successful if the service returns a 200 status code.

## <a name="connecting-over-websocket"></a>Connessione tramite Websocket
Quando ci si connette tramite `mqtt`Websocket, specificare il sottoprotocollo come . Seguire [RFC 6455](https://tools.ietf.org/html/rfc6455).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul protocollo MQTT, vedere la [documentazione di MQTT](https://mqtt.org/documentation).

Per esplorare ulteriormente le funzionalità di DPS, vedere:

* [Informazioni su IoT DPS](about-iot-dps.md)
