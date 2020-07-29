---
title: Creare ed eseguire i processi nell'applicazione Azure IoT Central | Microsoft Docs
description: I processi di IoT Central di Azure consentono funzionalità di gestione dei dispositivi in blocco, ad esempio l'aggiornamento delle proprietà o l'esecuzione di un comando.
ms.service: iot-central
services: iot-central
author: sarahhubbard
ms.author: sahubbar
ms.date: 06/08/2020
ms.topic: how-to
ms.openlocfilehash: 6073f71eb21ba4a6739647964d4888044d6ee59a
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87283622"
---
# <a name="create-and-run-a-job-in-your-azure-iot-central-application"></a>Creare ed eseguire un processo nell'applicazione IoT Central di Azure

È possibile usare Microsoft Azure IoT Central per gestire i dispositivi connessi su larga scala che usano i processi. I processi consentono di eseguire aggiornamenti in blocco alle proprietà dei dispositivi ed eseguire comandi. Questo articolo illustra come iniziare a usare i processi nella propria applicazione.

## <a name="create-and-run-a-job"></a>Creazione ed esecuzione di un processo

Questa sezione descrive come creare ed eseguire un processo. Viene illustrato come impostare la soglia di luce per un gruppo di dispositivi del gateway logistico.

1. Passare a **processi** dal riquadro sinistro.

2. Selezionare **+ nuovo** per creare un nuovo processo:

    ![Creare un nuovo processo](./media/howto-run-a-job/create-new-job.png)

3. Immettere un nome e una descrizione per identificare il processo che si sta creando.

4. Selezionare il gruppo di dispositivi di destinazione a cui si vuole applicare il processo. È possibile visualizzare il numero di dispositivi a cui si applica la configurazione del processo nella sezione di **Riepilogo** .

5. Quindi, scegliere **Proprietà Cloud**, **Proprietà**o **comando** come tipo di processo da configurare. Per impostare la configurazione di un processo di **Proprietà** , selezionare una proprietà e impostarne il nuovo valore. Per impostare un **comando**, scegliere il comando da eseguire. Un processo di proprietà può impostare più proprietà:

    ![Configurazione del processo](./media/howto-run-a-job/configure-job.png)

6. Dopo aver creato il processo, scegliere **Esegui** o **Salva**. Il processo verrà ora visualizzato nella pagina dei **processi** principali. In questa pagina è possibile visualizzare il processo attualmente in esecuzione e la cronologia dei processi precedentemente eseguiti o salvati. È possibile aprire nuovamente il processo salvato in qualsiasi momento per continuare a modificarlo o eseguirlo:

    ![Visualizzare il processo](./media/howto-run-a-job/view-job.png)

    > [!NOTE]
    > È possibile visualizzare 30 giorni di cronologia per i processi eseguiti in precedenza.

7. Fare clic sul processo salvato ed eseguire il processo facendo clic sul pulsante Esegui. Verrà visualizzato l'esecuzione di un popup del processo. Confermare facendo clic sul pulsante Esegui processo. 

    ![Eseguire un processo](./media/howto-run-a-job/run-job.png)

8. Il processo passa attraverso diverse fasi di pending, running e Completed. I dettagli di esecuzione del processo contengono la metrica dei risultati, i dettagli della durata e la griglia dell'elenco dei dispositivi. Da questa panoramica è inoltre possibile selezionare **log dei risultati** per scaricare un file CSV dei dettagli del processo, inclusi i dispositivi e i relativi valori di stato. Queste informazioni possono essere utili per la risoluzione dei problemi.

    ![Visualizzare lo stato dei dispositivi](./media/howto-run-a-job/download-details.png)

## <a name="manage-jobs"></a>Gestire i processi

Per arrestare uno dei processi in esecuzione, aprirlo e selezionare **Arresta**. Lo stato del processo cambia per riflettere che il processo è stato arrestato. La sezione **Riepilogo** Mostra i dispositivi completati, non riusciti o ancora in sospeso.

![Gestisci processo](./media/howto-run-a-job/manage-job.png)

Quando il processo è in stato interrotto, è possibile fare clic su **continua** per riprendere l'esecuzione del processo. Lo stato del processo cambia per riflettere che il processo è ora in esecuzione di nuovo. La sezione di **Riepilogo** continua ad aggiornare con lo stato più recente.

![Processo interrotto](./media/howto-run-a-job/stopped-job.png)

## <a name="copy-a-job"></a>Copiare un processo

Per copiare uno dei processi esistenti, selezionarlo nella pagina **processi** e selezionare **Dettagli processo**. Verrà visualizzata la pagina Dettagli processo. 

![Dettagli processo](./media/howto-run-a-job/job-details.png)

Fare clic su **copia**

![Dettagli processo](./media/howto-run-a-job/job-details-copy.png)

Verrà aperta una copia della configurazione del processo da modificare e la **copia** verrà aggiunta al nome del processo. È possibile salvare o eseguire il nuovo processo:

![Copia processo](./media/howto-run-a-job/copy-job.png)

## <a name="view-job-status"></a>Visualizzare lo stato di un processo

Dopo la creazione di un processo, la colonna **stato** viene aggiornata con l'ultimo messaggio di stato del processo. Nella tabella seguente sono elencati i valori di stato possibili:

| Messaggio di stato       | Significato dello stato                                          |
| -------------------- | ------------------------------------------------------- |
| Completi            | Questo processo è stato eseguito in tutti i dispositivi.              |
| Non riuscito               | Questo processo è non riuscito e non è stato completamente eseguito sui dispositivi.  |
| In sospeso              | Questo processo non è ancora iniziato a eseguire nei dispositivi.         |
| In esecuzione              | Questo processo è attualmente in esecuzione sui dispositivi.             |
| Arrestato              | Questo processo è stato arrestato manualmente da un utente.           |

Il messaggio di stato è seguito da una panoramica dei dispositivi nel processo. La tabella seguente elenca i possibili valori di stato del dispositivo:

| Messaggio di stato       | Significato dello stato                                                     |
| -------------------- | ------------------------------------------------------------------ |
| Completato            | Il numero di dispositivi in cui il processo è stato eseguito correttamente.       |
| Non riuscito               | Il numero di dispositivi in cui l'esecuzione del processo non è riuscita.       |

### <a name="view-the-device-status-values"></a>Visualizzare i valori dello stato del dispositivo

Per visualizzare lo stato del processo e di tutti i dispositivi interessati, aprire il processo. Accanto a ogni nome dispositivo viene visualizzato uno dei seguenti messaggi di stato:

| Messaggio di stato       | Significato dello stato                                                                |
| -------------------- | ----------------------------------------------------------------------------- |
| Completi            | Processo eseguito in questo dispositivo.                                     |
| Non riuscito               | Non è stato possibile eseguire il processo in questo dispositivo. Il messaggio di errore Mostra ulteriori informazioni.  |
| In sospeso              | Il processo non è ancora stato eseguito in questo dispositivo.                                   |

Per scaricare un file CSV che include i dettagli del processo e l'elenco dei dispositivi e i relativi valori di stato, selezionare **Scarica**.

### <a name="filter-the-list-of-devices"></a>Filtrare l'elenco di dispositivi

È possibile filtrare l'elenco dei dispositivi nella pagina dei dettagli del processo selezionando l'icona del filtro. È possibile filtrare i campi relativi a ID o **stato** del **dispositivo** :

:::image type="content" source="media/howto-run-a-job/filter.png" alt-text="Filtrare l'elenco dei dispositivi":::

### <a name="customize-columns-in-the-device-list"></a>Personalizzare le colonne nell'elenco dei dispositivi

È possibile scegliere colonne aggiuntive da visualizzare nell'elenco dei dispositivi selezionando l'icona Opzioni colonna:

:::image type="content" source="media/howto-run-a-job/column-options.png" alt-text="Opzioni colonne":::

Viene visualizzata una finestra di dialogo che consente di scegliere le colonne da visualizzare nell'elenco dei dispositivi. Selezionare le colonne che si desidera visualizzare, selezionare l'icona freccia destra e selezionare **OK**. Per selezionare tutte le colonne disponibili, **selezionare Seleziona tutto**:

:::image type="content" source="media/howto-run-a-job/column-picker-popup.png" alt-text="Finestra di dialogo Selezione colonne":::

Le colonne selezionate vengono visualizzate nell'elenco dei dispositivi:

:::image type="content" source="media/howto-run-a-job/column-picker-column-selected.png" alt-text="Seleziona colonne":::

Le colonne selezionate vengono rese permanente in una sessione utente o tra sessioni utente che hanno accesso all'applicazione.

## <a name="rerun-jobs"></a>Riesegui processi

È possibile rieseguire un processo con dispositivi non riusciti. Selezionare **Riesegui**:

:::image type="content" source="media/howto-run-a-job/rerun.png" alt-text="Rieseguire un processo":::

Immettere un nome e una descrizione per il processo, quindi selezionare **Riesegui processo**. Viene inviato un nuovo processo per ritentare l'azione nei dispositivi non riusciti:

:::image type="content" source="media/howto-run-a-job/rerun-failed.png" alt-text="Riesegui dispositivi non riusciti":::

> [!NOTE]
> Non è possibile eseguire più di cinque processi contemporaneamente da un'applicazione IoT Central.

> [!NOTE]
> Quando un processo viene completato e si elimina un dispositivo che si trova nell'elenco dei dispositivi del processo, la voce del dispositivo visualizzata come eliminata nel nome del dispositivo e il collegamento Dettagli dispositivo non è disponibile per il dispositivo eliminato.

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come creare processi nell'applicazione IoT Central di Azure, di seguito sono riportati alcuni passaggi successivi:

- [Gestire i dispositivi](howto-manage-devices.md)
- [Creare una nuova versione di un modello di dispositivo](howto-version-device-template.md)
