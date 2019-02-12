---
title: Configurare regole e azioni in Azure IoT Central | Microsoft Docs
description: Questa esercitazione illustra come i creatori possono configurare regole e azioni basate su dati di telemetria nell'applicazione Azure IoT Central.
author: ankitscribbles
ms.author: ankitgup
ms.date: 01/28/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: d8a5ca6285624720e23a4986917ab5e715f6ebfa
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/06/2019
ms.locfileid: "55768014"
---
# <a name="tutorial-configure-rules-and-actions-for-your-device-in-azure-iot-central-new-ui-design"></a>Esercitazione: Configurare regole e azioni per il dispositivo in Azure IoT Central (nuovo design dell'interfaccia utente)

*Questo articolo è rivolto a operatori, autori e amministratori.*

In questa esercitazione, si crea una regola che invia un messaggio di posta elettronica quando la temperatura di un condizionatore connesso supera 90&deg; F (circa 32° C).

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare una regola basata su dati di telemetria
> * Aggiungere un'azione

[!INCLUDE [iot-central-experimental-note](../../includes/iot-central-experimental-note.md)]

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, si consiglia di completare l'esercitazione [Definire un nuovo tipo di dispositivo nell'applicazione](tutorial-define-device-type-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json) per creare il modello di dispositivo **Condizionatore connesso** con cui lavorare.

## <a name="create-a-telemetry-based-rule"></a>Creare una regola basata su dati di telemetria

1. Per aggiungere una nuova regola basata su dati di telemetria all'applicazione, scegliere **Modelli di dispositivo** nel menu di spostamento a sinistra:

    ![Pagina Modelli di dispositivo](media/tutorial-configure-rules-experimental/templatespage1.png)

    Viene visualizzato il modello di dispositivo **Condizionatore connesso (1.0.0)** creato nell'esercitazione precedente.

2. Per personalizzare il modello di dispositivo, fare clic sul modello **Condizionatore connesso** creato nell'esercitazione precedente.

3. Per aggiungere una regola basata su dati di telemetria, nella visualizzazione **Regole** scegliere **Regole**, fare clic su **+ Nuova regola** e quindi scegliere **Telemetria**:

    ![Visualizzazione Regole](media/tutorial-configure-rules-experimental/newrule.png)

5. Usare le informazioni nella tabella seguente per definire la regola:

    | Impostazione                                      | Valore                             |
    | -------------------------------------------- | ------------------------------    |
    | NOME                                         | Avviso temperatura condizionatore |
    | Abilita la regola per tutti i dispositivi di questo modello | Attivato                                |
    | Condizione                                    | La temperatura è maggiore di 90    |
    | Aggregazione                                  | Nessuna                              |

    ![Condizione della regola di temperatura](media/tutorial-configure-rules-experimental/temperaturerule.png)

    Fare quindi clic su **Salva**.

## <a name="add-an-action"></a>Aggiungere un'azione

Quando si definisce una regola è anche possibile definire un'azione da eseguire quando vengono soddisfatte le condizioni della regola. In questa esercitazione viene creata una regola con un'azione che invia una notifica tramite posta elettronica.

1. Per aggiungere un'**azione**, **salvare** prima la regola e quindi scorrere verso il basso nel pannello **Configure Telemetry Rule** (Configura regola di telemetria). Scegliere il segno **+** accanto ad **Azioni** e quindi scegliere **Posta elettronica**:

    ![Azione della regola di temperatura](media/tutorial-configure-rules-experimental/addaction.png)

2. Usare le informazioni nella tabella seguente per definire l'azione:

    | Impostazione   | Valore                          |
    | --------- | ------------------------------ |
    | A        | Indirizzo di posta elettronica             |
    | Note     | La temperatura del condizionatore ha superato la soglia. |

    > [!NOTE]
    > Per ricevere una notifica, l'indirizzo di posta elettronica deve essere un [ID utente nell'applicazione](howto-administer-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json) e tale utente deve aver eseguito almeno una volta l'accesso all'applicazione.

    ![Azione della temperatura](media/tutorial-configure-rules-experimental/temperatureaction.png)

3. Fare clic su **Save**. La regola è elencata nella pagina **Regole**.

## <a name="test-the-rule"></a>Testare la regola

Subito dopo aver salvato la regola, questa viene applicata. Quando vengono soddisfatte le condizioni definite nella regola, l'applicazione invia un messaggio all'indirizzo di posta elettronica specificato nell'azione.

![Azione di posta elettronica](media/tutorial-configure-rules-experimental/email.png)

> [!NOTE]
> Al termine del test, disattivare la regola per smettere di ricevere gli avvisi nella posta in arrivo.

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

<!-- Repeat task list from intro -->
> [!div class="nextstepaction"]
> * Creare una regola basata su dati di telemetria
> * Aggiungere un'azione

Dopo aver definito la regola basata su una soglia, il passaggio successivo consigliato consiste nel [Personalizzare le visualizzazioni dell'operatore](tutorial-customize-operator-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).

Per altre informazioni sui diversi tipi di regole in Azure IoT Central e su come aggiungere parametri alla definizione della regola, vedere:
* [Creare una regola di telemetria e impostare le notifiche](howto-create-telemetry-rules-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).
* [Creare una regola di evento e impostare le notifiche](howto-create-event-rules-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).

<!-- Next tutorials in the sequence -->
