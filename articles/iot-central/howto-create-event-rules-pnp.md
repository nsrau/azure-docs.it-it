---
title: Creare e gestire le regole eventi nell'applicazione Azure IoT Central | Microsoft Docs
description: Le regole eventi di Azure IoT Central consentono il monitoraggio dei dispositivi in tempo reale e l'attivazione automatica di azioni quali l'invio di un messaggio di posta elettronica quando la regola si attiva.
author: dominicbetts
ms.author: dobett
ms.date: 07/23/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 1a7fc2b38e8354fb29255bb7f9d6b2c03ed53725
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69879955"
---
# <a name="create-an-event-rule-and-set-up-notifications-in-your-azure-iot-central-application-preview-features"></a>Creare una regola di evento e configurare le notifiche nell'applicazione IoT Central di Azure (funzionalità di anteprima)

*Questo articolo è rivolto a operatori, autori e amministratori.*

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

È possibile usare Azure IoT Central per monitorare in remoto i dispositivi connessi. Le regole di IoT Central di Azure consentono di monitorare i dispositivi quasi in tempo reale e richiamare automaticamente le azioni, ad esempio l'invio di un messaggio di posta elettronica. Con pochi clic è possibile definire la condizione per cui monitorare i dati del dispositivo e configurare l'azione corrispondente. Questo articolo illustra come creare regole che consentono di monitorare gli eventi inviati dal dispositivo.

I dispositivi possono usare le misurazioni di eventi per inviare gli eventi del dispositivo importanti o informativi. Una regola eventi si attiva quando l'evento dispositivo selezionato viene segnalato dal dispositivo.

## <a name="create-an-event-rule"></a>Creare una regola eventi

Per creare una regola di evento, il modello di dispositivo deve disporre di misurazione di almeno un evento definito. L'esempio usa un dispositivo distributore automatico refrigerato che segnala l'evento Errore motore ventola. La regola esegue il monitoraggio dell'evento segnalato dal dispositivo e invia un messaggio di posta elettronica ogni volta che l'evento viene segnalato.

1. Passare alla pagina **regole** .

1. Se non è ancora stata creata nessuna regola viene visualizzata la schermata seguente:

   ![Nessuna regola](media/howto-create-event-rules-pnp/rules-landing-page1.png)

1. Selezionare **+ nuova regola** per visualizzare i tipi di regole che è possibile creare.

1. Scegliere **evento** per creare una regola di monitoraggio degli eventi.

   ![Tipi di regola](media/howto-create-event-rules-pnp/rule-types1.png)

1. Immettere un nome che consenta di identificare la regola e premere INVIO.

1. Selezionare la definizione del dispositivo a cui definire l'ambito della regola nella sezione ambiti. Questa schermata consente anche di filtrare i dispositivi a cui si applica la regola usando il **filtro +** . La regola viene applicata automaticamente a tutti i dispositivi sotto il modello del dispositivo. Per disabilitare la regola, selezionare il pulsante **Disabilita** nell'intestazione.

### <a name="configure-the-rule-conditions"></a>Configurare le condizioni della regola

Condizione definisce i criteri che vengono monitorato dalla regola.

1. Specificare se si desidera **impostare** l'aggregazione su on o off.

   - Senza aggregazione, la regola viene attivata per ogni punto dati di evento che soddisfa la condizione. Se, ad esempio, si configura la condizione della regola da attivare quando si verifica un evento di **errore del motore ventola** , la regola viene attivata quasi immediatamente quando il dispositivo segnala tale evento.
   - Se **count** viene usato come funzione di aggregazione, è necessario specificare un **valore** e un intervallo di **tempo** in base al quale deve essere valutata la condizione. In questo caso, il numero di eventi viene aggregato e la regola viene attivata solo se il conteggio aggregato degli eventi corrisponde al valore.

1. Scegliere l'evento che si vuole monitorare dall'elenco a discesa **misurazione** . In questo esempio è stato selezionato l'evento **Errore motore ventola**.

1. Facoltativamente, è anche possibile impostare **count** come **aggregazione** e fornire il valore in corrispondenza del quale viene attivata la regola.

     Ad esempio, se si desidera ricevere un avviso quando sono presenti più di tre eventi dispositivo entro 5 minuti, selezionare l'evento e impostare la funzione di aggregazione come **conteggio**, operatore come **maggiore di**e **valore** come 3. Imposta intervallo di **tempo** su **5 minuti**. La regola viene attivata quando più di tre eventi vengono inviati dal dispositivo entro 5 minuti. La frequenza di valutazione della regola è uguale all'intervallo di **tempo**, ovvero in questo esempio la regola viene valutata ogni 5 minuti.

 ![Condizione](media/howto-create-event-rules-pnp/aggregate-condition-filled-out1.png)

> [!NOTE]
> È possibile aggiungere più di una misura di evento in **condizione**. Se si specificano più condizioni, la regola viene attivata solo quando tutte le condizioni risultano soddisfatte. Ogni condizione viene unita in modo implicito da una clausola ' AND '. Quando si usa una funzione di aggregazione, è necessario aggregare ogni misura.

### <a name="configure-actions"></a>Configurare le azioni

Questa sezione illustra come configurare le azioni da intraprendere quando la regola viene generata. Le azioni vengono richiamate quando tutte le condizioni specificate nella regola restituiscono il valore true.

1. Fare clic sull' **azione +** nelle sezioni **azione** . Viene visualizzato l'elenco delle azioni disponibili.  

   ![Aggiungere un'azione](media/howto-create-event-rules-pnp/add-action1.png)

1. Scegliere l'azione di **posta elettronica** , immettere un nome visualizzato per l'azione, un indirizzo di posta elettronica valido nel campo **a** e specificare una nota da visualizzare nel corpo del messaggio di posta elettronica quando la regola viene attivata.

   > [!NOTE]
   > I messaggi di posta elettronica vengono inviati solo agli utenti che sono stati aggiunti all'applicazione e hanno eseguito l'accesso almeno una volta. Altre informazioni sulla [gestione degli utenti](howto-administer-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) in Azure IoT Central.

   ![Configurare l'azione](media/howto-create-event-rules-pnp/configure-action1.png)

1. Per salvare l'azione, scegliere **fine**.

1. Per salvare la regola, scegliere **Save** (Salva). La regola diventa operativa entro pochi minuti e avvia il monitoraggio degli eventi inviati all'applicazione. Quando si verifica la condizione specificata nella regola, quest'ultima attiva l'azione di posta elettronica configurata.

## <a name="parameterize-the-rule"></a>Impostare parametri per la regola

Le regole possono derivare determinati valori da **Device Properties** (Proprietà dispositivo) sotto forma di parametri. L'uso di parametri è utile negli scenari in cui le soglie degli eventi variano per dispositivi diversi. Quando si crea la regola, scegliere una proprietà del dispositivo che specifichi la soglia, ad esempio la **soglia ideale massima**, anziché fornire un valore assoluto, ad esempio 3 eventi. Quando la regola viene eseguita, corrisponde agli eventi del dispositivo con il valore impostato nella proprietà del dispositivo.

L'utilizzo di parametri è un modo efficace per ridurre il numero di regole da gestire.

Le azioni possono anche essere configurate usando **Device Property** (Proprietà dispositivo) come parametro. Se un indirizzo di posta elettronica viene archiviato come una proprietà dispositivo, può essere usato quando si definisce l'indirizzo **To** (A).

## <a name="delete-a-rule"></a>Eliminare una regola

Quando una regola non è più necessaria, eliminarla aprendo la regola e scegliendo **Delete** (Elimina). Eliminando la regola, la si rimuove dal modello di dispositivo e da tutti i dispositivi associati.

## <a name="enable-or-disable-a-rule"></a>Abilitare o disabilitare una regola

Scegliere la regola che si desidera abilitare o disabilitare. Attivare o disattivare il pulsante **Abilita** o **Disabilita** nella regola per abilitare o disabilitare la regola per tutti i dispositivi che rientrano nell'ambito della regola.

## <a name="enable-or-disable-a-rule-for-a-device"></a>Abilitare o disabilitare una regola per un dispositivo

Scegliere la regola che si desidera abilitare o disabilitare. Aggiungere un filtro nella sezione **Scopes** per includere o escludere un determinato dispositivo nel modello di dispositivo.

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come creare regole nell'applicazione IoT Central di Azure, il passaggio successivo suggerito consiste nell'apprendere [come gestire i dispositivi](howto-manage-devices-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
