---
title: Creare e gestire le regole eventi nell'applicazione Azure IoT Central | Microsoft Docs
description: Le regole eventi di Azure IoT Central consentono il monitoraggio dei dispositivi in tempo reale e l'attivazione automatica di azioni quali l'invio di un messaggio di posta elettronica quando la regola si attiva.
author: ankitscribbles
ms.author: ankitgup
ms.date: 06/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: c931cfbcff750d96828641669c4aaa15e7932970
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69877400"
---
# <a name="create-an-event-rule-and-set-up-notifications-in-your-azure-iot-central-application"></a>Creare una regola eventi e impostare notifiche nell'applicazione Azure IoT Central

*Questo articolo è rivolto a operatori, autori e amministratori.*

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

È possibile usare Azure IoT Central per monitorare in remoto i dispositivi connessi. Le regole di Azure IoT Central consentono il monitoraggio dei dispositivi in tempo reale e l'attivazione automatica di azioni quali l'invio di un messaggio di posta elettronica o l'avvio di Microsoft Flow. Con pochi clic è possibile definire la condizione per cui monitorare i dati del dispositivo e configurare l'azione corrispondente. Questo articolo illustra come creare regole che consentono di monitorare gli eventi inviati dal dispositivo.

I dispositivi possono usare le misurazioni di eventi per inviare gli eventi del dispositivo importanti o informativi. Una regola eventi si attiva quando l'evento dispositivo selezionato viene segnalato dal dispositivo.

## <a name="create-an-event-rule"></a>Creare una regola eventi

Per creare una regola di evento, il modello di dispositivo deve disporre di misurazione di almeno un evento definito. L'esempio usa un dispositivo distributore automatico refrigerato che segnala l'evento Errore motore ventola. La regola esegue il monitoraggio dell'evento segnalato dal dispositivo e invia un messaggio di posta elettronica ogni volta che l'evento viene segnalato.

1. Usando la pagina **modelli di dispositivo** , passare al modello di dispositivo per cui si sta aggiungendo la regola.

1. Se non è ancora stata creata nessuna regola viene visualizzata la schermata seguente:

    ![Nessuna regola](media/howto-create-event-rules/rules_landing_page1.png)

1. Nella scheda **regole** selezionare **+ nuova regola** per visualizzare i tipi di regole che è possibile creare.

1. Scegliere il riquadro **evento** per creare una regola di monitoraggio degli eventi.

    ![Tipi di regola](media/howto-create-event-rules/rule_types1.png)

1. Immettere un nome che consente di identificare la regola in questo modello di dispositivo.

1. Per abilitare immediatamente la regola per tutti i dispositivi creati da questo modello, attivare la **regola per tutti i dispositivi di questo modello**.

    ![Dettagli regola](media/howto-create-event-rules/rule_detail1.png)

    La regola viene applicata automaticamente a tutti i dispositivi sotto il modello del dispositivo.

### <a name="configure-the-rule-conditions"></a>Configurare le condizioni della regola

Condizione definisce i criteri che vengono monitorato dalla regola.

1. Scegliere **+** accanto a **Condizioni** per aggiungere una nuova condizione.

1. Scegliere l2’evento che si vuole monitorare nell'elenco a discesa Misura. In questo esempio è stato selezionato l'evento **Errore motore ventola**.

   ![Condizione](media/howto-create-event-rules/condition_filled_out1.png)

1. Facoltativamente, è possibile impostare anche **conteggio** come **aggregazione** e specificare la soglia corrispondente.

   - Senza aggregazione, la regola viene attivata per ogni punto dati di evento che soddisfa la condizione. Se, ad esempio, si configura la condizione della regola da attivare quando si verifica un evento di **errore del motore ventola** , la regola viene attivata quasi immediatamente quando il dispositivo segnala tale evento.
   - Se Conteggio viene utilizzato come una funzione di aggregazione, è necessario specificare una **soglia** e un **intervallo di tempo di aggregazione** su cui la condizione deve essere valutata. In questo caso, il numero di eventi viene aggregato e la regola viene attivata solo se il conteggio aggregato degli eventi corrisponde alla soglia.

     Ad esempio, se si vuole generare un avviso quando sono presenti più di tre eventi del dispositivo entro 5 minuti, selezionare l'evento e impostare la funzione di aggregazione "conteggio", l'operatore "maggiore di" e "soglia" 3. Impostare "Aggregazione periodo di tempo" su "5 minuti". La regola viene attivata quando più di tre eventi vengono inviati dal dispositivo entro 5 minuti. La frequenza di valutazione della regola è identica all’**intervallo di tempo di aggregazione**, ovvero, in questo esempio, la regola viene valutata una volta ogni 5 minuti.

     ![Aggiungere una condizione evento](media/howto-create-event-rules/aggregate_condition_filled_out1.png)

     >[!NOTE]
     >È possibile aggiungere più di una misura di evento in **condizione**. Se si specificano più condizioni, la regola viene attivata solo quando tutte le condizioni risultano soddisfatte. Ogni condizione viene unita in modo implicito da una clausola ' AND '. Quando si usa una funzione di aggregazione, è necessario aggregare ogni misura.

### <a name="configure-actions"></a>Configurare le azioni

Questa sezione illustra come configurare le azioni da intraprendere quando la regola viene generata. Le azioni vengono richiamate quando tutte le condizioni specificate nella regola restituiscono il valore true.

1. Scegliere **+** accanto ad **Actions** (Azioni). Viene visualizzato l'elenco delle azioni disponibili.

    ![Aggiungere un'azione](media/howto-create-event-rules/add_action1.png)

1. Scegliere l'azione **Email** (E-mail), immettere un indirizzo di posta elettronica valido nel campo **To** (A) e specificare una nota che viene visualizzata nel corpo del messaggio di posta elettronica quando la regola si attiva.

    > [!NOTE]
    > I messaggi di posta elettronica vengono inviati solo agli utenti che sono stati aggiunti all'applicazione e hanno eseguito l'accesso almeno una volta. Altre informazioni sulla [gestione degli utenti](howto-administer.md) in Azure IoT Central.

   ![Configurare l'azione](media/howto-create-event-rules/configure_action1.png)

1. Per salvare la regola, scegliere **Save** (Salva). La regola diventa operativa entro pochi minuti e avvia il monitoraggio degli eventi inviati all'applicazione. Quando si verifica la condizione specificata nella regola, quest'ultima attiva l'azione e-mail configurata.

È possibile aggiungere altre azioni per la regola, ad esempio Microsoft Flow e i webhook. È possibile aggiungere fino a 5 azioni per ogni regola.

- [Azione di Microsoft Flow](howto-add-microsoft-flow.md) per avviare un flusso di lavoro in Microsoft Flow quando viene attivata una regola 
- [Azione webhook](howto-create-webhooks.md) per inviare una notifica agli altri servizi quando viene attivata una regola

## <a name="parameterize-the-rule"></a>Impostare parametri per la regola

Le azioni possono anche essere configurate usando **Device Property** (Proprietà dispositivo) come parametro. Se un indirizzo di posta elettronica viene archiviato come una proprietà dispositivo, può essere usato quando si definisce l'indirizzo **To** (A).

## <a name="delete-a-rule"></a>Eliminare una regola

Quando una regola non è più necessaria, eliminarla aprendo la regola e scegliendo **Delete** (Elimina). Eliminando la regola, la si rimuove dal modello di dispositivo e da tutti i dispositivi associati.

## <a name="enable-or-disable-a-rule-for-a-device-template"></a>Abilitare o disabilitare una regola per un modello di dispositivo

Passare al dispositivo e scegliere la regola che si vuole abilitare o disabilitare. Selezionare o disattivare il pulsante **Abilita regola per tutti i dispositivi di questo modello** per abilitare o disabilitare la regola per tutti i dispositivi associati al modello di dispositivo.

## <a name="enable-or-disable-a-rule-for-a-device"></a>Abilitare o disabilitare una regola per un dispositivo

Passare al dispositivo e scegliere la regola che si vuole abilitare o disabilitare. Selezionare o disattivare il pulsante **Enable rule for this device** (Abilita regola per questo dispositivo) per abilitare o disabilitare la regola per il dispositivo.

## <a name="next-steps"></a>Passaggi successivi

Ora che si conosce la procedura per creare regole nell'applicazione Azure IoT Central, il prossimo passo suggerito è:

- [Aggiungere azione di Microsoft Flow nelle regole](howto-add-microsoft-flow.md)
- [Aggiungere azione di Webhook nelle regole](howto-create-webhooks.md)
- [Raggruppare più azioni da eseguire da una o più regole](howto-use-action-groups.md)
- [Come gestire i dispositivi](howto-manage-devices.md)
