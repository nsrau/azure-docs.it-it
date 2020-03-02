---
title: Creare ed eseguire i processi nell'applicazione Azure IoT Central | Microsoft Docs
description: I processi di IoT Central di Azure consentono funzionalità di gestione dei dispositivi in blocco, ad esempio l'aggiornamento delle proprietà o l'esecuzione di un comando.
ms.service: iot-central
services: iot-central
author: sarahhubbard
ms.author: sahubbar
ms.date: 07/08/2019
ms.topic: conceptual
manager: peterpr
ms.openlocfilehash: 2dd1eddc841cc484957c2124de3419799c4e59b7
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/01/2020
ms.locfileid: "78206809"
---
# <a name="create-and-run-a-job-in-your-azure-iot-central-application"></a>Creare ed eseguire un processo nell'applicazione IoT Central di Azure

È possibile usare Microsoft Azure IoT Central per gestire i dispositivi connessi su larga scala che usano i processi. I processi consentono di eseguire aggiornamenti in blocco alle proprietà dei dispositivi ed eseguire comandi. Questo articolo illustra come iniziare a usare i processi nella propria applicazione.

## <a name="create-and-run-a-job"></a>Creazione ed esecuzione di un processo

Questa sezione descrive come creare ed eseguire un processo. Viene illustrato come impostare la soglia di luce per un gruppo di dispositivi del gateway logistico.

1. Passare a **processi** dal riquadro sinistro.

2. Selezionare **+ nuovo** per creare un nuovo processo:

    > [!div class="mx-imgBorder"]
    > ![creare un nuovo processo](./media/howto-run-a-job/createnewjob.png)

3. Immettere un nome e una descrizione per identificare il processo che si sta creando.

4. Selezionare il gruppo di dispositivi di destinazione a cui si vuole applicare il processo. È possibile visualizzare il numero di dispositivi a cui si applica la configurazione del processo nella sezione di **Riepilogo** .

5. Quindi, scegliere **Proprietà** o **comando** come tipo di processo da configurare. Per impostare la configurazione di un processo di **Proprietà** , selezionare una proprietà e impostarne il nuovo valore. Per impostare un **comando**oppure scegliere il comando da eseguire. Un processo di proprietà può impostare più proprietà:

    > [!div class="mx-imgBorder"]
    > ![configurare il processo](./media/howto-run-a-job/configurejob.png)

6. Dopo aver selezionato i dispositivi, scegliere **Esegui** o **Salva**. Il processo verrà ora visualizzato nella pagina dei **processi** principali. In questa pagina è possibile visualizzare il processo attualmente in esecuzione e la cronologia dei processi precedentemente eseguiti o salvati. È possibile aprire nuovamente il processo salvato in qualsiasi momento per continuare a modificarlo o eseguirlo:

    > [!div class="mx-imgBorder"]
    > ![Visualizza processo](./media/howto-run-a-job/viewjob.png)

    > [!NOTE]
    > È possibile visualizzare 30 giorni di cronologia per i processi eseguiti in precedenza.

7. Per ottenere una panoramica del processo, selezionare il processo da visualizzare nell'elenco. Questa panoramica contiene i dettagli del processo, i dispositivi e i valori dello stato del dispositivo. Da questa panoramica è inoltre possibile selezionare **Scarica dettagli processo** per scaricare un file CSV dei dettagli del processo, inclusi i dispositivi e i relativi valori di stato. Queste informazioni possono essere utili per la risoluzione dei problemi:

    > [!div class="mx-imgBorder"]
    > ![visualizzare lo stato del dispositivo](./media/howto-run-a-job/downloaddetails.png)

### <a name="stop-a-running-job"></a>Arrestare un processo in esecuzione

Per arrestare uno dei processi in esecuzione, aprirlo e selezionare **Arresta**. Lo stato del processo cambia per riflettere che il processo è stato arrestato. La sezione **Riepilogo** Mostra i dispositivi completati, non riusciti o ancora in sospeso:

    > [!div class="mx-imgBorder"]
    > ![Stop job](./media/howto-run-a-job/stopjob.png)

### <a name="run-a-stopped-job"></a>Eseguire un processo arrestato

Per eseguire un processo attualmente arrestato, selezionarlo e quindi selezionare **Esegui**. Lo stato del processo cambia per riflettere che il processo è ora in esecuzione di nuovo. La sezione di **Riepilogo** continua ad aggiornare con lo stato di avanzamento più recente:

    > [!div class="mx-imgBorder"]
    > ![Resumed job](./media/howto-run-a-job/resumejob.png)

## <a name="copy-a-job"></a>Copiare un processo

Per copiare uno dei processi esistenti, aprirlo e selezionare **copia**. Viene aperta una copia della configurazione del processo da modificare e la **copia** viene aggiunta alla fine del nome del processo. È possibile salvare o eseguire il nuovo processo:

    > [!div class="mx-imgBorder"]
    > ![Copy job](./media/howto-run-a-job/copyjob.png)

## <a name="view-the-job-status"></a>Visualizzare lo stato del processo

Dopo la creazione di un processo, la colonna **stato** viene aggiornata con l'ultimo messaggio di stato del processo. Nella tabella seguente sono elencati i valori di stato possibili:

| Messaggio di stato       | Significato dello stato                                          |
| -------------------- | ------------------------------------------------------- |
| Operazione completata            | Questo processo è stato eseguito in tutti i dispositivi.              |
| Operazione non riuscita               | Questo processo è non riuscito e non è stato completamente eseguito sui dispositivi.  |
| In sospeso              | Questo processo non è ancora iniziato a eseguire nei dispositivi.         |
| In esecuzione              | Questo processo è attualmente in esecuzione sui dispositivi.             |
| Arrestato              | Questo processo è stato arrestato manualmente da un utente.           |

Il messaggio di stato è seguito da una panoramica dei dispositivi nel processo. La tabella seguente elenca i possibili valori di stato del dispositivo:

| Messaggio di stato       | Significato dello stato                                                     |
| -------------------- | ------------------------------------------------------------------ |
| Succeeded            | Il numero di dispositivi in cui il processo è stato eseguito correttamente.       |
| Operazione non riuscita               | Il numero di dispositivi in cui l'esecuzione del processo non è riuscita.       |

### <a name="view-the-device-status"></a>Visualizzare lo stato del dispositivo

Per visualizzare lo stato del processo e di tutti i dispositivi interessati, selezionare il processo. Per scaricare un file CSV che include i dettagli del processo, incluso l'elenco dei dispositivi e i relativi valori di stato, selezionare **Scarica dettagli processo**. Accanto a ogni nome dispositivo viene visualizzato uno dei seguenti messaggi di stato:

| Messaggio di stato       | Significato dello stato                                                                |
| -------------------- | ----------------------------------------------------------------------------- |
| Operazione completata            | Il processo è stato eseguito su questo dispositivo.                                     |
| Operazione non riuscita               | Il processo non è stato eseguito su questo dispositivo. Il messaggio di errore Mostra ulteriori informazioni.  |
| In sospeso              | Il processo non è ancora stato eseguito in questo dispositivo.                                   |

> [!NOTE]
> Se un dispositivo è stato eliminato, non è possibile selezionare il dispositivo. Viene visualizzato come eliminato con l'ID del dispositivo.

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come creare processi nell'applicazione IoT Central di Azure, di seguito sono riportati alcuni passaggi successivi:

- [Gestire i dispositivi](howto-manage-devices.md)
- [Creare una nuova versione di un modello di dispositivo](howto-version-device-template.md)
