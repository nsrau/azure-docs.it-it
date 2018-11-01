---
title: Configurare regole e azioni in Azure IoT Central | Microsoft Docs
description: Questa esercitazione illustra come i creatori possono configurare regole e azioni basate su dati di telemetria nell'applicazione Azure IoT Central.
author: ankitgupta
ms.author: ankitgup
ms.date: 10/12/2018
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: fbe9e1fbd0891f2f39b05fa7ba53653188ef8e03
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/26/2018
ms.locfileid: "50158075"
---
# <a name="tutorial-configure-rules-and-actions-for-your-device-in-azure-iot-central"></a>Esercitazione: Configurare regole e azioni per il dispositivo in Azure IoT Central

*Questo articolo è rivolto a operatori, autori e amministratori.*

In questa esercitazione, si crea una regola che invia un messaggio di posta elettronica quando la temperatura di un condizionatore connesso supera 90&deg; F (circa 32° C).

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare una regola basata su dati di telemetria
> * Aggiungere un'azione

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, si consiglia di completare l'esercitazione [Definire un nuovo tipo di dispositivo nell'applicazione](tutorial-define-device-type.md) per creare il modello di dispositivo **Condizionatore connesso** con cui lavorare.

## <a name="create-a-telemetry-based-rule"></a>Creare una regola basata su dati di telemetria

1. Per aggiungere una nuova regola basata su dati di telemetria all'applicazione, scegliere **Device Explorer** nel menu di spostamento a sinistra:

    ![Pagina Device Explorer](media/tutorial-configure-rules/explorerpage1.png)

    Vengono visualizzati il modello di dispositivo **Condizionatore connesso (1.0.0)** e il dispositivo **Condizionatore connesso 1** creati nell'esercitazione precedente.

2. Per iniziare a personalizzare il condizionatore connesso, scegliere il dispositivo creato nell'esercitazione precedente:

    ![Pagina del condizionatore connesso](media/tutorial-configure-rules/builderdevicelist1.png)

3. Per iniziare ad aggiungere una regola nella visualizzazione **Regole**, scegliere **Regole** e fare clic su **Modifica modello**:

    ![Visualizzazione Regole](media/tutorial-configure-rules/builderedittemplate.png)

4. Per creare la regola di telemetria basata su una soglia, fare clic su **Nuova regola** e quindi **Telemetria**.

    ![Modifica modello](media/tutorial-configure-rules/buildernewrule.png)

5. Usare le informazioni nella tabella seguente per definire la regola:

    | Impostazione                                      | Valore                             |
    | -------------------------------------------- | ------------------------------    |
    | NOME                                         | Avviso temperatura condizionatore |
    | Abilita la regola per tutti i dispositivi di questo modello | Attivato                                |
    | Abilita la regola in questo dispositivo                   | Attivato                                |
    | Condizione                                    | La temperatura è maggiore di 90    |
    | Aggregazione                                  | Nessuna                              |

    ![Condizione della regola di temperatura](media/tutorial-configure-rules/buildertemperaturerule1.png)

## <a name="add-an-action"></a>Aggiungere un'azione

Quando si definisce una regola è anche possibile definire un'azione da eseguire quando vengono soddisfatte le condizioni della regola. In questa esercitazione si aggiunge un'azione per inviare un messaggio di posta elettronica come notifica quando la regola viene attivata.

1. Per aggiungere un'**Azione**, **salvare** innanzitutto la regola, quindi scorrere verso il basso fino al pannello **Configura regola di telemetria** e scegliere **+** accanto ad **Azioni**, quindi scegliere **Messaggio di posta elettronica**:

    ![Azione della regola di temperatura](media/tutorial-configure-rules/builderaddaction1.png)

2. Usare le informazioni nella tabella seguente per definire l'azione:

    | Impostazione   | Valore                          |
    | --------- | ------------------------------ |
    | A        | Indirizzo di posta elettronica             |
    | Note     | La temperatura del condizionatore ha superato la soglia. |

    > [!NOTE]
    > Per ricevere una notifica, l'indirizzo di posta elettronica deve essere un [ID utente nell'applicazione](howto-administer.md) e tale utente deve aver eseguito almeno una volta l'accesso all'applicazione.

    ![Azione di temperatura in Application Builder (Generatore applicazioni)](media/tutorial-configure-rules/buildertemperatureaction.png)

3. Scegliere **Salva**. La regola è elencata nella pagina **Regole**:

    ![Regole di Application Builder (Generatore applicazioni)](media/tutorial-configure-rules/builderrules1.png)

4. Scegliere **Fine** per uscire dalla modalità **Modifica modello**.
 

## <a name="test-the-rule"></a>Testare la regola

Subito dopo aver salvato la regola, questa viene applicata. Quando vengono soddisfatte le condizioni definite nella regola, l'applicazione invia un messaggio all'indirizzo di posta elettronica specificato nell'azione.

![Azione di posta elettronica](media/tutorial-configure-rules/email.png)

> [!NOTE]
> Al termine del test, disattivare la regola per smettere di ricevere gli avvisi nella posta in arrivo. 

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

<!-- Repeat task list from intro -->
> [!div class="nextstepaction"]
> * Creare una regola basata su dati di telemetria
> * Aggiungere un'azione

Dopo aver definito la regola basata su una soglia, il passaggio successivo consigliato consiste nel [Personalizzare le visualizzazioni dell'operatore](tutorial-customize-operator.md).

Per altre informazioni sui diversi tipi di regole in Azure IoT Central e su come aggiungere parametri alla definizione della regola, vedere:
* [Creare una regola di telemetria e impostare le notifiche](howto-create-telemetry-rules.md).
* [Creare una regola di evento e impostare le notifiche](howto-create-event-rules.md).

<!-- Next tutorials in the sequence -->
