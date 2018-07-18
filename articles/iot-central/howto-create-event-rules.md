---
title: Creare e gestire le regole eventi nell'applicazione Azure IoT Central | Microsoft Docs
description: Le regole eventi di Azure IoT Central consentono il monitoraggio dei dispositivi in tempo reale e l'attivazione automatica di azioni quali l'invio di un messaggio di posta elettronica quando la regola si attiva.
author: ankitgupta
ms.author: ankitgup
ms.date: 04/29/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 30223fdca9d848ddc407981bf4a3ca683a10575a
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2018
ms.locfileid: "34628369"
---
# <a name="create-an-event-rule-and-set-up-notifications-in-your-azure-iot-central-application"></a>Creare una regola eventi e impostare notifiche nell'applicazione Azure IoT Central

È possibile usare Microsoft Azure IoT Central per monitorare in remoto i dispositivi connessi. Le regole di Azure IoT Central consentono il monitoraggio dei dispositivi in tempo reale e l'attivazione automatica di azioni quali l'invio di un messaggio di posta elettronica quando la regola si attiva. Con pochi clic è possibile definire la condizione per monitorare i dati del dispositivo e configurare l'azione da chiamare. Questo articolo spiega la regola di monitoraggio eventi in modo dettagliato.

Azure IoT Central usa la [misura di evento](howto-set-up-template.md) per acquisire dati dai dispositivi. Ogni tipo di misura ha attributi chiave che definiscono la misura sessa. È possibile creare regole per monitorare ogni tipo di misura del dispositivo e generare avvisi quando la regola si attiva. Una regola eventi si attiva quando l'evento dispositivo selezionato viene segnalato dal dispositivo.

## <a name="create-an-event-rule"></a>Creare una regola eventi

Questa sezione descrive come creare una regola eventi. L'esempio usa un dispositivo distributore automatico refrigerato che segnala l'evento Fan Motor Error (Errore motore ventola). La regola esegue il monitoraggio dell'evento segnalato dal dispositivo e invia un messaggio di posta elettronica ogni volta che l'evento viene segnalato.

1. Passare alla pagina dei dettagli per il dispositivo a cui si vuole aggiungere la regola.

1. Se non è ancora stata creata nessuna regola viene visualizzata la schermata seguente:

    ![Nessuna regola](media\howto-create-event-rules\image1.png)

1. Nella scheda **Rules** (Regole) scegliere **+ New Rule** (+ Nuova regola) per visualizzare i tipi di regole che è possibile creare.

    ![Tipi di regole](media\howto-create-event-rules\image2.png)

1. Fare clic su **Event** (Evento) per aprire il modulo in cui creare la regola.

    ![Regola eventi](media\howto-create-event-rules\image3.png)

1. Scegliere un nome che consente di identificare la regola in questo modello di dispositivo.

1. Per attivare immediatamente la regola per tutti i dispositivi creati da questo modello, attivare o disattivare **Enable rule** (Abilita regola).

### <a name="configure-the-rule-condition"></a>Configurare la condizione della regola

Questa sezione descrive come aggiungere una condizione per monitorare la misura dell'evento Fan Motor Error (Errore motore ventola).

1. Scegliere **+** accanto a **Condition** (Condizione).

1. Scegliere la misura di evento che si vuole monitorare nell'elenco a discesa. In questo esempio è stato selezionato l'evento **Fan Motor Error** (Errore motore ventola).

1. Facoltativamente è possibile fornire anche un valore, se si vuole monitorare un valore specifico dell'evento segnalato dal dispositivo. Ad esempio, se il dispositivo segnala lo stesso evento con diversi codici di errore, fornendo il codice di errore come valore nella condizione della regola si garantisce che la regola si attivi solo quando il dispositivo invia quel valore specifico come payload dell'evento. Se lo si lascia vuoto, la regola si attiva ogni volta che il dispositivo invia l'evento, indipendentemente dal valore dell'evento.

    ![Aggiungere una condizione evento](media\howto-create-event-rules\image4.png)

    > [!NOTE]
    > Quando si definisce una condizione della regola eventi, è necessario selezionare almeno una misura di evento.

### <a name="configure-the-action"></a>Configurare l'azione

Questa sezione descrive come specificare il comportamento della regola mediante l'aggiunta di un'azione quando la condizione raggiunge la soglia prevista.

1. Scegliere **+** accanto ad **Actions** (Azioni). Viene visualizzato l'elenco delle azioni disponibili. L'unica azione supportata per l'anteprima pubblica è **Email** (E-mail).

    ![Aggiungere un'azione](media\howto-create-event-rules\image5.png)

1. Scegliere l'azione **Email** (E-mail), immettere un indirizzo di posta elettronica valido nel campo **To** (A) e specificare una nota che viene visualizzata nel corpo del messaggio di posta elettronica quando la regola si attiva.

    > [!NOTE]
    > I messaggi di posta elettronica vengono inviati solo agli utenti che sono stati aggiunti all'applicazione e hanno eseguito l'accesso almeno una volta. Altre informazioni sulla [gestione degli utenti](howto-administer.md) in Azure IoT Central.

   ![Configurare l'azione](media\howto-create-event-rules\image6.png)

1. Per salvare la regola, scegliere **Save** (Salva). La regola diventa operativa entro pochi minuti e avvia il monitoraggio degli eventi inviati all'applicazione. Quando si verifica la condizione specificata nella regola, quest'ultima attiva l'azione e-mail configurata.

## <a name="parameterize-the-rule"></a>Impostare parametri per la regola

Le azioni possono anche essere configurate usando **Device Property** (Proprietà dispositivo) come parametro. Se un indirizzo di posta elettronica viene archiviato come una proprietà dispositivo, può essere usato quando si definisce l'indirizzo **To** (A).

## <a name="delete-a-rule"></a>Eliminare una regola

Quando una regola non è più necessaria, eliminarla aprendo la regola e scegliendo **Delete** (Elimina). Eliminando la regola, la si rimuove dal modello di dispositivo e da tutti i dispositivi associati.

## <a name="enable-or-disable-a-rule-for-a-device-template"></a>Abilitare o disabilitare una regola per un modello di dispositivo

Passare al dispositivo e scegliere la regola che si vuole abilitare o disabilitare. Selezionare o disattivare il pulsante **Enable rule for all devices of this template** (Abilita regola per tutti i dispositivi di questo modello) nella regola per abilitare o disabilitare la regola per tutti i dispositivi associati al modello di dispositivo.

## <a name="enable-or-disable-a-rule-for-a-device"></a>Abilitare o disabilitare una regola per un dispositivo

Passare al dispositivo e scegliere la regola che si vuole abilitare o disabilitare. Selezionare o disattivare il pulsante **Enable rule for this device** (Abilita regola per questo dispositivo) per abilitare o disabilitare la regola per il dispositivo.

## <a name="next-steps"></a>Passaggi successivi

Ora che si conosce la procedura per creare regole nell'applicazione Azure IoT Central, il prossimo passo suggerito è:

> [!div class="nextstepaction"]
> [Come gestire i dispositivi](howto-manage-devices.md).