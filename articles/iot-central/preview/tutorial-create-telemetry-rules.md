---
title: "Esercitazione: Creare e gestire le regole nell'applicazione Azure IoT Central"
description: Questa esercitazione illustra come usare le regole di Azure IoT Central per il monitoraggio dei dispositivi in tempo reale e l'attivazione automatica di azioni quali l'invio di un messaggio di posta elettronica quando la regola si attiva.
author: dominicbetts
ms.author: dobett
ms.date: 10/24/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: e2ec01e372ebda79272b585ea6f1708029ea7b13
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74702533"
---
# <a name="tutorial-create-a-rule-and-set-up-notifications-in-your-azure-iot-central-application-preview-features"></a>Esercitazione: Creare una regola e impostare le notifiche nell'applicazione Azure IoT Central (funzionalità in anteprima)

*Questo articolo è rivolto a operatori, autori e amministratori.*

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

È possibile usare Azure IoT Central per monitorare in remoto i dispositivi connessi. Le regole di Azure IoT Central consentono di monitorare i dispositivi quasi in tempo reale e di richiamare automaticamente azioni quali l'invio di un messaggio di posta elettronica. Con pochi clic è possibile definire una condizione per monitorare i dati di telemetria del dispositivo e configurare un'azione corrispondente. Questo articolo illustra come creare regole che consentono di monitorare i dati di telemetria inviati dal dispositivo.

I dispositivi usano la telemetria per l'invio di dati numerici dal dispositivo. Una regola viene attivata quando i dati di telemetria del dispositivo selezionato superano una soglia specificata.

In questa esercitazione si crea una regola per l'invio di un messaggio di posta elettronica quando la temperatura di un dispositivo di tipo sensore ambientale supera 80&deg; F (circa 32° C).

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare una regola
> * Aggiungere un'azione di posta elettronica

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, è necessario completare gli argomenti di avvio rapido [Creare un'applicazione Azure IoT Central](./quick-deploy-iot-central.md) e [Aggiungere un dispositivo simulato a un'applicazione IoT Central](./quick-create-pnp-device.md) per creare il modello di dispositivo di tipo **Sensore ambientale** da usare.

## <a name="create-a-rule"></a>Creare una regola

Per creare una regola di telemetria, il modello di dispositivo deve disporre di misurazione di almeno una telemetria definita. Questa esercitazione usa un dispositivo di tipo sensore ambientale che invia dati di telemetria relativi a temperatura e umidità. Nell'argomento di avvio rapido [Aggiungere un dispositivo simulato a un'applicazione IoT Central](./quick-create-pnp-device.md) è stato aggiunto questo modello di dispositivo ed è stato creato un dispositivo simulato. La regola esegue il monitoraggio della temperatura segnalata dal dispositivo e invia un messaggio di posta elettronica quando la temperatura supera un determinato valore, ad esempio 27 gradi.

1. Nel riquadro sinistro selezionare **Regole**.

1. Se non è ancora stata creata alcuna regola, viene visualizzata la schermata seguente:

    ![Nessuna regola](media/tutorial-create-telemetry-rules/rules-landing-page1.png)

1. Selezionare **+** per aggiungere una nuova regola.

1. Immettere il nome _Temperature monitor_ per identificare la regola e premere INVIO.

1. Selezionare il modello di dispositivo **Sensore ambientale** (Environmental Sensor). Per impostazione predefinita, la regola viene applicata automaticamente a tutti i dispositivi associati al modello di dispositivo. Per filtrare un subset dei dispositivi, selezionare **+ Filtra** e usare le proprietà dei dispositivi per identificare i dispositivi. Per disabilitare la regola, attivare/disattivare il pulsante **Abilitato/Disabilitato** nell'intestazione della regola:

    ![Filtri e abilitazione](media/tutorial-create-telemetry-rules/device-filters.png)

### <a name="configure-the-rule-conditions"></a>Configurare le condizioni della regola

Le condizioni definiscono i criteri monitorati dalla regola. In questa esercitazione si configura la regola in modo che si attivi quando la temperatura supera 80&deg; F (circa 32° C).

1. Selezionare **Temperatura** nell'elenco a discesa **Telemetria**.

1. Scegliere quindi **È maggiore di** in **Operatore** e immettere _80_ in **Valore**.

    ![Condizione](media/tutorial-create-telemetry-rules/condition-filled-out1.png)

1. Facoltativamente, è possibile impostare un valore per **Aggregazione temporale**. Quando si seleziona un'aggregazione temporale, è necessario selezionare anche un tipo di aggregazione dall'elenco a discesa Aggregazione, ad esempio Media o Somma.

    * Senza aggregazione, la regola viene attivata per ogni punto dati di telemetria che soddisfa la condizione. Se ad esempio la regola è configurata per attivarsi quando la temperatura è superiore a 80, si attiva quasi immediatamente quando il dispositivo segnala una temperatura > 80.
    * Con l'aggregazione, la regola si attiva se il valore di aggregazione dei punti dati di telemetria nella finestra dei valori temporali soddisfa la condizione. Se ad esempio la regola è configurata per attivarsi quando la temperatura è superiore a 80, l'aggregazione temporale è impostata su 10 minuti e il tipo di aggregazione è impostato su Media, la regola si attiva quando il dispositivo segnala una temperatura media > 80, calcolata in un intervallo di tempo di 10 minuti.

     ![Condizione di aggregazione](media/tutorial-create-telemetry-rules/aggregate-condition-filled-out1.png)

È possibile aggiungere più condizioni a una regola selezionando **+ Condizione**. Se si specificano più condizioni, la regola viene attivata solo quando tutte le condizioni risultano soddisfatte. Ogni condizione viene unita da una clausola `AND` implicita. Se si usa un'aggregazione temporale con più condizioni, tutti i valori di telemetria devono essere aggregati.

### <a name="configure-actions"></a>Configurare le azioni

Dopo aver definito la condizione, è necessario impostare le azioni che devono essere eseguite quando si attiva la regola. Le azioni vengono richiamate quando tutte le condizioni specificate nella regola restituiscono il valore true. L'unica azione attualmente disponibile è l'invio di un messaggio di posta elettronica.

1. Selezionare **+ Posta elettronica** nella sezione **Azioni**.

1. Immettere _Temperature warning_ come nome visualizzato per l'azione, l'indirizzo di posta elettronica nel campo **A** e il messaggio _You should check the device!_ (Controllare il dispositivo) come nota da visualizzare nel corpo del messaggio di posta elettronica.

    > [!NOTE]
    > I messaggi di posta elettronica vengono inviati solo agli utenti che sono stati aggiunti all'applicazione e hanno eseguito l'accesso almeno una volta. Altre informazioni sulla [gestione degli utenti](howto-administer.md) in Azure IoT Central.

   ![Configurare l'azione](media/tutorial-create-telemetry-rules/configure-action1.png)

1. Per salvare l'azione, scegliere **Fine**. È possibile aggiungere più azioni a una regola.

1. Per salvare la regola, scegliere **Save** (Salva). La regola diventa attiva entro pochi minuti e avvia il monitoraggio dei dati di telemetria inviati all'applicazione. Quando si verifica la condizione specificata nella regola, quest'ultima attiva l'azione di posta elettronica configurata.

Dopo qualche tempo, si riceverà un messaggio di posta elettronica quando si attiva la regola:

![Messaggio di posta elettronica di esempio](media/tutorial-create-telemetry-rules/email.png)

## <a name="delete-a-rule"></a>Eliminare una regola

Quando una regola non è più necessaria, eliminarla aprendo la regola e scegliendo **Delete** (Elimina).

## <a name="enable-or-disable-a-rule"></a>Abilitare o disabilitare una regola

Scegliere la regola che si intende abilitare o disabilitare. Attivare o disattivare il pulsante **Abilita** o **Disabilita** nella regola per abilitarla o disabilitarla per tutti i dispositivi a cui si applica la regola.

## <a name="enable-or-disable-a-rule-for-a-device"></a>Abilitare o disabilitare una regola per un dispositivo

Scegliere la regola che si intende abilitare o disabilitare. Aggiungere un filtro nella sezione **Ambiti** per includere o escludere un dispositivo specifico nel modello di dispositivo.

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

* Creare una regola basata su dati di telemetria
* Aggiungere un'azione

Ora che è stata definita una regola basata su una soglia, il passaggio successivo consigliato consiste nell'ottenere informazioni su come:

> [!div class="nextstepaction"]
> [Configurare l'esportazione continua dei dati](./howto-export-data.md).
