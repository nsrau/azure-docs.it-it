<properties 
   pageTitle="Ripristino di un volume StorSimple da un set di backup"
   description="Viene illustrato come utilizzare il catalogo di backup per ripristinare un volume StorSimple da un set di backup."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="adinah"
   editor="tysonn" /> 

<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="04/13/2015"
   ms.author="v-sharos" />

# Ripristino di un volume StorSimple da un set di backup

## Panoramica

Nella pagina **Catalogo di backup** vengono visualizzati tutti i set di backup creati quando si eseguono backup manuali o automatizzati. È possibile utilizzare questa pagina per elencare tutti i backup per un criterio di backup o un volume, selezionare o eliminare i backup o utilizzare un backup per ripristinare o clonare un volume.

 ![Pagina catalogo di backup](./media/storsimple-restore-from-backup-set/HCS_BackupCatalog.png)

In questa esercitazione viene illustrato come utilizzare la pagina **Catalogo di backup** per ripristinare il dispositivo da un set di backup.

## Come utilizzare il catalogo di backup 

La pagina **Catalogo di backup** fornisce una query che consente di limitare la selezione del set di backup. È possibile filtrare i set di backup che vengono recuperati in base ai parametri seguenti:

- **Dispositivo**: il dispositivo in cui è stato creato il set di backup.
- **Criterio di backup** o **Volume**: il criterio di backup o volume associato a questo set di backup.
- **Da** e **A**: intervallo di data e ora di creazione del set di backup.

I set di backup filtrati vengono quindi catalogati in base ai seguenti attributi:

- **Nome**: nome del criterio di backup o volume associato al set di backup.
- **Dimensioni**: dimensione effettiva del set di backup.
- **Creato il**: data e ora di creazione dei backup. 
- **Tipo**: i set di backup possono essere snapshot in locale o del cloud. Uno snapshot locale è un backup di tutti i dati di volume archiviati localmente sul dispositivo, mentre uno snapshot del cloud si riferisce al backup dei dati di volume che risiedono nel cloud. Gli snapshot in locale forniscono un accesso più rapido, mentre gli snapshot del cloud vengono scelti per la resilienza dei dati.
- **Avviato da**: i backup possono essere avviati automaticamente in base a una pianificazione o manualmente dall'utente. Per pianificare i backup, è possibile utilizzare un criterio di backup. In alternativa, è possibile utilizzare l’opzione **Esegui backup** per eseguire un backup interattivo.

## Come ripristinare il dispositivo dal backup

È possibile utilizzare la pagina **Catalogo di backup** per ripristinare il dispositivo da un backup specifico. Tenere presente che, effettuando il ripristino, lo stato di un volume torna a quello in cui si trovava al momento dell'esecuzione del backup. Tutti i dati aggiunti dopo l'operazione di backup andranno persi.

> [AZURE.WARNING]Il ripristino da un backup sostituisce i volumi esistenti dal backup. Ciò può comportare la perdita dei dati scritti dopo l'esecuzione del backup.


### Per effettuare il ripristino da un set di backup

1. Nella pagina del servizio StorSimple Manager, fare clic sulla scheda **Catalogo di backup**.

    ![Catalogo di backup](./media/storsimple-restore-from-backup-set/HCS_Restore.png)

2. Procedura di selezione di un set di backup:
  1. Selezionare un dispositivo appropriato.
  2. Nell'elenco a discesa, scegliere il volume o il criterio di backup per il backup che si desidera selezionare.
  3. Specificare l'intervallo di tempo.
  4. Fare clic sull'icona del segno di spunta ![icona del segno di spunta](./media/storsimple-restore-from-backup-set/HCS_CheckIcon.png) per eseguire la query.
 
    I backup associati al volume selezionato o al criterio di backup dovrebbero essere visualizzati nell'elenco dei set di backup.

3. Espandere il set di backup per visualizzare i volumi associati. Questi volumi devono essere disconnessi nell'host e nel dispositivo prima di poterli ripristinare. Accedere ai volumi sulla pagina **Contenitori di volumi**, quindi attenersi alla procedura descritta in [Disattivare un volume](storsimple-manage-volumes.md#take-a-volume-offline) per disattivarli.

    >  [AZURE.IMPORTANT]Assicurarsi, in primo luogo, di aver disattivato tutti i volumi sull'host, prima di disattivarli sul dispositivo. Se i volumi non vengono disattivati sull'host, questa operazione verrà effettuata dal servizio StorSimple Manager. In questo modo alcuni dati potrebbero essere danneggiati.

4. Tornare alla scheda **Catalogo di backup** e selezionare un set di backup.

5. Fare clic su **Ripristina** nella parte inferiore della pagina.

6. Verrà richiesto di confermare.

    ![Pagina di conferma](./media/storsimple-restore-from-backup-set/HCS_ConfirmRestore.png)

7. Esaminare le informazioni di ripristino e fare clic sull'icona di segno di spunta![icona del segno di spunta](./media/storsimple-restore-from-backup-set/HCS_CheckIcon.png). In questo modo viene avviato un processo di ripristino che è possibile visualizzare accedendo alla pagina **Processi**.

8. Dopo aver completato il ripristino, è possibile verificare che i contenuti dei volumi siano sostituiti dai volumi dal backup.

## Passaggi successivi

Informazioni su come [gestire i volumi StorSimple](storsimple-manage-volumes.md) <!--HONumber=52-->
