---
title: Configurare regole e azioni in Azure IoT Central | Microsoft Docs
description: Questo articolo sulle procedure illustra come generatore come configurare le regole e le azioni basate sulla telemetria nell'applicazione Azure IoT Central.This how-to article shows you, as a builder, how to configure telemetry-based rules and actions in your Azure IoT Central application.
author: vavilla
ms.author: vavilla
ms.date: 11/27/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 509f9557a8128df12353ad02a7c7db02b7b42631
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158463"
---
# <a name="configure-rules"></a>Configurare le regole



*Questo articolo è rivolto a operatori, autori e amministratori.*

Le regole in IoT Central fungono da strumento di risposta personalizzabile che si attivano su eventi monitorati attivamente dai dispositivi connessi. Nelle sezioni seguenti viene descritto come vengono valutate le regole.

## <a name="select-target-devices"></a>Selezionare i dispositivi di destinazione

Utilizzare la sezione dispositivi di destinazione per selezionare il tipo di dispositivi a cui verrà applicata questa regola. I filtri consentono di perfezionare ulteriormente i dispositivi da includere. I filtri utilizzano le proprietà nel modello di dispositivo per filtrare il set di dispositivi. I filtri stessi non attivano un'azione. Nella schermata seguente, i dispositivi di destinazione sono di tipo **Refrigerator**. Il filtro indica che la regola deve includere solo **i refrigeratori** in cui la proprietà **Stato prodotto** è uguale a **Washington**.

![Condizioni](media/howto-configure-rules/filters.png)

## <a name="use-multiple-conditions"></a>Utilizzare più condizioni

Le condizioni sono le regole che si attivano. Attualmente, quando si aggiungono più condizioni a una regola, sono logicamente e'd insieme. In altre parole, tutte le condizioni devono essere soddisfatte affinché la regola venga valutata come vera.  

Nella schermata seguente, le condizioni controllano&deg; quando la temperatura è maggiore di 70 F e l'umidità è inferiore a 10. Quando entrambe queste istruzioni sono vere, la regola restituisce true e attiva un'azione.

![Condizioni](media/howto-configure-rules/conditions.png)

## <a name="use-aggregate-windowing"></a>Utilizzare la finestra aggregata

Le regole valutano le finestre temporali aggregate come finestre a cascata. Nella schermata seguente, l'intervallo di tempo è di cinque minuti. Ogni cinque minuti, la regola valuta gli ultimi cinque minuti di dati. I dati vengono valutati una sola volta nella finestra a cui corrispondono.

![Finestre a cascata](media/howto-configure-rules/tumbling-window.png)

## <a name="use-rules-with-iot-edge-modules"></a>Usare le regole con i moduli Edge IoT

Una restrizione si applica alle regole applicate ai moduli IoT Edge. Le regole sui dati di telemetria di moduli diversi non vengono valutate come regole valide. Prendiamo ad esempio quanto segue. The first condition of the rule is on a temperature telemetry from Module A. La seconda condizione della regola è su una telemetria di umidità sul modulo B. Poiché le due condizioni provengono da moduli diversi, si tratta di un insieme di condizioni non valido. La regola non è valida e genererà un errore durante il tentativo di salvare la regola.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso come configurare una regola nell'applicazione Azure IoT Central, è possibile:Now that you've learned how to configure a rule in your Azure IoT Central application, you can:

> [!div class="nextstepaction"]
> [Analizza i tuoi dati in tempo reale](howto-create-analytics.md)
