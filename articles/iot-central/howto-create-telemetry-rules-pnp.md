---
title: Creare e gestire regole di telemetria nell'applicazione Azure IoT Central | Microsoft Docs
description: Le regole di telemetria di Azure IoT Central consentono il monitoraggio dei dispositivi in tempo reale e l'attivazione automatica di azioni come l'invio di un messaggio di posta elettronica quando la regola viene attivata.
author: ankitgupta
ms.author: ankitgup
ms.date: 06/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: d6deecf558105701be591c1f08923eca2c1e3bbd
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69879942"
---
# <a name="create-a-telemetry-rule-and-set-up-notifications-in-your-azure-iot-central-application-preview-features"></a>Creare una regola di telemetria e configurare le notifiche nell'applicazione IoT Central di Azure (funzionalità di anteprima)

*Questo articolo è rivolto a operatori, autori e amministratori.*

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

È possibile usare Azure IoT Central per monitorare in remoto i dispositivi connessi. Le regole di Azure IoT Central consentono il monitoraggio dei dispositivi in tempo reale e l'attivazione automatica di azioni quali l'invio di un messaggio di posta elettronica o l'avvio di Microsoft Flow. Con pochi clic è possibile definire la condizione per cui monitorare i dati del dispositivo e configurare l'azione corrispondente. Questo articolo illustra come creare regole che consentono di monitorare i dati di telemetria inviati dal dispositivo.

I dispositivi possono usare unità di misura di dati di telemetria per l'invio di dati numerici dal dispositivo. Una regola di telemetria viene attivata quando la telemetria del dispositivo selezionato supera una soglia specificata.

## <a name="create-a-telemetry-rule"></a>Creare una regola di telemetria

Per creare una regola di telemetria, è necessario definire almeno una misura di telemetria per la definizione del dispositivo. L'esempio usa un distributore automatico refrigerato, che invia dati di telemetria relativi alla temperatura e all'umidità. La regola esegue il monitoraggio della temperatura segnalata dal dispositivo e invia un messaggio di posta elettronica quando la temperatura supera un determinato valore, ad esempio 27 gradi.

1. Passare alla pagina **regole** .

1. Se non sono ancora state create regole, viene visualizzata la schermata seguente:

    ![Nessuna regola](media/howto-create-telemetry-rules-pnp/rules-landing-page1.png)

1. Selezionare **+ nuova regola** per visualizzare i tipi di regole che è possibile creare.

1. Selezionare telemetria per creare una regola per monitorare i dati di telemetria del dispositivo.

    ![Tipi di regola](media/howto-create-telemetry-rules-pnp/rule-types1.png)


1. Immettere un nome che consenta di identificare la regola e premere INVIO.

1. Selezionare la definizione del dispositivo a cui si desidera definire l'ambito della regola nella sezione Scopes (ambiti). Questa schermata consente anche di filtrare i dispositivi a cui si applica la regola usando il **filtro +** . La regola viene applicata automaticamente a tutti i dispositivi sotto il modello del dispositivo. Per disabilitare la regola, selezionare il pulsante **Disabilita** nell'intestazione.

### <a name="configure-the-rule-conditions"></a>Configurare le condizioni della regola

Condizione definisce i criteri che vengono monitorato dalla regola.

1. Specificare se si desidera **impostare** l'aggregazione su on o off.

      - L'aggregazione è facoltativa. Senza aggregazione, la regola viene attivata per ogni punto dati di telemetria che soddisfa la condizione. Se, ad esempio, la regola è configurata per essere attivata quando la temperatura è superiore a 80, la regola viene attivata quasi immediatamente quando il dispositivo segnala la temperatura > 80.
      - Se viene scelta una funzione di aggregazione, ad esempio Average, min, Max e count, l'utente deve fornire un intervallo di **tempo** in base al quale deve essere valutata la condizione. Ad esempio, se si imposta il periodo su "5 minuti" e la regola cerca una temperatura media superiore a 80, la regola viene attivata quando la temperatura media è superiore a 80 per almeno 5 minuti. La frequenza di valutazione della regola è uguale all'intervallo di **tempo**, ovvero in questo esempio la regola viene valutata ogni 5 minuti.

1. Selezionare la telemetria da monitorare nell'elenco a discesa **Misura**.

1. Scegliere quindi un **operatore** e specificare un **valore**.

     ![Condizione](media/howto-create-telemetry-rules-pnp/aggregate-condition-filled-out1.png)


>[!NOTE]
>È possibile aggiungere più di una misura di telemetria selezionando **+ Condition**. Se si specificano più condizioni, la regola viene attivata solo quando tutte le condizioni risultano soddisfatte. Ogni condizione viene unita in modo implicito da una clausola ' AND '. Quando si usa una funzione di aggregazione, è necessario aggregare ogni misura.

### <a name="configure-actions"></a>Configurare le azioni

Questa sezione illustra come configurare le azioni da intraprendere quando la regola viene generata. Le azioni vengono richiamate quando tutte le condizioni specificate nella regola restituiscono il valore true.

1. Fare clic sull' **azione +** nelle sezioni **azione** . Viene visualizzato l'elenco delle azioni disponibili.  

    ![Aggiungere un'azione](media/howto-create-telemetry-rules-pnp/add-action1.png)


1. Scegliere l'azione di **posta elettronica** , immettere un nome visualizzato per l'azione, un indirizzo di posta elettronica valido nel campo **a** e specificare una nota da visualizzare nel corpo del messaggio di posta elettronica quando la regola viene attivata.

    > [!NOTE]
    > I messaggi di posta elettronica vengono inviati solo agli utenti che sono stati aggiunti all'applicazione e hanno eseguito l'accesso almeno una volta. Altre informazioni sulla [gestione degli utenti](howto-administer-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) in Azure IoT Central.

   ![Configurare l'azione](media/howto-create-telemetry-rules-pnp/configure-action1.png)


1. Per salvare l'azione, scegliere **fine**.

1. Per salvare la regola, scegliere **Save** (Salva). La regola diventa attiva entro pochi minuti e avvia il monitoraggio dei dati di telemetria inviati all'applicazione. Quando si verifica la condizione specificata nella regola, quest'ultima attiva l'azione di posta elettronica configurata.

## <a name="parameterize-the-rule"></a>Impostare parametri per la regola

Le regole possono derivare determinati valori da **Device Properties** (Proprietà dispositivo) sotto forma di parametri. L'uso dei parametri è pratico negli scenari in cui le soglie di telemetria variano a seconda del dispositivo. Durante la creazione della regola, scegliere una proprietà del dispositivo che specifichi la soglia, ad esempio la **soglia ideale massima**, anziché fornire un valore assoluto, ad esempio 80 gradi. Quando la regola viene eseguita, rileva la corrispondenza tra la telemetria del dispositivo e il valore specificato nella proprietà dispositivo.

L'utilizzo di parametri è un modo efficace per ridurre il numero di regole da gestire.

Le azioni possono anche essere configurate usando **Device Property** (Proprietà dispositivo) come parametro. Se un indirizzo di posta elettronica viene archiviato come una proprietà, può essere usato quando si definisce l'indirizzo **A**.

## <a name="delete-a-rule"></a>Eliminare una regola

Quando una regola non è più necessaria, eliminarla aprendo la regola e scegliendo **Delete** (Elimina). Eliminando la regola, la si rimuove dal modello di dispositivo e da tutti i dispositivi associati.

## <a name="enable-or-disable-a-rule"></a>Abilitare o disabilitare una regola

Scegliere la regola che si desidera abilitare o disabilitare. Attivare o disattivare il pulsante **Abilita** o **Disabilita** nella regola per abilitare o disabilitare la regola per tutti i dispositivi che hanno come ambito la regola.

## <a name="enable-or-disable-a-rule-for-a-device"></a>Abilitare o disabilitare una regola per un dispositivo

Scegliere la regola che si desidera abilitare o disabilitare. Aggiungere un filtro nella sezione **Scopes** per includere o escludere un determinato dispositivo nel modello di dispositivo.

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come creare regole nell'applicazione IoT Central di Azure, il passaggio successivo suggerito consiste nell'apprendere [come gestire i dispositivi](howto-manage-devices-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
