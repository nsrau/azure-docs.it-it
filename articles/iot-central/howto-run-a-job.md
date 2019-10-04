---
title: Creare ed eseguire i processi nell'applicazione Azure IoT Central | Microsoft Docs
description: I processi di Azure IoT Central forniscono funzionalità di gestione in blocco dei dispositivi, ad esempio l'aggiornamento di una proprietà del dispositivo, l'impostazione o l'esecuzione di un comando.
ms.service: iot-central
services: iot-central
author: sarahhubbard
ms.author: sahubbar
ms.date: 07/08/2019
ms.topic: conceptual
manager: peterpr
ms.openlocfilehash: 6c18a244ceae2ccd9a536abeb6bc2d85760bb0a6
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69875918"
---
# <a name="create-and-run-a-job-in-your-azure-iot-central-application"></a>Creare ed eseguire un processo nell'applicazione IoT Central di Azure

È possibile usare Microsoft Azure IoT Central per gestire i dispositivi connessi su larga scala che usano i processi. I processi consentono di eseguire aggiornamenti in blocco per le proprietà, le impostazioni e i comandi del dispositivo. Questo articolo illustra come iniziare a usare i processi nella propria applicazione.

## <a name="create-and-run-a-job"></a>Creazione ed esecuzione di un processo

Questa sezione descrive come creare ed eseguire un processo. Viene illustrato come aumentare la velocità della ventola per più computer distributori frigoriferi.

1. Passare a Processi nel riquadro di spostamento.

1. Selezionare **+ nuovo** per creare un nuovo processo.

    ![Crea nuovo processo](./media/howto-run-a-job/createnewjob.png)

1. Immettere un nome e una descrizione per identificare il processo che si sta creando.

1. Selezionare il set di dispositivi a cui si vuole applicare il processo. Dopo aver selezionato il set di dispositivi, viene visualizzato il lato destro popolare con i dispositivi nel set di dispositivi. Se si seleziona un set di dispositivi interrotti, non viene visualizzato alcun dispositivo e viene visualizzato un messaggio indicante che il set di dispositivi è danneggiato.

1. Scegliere quindi il tipo di processo da definire (impostazione, proprietà o comando). Selezionare **+** accanto al tipo di processo selezionato e aggiungere le operazioni.

    ![Configurazione del processo](./media/howto-run-a-job/configurejob.png)

1. Sul lato destro scegliere i dispositivi su cui si vuole eseguire il processo. Selezionando la casella di controllo top, tutti i dispositivi vengono selezionati nell'intero set di dispositivi. Selezionando la casella di controllo accanto a **nome**, verranno selezionati tutti i dispositivi nella pagina corrente.

1. Dopo aver selezionato i dispositivi, scegliere **Esegui** o **Salva**. Il processo verrà ora visualizzato nella pagina dei **processi** principali. In questa visualizzazione è possibile visualizzare il processo attualmente in esecuzione e la cronologia di tutti i processi eseguiti in precedenza. Il processo in esecuzione viene sempre visualizzato nella parte superiore dell'elenco. È possibile aprire nuovamente il processo salvato in qualsiasi momento per continuare la modifica o l'esecuzione.

    ![Visualizza processo](./media/howto-run-a-job/viewjob.png)

    > [!NOTE]
    > È possibile visualizzare la cronologia dei processi eseguiti in precedenza per un massimo di 30 giorni.

1. Per ottenere una panoramica del processo, selezionare il processo da visualizzare nell'elenco. Questa panoramica contiene i dettagli del processo, i dispositivi e i valori dello stato del dispositivo. Da questa panoramica è inoltre possibile selezionare **Scarica dettagli processo** per scaricare un file con estensione CSV dei dettagli del processo, inclusi i dispositivi e i relativi valori di stato. Queste informazioni possono essere utili per la risoluzione dei problemi.

    ![Visualizzare lo stato dei dispositivi](./media/howto-run-a-job/downloaddetails.png)

### <a name="stop-a-running-job"></a>Arrestare un processo in esecuzione

Per arrestare un processo in esecuzione, selezionarlo e scegliere **Arresta** sul pannello. Lo stato del processo cambia per riflettere che il processo è stato arrestato.

   ![Arrestare il processo](./media/howto-run-a-job/stopjob.png)

### <a name="run-a-stopped-job"></a>Eseguire un processo arrestato

Per eseguire un processo attualmente arrestato, selezionare il processo interrotto. Scegliere **Esegui** sul pannello. Lo stato del processo cambia per riflettere che il processo è ora in esecuzione di nuovo.

   ![Processo di nuovo in esecuzione](./media/howto-run-a-job/resumejob.png)

## <a name="copy-a-job"></a>Copiare un processo

Per copiare un processo esistente creato, selezionarlo nella pagina principale processi e selezionare **copia**. Verrà aperta una nuova copia della configurazione del processo da modificare. È possibile salvare o eseguire il nuovo processo. Se sono state apportate modifiche al set di dispositivi selezionato, queste vengono riflesse in questo processo copiato per poter essere modificate.

   ![Copia processo](./media/howto-run-a-job/copyjob.png)

## <a name="view-the-job-status"></a>Visualizzare lo stato del processo

Dopo la creazione di un processo, la colonna **stato** viene aggiornata con l'ultimo messaggio di stato del processo. Nella tabella seguente sono elencati i valori di stato possibili:

| Messaggio di stato       | Significato dello stato                                          |
| -------------------- | ------------------------------------------------------- |
| Operazione completata            | Questo processo è stato eseguito in tutti i dispositivi.              |
| Failed               | Questo processo è non riuscito e non è stato completamente eseguito sui dispositivi.  |
| In sospeso              | Questo processo non è ancora iniziato a eseguire nei dispositivi.         |
| In esecuzione              | Questo processo è attualmente in esecuzione sui dispositivi.             |
| Arrestato              | Questo processo è stato arrestato manualmente da un utente.           |

Il messaggio di stato è seguito da una panoramica dei dispositivi nel processo. La tabella seguente elenca i possibili valori di stato del dispositivo:

| Messaggio di stato       | Significato dello stato                                                     |
| -------------------- | ------------------------------------------------------------------ |
| Riuscita            | Il numero di dispositivi in cui il processo è stato eseguito correttamente.       |
| Failed               | Il numero di dispositivi in cui l'esecuzione del processo non è riuscita.       |

### <a name="view-the-device-status"></a>Visualizzare lo stato del dispositivo

Per visualizzare lo stato del processo e di tutti i dispositivi interessati, selezionare il processo. Per scaricare un file con estensione CSV che include i dettagli del processo, incluso l'elenco dei dispositivi e i relativi valori di stato, selezionare **Scarica dettagli processo**. Accanto a ogni nome dispositivo viene visualizzato uno dei seguenti messaggi di stato:

| Messaggio di stato       | Significato dello stato                                                                |
| -------------------- | ----------------------------------------------------------------------------- |
| Operazione completata            | Il processo è stato eseguito su questo dispositivo.                                     |
| Failed               | Il processo non è stato eseguito su questo dispositivo. Il messaggio di errore Mostra ulteriori informazioni.  |
| In sospeso              | Il processo non è ancora stato eseguito in questo dispositivo.                                   |

> [!NOTE]
> Se un dispositivo è stato eliminato, non è possibile selezionare il dispositivo e viene visualizzato come eliminato con l'ID del dispositivo.

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come creare processi nell'applicazione IoT Central di Azure, di seguito sono riportati alcuni passaggi successivi:

- [Usare i set di dispositivi](howto-use-device-sets.md)
- [Gestire i dispositivi](howto-manage-devices.md)
- [Creare una nuova versione di un modello di dispositivo](howto-version-device-template.md)
