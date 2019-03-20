---
title: Creare ed eseguire i processi nell'applicazione Azure IoT Central | Microsoft Docs
description: I processi di Azure IoT Central forniscono funzionalità di gestione in blocco dei dispositivi, ad esempio l'aggiornamento di una proprietà del dispositivo, l'impostazione o l'esecuzione di un comando.
ms.service: iot-central
services: iot-central
author: sarahhubbard
ms.author: sahubbar
ms.date: 03/18/2019
ms.topic: conceptual
manager: peterpr
ms.openlocfilehash: bb5eb2904ae969c3ffcd4ee8e365a51853add2fd
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58182235"
---
# <a name="create-and-run-a-job-in-your-azure-iot-central-application"></a>Creare ed eseguire un processo nell'applicazione Azure IoT Central

È possibile usare Microsoft Azure IoT Central per gestire i dispositivi connessi su larga scala che usano i processi. La funzionalità dei processi consente di eseguire aggiornamenti in blocco alle proprietà, alle impostazioni e ai comandi. Questo articolo illustra come iniziare a usare i processi nella propria applicazione.

## <a name="create-and-run-a-job"></a>Creazione ed esecuzione di un processo

Questa sezione descrive come creare ed eseguire un processo. Ogni passaggio è accompagnato da un esempio che illustra come eseguire un processo per distributori automatici refrigerati per i quali è necessario aumentare la velocità della ventola.

1. Passare a Processi nel riquadro di spostamento.

1. Selezionare **+ nuovo** per poter iniziare a creare un nuovo processo.

    ![Creare un nuovo processo](./media/howto-run-a-job/createnewjob.png)

1. Immettere un nome e una descrizione per identificare il processo che si sta creando.

1. Selezionare il set di dispositivi a cui si vuole applicare il processo. Selezionando il dispositivo è stato impostato, viene visualizzata la parte destra popolare con i dispositivi all'interno del set di dispositivo selezionato. Se si seleziona un set di dispositivo danneggiato, non ci sono dispositivi visualizzati e viene visualizzato un messaggio che spiega che il set di dispositivi viene interrotto.

1. Successivamente, scegliere il tipo di processo per definire (un'impostazione, una proprietà o un comando). Selezionare **+** accanto al tipo di processo selezionato e aggiungere le operazioni desiderate.

    ![Configurazione del processo](./media/howto-run-a-job/configurejob.png)

1. Sul lato destro, scegliere i dispositivi sui quali si desidera eseguire il processo. Selezionando la casella di controllo superiore, vengono selezionati tutti i dispositivi nel set di tutto il dispositivo. Selezionando la casella di controllo quasi **nome**, vengono selezionati tutti i dispositivi nella pagina corrente.

1. Dopo aver selezionato i dispositivi, scegliere **eseguiti** oppure **salvare**. Il processo viene ora visualizzato nel principale **processi** pagina. In questa visualizzazione, è possibile visualizzare il processo attualmente in esecuzione e la cronologia di qualsiasi precedentemente eseguire i processi. Il processo in esecuzione viene sempre visualizzata nella parte superiore dell'elenco. Il processo salvato può essere aperto in qualsiasi momento per continuare a modificare o eseguire.

    ![Visualizzare il processo](./media/howto-run-a-job/viewjob.png)

    > [!NOTE]
    > È possibile visualizzare la cronologia dei processi eseguiti in precedenza fino a 30 giorni.

1. Per ottenere una panoramica del processo, selezionare il nome del processo che si desidera visualizzare dall'elenco. In questo argomento contiene i dettagli del processo, dispositivi e i valori di stato di dispositivi. In questa panoramica, è anche possibile selezionare **Scarica i dettagli dei processi** per scaricare un file con estensione CSV dei dettagli del processo, inclusi i dispositivi e i relativi valori di stato. Queste informazioni possono essere utili per la risoluzione.

    ![Visualizzare lo stato dei dispositivi](./media/howto-run-a-job/downloaddetails.png)

### <a name="stop-a-running-job"></a>Arrestare un processo in esecuzione

Se si vuole arrestare un processo attualmente in esecuzione, selezionare il nome del processo in esecuzione che si desidera arrestare. Scegliere il pulsante **Arresta** sul pannello. Lo stato del processo verrà modificato in modo da riflettere il fatto che il processo è stato arrestato.

   ![Arrestare il processo](./media/howto-run-a-job/stopjob.png)

### <a name="run-a-stopped-job"></a>Eseguire un processo arrestato

Per eseguire un processo che è inattivo, selezionare il nome del processo di arresto che si desidera eseguire. Scegliere il pulsante **Esegui** nel pannello. Le modifiche dello stato di processo in modo da riflettere che il processo è in esecuzione anche in questo caso.

   ![Processo di nuovo in esecuzione](./media/howto-run-a-job/resumejob.png)

## <a name="copy-a-job"></a>Un processo di copia

Per copiare un processo esistente è stato creato, selezionarlo dalla pagina processi principali e selezionare **copia**. Verrà visualizzata una nuova copia della configurazione del processo da modificare. È possibile salvare o eseguire il nuovo processo. Se sono state apportate modifiche al set di dispositivo selezionato, sono indicati in questo processo copiato da modificare.

   ![processo di copia](./media/howto-run-a-job/copyjob.png)

## <a name="view-the-job-status"></a>Visualizzare lo stato del processo

Dopo aver creato un processo, il **stato** degli aggiornamenti delle colonne con il messaggio di stato più recente del processo. I messaggi di stato hanno il seguente significato:

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

Per visualizzare lo stato di tutti i dispositivi nel processo, selezionare il nome del processo. Vengono visualizzati i dettagli del processo e tutti i dispositivi che facevano una parte di esso. È possibile selezionare **Scarica i dettagli dei processi** per scaricare un file con estensione csv che include i dettagli del processo, i dispositivi e i relativi valori di stato. Accanto a ogni nome di dispositivo, si verifica uno dei messaggi di stato seguenti:

| Messaggio di stato       | Significato dello stato                                                                |
| -------------------- | ----------------------------------------------------------------------------- |
| Completi            | Il processo è stato eseguito su questo dispositivo.                                     |
| Operazione non riuscita               | Il processo non è stato eseguito su questo dispositivo. Il messaggio di errore associato Mostra altre informazioni.  |
| In sospeso              | Il processo non è stato ancora eseguito su questo dispositivo.                                  |

> [!NOTE]
> Se un dispositivo è stato eliminato, non è possibile selezionare il dispositivo e lo visualizza come eliminato con l'ID dispositivo.

## <a name="next-steps"></a>Passaggi successivi

Ora che si conosce la procedura per creare i processi nell'applicazione Azure IoT Central, i prossimi passi suggeriti sono:

- [Usare i set di dispositivi](howto-use-device-sets.md)
- [Gestire i dispositivi](howto-manage-devices.md)
- [Creare una nuova versione di un modello di dispositivo](howto-version-devicetemplate.md)
