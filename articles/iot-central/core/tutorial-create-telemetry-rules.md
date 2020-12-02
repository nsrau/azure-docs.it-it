---
title: "Esercitazione: Creare e gestire le regole nell'applicazione Azure IoT Central"
description: Questa esercitazione illustra come usare le regole di Azure IoT Central per il monitoraggio dei dispositivi in tempo reale e l'attivazione automatica di azioni quali l'invio di un messaggio di posta elettronica quando la regola si attiva.
author: dominicbetts
ms.author: dobett
ms.date: 11/16/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 6d49e3585460c95ca931f497a63cbc281aed1db1
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94991014"
---
# <a name="tutorial-create-a-rule-and-set-up-notifications-in-your-azure-iot-central-application"></a>Esercitazione: Creare una regola e configurare le notifiche nell'applicazione Azure IoT Central

*Questo articolo è rivolto a operatori, autori e amministratori.*

È possibile usare Azure IoT Central per monitorare in remoto i dispositivi connessi. Le regole di Azure IoT Central consentono di monitorare i dispositivi quasi in tempo reale e di richiamare automaticamente le azioni, ad esempio l'invio di un messaggio di posta elettronica. Questo articolo illustra come creare regole che consentono di monitorare i dati di telemetria inviati dal dispositivo.

I dispositivi usano la telemetria per l'invio di dati numerici dal dispositivo. Una regola viene attivata quando i dati di telemetria selezionati superano una soglia specificata.

In questa esercitazione viene creata una regola per l'invio di un messaggio di posta elettronica quando la temperatura di un dispositivo sensore simulato supera 70&deg; F (circa 32° C).

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
>
> * Creare una regola
> * Aggiungere un'azione di posta elettronica

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, completare le guide di avvio rapido [Creare un'applicazione Azure IoT Central](./quick-deploy-iot-central.md) e [Aggiungere un dispositivo simulato a un'applicazione IoT Central](./quick-create-simulated-device.md) per creare il modello di dispositivo **Sensor Controller** da usare.

## <a name="create-a-rule"></a>Creare una regola

Per creare una regola di telemetria, il modello di dispositivo deve includere almeno un valore di telemetria. Questa esercitazione usa un dispositivo **Sensor Controller** simulato che invia dati di telemetria relativi a temperatura e umidità. Nell'argomento di avvio rapido [Aggiungere un dispositivo simulato a un'applicazione IoT Central](./quick-create-simulated-device.md) è stato aggiunto questo modello di dispositivo ed è stato creato un dispositivo simulato. La regola esegue il monitoraggio della temperatura segnalata dal dispositivo e invia un messaggio di posta elettronica quando la temperatura supera un determinato valore, ad esempio 70 gradi.

1. Nel riquadro sinistro selezionare **Regole**.

1. Se non è ancora stata creata alcuna regola, viene visualizzata la schermata seguente:

    :::image type="content" source="media/tutorial-create-telemetry-rules/rules-landing-page.png" alt-text="Screenshot che mostra l'elenco di regole vuoto":::

1. Selezionare **+ Nuovo** per aggiungere una nuova regola.

1. Immettere il nome _Temperature monitor_ per identificare la regola e premere INVIO.

1. Selezionare il modello di dispositivo **Sensor Controller**. Per impostazione predefinita, la regola viene applicata automaticamente a tutti i dispositivi associati al modello di dispositivo. Per filtrare un subset dei dispositivi, selezionare **+ Filtra** e usare le proprietà dei dispositivi per identificare i dispositivi. Per disabilitare la regola, attivare/disattivare il pulsante **Abilitato/Disabilitato**:

    :::image type="content" source="media/tutorial-create-telemetry-rules/device-filters.png" alt-text="Screenshot che mostra la selezione del modello di dispositivo nella definizione della regola":::

### <a name="configure-the-rule-conditions"></a>Configurare le condizioni della regola

Le condizioni definiscono i criteri monitorati dalla regola. In questa esercitazione si configura la regola in modo che si attivi quando la temperatura supera 70&deg; F (circa 32° C).

1. Selezionare **Temperatura** nell'elenco a discesa **Telemetria**.

1. Scegliere quindi **È maggiore di** in **Operatore** e immettere _70_ in **Valore**.

    :::image type="content" source="media/tutorial-create-telemetry-rules/condition-filled-out.png" alt-text="Screenshot che mostra la condizione della regola per la temperatura":::

1. Facoltativamente, è possibile impostare un valore per **Aggregazione temporale**. Quando si seleziona un'aggregazione temporale, è necessario selezionare anche un tipo di aggregazione dall'elenco a discesa Aggregazione, ad esempio Media o Somma.

    * Senza aggregazione, la regola viene attivata per ogni punto dati di telemetria che soddisfa la condizione. Se ad esempio si configura la regola per attivarsi quando la temperatura è superiore a 70, si attiva quasi immediatamente quando la temperatura del dispositivo supera questo valore.
    * Con l'aggregazione, la regola si attiva se il valore di aggregazione dei punti dati di telemetria nella finestra dei valori temporali soddisfa la condizione. Se ad esempio si configura la regola per attivarsi quando la temperatura è superiore a 70 e con un'aggregazione temporale media di 10 minuti, la regola si attiva quando il dispositivo segnala una temperatura media maggiore di 70, calcolata in un intervallo di tempo di 10 minuti.

    :::image type="content" source="media/tutorial-create-telemetry-rules/aggregate-condition-filled-out.png" alt-text="Screenshot che mostra la condizione di aggregazione completata":::

È possibile aggiungere più condizioni a una regola selezionando **+ Condizione**. Se si specificano più condizioni, la regola viene attivata solo quando tutte le condizioni risultano soddisfatte. Ogni condizione viene unita da una clausola `AND` implicita. Se si usa un'aggregazione temporale con più condizioni, tutti i valori di telemetria devono essere aggregati.

### <a name="configure-actions"></a>Configurare le azioni

Dopo aver definito la condizione, è necessario impostare le azioni che devono essere eseguite quando si attiva la regola. Le azioni vengono richiamate quando tutte le condizioni specificate nella regola restituiscono il valore true.

1. Selezionare **+ Posta elettronica** nella sezione **Azioni**.

1. Immettere _Temperature warning_ come nome visualizzato per l'azione, l'indirizzo di posta elettronica nel campo **A** e il messaggio _You should check the device!_ (Controllare il dispositivo) come nota da visualizzare nel corpo del messaggio di posta elettronica.

    > [!NOTE]
    > I messaggi di posta elettronica vengono inviati solo agli utenti che sono stati aggiunti all'applicazione e hanno eseguito l'accesso almeno una volta. Altre informazioni sulla [gestione degli utenti](howto-administer.md) in Azure IoT Central.

    :::image type="content" source="media/tutorial-create-telemetry-rules/configure-action.png" alt-text="Screenshot che mostra l'azione della regola per l'invio di un messaggio di posta elettronica":::

1. Per salvare l'azione, scegliere **Fine**. È possibile aggiungere più azioni a una regola.

1. Per salvare la regola, scegliere **Save** (Salva). La regola diventa attiva entro pochi minuti e avvia il monitoraggio dei dati di telemetria inviati all'applicazione. Quando si verifica la condizione specificata nella regola, quest'ultima attiva l'azione di posta elettronica configurata.

Dopo qualche tempo, si riceverà un messaggio di posta elettronica quando si attiva la regola:

:::image type="content" source="media/tutorial-create-telemetry-rules/email.png" alt-text="Screenshot che mostra il messaggio di posta elettronica di notifica":::

## <a name="delete-a-rule"></a>Eliminare una regola

Quando una regola non è più necessaria, eliminarla aprendo la regola e scegliendo **Delete** (Elimina).

## <a name="enable-or-disable-a-rule"></a>Abilitare o disabilitare una regola

Scegliere la regola che si intende abilitare o disabilitare. Attivare o disattivare il pulsante **Abilitata/Disabilitata** nella regola per abilitarla o disabilitarla per tutti i dispositivi a cui si applica la regola.

## <a name="enable-or-disable-a-rule-for-specific-devices"></a>Abilitare o disabilitare una regola per dispositivi specifici

Scegliere la regola che si vuole personalizzare. Usare uno o più filtri nella sezione **Dispositivi di destinazione** per limitare l'ambito della regola ai dispositivi da monitorare.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state illustrate le procedure per:

* Creare una regola basata su dati di telemetria
* Aggiungere un'azione

Ora che è stata definita una regola basata su una soglia, il passaggio successivo consigliato consiste nell'ottenere informazioni su come:

> [!div class="nextstepaction"]
> [Configurare l'esportazione continua dei dati](./howto-export-data.md).
