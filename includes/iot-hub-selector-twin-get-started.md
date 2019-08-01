---
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: eef073f5f4d1eb39fd5ccd8dafacd7074158fa37
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68667831"
---
> [!div class="op_single_selector"]
> * [Node.js](../articles/iot-hub/iot-hub-node-node-twin-getstarted.md)
> * [.NET](../articles/iot-hub/iot-hub-csharp-csharp-twin-getstarted.md)
> * [Java](../articles/iot-hub/iot-hub-java-java-twin-getstarted.md)
> * [Python](../articles/iot-hub/iot-hub-python-twin-getstarted.md)

I dispositivi gemelli sono documenti JSON nei quali vengono archiviate informazioni sullo stato dei dispositivi (metadati, configurazioni e condizioni). L'hub IoT rende permanente un dispositivo gemello per ogni dispositivo che si connette.

[!INCLUDE [iot-hub-basic](iot-hub-basic-whole.md)]

Usare i dispositivi gemelli per:

* Archiviare i metadati dei dispositivi dal back-end della soluzione.

* Segnalare informazioni sullo stato corrente, come funzionalità disponibili e condizioni (ad esempio, il metodo di connettività usato) dall'app per dispositivi.

* Sincronizzare lo stato dei flussi di lavoro a esecuzione prolungata (come gli aggiornamenti del firmware e della configurazione) tra un'app per dispositivi e un'app di back-end.

* Eseguire query sui metadati, la configurazione o lo stato dei dispositivi.

I dispositivi gemelli sono progettati per la sincronizzazione e per l'esecuzione di query sulle configurazioni e le condizioni dei dispositivi. Altre informazioni su quando usare i dispositivi gemelli sono reperibili in [Informazioni sui dispositivi gemelli](../articles/iot-hub/iot-hub-devguide-device-twins.md).

I dispositivi gemelli vengono archiviati in un hub IoT e contengono gli elementi seguenti.

* *Tag*: metadati dei dispositivi accessibili solo dal back-end della soluzione

* *Proprietà desiderate*: oggetti JSON modificabili dal back-end della soluzione e osservabili dall'app per dispositivi

* *Proprietà segnalate*: oggetti JSON modificabili dall'app per dispositivi e leggibili dal back-end della soluzione I tag e le proprietà non possono contenere matrici, ma gli oggetti possono essere annidati.

![Immagine che illustra le funzionalità del dispositivo gemello](./media/iot-hub-selector-twin-get-started/twin.png)

Il back-end della soluzione può anche eseguire query sui dispositivi gemelli in base a tutti i dati sopra indicati.
Per altre informazioni sui dispositivi gemelli, vedere [Informazioni sui dispositivi gemelli](../articles/iot-hub/iot-hub-devguide-device-twins.md) e per informazioni sull'esecuzione di query, vedere [Linguaggio di query dell'hub IoT](../articles/iot-hub/iot-hub-devguide-query-language.md).


Questa esercitazione illustra come:

* Creare un'app back-end che aggiunge *tag* a un dispositivo gemello e un'app per dispositivo simulato che segnala il proprio canale di connettività come *proprietà segnalata* nel dispositivo gemello.

* Eseguire query sui dispositivi dall'app back-end con filtri sui tag e sulle proprietà creati in precedenza.
