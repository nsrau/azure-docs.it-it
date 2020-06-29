---
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 239ea2eb4d5bc8d326d5ca503a18b149252dc1be
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "69558830"
---
> [!div class="op_single_selector"]
> * [Node.js](../articles/iot-hub/iot-hub-node-node-device-management-get-started.md)
> * [.NET](../articles/iot-hub/iot-hub-csharp-csharp-device-management-get-started.md)
> * [Java](../articles/iot-hub/iot-hub-java-java-device-management-getstarted.md)
> * [Python](../articles/iot-hub/iot-hub-python-python-device-management-get-started.md)

Le app back-end possono usare primitive dell'hub IoT di Azure, ad esempio [dispositivo gemello][lnk-devtwin] e [metodi diretti][lnk-c2dmethod], per avviare e monitorare le azioni di gestione nei dispositivi in modalità remota. Questa esercitazione mostra il funzionamento combinato di un'app back-end e un'app per dispositivo per avviare e monitorare il riavvio remoto di un dispositivo tramite l'hub IoT.

[!INCLUDE [iot-hub-basic](iot-hub-basic-whole.md)]

Usare un metodo diretto per avviare le operazioni di gestione dei dispositivi, ad esempio il riavvio, il ripristino delle impostazioni predefinite e l'aggiornamento del firmware, da un'applicazione back-end nel cloud. Il dispositivo è responsabile per:

* La gestione della richiesta di metodo inviata dall'hub IoT.

* L'avvio, nel dispositivo, dell'azione corrispondente specifica del dispositivo.

* Gli aggiornamenti di stato tramite le *proprietà segnalate* nell'hub IoT.

È possibile usare un'applicazione back-end nel cloud per eseguire query di un dispositivo gemello in modo da creare report sullo stato di avanzamento delle operazioni di gestione del dispositivo.

[lnk-devtwin]: ../articles/iot-hub/iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
