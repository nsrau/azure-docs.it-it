---
title: Configurare regole e azioni in Azure IoT Central | Microsoft Docs
description: Questo articolo illustra come un generatore, come configurare le regole e le azioni basate sulla telemetria nell'applicazione IoT Central di Azure.
author: vavilla
ms.author: vavilla
ms.date: 11/27/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 987e8d9e667339d7526e8acbc3164e58abcd3e4d
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74895738"
---
# <a name="configure-rules-preview-features"></a>Configurare le regole (funzionalità di anteprima)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

*Questo articolo è rivolto a operatori, autori e amministratori.*

Le regole in IoT Central fungeranno da strumento di risposta personalizzabile che attiva gli eventi monitorati attivamente dai dispositivi connessi. Nelle sezioni seguenti viene descritto il modo in cui vengono valutate le regole.

## <a name="select-target-devices"></a>Selezionare i dispositivi di destinazione

Usare la sezione dispositivi di destinazione per selezionare il tipo di dispositivi a cui verrà applicata questa regola. I filtri consentono di perfezionare ulteriormente i dispositivi da includere. I filtri usano le proprietà nel modello di dispositivo per filtrare il set di dispositivi. I filtri non attivano un'azione. Nello screenshot seguente i dispositivi di destinazione sono di tipo modello di dispositivo **frigorifero**. Il filtro indica che la regola deve includere solo i **frigoriferi** in cui la proprietà **stato prodotto** è uguale a **Washington**.

![Condizioni](media/howto-configure-rules/filters.png)

## <a name="use-multiple-conditions"></a>Usare più condizioni

Condizioni in base alle quali vengono attivate le regole. Attualmente, quando si aggiungono più condizioni a una regola, le condizioni sono logiche e combinate. In altre parole, è necessario che tutte le condizioni siano soddisfatte perché la regola valuti come true.  

Nello screenshot seguente le condizioni verificano quando la temperatura è maggiore di 90 e l'umidità è minore di 10. Quando entrambe le istruzioni sono true, la regola restituisce true e attiva un'azione.

![Condizioni](media/howto-configure-rules/conditions.png)

## <a name="use-aggregate-windowing"></a>Usa finestra di aggregazione

Le regole valutano le finestre temporali aggregate come finestre a cascata. Nella schermata seguente l'intervallo di tempo è di cinque minuti. Ogni cinque minuti la regola valuta gli ultimi cinque minuti di dati. I dati vengono valutati solo una volta nella finestra a cui corrisponde.

![Finestre a cascata](media/howto-configure-rules/tumbling-window.png)

## <a name="use-rules-with-iot-edge-modules"></a>Usare le regole con moduli IoT Edge

Una restrizione si applica alle regole applicate ai moduli IoT Edge. Le regole sui dati di telemetria di moduli diversi non vengono valutate come regole valide. Come esempio, seguire questa procedura. La prima condizione della regola è la telemetria della temperatura del modulo A. La seconda condizione della regola si trova in una telemetria dell'umidità sul modulo B. Poiché le due condizioni sono da moduli diversi, si tratta di un set di condizioni non valido. La regola non è valida e genererà un errore durante il tentativo di salvare la regola.

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come configurare una regola nell'applicazione IoT Central di Azure, è possibile:

> [!div class="nextstepaction"]
> [Analizza i dati in tempo reale](howto-create-analytics.md)
