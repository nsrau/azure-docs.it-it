---
title: Creare ed eseguire i processi nell'applicazione Azure IoT Central | Microsoft Docs
description: I processi di IoT Central di Azure consentono funzionalità di gestione dei dispositivi in blocco, ad esempio l'aggiornamento delle proprietà o l'esecuzione di un comando.
ms.service: iot-central
services: iot-central
author: sarahhubbard
ms.author: sahubbar
ms.date: 06/08/2020
ms.topic: how-to
ms.openlocfilehash: 406881a9131aae35b91dcab248745bb426cecf0e
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/25/2020
ms.locfileid: "88797837"
---
# <a name="create-and-run-a-job-in-your-azure-iot-central-application"></a>Creare ed eseguire un processo nell'applicazione IoT Central di Azure

È possibile usare Microsoft Azure IoT Central per gestire i dispositivi connessi su larga scala tramite processi. I processi consentono di eseguire aggiornamenti in blocco alle proprietà dei dispositivi ed eseguire comandi. Questo articolo illustra come iniziare a usare i processi nella propria applicazione.

## <a name="create-and-run-a-job"></a>Creazione ed esecuzione di un processo

In questa sezione viene illustrato come creare ed eseguire un processo sotto forma di impostazione della soglia di luce per un gruppo di dispositivi del gateway logistico.

1. Nel riquadro sinistro selezionare **processi**.

2. Selezionare **+ Nuovo**.

   ![Screenshot che mostra le selezioni per la creazione di un processo.](./media/howto-run-a-job/create-new-job.png)

3. Immettere un nome e una descrizione per identificare il processo che si sta creando.

4. Selezionare il gruppo di dispositivi di destinazione a cui si desidera applicare il processo. È possibile visualizzare il numero di dispositivi a cui si applica la configurazione del processo nella sezione di **Riepilogo** .

5. Scegliere **Proprietà**, **Proprietà**o **comando** cloud come tipo di processo da configurare. 

   Per impostare la configurazione di un processo di **Proprietà** , selezionare una proprietà e impostarne il nuovo valore. Per configurare una configurazione del processo del **comando** , scegliere il comando da eseguire. Un processo di proprietà può impostare più proprietà.

   ![Screenshot che mostra le selezioni per la creazione di un processo di proprietà denominato set Light Threshold.](./media/howto-run-a-job/configure-job.png)

6. Selezionare **Esegui** o **Salva**. Il processo verrà ora visualizzato nella pagina dei **processi** principali. In questa pagina è possibile visualizzare il processo attualmente in esecuzione e la cronologia dei processi precedentemente eseguiti o salvati. È possibile riaprire il processo salvato in qualsiasi momento per continuare a modificarlo o eseguirlo.

   ![Screenshot che mostra il nome, lo stato e la descrizione di un processo creato.](./media/howto-run-a-job/view-job.png)

   > [!NOTE]
   > È possibile visualizzare 30 giorni di cronologia per i processi eseguiti in precedenza.

7. Selezionare il processo salvato ed eseguirlo selezionando il pulsante **Esegui** . 

   Verrà visualizzata la finestra di dialogo **Esegui processo?** . Confermare selezionando il pulsante **Esegui processo** . 

   ![Screenshot della finestra di dialogo che conferma che si desidera eseguire un processo.](./media/howto-run-a-job/run-job.png)

8. Un processo passa attraverso le fasi di pending, running e Completed. I dettagli di esecuzione del processo contengono metriche dei risultati, dettagli della durata e una griglia dell'elenco di dispositivi. 

   Da questa panoramica è inoltre possibile selezionare **log dei risultati** per scaricare un file CSV dei dettagli del processo, inclusi i dispositivi e i relativi valori di stato. Queste informazioni possono essere utili per la risoluzione dei problemi.

   ![Screenshot che mostra lo stato del dispositivo.](./media/howto-run-a-job/download-details.png)

## <a name="manage-jobs"></a>Gestire i processi

Per arrestare un processo in esecuzione, aprirlo e selezionare **Arresta**. Lo stato del processo cambia per indicare che il processo è stato arrestato. La sezione **Riepilogo** Mostra i dispositivi completati, non riusciti o ancora in sospeso.

![Screenshot che mostra un processo in esecuzione e il pulsante per l'arresto di un processo.](./media/howto-run-a-job/manage-job.png)

Quando un processo si trova nello stato interrotto, è possibile selezionare **continua** per riprendere l'esecuzione del processo. Lo stato del processo cambia in modo da riflettere il fatto che il processo è ora in esecuzione di nuovo. La sezione di **Riepilogo** continua a essere aggiornata con lo stato di avanzamento più recente.

![Screenshot che mostra un processo interrotto e il pulsante per la continuazione di un processo.](./media/howto-run-a-job/stopped-job.png)

## <a name="copy-a-job"></a>Copiare un processo

Per copiare uno dei processi esistenti, selezionarlo nella pagina **processi** e selezionare **Dettagli processo**. Verrà visualizzata la pagina **Dettagli processo** . 

![Screenshot che mostra la pagina relativa ai dettagli dei processi.](./media/howto-run-a-job/job-details.png)

Selezionare **Copia**.

![Screenshot che mostra il pulsante copia.](./media/howto-run-a-job/job-details-copy.png)

Verrà aperta una copia della configurazione del processo da modificare e la **copia** verrà aggiunta al nome del processo. È possibile salvare o eseguire il nuovo processo.

![Screenshot che mostra una copia della configurazione del processo.](./media/howto-run-a-job/copy-job.png)

## <a name="view-job-status"></a>Visualizzare lo stato di un processo

Dopo la creazione di un processo, la colonna **stato** viene aggiornata con il messaggio di stato più recente per il processo. Nella tabella seguente sono elencati i possibili valori di stato del processo:

| Messaggio di stato       | Significato dello stato                                          |
| -------------------- | ------------------------------------------------------- |
| Completato            | Questo processo è stato eseguito in tutti i dispositivi.              |
| Non riuscito               | Questo processo non è riuscito e non è stato completamente eseguito nei dispositivi.  |
| In sospeso              | Questo processo non è ancora iniziato a funzionare sui dispositivi.         |
| In esecuzione              | Questo processo è attualmente in esecuzione nei dispositivi.             |
| Arrestato              | Il processo è stato interrotto manualmente da un utente.           |

Il messaggio di stato è seguito da una panoramica dei dispositivi nel processo. La tabella seguente elenca i possibili valori di stato del dispositivo:

| Messaggio di stato       | Significato dello stato                                                     |
| -------------------- | ------------------------------------------------------------------ |
| Completato            | Il numero di dispositivi in cui il processo è stato eseguito correttamente.       |
| Non riuscito               | Il numero di dispositivi in cui il processo non è stato eseguito.       |

Per visualizzare lo stato del processo e di tutti i dispositivi interessati, aprire il processo. Accanto a ogni nome dispositivo viene visualizzato uno dei seguenti messaggi di stato:

| Messaggio di stato       | Significato dello stato                                                                |
| -------------------- | ----------------------------------------------------------------------------- |
| Completato            | Il processo è stato eseguito in questo dispositivo.                                     |
| Non riuscito               | Non è stato possibile eseguire il processo in questo dispositivo. Il messaggio di errore Mostra ulteriori informazioni.  |
| In sospeso              | Il processo non è ancora stato eseguito in questo dispositivo.                                   |

Per scaricare un file CSV che include i dettagli del processo e l'elenco dei dispositivi e i relativi valori di stato, selezionare **Scarica**.

## <a name="filter-the-device-list"></a>Filtrare l'elenco dei dispositivi

È possibile filtrare l'elenco dei dispositivi nella pagina dei **Dettagli del processo** selezionando l'icona del filtro. È possibile filtrare in un campo relativo all'ID o **allo stato** del **dispositivo** .

:::image type="content" source="media/howto-run-a-job/filter.png" alt-text="Screenshot che mostra le selezioni per filtrare un elenco di dispositivi.":::

## <a name="customize-columns-in-the-device-list"></a>Personalizzare le colonne nell'elenco dei dispositivi

È possibile scegliere colonne aggiuntive da visualizzare nell'elenco dei dispositivi selezionando l'icona Opzioni colonna.

:::image type="content" source="media/howto-run-a-job/column-options.png" alt-text="Screenshot che mostra l'icona per le opzioni della colonna.":::

Una finestra di dialogo consente di scegliere le colonne da visualizzare nell'elenco dei dispositivi. Selezionare le colonne che si desidera visualizzare, selezionare la freccia destra e quindi fare clic su **OK**. Per selezionare tutte le colonne disponibili, **selezionare Seleziona tutto**.

:::image type="content" source="media/howto-run-a-job/column-picker-popup.png" alt-text="Screenshot che mostra la finestra di dialogo per la scelta delle colonne da visualizzare.":::

Le colonne selezionate vengono visualizzate nell'elenco dei dispositivi.

:::image type="content" source="media/howto-run-a-job/column-picker-column-selected.png" alt-text="Screenshot che mostra le colonne selezionate nell'elenco dei dispositivi.":::

Le colonne selezionate vengono rese permanente in una sessione utente o tra sessioni utente che hanno accesso all'applicazione.

## <a name="rerun-jobs"></a>Riesegui processi

È possibile rieseguire un processo con dispositivi non riusciti. Selezionare **Riesegui su non riuscito**.

:::image type="content" source="media/howto-run-a-job/rerun.png" alt-text="Screenshot che mostra il pulsante per la riesecuzione di un processo nei dispositivi non riusciti.":::

Immettere un nome e una descrizione per il processo e quindi fare clic su **Riesegui processo**. Viene inviato un nuovo processo per ritentare l'azione nei dispositivi non riusciti.

:::image type="content" source="media/howto-run-a-job/rerun-failed.png" alt-text="Screenshot che mostra la finestra di dialogo per la riesecuzione dei dispositivi non riusciti.":::

> [!NOTE]
> Non è possibile eseguire più di cinque processi contemporaneamente da un'applicazione IoT Central di Azure.
>
> Quando un processo viene completato e si elimina un dispositivo che si trova nell'elenco dei dispositivi del processo, la voce del dispositivo viene visualizzata come eliminata nel nome del dispositivo. Il collegamento dettagli non è disponibile per il dispositivo eliminato.

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come creare processi nell'applicazione IoT Central di Azure, di seguito sono riportati alcuni passaggi successivi:

- [Gestire i dispositivi](howto-manage-devices.md)
- [Creare una nuova versione di un modello di dispositivo](howto-version-device-template.md)
