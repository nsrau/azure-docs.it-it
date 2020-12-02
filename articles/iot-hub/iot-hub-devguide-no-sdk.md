---
title: Sviluppare senza un SDK di Azure. Microsoft Docs
description: 'Guida per gli sviluppatori: informazioni su e collegamenti ad argomenti che è possibile usare per creare app per dispositivi e back-end senza usare un SDK di Azure.'
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/12/2020
ms.custom:
- mqtt
- amqp
- 'Role: IoT Device'
- 'Role: Cloud Development'
ms.openlocfilehash: ae8b01522a248b8b1dbdd255a9fcd55f16cf2369
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96461709"
---
# <a name="develop-without-using-an-azure-iot-hub-sdk"></a>Sviluppare senza usare un SDK dell'hub Azure.

Questo argomento fornisce informazioni utili e collegamenti per gli sviluppatori che vogliono sviluppare app di dispositivo o back-end senza usare gli SDK di Azure.

Microsoft consiglia vivamente di usare un SDK di Azure. Gli SDK per dispositivi e servizi Azure e sono pubblicati su molte piattaforme più diffuse. Gli SDK forniscono un livello di praticità che gestisce gran parte della complessità del protocollo di comunicazione sottostante, inclusi la connessione e la riconnessione del dispositivo e i criteri di ripetizione dei tentativi. Gli SDK vengono aggiornati regolarmente per fornire le funzionalità più recenti esposte dall'hub Internet e dagli aggiornamenti della sicurezza. L'uso degli SDK consente di ridurre i tempi e i tempi di sviluppo dedicati alla manutenzione del codice. Per altre informazioni sugli SDK Azure, vedere la pagina relativa agli [SDK per dispositivi e servizi Azure](iot-hub-devguide-sdks.md). Per informazioni più dettagliate sui vantaggi derivanti dall'uso di un SDK di Azure, vedere i [vantaggi derivanti dall'uso degli SDK e degli inconvenienti di Azure](https://azure.microsoft.com/en-us/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/) .

Anche se l'hub Internet delle cose supporta AMQP, AMQP su WebSocket, HTTPS, MQTT e MQTT su WebSocket per la comunicazione con i dispositivi, è consigliabile usare MQTT se il dispositivo lo supporta.

## <a name="development-prerequisites"></a>Prerequisiti per lo sviluppo

Prima di iniziare lo sviluppo, è necessario avere una conoscenza approfondita dell'hub Internet e delle funzionalità che si desidera vengano implementate dal dispositivo o dall'app back-end. Di seguito è riportato un elenco di argomenti molto abbreviato che è necessario conoscere:

* Assicurarsi di comprendere gli endpoint esposti dall'hub Internet e i protocolli supportati in ogni endpoint. Per altre informazioni, vedere [endpoint dell'hub](iot-hub-devguide-endpoints.md)Internet.

* Quando è richiesta una scelta di protocollo per le app per dispositivi, si consiglia vivamente di usare MQTT. Prima di scegliere un protocollo, tuttavia, assicurarsi di comprendere le limitazioni imposte da ciascuno di essi. Per altre informazioni, vedere [scegliere un protocollo di comunicazione](iot-hub-devguide-protocols.md).

* Per informazioni sull'autenticazione con l'hub Internet delle cose, vedere [controllare l'accesso all'hub](iot-hub-devguide-security.md)Internet.

[!INCLUDE [iot-hub-include-x509-ca-signed-support-note](../../includes/iot-hub-include-x509-ca-signed-support-note.md)]

## <a name="help-on-different-protocols"></a>Guida su diversi protocolli

Per informazioni sull'uso dei protocolli seguenti senza Azure:

* Dispositivo o app back-end in **AMQP**, vedere [supporto di AMQP](iot-hub-amqp-support.md).

* App per dispositivi in **MQTT**, vedere [supporto di MQTT](iot-hub-mqtt-support.md). La maggior parte di questo argomento tratta direttamente l'uso del protocollo MQTT. Contiene anche informazioni sull'uso del [repository di esempio MQTT](https://github.com/Azure-Samples/IoTMQTTSample). Questo repository contiene esempi C che usano la libreria Eclipse mosquitto per inviare messaggi all'hub Internet.

* Dispositivi o app back-end su **https**, consultare le [API REST dell'hub Azure](/rest/api/iothub/). Tenere presente, come indicato nei [prerequisiti di sviluppo](#development-prerequisites), che non è possibile usare l'autenticazione dell'autorità di certificazione (CA) X. 509 con HTTPS.

Per i dispositivi, si consiglia vivamente di usare MQTT se il dispositivo lo supporta.

## <a name="next-steps"></a>Passaggi successivi

* [Supporto di MQTT](iot-hub-mqtt-support.md)