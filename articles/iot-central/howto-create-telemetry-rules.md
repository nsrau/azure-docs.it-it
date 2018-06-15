---
title: Creare e gestire regole di telemetria nell'applicazione Azure IoT Central | Microsoft Docs
description: Le regole di telemetria di Azure IoT Central consentono il monitoraggio dei dispositivi in tempo reale e l'attivazione automatica di azioni come l'invio di un messaggio di posta elettronica quando la regola viene attivata.
author: tbhagwat3
ms.author: tanmayb
ms.date: 04/16/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: caca4e9db898b3766995fde8c5eebd4767abd85b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2018
ms.locfileid: "34629814"
---
# <a name="create-a-telemetry-rule-and-set-up-notifications-in-your-azure-iot-central-application"></a>Creare una regola di telemetria e impostare notifiche nell'applicazione Azure IoT Central

È possibile usare Microsoft Azure IoT Central per monitorare in remoto i dispositivi connessi. Le regole di Azure IoT Central consentono il monitoraggio dei dispositivi in tempo reale e l'attivazione automatica di azioni quali l'invio di un messaggio di posta elettronica quando la regola si attiva. Con pochi clic è possibile definire la condizione per il monitoraggio dei dati del dispositivo e configurare l'azione da chiamare. Questo articolo descrive nel dettaglio una regola di telemetria.

Azure IoT Central usa [misurazioni di telemetria](howto-set-up-template.md) per acquisire dati dei dispositivi. Ogni tipo di misura include attributi chiave che definiscono la misura stessa. È possibile creare regole per monitorare ogni tipo di misura del dispositivo e generare avvisi quando la regola viene attivata. Una regola di telemetria viene attivata quando la telemetria del dispositivo selezionato supera una soglia specificata.

## <a name="create-a-telemetry-rule"></a>Creare una regola di telemetria

Questa sezione illustra come creare una regola di telemetria. L'esempio usa un condizionatore d'aria connesso, che invia dati di telemetria relativi alla temperatura e all'umidità. La regola esegue il monitoraggio della temperatura segnalata dal dispositivo e invia un messaggio di posta elettronica quando la temperatura supera un determinato valore, ad esempio 27 gradi.

1. Passare alla pagina dei dettagli per il dispositivo al quale si aggiunge la regola.

1. Se non è ancora stata creata nessuna regola viene visualizzata la schermata seguente:

    ![Nessuna regola](media\howto-create-telemetry-rules\image1.png)

1. Nella scheda **Rules** (Regole) scegliere **+ New Rule** (+ Nuova regola) per visualizzare i tipi di regole che è possibile creare.

    ![Tipi di regola](media\howto-create-telemetry-rules\image2.png)

1. Scegliere il riquadro **Telemetry** (Telemetria) per aprire il modulo che consente di creare la regola.

    ![Regola di telemetria](media\howto-create-telemetry-rules\image3.png)

1. Scegliere un nome che consente di identificare la regola in questo modello di dispositivo.

1. Per attivare immediatamente la regola per tutti i dispositivi creati da questo modello, attivare o disattivare **Enable rule** (Abilita regola).

### <a name="configure-the-rule-condition"></a>Configurare la condizione della regola

Questa sezione illustra come aggiungere una condizione per monitorare la telemetria della temperatura.

1. Scegliere **+** accanto a **Condition** (Condizione).

1. Scegliere il tipo di telemetria **Temperature** (Temperatura) nell'elenco a discesa. Quindi scegliere l'operatore e specificare un valore soglia. È possibile aggiungere più condizioni di telemetria. Se si specificano più condizioni, la regola viene attivata solo quando tutte le condizioni risultano soddisfatte.

    ![Aggiungere una condizione di telemetria](media\howto-create-telemetry-rules\image4.png)

    > [!NOTE]
    > Quando si definisce una condizione della regola di telemetria, selezionare almeno una misura di telemetria.

### <a name="configure-the-action"></a>Configurare l'azione

Questa sezione descrive come specificare il comportamento della regola mediante l'aggiunta di un'azione quando la condizione raggiunge la soglia prevista.

1. Scegliere **+** accanto ad **Actions** (Azioni). Viene visualizzato l'elenco delle azioni disponibili. L'unica azione supportata per l'anteprima pubblica è **Email** (E-mail).

    ![Aggiungere un'azione](media\howto-create-telemetry-rules\image5.png)

1. Scegliere l'azione **Email** (E-mail), immettere un indirizzo di posta elettronica valido nel campo **To** (A) e specificare una nota che viene visualizzata nel corpo del messaggio di posta elettronica quando la regola si attiva.

    > [!NOTE]
    > I messaggi di posta elettronica vengono inviati solo agli utenti che sono stati aggiunti all'applicazione e hanno eseguito l'accesso almeno una volta. Altre informazioni sulla [gestione degli utenti](howto-administer.md) in Azure IoT Central.

   ![Configurare l'azione](media\howto-create-telemetry-rules\image6.png)

1. Per salvare la regola, scegliere **Save** (Salva). La regola diventa attiva entro pochi minuti e avvia il monitoraggio dei dati di telemetria inviati all'applicazione. Quando si verifica la condizione specificata nella regola, questa attiva l'azione di posta elettronica configurata.

## <a name="parameterize-the-rule"></a>Impostare parametri per la regola

Le regole possono derivare determinati valori da **Device Properties** (Proprietà dispositivo) sotto forma di parametri. L'uso dei parametri è pratico negli scenari in cui le soglie di telemetria variano a seconda del dispositivo. Quando si crea la regola, invece di specificare un valore assoluto, ad esempio 27 gradi, scegliere una proprietà del dispositivo che specifica la soglia, ad esempio **Maximum Ideal Threshold** (Soglia massima ideale). Quando la regola viene eseguita, rileva la corrispondenza tra la telemetria del dispositivo e il valore specificato nella proprietà dispositivo.

L'uso dei parametri è un metodo efficace per ridurre il numero di regole da gestire per ogni modello di dispositivo.

Le azioni possono essere configurate anche usando **Device Property** (Proprietà dispositivo) come parametro. Se un indirizzo di posta elettronica viene archiviato come una proprietà dispositivo, può essere usato quando si definisce l'indirizzo **To** (A).

## <a name="delete-a-rule"></a>Eliminare una regola

Quando una regola non è più necessaria, eliminarla aprendo la regola e scegliendo **Delete** (Elimina). Eliminando la regola, la si rimuove dal modello di dispositivo e da tutti i dispositivi associati.

## <a name="enable-or-disable-a-rule-for-a-device-template"></a>Abilitare o disabilitare una regola per un modello di dispositivo

Passare al dispositivo e scegliere la regola che si vuole abilitare o disabilitare. Selezionare o disattivare il pulsante **Enable rule for all devices of this template** (Abilita regola per tutti i dispositivi di questo modello) nella regola per abilitare o disabilitare la regola per tutti i dispositivi associati al modello di dispositivo.

## <a name="enable-or-disable-a-rule-for-a-device"></a>Abilitare o disabilitare una regola per un dispositivo

Passare al dispositivo e scegliere la regola che si vuole abilitare o disabilitare. Selezionare o disattivare il pulsante **Enable rule for this device** (Abilita regola per questo dispositivo) per abilitare o disabilitare la regola per il dispositivo.

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come modificare le regole nell'applicazione Azure IoT Central, il prossimo passo suggerito è:

> [!div class="nextstepaction"]
> [Come gestire i dispositivi](howto-manage-devices.md).