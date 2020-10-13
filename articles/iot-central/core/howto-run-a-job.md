---
title: Creare ed eseguire i processi nell'applicazione Azure IoT Central | Microsoft Docs
description: I processi di IoT Central di Azure consentono funzionalità di gestione dei dispositivi in blocco, ad esempio l'aggiornamento delle proprietà o l'esecuzione di un comando.
ms.service: iot-central
services: iot-central
author: sarahhubbard
ms.author: sahubbar
ms.date: 09/30/2020
ms.topic: how-to
ms.openlocfilehash: 5b8aba74cb0914cf26382e0d17a8ce2ba6bd4063
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91573964"
---
# <a name="create-and-run-a-job-in-your-azure-iot-central-application"></a>Creare ed eseguire un processo nell'applicazione IoT Central di Azure

È possibile usare IoT Central di Azure per gestire i dispositivi connessi su larga scala tramite processi. I processi consentono di eseguire aggiornamenti in blocco per le proprietà del dispositivo e del cloud e di eseguire comandi. Questo articolo illustra come iniziare a usare i processi nella propria applicazione.

## <a name="create-and-run-a-job"></a>Creazione ed esecuzione di un processo

Nell'esempio seguente viene illustrato come creare ed eseguire un processo per impostare la soglia di luce per un gruppo di dispositivi del gateway logistico. Utilizzare la creazione guidata processo per creare ed eseguire i processi. È possibile salvare un processo per l'esecuzione in un secondo momento.

1. Nel riquadro sinistro selezionare **processi**.

1. Selezionare **+ nuovo processo**.

1. Nella pagina **Configura il processo** immettere un nome e una descrizione per identificare il processo che si sta creando.

1. Selezionare il gruppo di dispositivi di destinazione a cui si desidera applicare il processo. È possibile visualizzare il numero di dispositivi a cui si applica la configurazione del processo sotto la selezione del **gruppo** di dispositivi.

1. Scegliere **Proprietà**, **Proprietà**o **comando** cloud come tipo di **processo**:

    Per configurare un processo di **Proprietà** , selezionare una proprietà e impostarne il nuovo valore. Per configurare un processo del **comando** , scegliere il comando da eseguire. Un processo di proprietà può impostare più proprietà.

    :::image type="content" source="media/howto-run-a-job/configure-job.png" alt-text="Screenshot che mostra le selezioni per la creazione di un processo di proprietà denominato set Light Threshold":::

    Selezionare **Salva e Chiudi** per aggiungere il processo all'elenco dei processi salvati nella pagina **processi** . In seguito sarà possibile tornare a un processo dall'elenco dei processi salvati.

    Selezionare **Avanti** per passare alla pagina **Opzioni di recapito** . La pagina **Opzioni di recapito** consente di impostare le opzioni di recapito per questo processo: **batch** e **soglia di annullamento**.

    I batch consentono di scaglionare i processi per un numero elevato di dispositivi. Il processo è suddiviso in più batch e ogni batch contiene un subset di dispositivi. I batch vengono accodati ed eseguiti in sequenza.

    La soglia di annullamento consente di annullare automaticamente un processo se il numero di errori supera il limite impostato. La soglia può essere applicata a tutti i dispositivi nel processo o a singoli batch.

    :::image type="content" source="media/howto-run-a-job/job-wizard-delivery-options.png" alt-text="Screenshot che mostra le selezioni per la creazione di un processo di proprietà denominato set Light Threshold":::

    Selezionare **Avanti** per passare alla pagina **Verifica** . La pagina **Verifica** Mostra i dettagli di configurazione del processo. Selezionare **Run (Esegui** ) per inviare il processo.

    :::image type="content" source="media/howto-run-a-job/job-wizard-review.png" alt-text="Screenshot che mostra le selezioni per la creazione di un processo di proprietà denominato set Light Threshold":::

1. Un processo passa attraverso le fasi *in sospeso*, *in esecuzione*e *completate* . I dettagli di esecuzione del processo contengono metriche dei risultati, dettagli della durata e una griglia dell'elenco di dispositivi.

    Al termine del processo, è possibile selezionare **log dei risultati** per scaricare un file CSV dei dettagli del processo, inclusi i dispositivi e i relativi valori di stato. Queste informazioni possono essere utili per la risoluzione dei problemi.

    :::image type="content" source="media/howto-run-a-job/download-details.png" alt-text="Screenshot che mostra le selezioni per la creazione di un processo di proprietà denominato set Light Threshold":::

1. Il processo è ora visualizzato nell'elenco degli **ultimi 30 giorni** della pagina **processi** . Questa pagina mostra i processi attualmente in esecuzione e la cronologia di tutti i processi eseguiti o salvati in precedenza.

    > [!NOTE]
    > È possibile visualizzare 30 giorni di cronologia per i processi eseguiti in precedenza.

## <a name="manage-jobs"></a>Gestire i processi

Per arrestare un processo in esecuzione, aprirlo e selezionare **Arresta**. Lo stato del processo cambia per indicare che il processo è stato arrestato. La sezione **Riepilogo** Mostra i dispositivi completati, non riusciti o ancora in sospeso.

:::image type="content" source="media/howto-run-a-job/manage-job.png" alt-text="Screenshot che mostra le selezioni per la creazione di un processo di proprietà denominato set Light Threshold":::

Quando un processo si trova nello stato interrotto, è possibile selezionare **continua** per riprendere l'esecuzione del processo. Lo stato del processo cambia in modo da riflettere il fatto che il processo è ora in esecuzione di nuovo. La sezione di **Riepilogo** continua ad aggiornare con lo stato più recente.

:::image type="content" source="media/howto-run-a-job/stopped-job.png" alt-text="Screenshot che mostra le selezioni per la creazione di un processo di proprietà denominato set Light Threshold":::

## <a name="copy-a-job"></a>Copiare un processo

Per copiare un processo esistente, selezionare un processo eseguito. Selezionare **copia** nella pagina risultati processo o dettagli processi:

:::image type="content" source="media/howto-run-a-job/job-details-copy.png" alt-text="Screenshot che mostra le selezioni per la creazione di un processo di proprietà denominato set Light Threshold":::

Verrà aperta una copia della configurazione del processo da modificare e la **copia** verrà aggiunta al nome del processo.

## <a name="view-job-status"></a>Visualizzare lo stato di un processo

Dopo la creazione di un processo, la colonna **stato** viene aggiornata con il messaggio di stato del processo più recente. Nella tabella seguente sono elencati i possibili valori di *stato del processo* :

| Messaggio di stato       | Significato dello stato                                          |
| -------------------- | ------------------------------------------------------- |
| Completato            | Questo processo è stato eseguito in tutti i dispositivi.              |
| Operazione non riuscita               | Questo processo non è riuscito e non è stato completamente eseguito nei dispositivi.  |
| Pending              | Questo processo non è ancora iniziato a funzionare sui dispositivi.         |
| In esecuzione              | Questo processo è attualmente in esecuzione nei dispositivi.             |
| Arrestato              | Il processo è stato interrotto manualmente da un utente.           |
| Cancellati             | Il processo è stato annullato perché è stata superata la soglia impostata nella pagina **Opzioni di recapito** . |

Il messaggio di stato è seguito da una panoramica dei dispositivi nel processo. La tabella seguente elenca i possibili valori di *stato del dispositivo* :

| Messaggio di stato       | Significato dello stato                                                     |
| -------------------- | ------------------------------------------------------------------ |
| Operazione riuscita            | Il numero di dispositivi in cui il processo è stato eseguito correttamente.       |
| Operazione non riuscita               | Il numero di dispositivi in cui il processo non è stato eseguito.       |

Per visualizzare lo stato del processo e di tutti i dispositivi interessati, aprire il processo. Accanto a ogni nome dispositivo viene visualizzato uno dei seguenti messaggi di stato:

| Messaggio di stato       | Significato dello stato                                                                |
| -------------------- | ----------------------------------------------------------------------------- |
| Completato            | Il processo è stato eseguito in questo dispositivo.                                     |
| Operazione non riuscita               | Non è stato possibile eseguire il processo in questo dispositivo. Il messaggio di errore Mostra ulteriori informazioni.  |
| Pending              | Il processo non è ancora stato eseguito in questo dispositivo.                                   |

Per scaricare un file CSV che include i dettagli del processo e l'elenco dei dispositivi e i relativi valori di stato, selezionare **log dei risultati**.

## <a name="filter-the-device-list"></a>Filtrare l'elenco dei dispositivi

È possibile filtrare l'elenco dei dispositivi nella pagina dei **Dettagli del processo** selezionando l'icona del filtro. È possibile filtrare in un campo relativo all'ID o **allo stato** del **dispositivo** :

:::image type="content" source="media/howto-run-a-job/filter.png" alt-text="Screenshot che mostra le selezioni per la creazione di un processo di proprietà denominato set Light Threshold":::

## <a name="customize-columns-in-the-device-list"></a>Personalizzare le colonne nell'elenco dei dispositivi

È possibile aggiungere colonne all'elenco dei dispositivi selezionando l'icona Opzioni colonna:

:::image type="content" source="media/howto-run-a-job/column-options.png" alt-text="Screenshot che mostra le selezioni per la creazione di un processo di proprietà denominato set Light Threshold":::

Utilizzare la finestra di dialogo **Opzioni colonna** per scegliere le colonne dell'elenco dei dispositivi. Selezionare le colonne che si desidera visualizzare, selezionare la freccia destra e quindi fare clic su **OK**. Per selezionare tutte le colonne disponibili, scegliere **Seleziona tutto**. Le colonne selezionate vengono visualizzate nell'elenco dei dispositivi.

Le colonne selezionate vengono mantenute in una sessione utente o tra sessioni utente che hanno accesso all'applicazione.

## <a name="rerun-jobs"></a>Riesegui processi

È possibile rieseguire un processo con dispositivi non riusciti. Selezionare **Riesegui su non riuscito**:

:::image type="content" source="media/howto-run-a-job/rerun.png" alt-text="Screenshot che mostra le selezioni per la creazione di un processo di proprietà denominato set Light Threshold":::

Immettere un nome e una descrizione per il processo e quindi fare clic su **Riesegui processo**. Viene inviato un nuovo processo per ritentare l'azione nei dispositivi non riusciti.

> [!NOTE]
> Non è possibile eseguire più di cinque processi contemporaneamente da un'applicazione IoT Central di Azure.
>
> Quando un processo viene completato e si elimina un dispositivo che si trova nell'elenco dei dispositivi del processo, la voce del dispositivo viene visualizzata come eliminata nel nome del dispositivo. Il collegamento dettagli non è disponibile per il dispositivo eliminato.

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come creare processi nell'applicazione IoT Central di Azure, di seguito sono riportati alcuni passaggi successivi:

- [Gestire i dispositivi](howto-manage-devices.md)
- [Creare una nuova versione di un modello di dispositivo](howto-version-device-template.md)
