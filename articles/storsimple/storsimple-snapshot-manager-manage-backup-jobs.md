<properties 
   pageTitle="Gestione snapshot StorSimple operazioni di backup | Microsoft Azure"
   description="Viene descritto come utilizzare lo snap-in MMC di Gestione snapshot StorSimple per visualizzare e gestire i processi di backup pianificati, attualmente in esecuzione e completati."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carolz"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="09/15/2015"
   ms.author="v-sharos" />


# Utilizzo di Gestione snapshot StorSimple per visualizzare e gestire i processi di backup

## Panoramica

Nel nodo **Processi** del riquadro **Ambito** vengono mostrate le attività di backup **pianificate**, delle **ultime 24 ore** e **in esecuzione** avviate in modo interattivo o da un criterio configurato.

In questa esercitazione viene illustrato come utilizzare il nodo **Processi** per visualizzare le informazioni sui processi di backup pianificati, recenti e attualmente in esecuzione. (L'elenco dei processi e le informazioni corrispondenti vengono visualizzati nel riquadro **Risultati**.) Inoltre, è possibile fare clic con il pulsante destro del mouse su un processo elencato e visualizzare un menu di scelta rapida in cui sono elencate le azioni disponibili.

## Visualizzazione dei processi pianificati

Utilizzare la procedura seguente per visualizzare i processi di backup pianificati.

#### Per visualizzare i processi pianificati

1. Fare clic sull’icona del desktop per avviare StorSimple Snapshot Manager. 

2. Nel riquadro **Ambito**, espandere il nodo **Processi**, quindi fare clic su **Pianificati**. Nel riquadro **Risultati** vengono visualizzate le informazioni seguenti:

    - **Nome**: il nome dello snapshot pianificato

    - **Prossima esecuzione**: la data e l’ora del successivo snapshot pianificato.

    - **Ultima esecuzione**: la data e l'ora dello snapshot pianificato più recente.

    >[AZURE.NOTE]Solo per gli snapshot monouso, il valore di **Prossima esecuzione** e **Ultima esecuzione** sarà lo stesso.
 
    ![Processi di backup pianificati](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_scheduled.png)
 
3. Per eseguire azioni aggiuntive su un processo specifico, fare clic con il pulsante destro del mouse sul nome del processo nel riquadro **Risultati** e selezionare un’opzione del menu.

## Visualizzazione dei processi recenti

Utilizzare la procedura seguente per visualizzare i processi di backup e ripristino completati nelle ultime 24 ore.

#### Per visualizzare i processi recenti

1. Fare clic sull’icona del desktop per avviare StorSimple Snapshot Manager.

2. Nel riquadro **Ambito**, espandere il nodo **Processi**, quindi fare clic su **Ultime 24 ore**. Nel riquadro **Risultati** vengono mostrati i processi di backup per le ultime 24 ore (per un massimo di 64 processi). Nel riquadro **Risultati** vengono visualizzate le informazioni seguenti, a seconda delle opzioni di **Visualizza** specificate:

    - **Nome**: il nome dello snapshot pianificato.
 
    - **Avviato**: la data e l’ora di avvio dello snapshot.

    - **Arrestato**: la data e l'ora in cui lo snapshot è stato completato o terminato.

    - **Trascorso**: la quantità di tempo che intercorre tra l’ora di **Avviato** e **Arrestato**.

    - **Stato**: lo stato del processo completato di recente. **Esito positivo** indica che il backup è stato creato correttamente. **Operazione non riuscita** indica che il processo non è stato eseguito correttamente.

    - **Informazioni**: il motivo dell'errore.

    - **Byte elaborati (MB)**: la quantità di dati del gruppo di volumi che è stata elaborata (in MB).

    ![Processi eseguiti nelle ultime 24 ore](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_Last_24_hours.png)

3. Per eseguire azioni aggiuntive su un processo specifico, fare clic con il pulsante destro del mouse sul nome del processo nel riquadro **Risultati** e selezionare un’opzione del menu.

    ![Eliminare un processo](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Delete_backup.png)
     
## Visualizzazione dei processi attualmente in esecuzione

Utilizzare la procedura seguente per visualizzare i processi attualmente in esecuzione.

#### Per visualizzare i processi attualmente in esecuzione

1. Fare clic sull’icona del desktop per avviare StorSimple Snapshot Manager.

2. Nel riquadro **Ambito**, espandere il nodo **Processi**, quindi fare clic su **In esecuzione**. Nel riquadro **Risultati** vengono visualizzate le informazioni seguenti, a seconda delle opzioni di **Visualizza** specificate:

    - **Nome**: il nome dello snapshot pianificato.

    - **Avviato**: la data e l’ora di avvio dello snapshot.

    - **Checkpoint**: l'azione corrente del backup.

    - **Stato**: la percentuale di completamento.
    
    - **Trascorso**: la quantità di tempo trascorso dall'avvio del backup.

    - **Produttività media (MB)**: la quantità media dei dati consegnati, espressa in megabyte (MB).

    - **Byte elaborati (MB)**: la quantità di dati del gruppo di volumi che è stata elaborata (in MB).

    - **Byte scritti (MB)**: la quantità di dati scritti per il backup (in MB).

    ![Processi attualmente in esecuzione](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_running.png)

3. Per eseguire azioni aggiuntive su un processo specifico, fare clic con il pulsante destro del mouse sul nome del processo nel riquadro **Risultati** e selezionare un’opzione del menu.

## Passaggi successivi

- Informazioni su come [Usare Gestione Snapshot StorSimple per amministrare la soluzione di StorSimple](storsimple-snapshot-manager-admin.md).
- Informazioni su come[Utilizzare Gestione snapshot StorSimple per gestire il catalogo di backup](storsimple-snapshot-manager-manage-backup-catalog.md)















            


 

<!---HONumber=Oct15_HO3-->