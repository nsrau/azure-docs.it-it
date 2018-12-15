---
title: Creare e gestire regole di telemetria nell'applicazione Azure IoT Central | Microsoft Docs
description: Le regole di telemetria di Azure IoT Central consentono il monitoraggio dei dispositivi in tempo reale e l'attivazione automatica di azioni come l'invio di un messaggio di posta elettronica quando la regola viene attivata.
author: ankitgupta
ms.author: ankitgup
ms.date: 11/02/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: a5475ad2f487bca90f600406ca9bb8f0925a4988
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2018
ms.locfileid: "52964816"
---
# <a name="create-a-telemetry-rule-and-set-up-notifications-in-your-azure-iot-central-application"></a>Creare una regola di telemetria e impostare notifiche nell'applicazione Azure IoT Central

*Questo articolo è rivolto a operatori, autori e amministratori.*

È possibile usare Azure IoT Central per monitorare in remoto i dispositivi connessi. Le regole di Azure IoT Central consentono il monitoraggio dei dispositivi in tempo reale e l'attivazione automatica di azioni quali l'invio di un messaggio di posta elettronica o l'avvio di Microsoft Flow. Con pochi clic è possibile definire la condizione per cui monitorare i dati del dispositivo e configurare l'azione corrispondente. Questo articolo illustra come creare regole che consentono di monitorare i dati di telemetria inviati dal dispositivo.

I dispositivi possono usare unità di misura di dati di telemetria per l'invio di dati numerici dal dispositivo. Una regola di telemetria viene attivata quando la telemetria del dispositivo selezionato supera una soglia specificata.

## <a name="create-a-telemetry-rule"></a>Creare una regola di telemetria

Per creare una regola di telemetria, il modello di dispositivo deve disporre di misurazione di almeno una telemetria definita. L'esempio usa un distributore automatico refrigerato, che invia dati di telemetria relativi alla temperatura e all'umidità. La regola esegue il monitoraggio della temperatura segnalata dal dispositivo e invia un messaggio di posta elettronica quando la temperatura supera un determinato valore, ad esempio 27 gradi.

1. Usando Device Explorer, passare al modello del dispositivo per cui si aggiunge la regola.

1. Sotto il modello selezionato, fare clic su un dispositivo esistente. 

    >[!TIP] 
    >Se il modello non dispone di tutti i dispositivi, è innanzitutto necessario aggiungere un nuovo dispositivo.

1. Se non è ancora stata creata nessuna regola verrà visualizzata la schermata seguente:

    ![Nessuna regola](media/howto-create-telemetry-rules/Rules_Landing_Page.png)

1. Nella scheda **Regole** fare clic **Modifica modello** e quindi su **+ Nuova regola** per visualizzare i tipi di regole che è possibile creare.

1. Fare clic su **Telemetria** per creare una regola per il monitoraggio della telemetria del dispositivo.

    ![Tipi di regola](media/howto-create-telemetry-rules/Rule_Types.png)

1. Immettere un nome che consente di identificare la regola in questo modello di dispositivo.

1. Per attivare immediatamente la regola per tutti i dispositivi creati per questo modello, attivare o disattivare **Abilita regola** per tutti i dispositivi di questo modello.

   ![Dettagli regola](media/howto-create-telemetry-rules/Rule_Detail.png)
    
    La regola viene applicata automaticamente a tutti i dispositivi sotto il modello del dispositivo.
    

### <a name="configure-the-rule-conditions"></a>Configurare le condizioni della regola

Condizione definisce i criteri che vengono monitorato dalla regola.

1. Fare clic su **+** accanto a **Condizioni** per aggiungere una nuova condizione.

1. Selezionare la telemetria da monitorare nell'elenco a discesa **Misura**.

   ![Condizione](media/howto-create-telemetry-rules/Aggregate_Condition_Filled_Out.png)

1. Scegliere quindi **Aggregazione**, **Operatore** e specificare un valore **Soglia**.
    - L'aggregazione è facoltativa. Senza aggregazione, la regola viene attivata per ogni punto dati di telemetria che soddisfa la condizione. Ad esempio, se la regola è configurata per essere attivata quando la temperatura è superiore a 80, si attiverà quasi immediatamente quando il dispositivo segnala una temperatura > 80.
    - Se viene usata una funzione di aggregazione come Media, Min, Max, Calcolo, è necessario specificare un **intervallo di tempo di aggregazione** su cui la condizione deve essere valutata. Ad esempio, se si imposta il periodo su "5 minuti" e la regola cerca una temperatura media superiore a 80, la regola viene attivata quando la temperatura media è superiore a 80 per almeno 5 minuti. La frequenza di valutazione della regola è identica all'**intervallo di tempo di aggregazione**, ovvero, in questo esempio, la regola viene valutata una volta ogni 5 minuti.

    >[!NOTE]
    >È possibile aggiungere più di una misura di telemetria in **Condizione**. Se si specificano più condizioni, la regola viene attivata solo quando tutte le condizioni risultano soddisfatte. Ogni condizione è unita in modo implicito da una clausola “AND”. Quando si usa una funzione di aggregazione, è necessario aggregare ogni misura.
    
    

### <a name="configure-actions"></a>Configurare le azioni

Questa sezione illustra come configurare le azioni da intraprendere quando la regola viene generata. Le azioni vengono richiamate quando tutte le condizioni specificate nella regola restituiscono il valore true.

1. Scegliere **+** accanto ad **Actions** (Azioni). Viene visualizzato l'elenco delle azioni disponibili.  

    ![Aggiungere un'azione](media/howto-create-telemetry-rules/Add_Action.png)

1. Scegliere l'azione **Email** (E-mail), immettere un indirizzo di posta elettronica valido nel campo **To** (A) e specificare una nota che viene visualizzata nel corpo del messaggio di posta elettronica quando la regola si attiva.

    > [!NOTE]
    > I messaggi di posta elettronica vengono inviati solo agli utenti che sono stati aggiunti all'applicazione e hanno eseguito l'accesso almeno una volta. Altre informazioni sulla [gestione degli utenti](howto-administer.md) in Azure IoT Central.

   ![Configurare l'azione](media/howto-create-telemetry-rules/Configure_Action.png)

1. Per salvare la regola, scegliere **Save** (Salva). La regola diventa attiva entro pochi minuti e avvia il monitoraggio dei dati di telemetria inviati all'applicazione. Quando si verifica la condizione specificata nella regola, quest'ultima attiva l'azione di posta elettronica configurata.

1. Scegliere **Fine** per uscire dalla modalità **Modifica modello**.

È possibile aggiungere altre azioni per la regola, ad esempio Microsoft Flow e i webhook. È possibile aggiungere fino a 5 azioni per ogni regola.

- [Azione di Microsoft Flow](howto-add-microsoft-flow.md) per avviare un flusso di lavoro in Microsoft Flow quando viene attivata una regola 
- [Azione webhook](howto-create-webhooks.md) per inviare una notifica agli altri servizi quando viene attivata una regola

## <a name="parameterize-the-rule"></a>Impostare parametri per la regola

Le regole possono derivare determinati valori da **Device Properties** (Proprietà dispositivo) sotto forma di parametri. L'uso dei parametri è pratico negli scenari in cui le soglie di telemetria variano a seconda del dispositivo. Quando si crea la regola, invece di specificare un valore assoluto, ad esempio 27 gradi, scegliere una proprietà del dispositivo che specifica la soglia, ad esempio **Maximum Ideal Threshold** (Soglia massima ideale). Quando la regola viene eseguita, rileva la corrispondenza tra la telemetria del dispositivo e il valore specificato nella proprietà dispositivo.

L'uso dei parametri è un metodo efficace per ridurre il numero di regole da gestire per ogni modello di dispositivo.

Le azioni possono anche essere configurate usando **Device Property** (Proprietà dispositivo) come parametro. Se un indirizzo di posta elettronica viene archiviato come una proprietà, può essere usato quando si definisce l'indirizzo **A**.

## <a name="delete-a-rule"></a>Eliminare una regola

Quando una regola non è più necessaria, eliminarla aprendo la regola e scegliendo **Delete** (Elimina). Eliminando la regola, la si rimuove dal modello di dispositivo e da tutti i dispositivi associati.

## <a name="enable-or-disable-a-rule-for-a-device-template"></a>Abilitare o disabilitare una regola per un modello di dispositivo

Passare al dispositivo e scegliere la regola che si vuole abilitare o disabilitare. Selezionare o disattivare il pulsante **Abilita regola per tutti i dispositivi di questo modello** nella regola per abilitare o disabilitare la regola per tutti i dispositivi associati al modello di dispositivo.

## <a name="enable-or-disable-a-rule-for-a-device"></a>Abilitare o disabilitare una regola per un dispositivo

Passare al dispositivo e scegliere la regola che si vuole abilitare o disabilitare. Selezionare o disattivare il pulsante **Enable rule for this device** (Abilita regola per questo dispositivo) per abilitare o disabilitare la regola per il dispositivo.

## <a name="next-steps"></a>Passaggi successivi

Ora che si conosce la procedura per creare regole nell'applicazione Azure IoT Central, il prossimo passo suggerito è:

- [Aggiungere azione di Microsoft Flow nelle regole](howto-add-microsoft-flow.md)
- [Aggiungere azione di Webhook nelle regole](howto-create-webhooks.md)
- [Come gestire i dispositivi](howto-manage-devices.md)
