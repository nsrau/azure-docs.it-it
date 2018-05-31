---
title: Creazione di analisi personalizzate per l'applicazione Azure IoT Central | Microsoft Docs
description: Come creare analisi personalizzate per l'applicazione Azure IoT Central in qualità di operatore.
services: iot-central
author: tanmaybhagwat
ms.author: tanmayb
ms.date: 04/16/2018
ms.topic: article
ms.prod: microsoft-iot-central
manager: timlt
ms.openlocfilehash: 2f19998429daab9000e35b520673c417b4bc828a
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/16/2018
ms.locfileid: "34199936"
---
# <a name="how-to-use-analytics-to-analyze-your-device-data"></a>Come usare le analisi per analizzare i dati dei dispositivi

Microsoft Azure IoT Central offre funzionalità avanzate di analisi che consentono di comprendere le grandi quantità di dati provenienti dai dispositivi. È possibile usare le analisi per visualizzare e analizzare i dati di un [set di dispositivi](howto-use-device-sets.md) nell'applicazione. Un set di dispositivi è un gruppo di dispositivi definito dall'utente. È possibile restringere l'analisi a un piccolo set di dispositivi o a un singolo dispositivo.

Come operatore, scegliere **Analytics** nel menu di navigazione a sinistra, scegliere un set di dispositivi, quindi le misure da visualizzare nel grafico. Ecco alcuni strumenti che è possibile usare per sezionare ulteriormente i dati:

* **Misure:** scegliere le misure da visualizzare, ad esempio temperatura e umidità.
* **Aggregazione:** scegliere la funzione di aggregazione per le misure. Ad esempio **minimo** o **media**.
* **Suddivisione per:** suddivisione dei dati in base alle proprietà del dispositivo o al nome del dispositivo. Ad esempio, suddivisione per posizione del dispositivo:

     ![Pagina principale di Analytics](media\howto-create-analytics\analytics-main.png)

* **Intervallo di tempo:** è possibile scegliere l'intervallo di tempo da uno di quelli predefiniti o creare un intervallo di tempo personalizzato: ![Intervallo di tempo di Analytics](media\howto-create-analytics\analytics-time-range.png)

## <a name="change-the-visualizations"></a>Cambiare le visualizzazioni

È possibile modificare i grafici per soddisfare i requisiti di visualizzazione, scegliendo una fra tre modalità:

* **In pila:** i grafici per ogni misura sono impilati e ciascuno dei grafici ha il proprio asse y. I grafici in pila sono utili quando si hanno più misure selezionate e si vuole avere una singola visualizzazione delle misure.
* **Istogrammi:** viene tracciato un grafico per ogni misura rispetto a un solo asse y, ma i valori per l'asse y cambiano in base alla misura evidenziata. Gli istogrammi sono utili quando si vuole sovrapporre più misure e si vuole vedere gli schemi fra le misure per lo stesso intervallo di tempo.
* **Asse y condiviso:** tutti i grafici condividono lo stesso asse y e i valori per l'asse non cambiano. I grafici con l'asse y condiviso sono utili quando si vuole esaminare una sola misura suddividendo i dati in base a qualche criterio.

## <a name="next-steps"></a>Passaggi successivi

Ora che si conosce la procedura per creare analisi personalizzate per l'applicazione Azure IoT Central, il prossimo passo suggerito è:

> [!div class="nextstepaction"]
> [Preparare e connettere un'applicazione Node.js](howto-connect-nodejs.md)