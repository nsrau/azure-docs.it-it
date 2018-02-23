---
title: Informazioni sulla messaggistica da dispositivo a cloud dell'hub IoT di Azure | Microsoft Docs
description: 'Guida per gli sviluppatori: come usare la messaggistica da dispositivo a cloud con l''hub IoT. Include informazioni sull''invio di dati di telemetria e non e sull''uso del routing per recapitare i messaggi.'
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/29/2018
ms.author: dobett
ms.openlocfilehash: 48b904818c80b9175d45b88345634f11cf4a4812
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2018
---
# <a name="send-device-to-cloud-messages-to-iot-hub"></a>Inviare messaggi da dispositivo a cloud all'hub IoT

Per inviare i dati e gli avvisi di telemetria di serie temporali dai dispositivi al back-end della soluzione, inviare messaggi da dispositivo a cloud dal dispositivo all'hub IoT. Per una descrizione delle altre opzioni da dispositivo a cloud supportate dall'hub IoT, vedere [Indicazioni sulle comunicazioni da dispositivo a cloud][lnk-d2c-guidance].

I messaggi da dispositivo a cloud vengono inviati tramite un endpoint per il dispositivo (**/devices/{deviceId}/messages/events**). A quel punto le regole di routing reindirizzano i messaggi a uno degli endpoint di servizio nell'hub IoT. Le regole di routing usano le intestazioni e il corpo dei messaggi da dispositivo a cloud per determinare dove indirizzare i messaggi. Per impostazione predefinita, i messaggi vengono instradati all'endpoint per servizi predefinito (**messaggi/eventi**) compatibile con [Hub eventi][lnk-event-hubs]. È quindi possibile usare l'[integrazione standard di Hub eventi e gli SDK][lnk-compatible-endpoint] per ricevere i messaggi da dispositivo a cloud nel back-end della soluzione.

L'hub IoT implementa la messaggistica da dispositivo a cloud usando un modello di messaggistica di flusso. I messaggi da dispositivo a cloud dell'hub IoT somigliano più a *eventi* di [Hub eventi][lnk-event-hubs] che non a *messaggi* del [bus di servizio][lnk-servicebus], poiché è presente un volume elevato di eventi che passa nel servizio ed è leggibile da più lettori.

La messaggistica da dispositivo a cloud con hub IoT ha le caratteristiche seguenti:

* I messaggi da dispositivo a cloud sono durevoli e vengono mantenuti nell'endpoint **messages/events** predefinito in un hub IoT per un massimo di sette giorni.
* I messaggi da dispositivo a cloud possono avere dimensioni massime pari a 256 KB e possono essere raggruppati in batch per ottimizzare gli invii. I batch possono avere dimensioni massime pari a 256 KB.
* Come illustrato nella sezione [Controllare l'accesso all'hub IoT][lnk-devguide-security], l'hub IoT consente il controllo di accesso e l'autenticazione per singoli dispositivi.
* L'hub IoT consente di creare fino a 10 endpoint personalizzati. I messaggi vengono recapitati agli endpoint in base alle route configurate nell'hub IoT. Per altre informazioni, vedere [Regole di routing](#routing-rules).
* L'hub IoT abilita milioni di dispositivi connessi contemporaneamente (vedere [Quote e limitazioni][lnk-quotas]).
* L'hub IoT non consente il partizionamento arbitrario. I messaggi da dispositivo a cloud vengono partizionati in base al valore **deviceId**di origine.

Per altre informazioni sulle differenze tra l'hub IoT e Hub eventi, vedere [Confronto tra l'hub IoT e Hub eventi di Azure][lnk-comparison].

## <a name="send-non-telemetry-traffic"></a>Invio di traffico non di telemetria

Spesso, oltre alla telemetria, i dispositivi inviano messaggi e richieste da eseguire e gestire separatamente nel back-end della soluzione. Ad esempio, gli avvisi critici che devono attivare un'azione specifica nel back-end. È possibile scrivere una [regola di gestione][lnk-devguide-custom] per l'invio di questi tipi di messaggi a un endpoint dedicato alla loro elaborazione in base all'intestazione del messaggio o a un valore del corpo del messaggio.

Per altre informazioni sul modo migliore di elaborare questo tipo di messaggio, vedere l'[Esercitazione: Elaborare messaggi da dispositivo a cloud dell'hub IoT usando .Net][lnk-d2c-tutorial].

## <a name="route-device-to-cloud-messages"></a>Routing di messaggi da dispositivo a cloud

Sono disponibili due opzioni per il routing dei messaggi da dispositivo a cloud alle app back-end:

* Usare l'[endpoint compatibile con Hub eventi][lnk-compatible-endpoint] predefinito per consentire alle app back-end di leggere i messaggi da dispositivo a cloud ricevuti dall'hub. Per informazioni sull'endpoint compatibile con l'Hub eventi predefinito, vedere [Read device-to-cloud messages from the built-in endpoint][lnk-devguide-builtin] (Leggere i messaggi da dispositivo a cloud dall'endpoint predefinito).
* Usare le regole di routing per inviare messaggi a endpoint personalizzati nell'hub IoT. Gli endpoint personalizzati consentono alle app back-end di leggere i messaggi da dispositivo a cloud mediante gli hub eventi, le code o gli argomenti del bus di servizio. Per informazioni sugli endpoint personalizzati e di routing, vedere [Usare endpoint e regole di routing personalizzati per i messaggi da dispositivo a cloud][lnk-devguide-custom].

## <a name="anti-spoofing-properties"></a>Proprietà anti-spoofing

Per evitare lo spoofing di dispositivi nei messaggi da dispositivo a cloud, l'hub IoT contrassegna tutti i messaggi con le proprietà seguenti:

* **ConnectionDeviceId**
* **ConnectionDeviceGenerationId**
* **ConnectionAuthMethod**

Le prime due contengono le proprietà **deviceId** e **generationId** del dispositivo di origine, come indicato in [Proprietà delle identità dei dispositivi][lnk-device-properties].

La proprietà **ConnectionAuthMethod** contiene un oggetto serializzato JSON con le proprietà seguenti:

```json
{
  "scope": "{ hub | device}",
  "type": "{ symkey | sas}",
  "issuer": "iothub"
}
```

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sugli SDK che è possibile usare per inviare i messaggi da dispositivo a cloud, vedere [Azure IoT SDK][lnk-sdks].

Le esercitazioni di [Introduzione][lnk-get-started] illustrano come inviare messaggi da dispositivo a cloud da dispositivi fisici e simulati. Per altre informazioni, vedere l'esercitazione [Elaborare messaggi da dispositivo a cloud dell'hub IoT usando i route][lnk-d2c-tutorial].

[lnk-devguide-builtin]: iot-hub-devguide-messages-read-builtin.md
[lnk-devguide-custom]: iot-hub-devguide-messages-read-custom.md
[lnk-comparison]: iot-hub-compare-event-hubs.md
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md
[lnk-get-started]: iot-hub-get-started.md

[lnk-event-hubs]: http://azure.microsoft.com/documentation/services/event-hubs/
[lnk-servicebus]: http://azure.microsoft.com/documentation/services/service-bus/
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-compatible-endpoint]: iot-hub-devguide-messages-read-builtin.md
[lnk-device-properties]: iot-hub-devguide-identity-registry.md#device-identity-properties
[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md
