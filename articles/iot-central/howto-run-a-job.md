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
ms.openlocfilehash: ec7033719316bb186408ea78f6dabac43c383491
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60519361"
---
# <a name="create-and-run-a-job-in-your-azure-iot-central-application"></a>Creare ed eseguire un processo nell'applicazione Azure IoT Central

È possibile usare Microsoft Azure IoT Central per gestire i dispositivi connessi su larga scala che usano i processi. I processi consentono gli aggiornamenti per le proprietà dei dispositivi, impostazioni e i comandi di massa. Questo articolo illustra come iniziare a usare i processi nella propria applicazione.

## <a name="create-and-run-a-job"></a>Creazione ed esecuzione di un processo

Questa sezione descrive come creare ed eseguire un processo. Viene illustrato come aumentare la velocità della ventola per più frigoriferi distributori automatici.

1. Passare a Processi nel riquadro di spostamento.

1. Selezionare **+ nuovo** per creare un nuovo processo.

    ![Creare un nuovo processo](./media/howto-run-a-job/createnewjob.png)

1. Immettere un nome e una descrizione per identificare il processo che si sta creando.

1. Selezionare il set di dispositivi si desidera che il processo da applicare ai. Selezionando il dispositivo è stato impostato, viene visualizzata la parte destra popolare con i dispositivi nel set di dispositivi. Se si seleziona un set di dispositivo danneggiato, non ci sono dispositivi visualizzati e viene visualizzato un messaggio che il set di dispositivi viene interrotto.

1. Successivamente, scegliere il tipo di processo per definire (un'impostazione, una proprietà o un comando). Selezionare **+** accanto al tipo di processo selezionato e aggiungere le operazioni.

    ![Configurazione del processo](./media/howto-run-a-job/configurejob.png)

1. Sul lato destro, scegliere i dispositivi che si desidera eseguire il processo. Selezionando la casella di controllo superiore, vengono selezionati tutti i dispositivi nel set di tutto il dispositivo. Selezionando la casella di controllo quasi **nome**, vengono selezionati tutti i dispositivi nella pagina corrente.

1. Dopo aver selezionato i dispositivi, scegliere **eseguiti** oppure **salvare**. Il processo viene ora visualizzato nel principale **processi** pagina. In questa visualizzazione, è possibile visualizzare il processo attualmente in esecuzione e la cronologia di qualsiasi precedentemente eseguire i processi. Il processo in esecuzione viene sempre visualizzata nella parte superiore dell'elenco. Il processo salvato può essere aperto in qualsiasi momento per continuare a modificare o eseguire.

    ![Visualizzare il processo](./media/howto-run-a-job/viewjob.png)

    > [!NOTE]
    > È possibile visualizzare la cronologia dei processi eseguiti in precedenza fino a 30 giorni.

1. Per ottenere una panoramica del processo, selezionare il processo da visualizzare nell'elenco. In questo argomento contiene i dettagli del processo, dispositivi e i valori di stato di dispositivi. In questa panoramica, è anche possibile selezionare **Scarica i dettagli dei processi** per scaricare un file con estensione CSV dei dettagli del processo, inclusi i dispositivi e i relativi valori di stato. Queste informazioni possono essere utili per la risoluzione.

    ![Visualizzare lo stato dei dispositivi](./media/howto-run-a-job/downloaddetails.png)

### <a name="stop-a-running-job"></a>Arrestare un processo in esecuzione

Per arrestare un processo in esecuzione, selezionarla e scegliere **arrestare** sul pannello. Le modifiche dello stato di processo in modo da riflettere che il processo viene interrotto.

   ![Arrestare il processo](./media/howto-run-a-job/stopjob.png)

### <a name="run-a-stopped-job"></a>Eseguire un processo arrestato

Per eseguire un processo che è inattivo, selezionare il processo arrestato. Scegli **eseguire** sul pannello. Le modifiche dello stato di processo in modo da riflettere il processo è in esecuzione anche in questo caso.

   ![Processo di nuovo in esecuzione](./media/howto-run-a-job/resumejob.png)

## <a name="copy-a-job"></a>Un processo di copia

Per copiare un processo esistente è stato creato, selezionarlo dalla pagina processi principali e selezionare **copia**. Consente di aprire una nuova copia della configurazione del processo da modificare. È possibile salvare o eseguire il nuovo processo. Se sono state apportate modifiche al set di dispositivo selezionato, gli si riportati in questo processo copiato da modificare.

   ![processo di copia](./media/howto-run-a-job/copyjob.png)

## <a name="view-the-job-status"></a>Visualizzare lo stato del processo

Dopo aver creato un processo, il **stato** degli aggiornamenti delle colonne con il messaggio di stato più recente del processo. Nella tabella seguente sono elencati i valori di stato possibili:

| Messaggio di stato       | Significato dello stato                                          |
| -------------------- | ------------------------------------------------------- |
| Completi            | Questo processo è stato eseguito in tutti i dispositivi.              |
| Failed               | Questo processo è non riuscito e non è stato completamente eseguito sui dispositivi.  |
| In sospeso              | Questo processo non è ancora iniziato l'esecuzione nei dispositivi.         |
| In esecuzione              | Questo processo è attualmente in esecuzione sui dispositivi.             |
| Arrestato              | Questo processo è stato arrestato manualmente da un utente.           |

Il messaggio di stato è seguito da una panoramica dei dispositivi nel processo. La tabella seguente elenca i valori di stato dispositivo possibili:

| Messaggio di stato       | Significato dello stato                                                     |
| -------------------- | ------------------------------------------------------------------ |
| Succeeded            | Il numero di dispositivi che il processo è stato eseguito correttamente in.       |
| Failed               | Il numero di dispositivi che il processo non è stato eseguito in.       |

### <a name="view-the-device-status"></a>Visualizzare lo stato del dispositivo

Per visualizzare lo stato del processo e tutti i dispositivi interessati, selezionare il processo. Per scaricare un file con estensione csv che include i dettagli del processo, incluso l'elenco dei dispositivi e i relativi valori di stato, selezionare **Scarica i dettagli dei processi**. Accanto a ogni nome di dispositivo, si verifica uno dei messaggi di stato seguenti:

| Messaggio di stato       | Significato dello stato                                                                |
| -------------------- | ----------------------------------------------------------------------------- |
| Completi            | Il processo è stato eseguito su questo dispositivo.                                     |
| Failed               | Il processo non è stato eseguito su questo dispositivo. Il messaggio di errore Mostra altre informazioni.  |
| In sospeso              | In questo dispositivo non ha ancora eseguito il processo.                                   |

> [!NOTE]
> Se un dispositivo è stato eliminato, non è possibile selezionare il dispositivo e lo visualizza come eliminato con l'ID dispositivo.

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come creare i processi nell'applicazione Azure IoT Central, ecco alcuni passaggi successivi:

- [Usare i set di dispositivi](howto-use-device-sets.md)
- [Gestire i dispositivi](howto-manage-devices.md)
- [Creare una nuova versione di un modello di dispositivo](howto-version-devicetemplate.md)
