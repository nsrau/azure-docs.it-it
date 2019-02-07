---
title: Creare ed eseguire i processi nell'applicazione Azure IoT Central | Microsoft Docs
description: I processi di Azure IoT Central forniscono funzionalità di gestione in blocco dei dispositivi, ad esempio l'aggiornamento di una proprietà del dispositivo, l'impostazione o l'esecuzione di un comando.
ms.service: iot-central
services: iot-central
author: sarahhubbard
ms.author: sahubbar
ms.date: 02/04/2019
ms.topic: conceptual
manager: peterpr
ms.openlocfilehash: e418ec7d22622c341abd972763d78ac2f0df46d9
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/06/2019
ms.locfileid: "55772340"
---
# <a name="create-and-run-a-job-in-your-azure-iot-central-application"></a>Creare ed eseguire un processo nell'applicazione Azure IoT Central

È possibile usare Microsoft Azure IoT Central per gestire i dispositivi connessi su larga scala che usano i processi. La funzionalità dei processi consente di eseguire aggiornamenti in blocco alle proprietà, alle impostazioni e ai comandi. Questo articolo illustrerà come iniziare a usare i processi nella propria applicazione.

## <a name="create-and-run-a-job"></a>Creazione ed esecuzione di un processo

Questa sezione descrive come creare ed eseguire un processo. Ogni passaggio è accompagnato da un esempio che illustra come eseguire un processo per distributori automatici refrigerati per i quali è necessario aumentare la velocità della ventola.

1. Passare a Processi nel riquadro di spostamento.

1. Fare clic su **+ Nuovo** per iniziare a creare un nuovo processo.

    ![Creare un nuovo processo](./media/howto-run-a-job-experimental/createnewjob.png)

1. Immettere un nome e una descrizione per identificare il processo che si sta creando.

1. Selezionare il set di dispositivi a cui si vuole applicare il processo. Dopo aver selezionato il set di dispositivi, sulla parte destra verranno visualizzati i dispositivi che fanno parte del set di dispositivi selezionato. Se si seleziona un set di dispositivi danneggiato, non verranno visualizzati dispositivi e verrà visualizzato un messaggio che indica che il set di dispositivi è danneggiato.

1. Scegliere quindi il tipo di processo che verrà definito (un'impostazione, una proprietà o un comando). Fare clic su **+** accanto al tipo di processo selezionato e aggiungere le operazioni desiderate.

    ![Configurazione del processo](./media/howto-run-a-job-experimental/configurejob.png)

1. Sul lato destro, scegliere i dispositivi sui quali si desidera eseguire il processo. Facendo clic sulla casella di controllo superiore, vengono selezionati tutti i dispositivi nel set dei dispositivi. Facendo clic sulla casella di controllo accanto a Nome, vengono selezionati tutti i dispositivi nella pagina corrente.

1. Dopo aver selezionato i dispositivi desiderati, scegliere **Esegui**. Il processo verrà ora visualizzato nella pagina **Processi** principale. In questa vista sarà possibile visualizzare il processo attualmente in esecuzione e la cronologia dei processi eseguiti in precedenza. Il processo in esecuzione verrà sempre visualizzato all'inizio dell'elenco.

    ![Visualizzare il processo](./media/howto-run-a-job-experimental/viewjob.png)

    > [!NOTE]
    > Sarà possibile visualizzare la cronologia dei processi eseguiti in precedenza fino a 30 giorni.

1. Per una panoramica del processo, fare clic sul nome del processo che si desidera visualizzare dall'elenco. Questa panoramica contiene i dettagli del processo, i dispositivi e i relativi stati.

    ![Visualizzare lo stato dei dispositivi](./media/howto-run-a-job-experimental/viewdevicestatus.png)

### <a name="stop-a-running-job"></a>Arrestare un processo in esecuzione

Se si desidera arrestare un processo attualmente in esecuzione, fare clic sul nome del processo in esecuzione che si desidera arrestare. Scegliere il pulsante **Arresta** sul pannello. Lo stato del processo verrà modificato in modo da riflettere il fatto che il processo è stato arrestato.

   ![Arrestare il processo](./media/howto-run-a-job-experimental/stopjob.png)

### <a name="run-a-stopped-job"></a>Eseguire un processo arrestato

Se si vuole eseguire un processo attualmente arrestato, fare clic sul nome del processo arrestato da eseguire. Scegliere il pulsante **Esegui** nel pannello. Lo stato del processo verrà modificato in modo da riflettere il fatto che è ora in esecuzione.

   ![Processo di nuovo in esecuzione](./media/howto-run-a-job-experimental/resumejob.png)

## <a name="view-the-job-status"></a>Visualizzare lo stato del processo

Dopo aver creato un processo, la colonna **Stato** verrà aggiornata con il messaggio di stato più recente del processo. I messaggi di stato hanno il seguente significato:

| Messaggio di stato       | Significato dello stato                                          |
| -------------------- | ------------------------------------------------------- |
| Completi            | Questo processo è stato eseguito in tutti i dispositivi.              |
| Operazione non riuscita               | Questo processo è non riuscito e non è stato completamente eseguito sui dispositivi.  |
| In sospeso              | Questo processo non è ancora in esecuzione sui dispositivi.        |
| In esecuzione              | Questo processo è attualmente in esecuzione sui dispositivi.             |
| Arrestato              | Questo processo è stato arrestato manualmente da un utente.           |

Il messaggio di stato è seguito da una panoramica dei dispositivi all'interno del processo. Questi stati del dispositivo hanno il seguente significato:

| Messaggio di stato       | Significato dello stato                                                     |
| -------------------- | ------------------------------------------------------------------ |
| Succeeded            | Il numero di dispositivi in cui il processo è stato eseguito correttamente.  |
| Operazione non riuscita               | Il numero di dispositivi in cui il processo non è stato eseguito correttamente.      |

### <a name="view-the-device-status"></a>Visualizzare lo stato del dispositivo

Per visualizzare lo stato di ogni dispositivo nel processo, fare clic sul nome del processo. Verranno visualizzati i dettagli del processo e tutti i dispositivi che facevano parte del processo specifico. Accanto a ogni nome di dispositivo verrà visualizzato uno dei messaggi di stato seguenti:

| Messaggio di stato       | Significato dello stato                                                                |
| -------------------- | ----------------------------------------------------------------------------- |
| Completi            | Il processo è stato eseguito su questo dispositivo.                                     |
| Operazione non riuscita               | Il processo non è stato eseguito su questo dispositivo. Il messaggio di errore associato mostrerà altre informazioni.  |
| In sospeso              | Il processo non è stato ancora eseguito su questo dispositivo.                                  |

> [!NOTE]
> Se un dispositivo è stato eliminato, non sarà possibile selezionarlo e verrà visualizzato come eliminato con l'ID dispositivo.

## <a name="next-steps"></a>Passaggi successivi

Ora che si conosce la procedura per creare i processi nell'applicazione Azure IoT Central, i prossimi passi suggeriti sono:

- [Usare i set di dispositivi](howto-use-device-sets-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
- [Gestire i dispositivi](howto-manage-devices-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
- [Creare una nuova versione di un modello di dispositivo](howto-version-devicetemplate.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
