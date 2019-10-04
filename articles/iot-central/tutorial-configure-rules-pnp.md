---
title: Configurare regole e azioni in Azure IoT Central | Microsoft Docs
description: Questa esercitazione illustra come i creatori possono configurare regole e azioni basate su dati di telemetria nell'applicazione Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 08/06/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: c2aa6edfe7ce9b670ea1015e2da72f3ecc48c9ec
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69878832"
---
# <a name="tutorial-configure-rules-and-actions-for-your-device-in-azure-iot-central-preview-features"></a>Esercitazione: Configurare regole e azioni per il dispositivo in Azure IoT Central (funzionalità di anteprima)

*Questo articolo è rivolto a operatori, autori e amministratori.*

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

In questa esercitazione si crea una regola che invia un messaggio di posta elettronica quando la temperatura di un dispositivo di tipo sensore ambientale supera 90&deg; F (circa 32° C).

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare una regola basata su dati di telemetria
> * Aggiungere un'azione

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, è consigliabile completare l'esercitazione [Definire un nuovo tipo di dispositivo nell'applicazione](tutorial-define-device-type-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) per creare il modello di dispositivo **Sensore ambientale** da usare.

## <a name="create-a-telemetry-based-rule"></a>Creare una regola basata su dati di telemetria

1. Per aggiungere una nuova regola basata sui dati di telemetria all'applicazione, scegliere **Regole** nel menu di spostamento a sinistra.

1. Per creare una nuova regola, selezionare **+ Nuovo**. Scegliere quindi **Telemetria**.

1. Immettere **Temperatura ambiente** come nome della regola.

1. Nella sezione **Ambito** selezionare **Sensore ambientale** come modello di dispositivo. L'ambito indica i dispositivi a cui si applica la regola. È possibile aggiungere altri criteri di filtro tramite il comando **+ Filtro**.

1. Nella sezione **Condizione** definire ciò che attiva la regola. Usare le informazioni seguenti per definire una condizione basata su dati di telemetria relativi alla temperatura:

    | Campo                                        | Valore                             |
    | -------------------------------------------- | ------------------------------    |
    | Misura                                  | Temperatura                       |
    | Operatore                                     | è maggiore di                   |
    | Valore                                        | 90                                |

    Per aggiungere altre condizioni, selezionare **+ Condizione**.

    ![Creare una condizione per la regola](./media/tutorial-configure-rules-pnp/condition.png)

1. Per aggiungere un'azione da eseguire quando la regola viene attivata, selezionare **+ Azione** e scegliere **Posta elettronica**.

1. Usare le informazioni nella tabella seguente per definire l'azione:

    | Impostazione   | Valore                                             |
    | --------- | ------------------------------------------------- |
    | A        | Indirizzo di posta elettronica                                |
    | Note     | La temperatura ambiente ha superato la soglia. |

    > [!NOTE]
    > Per ricevere una notifica, l'indirizzo di posta elettronica deve essere un [ID utente nell'applicazione](howto-administer-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) e tale utente deve aver eseguito almeno una volta l'accesso all'applicazione.

    ![Creare l'azione per la regola](./media/tutorial-configure-rules-pnp/action.png)

1. Selezionare **Salva**. La regola è elencata nella pagina **Regole**.

## <a name="test-the-rule"></a>Testare la regola

Subito dopo aver salvato la regola, questa viene applicata. Quando vengono soddisfatte le condizioni definite nella regola, l'applicazione invia un messaggio all'indirizzo di posta elettronica specificato nell'azione.

> [!NOTE]
> Al termine del test, disattivare la regola per smettere di ricevere gli avvisi nella posta in arrivo.

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

* Creare una regola basata su dati di telemetria
* Aggiungere un'azione

Ora che è stata definita una regola basata su una soglia, il passaggio successivo consigliato è il seguente:

> [!div class="nextstepaction"]
> [Creare una regola di evento e impostare le notifiche](howto-create-event-rules-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).
