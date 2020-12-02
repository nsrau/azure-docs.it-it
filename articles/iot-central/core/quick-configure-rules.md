---
title: 'Avvio rapido: Configurare regole e azioni in Azure IoT Central'
description: Questo avvio rapido illustra come i creatori possono configurare regole e azioni basate su dati di telemetria nell'applicazione Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 11/16/2020
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 99846a5f2435398d13c436460a2756b1b021a1be
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94990204"
---
# <a name="quickstart-configure-rules-and-actions-for-your-device-in-azure-iot-central"></a>Guida introduttiva: Configurare regole e azioni per il dispositivo in Azure IoT Central

*Questo articolo è rivolto a operatori, autori e amministratori.*

In questa guida di avvio rapido si crea una regola che invia un messaggio di posta elettronica quando l'umidità segnalata da un dispositivo sensore supera il 55%.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, è necessario completare le due guide di avvio rapido precedenti, [Creare un'applicazione Azure IoT Central](./quick-deploy-iot-central.md) e [Aggiungere un dispositivo simulato a un'applicazione IoT Central](./quick-create-simulated-device.md), per creare il modello di dispositivo **Sensor Controller** da usare.

## <a name="create-a-telemetry-based-rule"></a>Creare una regola basata su dati di telemetria

1. Per aggiungere una nuova regola basata sui dati di telemetria all'applicazione, selezionare **Regole**.

1. Per creare una nuova regola, selezionare **+ Nuovo**.

1. Immettere **Environmental humidity** (Umidità ambientale) come nome della regola.

1. Nella sezione **Dispositivi di destinazione** selezionare **Sensor Controller** come modello di dispositivo. Questa opzione filtra i dispositivi a cui si applica la regola per tipo di modello di dispositivo. È possibile aggiungere altri criteri di filtro selezionando **+ Filtro**.

1. Nella sezione **Condizioni** definire ciò che attiva la regola. Usare le informazioni seguenti per definire una condizione basata su dati di telemetria relativi alla temperatura:

    | Campo        | valore            |
    | ------------ | ---------------- |
    | Misura  | SensorHumid      |
    | Operatore     | è maggiore di  |
    | valore        | 55               |

    Per aggiungere altre condizioni, selezionare **+ Condizione**.

    :::image type="content" source="media/quick-configure-rules/condition.png" alt-text="Screenshot che mostra la condizione della regola":::

1. Per aggiungere un'azione di posta elettronica da eseguire quando la regola viene attivata, selezionare **+ Posta elettronica**.

1. Usare le informazioni nella tabella seguente per definire l'azione e quindi selezionare **Fine**:

    | Impostazione   | valore                                             |
    | --------- | ------------------------------------------------- |
    | Nome visualizzato | Azione di posta elettronica dell'operatore                          |
    | A        | Indirizzo di posta elettronica                                |
    | Note     | L'umidità ambientale ha superato la soglia. |

    > [!NOTE]
    > Per ricevere una notifica, l'indirizzo di posta elettronica deve essere un [ID utente nell'applicazione](howto-administer.md) e tale utente deve aver eseguito almeno una volta l'accesso all'applicazione.

    :::image type="content" source="media/quick-configure-rules/action.png" alt-text="Screenshot che mostra l'azione per l'invio di un messaggio di posta elettronica aggiunta alla regola":::

1. Selezionare **Salva**. La regola è elencata nella pagina **Regole**.

## <a name="test-the-rule"></a>Testare la regola

Subito dopo aver salvato la regola, questa viene applicata. Quando vengono soddisfatte le condizioni definite nella regola, l'applicazione invia un messaggio all'indirizzo di posta elettronica specificato nell'azione.

> [!NOTE]
> Al termine del test, disattivare la regola per smettere di ricevere gli avvisi nella posta in arrivo.

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva si è appreso come:

* Creare una regola basata su dati di telemetria
* Aggiungere un'azione

Per altre informazioni sul monitoraggio dei dispositivi connessi all'applicazione, continuare con l'avvio rapido:

> [!div class="nextstepaction"]
> [Usare Azure IoT Central per monitorare i dispositivi](quick-monitor-devices.md).
