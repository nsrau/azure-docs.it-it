---
title: Creare ed eseguire i processi nell'applicazione Azure IoT Central | Microsoft Docs
description: I processi di Azure IoT Central consentono funzionalità di gestione dei dispositivi in blocco, ad esempio l'aggiornamento delle proprietà o l'esecuzione di un comando.
ms.service: iot-central
services: iot-central
author: sarahhubbard
ms.author: sahubbar
ms.date: 03/03/2020
ms.topic: how-to
manager: peterpr
ms.openlocfilehash: c9d5a0daa364b09e45699e898511c28d4b4d92ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80157756"
---
# <a name="create-and-run-a-job-in-your-azure-iot-central-application"></a>Creare ed eseguire un processo nell'applicazione Azure IoT CentralCreate and run a job in your Azure IoT Central application

È possibile usare Microsoft Azure IoT Central per gestire i dispositivi connessi su larga scala che usano i processi. I processi consentono di eseguire aggiornamenti in blocco delle proprietà del dispositivo ed eseguire comandi. In questo articolo viene illustrato come iniziare a utilizzare i processi nella propria applicazione.

## <a name="create-and-run-a-job"></a>Creazione ed esecuzione di un processo

Questa sezione descrive come creare ed eseguire un processo. Viene illustrato come impostare la soglia della luce per un gruppo di dispositivi gateway logistici.

1. Passare a **Processi** dal riquadro sinistro.

2. Per creare un nuovo lavoro, selezionare **: Nuovo:**

    ![Creare un nuovo processo](./media/howto-run-a-job/createnewjob.png)

3. Immettere un nome e una descrizione per identificare il processo che si sta creando.

4. Selezionare il gruppo di dispositivi di destinazione a cui si desidera applicare il processo. Nella sezione **Riepilogo** è possibile visualizzare il numero di dispositivi a cui si applica la configurazione del processo.

5. Scegliere quindi **Proprietà cloud**, **Proprietà** o **Comando** come tipo di processo da configurare. Per impostare una configurazione del processo **Proprietà,** selezionare una proprietà e impostarne il nuovo valore. Per impostare un **comando,** scegliere il comando da eseguire. Un processo di proprietà può impostare più proprietà:A property job can set multiple properties:

    ![Configurazione del processo](./media/howto-run-a-job/configurejob.png)

6. Dopo aver creato il processo, scegliere **Esegui** o **Salva**. Il lavoro viene ora visualizzato nella pagina **Lavori** principale. In questa pagina è possibile visualizzare il processo attualmente in esecuzione e la cronologia di tutti i processi eseguiti o salvati in precedenza. Il lavoro salvato può essere aperto di nuovo in qualsiasi momento per continuare a modificarlo o per eseguirlo:

    ![Visualizzare il processo](./media/howto-run-a-job/viewjob.png)

    > [!NOTE]
    > È possibile visualizzare fino a 30 giorni di cronologia per i processi eseguiti in precedenza.

7. Per ottenere una panoramica del processo, selezionare il processo da visualizzare dall'elenco. Questa panoramica contiene i dettagli del processo, i dispositivi e i valori di stato del dispositivo. Da questa panoramica, puoi anche selezionare **Scarica dettagli lavoro** per scaricare un file CSV dei dettagli del tuo lavoro, inclusi i dispositivi e i relativi valori di stato. Queste informazioni possono essere utili per la risoluzione dei problemi:This information can be useful for troubleshooting:

    ![Visualizzare lo stato dei dispositivi](./media/howto-run-a-job/downloaddetails.png)

### <a name="manage-a-job"></a>Gestire un processo

Per arrestare uno dei processi in esecuzione, aprirlo e selezionare **Arresta**. Lo stato del processo cambia per riflettere il processo viene interrotto. La sezione **Riepilogo** mostra quali dispositivi sono stati completati, non riusciti o sono ancora in sospeso.

Per eseguire un processo attualmente arrestato, selezionarlo e quindi scegliere **Esegui**. Lo stato del processo cambia per riflettere che il processo è ora in esecuzione di nuovo. La sezione **Riepilogo** continua ad aggiornarsi con lo stato di avanzamento più recente.

![Gestisci lavoro](./media/howto-run-a-job/managejob.png)

## <a name="copy-a-job"></a>Copiare un processo

Per copiare uno dei processi esistenti, selezionarlo nella pagina **Processi** e selezionare **Copia**. Viene aperta una copia della configurazione del processo e **copia** viene aggiunta al nome del processo. È possibile salvare o eseguire il nuovo processo:

![Copia processo](./media/howto-run-a-job/copyjob.png)

## <a name="view-the-job-status"></a>Visualizzare lo stato del processo

Dopo la creazione di un processo, la colonna **Stato** viene aggiornata con l'ultimo messaggio di stato del processo. Nella tabella seguente sono elencati i possibili valori di stato:

| Messaggio di stato       | Significato dello stato                                          |
| -------------------- | ------------------------------------------------------- |
| Completi            | Questo processo è stato eseguito in tutti i dispositivi.              |
| Operazione non riuscita               | Questo processo è non riuscito e non è stato completamente eseguito sui dispositivi.  |
| In sospeso              | Questo processo non ha ancora iniziato l'esecuzione sui dispositivi.         |
| In esecuzione              | Questo processo è attualmente in esecuzione sui dispositivi.             |
| Arrestato              | Questo processo è stato arrestato manualmente da un utente.           |

Il messaggio di stato è seguito da una panoramica dei dispositivi nel processo. La tabella seguente elenca i possibili valori di stato del dispositivo:

| Messaggio di stato       | Significato dello stato                                                     |
| -------------------- | ------------------------------------------------------------------ |
| Operazione completata            | Numero di dispositivi su cui è stato eseguito correttamente il processo.       |
| Operazione non riuscita               | Numero di dispositivi su cui il processo non è stato eseguito.       |

### <a name="view-the-device-status"></a>Visualizzare lo stato del dispositivo

Per visualizzare lo stato del processo e di tutti i dispositivi interessati, aprire il processo. Per scaricare un file CSV che include i dettagli del processo, incluso l'elenco dei dispositivi e i relativi valori di stato, selezionare **Scarica dettagli processo**. Accanto al nome di ogni dispositivo viene visualizzato uno dei seguenti messaggi di stato:

| Messaggio di stato       | Significato dello stato                                                                |
| -------------------- | ----------------------------------------------------------------------------- |
| Completi            | Il processo è stato eseguito su questo dispositivo.                                     |
| Operazione non riuscita               | Il processo non è stato eseguito su questo dispositivo. Il messaggio di errore mostra ulteriori informazioni.  |
| In sospeso              | Il processo non è ancora stato eseguito su questo dispositivo.                                   |

> [!NOTE]
> Se un dispositivo è stato eliminato, non è possibile selezionarlo. Viene visualizzato come eliminato con l'ID del dispositivo.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso come creare processi nell'applicazione Azure IoT Central, ecco alcuni passaggi successivi:Now that you've learned how to create jobs in your Azure IoT Central application, here are some next steps:

- [Gestire i dispositivi](howto-manage-devices.md)
- [Creare una nuova versione di un modello di dispositivo](howto-version-device-template.md)
