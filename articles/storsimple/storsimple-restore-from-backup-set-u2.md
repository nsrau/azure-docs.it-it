---
title: Ripristinare un volume StorSimple dal backup | Microsoft Docs
description: Viene illustrato come utilizzare la pagina del catalogo di backup del servizio StorSimple Manager per ripristinare un volume StorSimple da un set di backup.
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: 
ms.assetid: 6f289c39-96c7-4d57-b68a-4bc2e99aef9d
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 04/26/2016
ms.author: v-sharos
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: fbb5a38e5ef92de7ca4752441d72d29135867a51


---
# <a name="restore-a-storsimple-volume-from-a-backup-set-update-2"></a>Ripristinare un volume StorSimple da un set di backup (aggiornamento 2)
[!INCLUDE [storsimple-version-selector-restore-from-backup](../../includes/storsimple-version-selector-restore-from-backup.md)]

## <a name="overview"></a>Panoramica
Nella pagina **Catalogo di backup** vengono visualizzati tutti i set di backup creati quando si eseguono backup manuali o automatizzati. È possibile utilizzare questa pagina per elencare tutti i backup per un criterio di backup o un volume, selezionare o eliminare i backup o utilizzare un backup per ripristinare o clonare un volume.

 ![Pagina catalogo di backup](./media/storsimple-restore-from-backup-set-u2/restore.png)

In questa esercitazione viene illustrato come utilizzare la pagina **Catalogo di backup** per ripristinare il dispositivo da un set di backup.

È possibile ripristinare un volume da un backup locale o cloud. In entrambi i casi, l'operazione di ripristino porta immediatamente il volume online, mentre i dati vengono scaricati in background. 

Prima di avviare un'operazione di ripristino, è necessario tenere presente quanto segue:

* **È necessario portare offline il volume**: portare offline il volume sia nel dispositivo che nell'host prima di avviare l'operazione di ripristino. Anche se l'operazione di ripristino riporta automaticamente online il volume nel dispositivo, è necessario portare il dispositivo online manualmente nell'host. È possibile portare online il volume nell'host non appena il volume è online sul dispositivo (non è necessario attendere fino al completamento dell'operazione di ripristino). Per le procedure, vedere [Portare un volume offline](storsimple-manage-volumes-u2.md#take-a-volume-offline).
* **Tipo di volume dopo il ripristino** : i volumi eliminati vengono ripristinati in base al tipo nello snapshot; ovvero i volumi aggiunti in locale vengono ripristinati come volumi aggiunti in locale e i volumi a livelli vengono ripristinati come volumi a livelli.
  
    Per i volumi esistenti, il tipo di uso corrente del volume sovrascrive il tipo che viene archiviato nello snapshot. Ad esempio, se si ripristina un volume da uno snapshot che è stato eseguito quando il tipo di volume era a livelli e tale tipo di volume è ora aggiunto in locale (a causa di un'operazione di conversione eseguita), il volume verrà ripristinato come volume aggiunto in locale. Analogamente, se un volume aggiunto in locale è stato espanso e successivamente ripristinato da uno snapshot meno recente eseguito quando il volume era di dimensioni inferiori, il volume ripristinato manterrà la dimensione espansa corrente.
  
    Non è possibile convertire un volume da un volume a livelli a un volume aggiunto in locale o da un volume aggiunto in locale a un volume a livelli mentre il volume è in fase di ripristino. Attendere il completamento dell'operazione di ripristino e quindi convertire il volume in un altro tipo. Per informazioni sulla conversione di un volume, vedere l'articolo relativo alla [modifica di un tipo di volume](storsimple-manage-volumes-u2.md#change-the-volume-type). 
* **La dimensione del volume si rifletterà nel volume ripristinato** : si tratta di un fattore importante se si vuole ripristinare un volume aggiunto in locale che è stato eliminato (perché viene eseguito il provisioning completo dei volumi aggiunti in locale). Verificare che sia disponibile spazio sufficiente prima di tentare di ripristinare un volume aggiunto in locale eliminato in precedenza. 
* **Durante il ripristino non è possibile espandere un volume** : attendere il completamento dell'operazione di ripristino prima di cercare di espandere il volume. Per altre informazioni sull'espansione di un volume, vedere [Modificare un volume](storsimple-manage-volumes-u2.md#modify-a-volume).
* **È possibile eseguire un backup mentre si sta ripristinando un volume locale** : per le procedure, vedere l'articolo dedicato all' [uso del servizio StorSimple Manager per gestire i criteri di backup](storsimple-manage-backup-policies.md).
* **È possibile annullare un'operazione di ripristino** : se si annulla il processo di ripristino, il volume verrà riportato allo stato in cui si trovava prima di avviare l'operazione di ripristino. Per le procedure, vedere [Annullare un processo](storsimple-manage-jobs-u2.md#cancel-a-job).

## <a name="how-to-use-the-backup-catalog"></a>Come utilizzare il catalogo di backup
La pagina **Catalogo di backup** fornisce una query che consente di limitare la selezione del set di backup. È possibile filtrare i set di backup che vengono recuperati in base ai parametri seguenti:

* **Dispositivo** : il dispositivo in cui è stato creato il set di backup.
* **Criterio di backup** o **Volume**: il criterio di backup o volume associato a questo set di backup.
* **Da** e **A**: intervallo di data e ora di creazione del set di backup.

I set di backup filtrati vengono quindi catalogati in base ai seguenti attributi:

* **Nome** : nome del criterio di backup o volume associato al set di backup.
* **Dimensioni** : dimensione effettiva del set di backup.
* **Creato il** : data e ora di creazione dei backup. 
* **Tipo** : i set di backup possono essere snapshot in locale o del cloud. Uno snapshot locale è un backup di tutti i dati di volume archiviati localmente sul dispositivo, mentre uno snapshot del cloud si riferisce al backup dei dati di volume che risiedono nel cloud. Gli snapshot in locale forniscono un accesso più rapido, mentre gli snapshot del cloud vengono scelti per la resilienza dei dati.
* **Avviato da** : i backup possono essere avviati automaticamente in base a una pianificazione o manualmente dall'utente. Per pianificare i backup, è possibile utilizzare un criterio di backup. In alternativa, è possibile utilizzare l’opzione **Esegui backup** per eseguire un backup interattivo.

## <a name="how-to-restore-your-storsimple-volume-from-a-backup"></a>Come ripristinare il volume di StorSimple da un backup
È possibile usare la pagina **Catalogo di backup** per ripristinare il volume StorSimple da un backup specifico. Tenere presente che, effettuando il ripristino, lo stato di un volume torna a quello in cui si trovava al momento dell'esecuzione del backup. Tutti i dati aggiunti dopo l'operazione di backup andranno persi.

> [!WARNING]
> Il ripristino da un backup sostituisce i volumi esistenti dal backup. Ciò può comportare la perdita dei dati scritti dopo l'esecuzione del backup.
> 
> 

### <a name="to-restore-your-volume"></a>Per ripristinare il volume
1. Nella pagina del servizio StorSimple Manager, fare clic sulla scheda **Catalogo di backup** .
   
    ![Catalogo di backup](./media/storsimple-restore-from-backup-set-u2/restore.png)
2. Procedura di selezione di un set di backup:
   
   1. Selezionare un dispositivo appropriato.
   2. Nell'elenco a discesa, scegliere il volume o il criterio di backup per il backup che si desidera selezionare.
   3. Specificare l'intervallo di tempo.
   4. Fare clic sull'icona del segno di spunta  ![icona del segno di spunta](./media/storsimple-restore-from-backup-set-u2/HCS_CheckIcon.png)  per eseguire la query.
      
      I backup associati al volume selezionato o al criterio di backup dovrebbero essere visualizzati nell'elenco dei set di backup.
3. Espandere il set di backup per visualizzare i volumi associati. Questi volumi devono essere disconnessi nell'host e nel dispositivo prima di poterli ripristinare. Accedere ai volumi sulla pagina **Contenitori di volumi** , quindi attenersi alla procedura descritta in [Disattivare un volume](storsimple-manage-volumes-u2.md#take-a-volume-offline) per disattivarli.
   
   > [!IMPORTANT]
   > Assicurarsi, in primo luogo, di aver disattivato tutti i volumi sull'host, prima di disattivarli sul dispositivo. Se non si eseguono i volumi offline nell'host, si potrebbe verificare un danneggiamento dei dati.
   > 
   > 
4. Tornare alla scheda **Catalogo di backup** e selezionare un set di backup.
5. Fare clic su **Ripristina** nella parte inferiore della pagina.
6. Verrà richiesto di confermare. Esaminare le informazioni di ripristino e quindi selezionare la casella di controllo di conferma.
   
    ![Pagina di conferma](./media/storsimple-restore-from-backup-set-u2/ConfirmRestore.png)
7. Fare clic sull'icona del segno di spunta ![icona del segno di spunta](./media/storsimple-restore-from-backup-set-u2/HCS_CheckIcon.png). In questo modo viene avviato un processo di ripristino che è possibile visualizzare accedendo alla pagina **Processi** . 
8. Dopo aver completato il ripristino, è possibile verificare che i contenuti dei volumi siano sostituiti dai volumi dal backup.

![Video disponibile](./media/storsimple-restore-from-backup-set-u2/Video_icon.png) **Video disponibile**

Per guardare un video che illustra come è possibile usare le funzionalità di clonazione e ripristino in StorSimple per ripristinare file eliminati, fare clic [qui](https://azure.microsoft.com/documentation/videos/storsimple-recover-deleted-files-with-storsimple/).

## <a name="if-the-restore-fails"></a>Se il ripristino ha esito negativo
Se l'operazione di ripristino ha esito negativo per qualsiasi motivo, si riceverà un avviso. Se questo si verifica, aggiornare l'elenco dei backup per verificare che il backup sia ancora valido. Se il backup è valido e si esegue il ripristino dal cloud, il problema potrebbe essere causato da problemi di connettività. 

Per completare l'operazione di ripristino, portare offline il volume nell'host e ripetere l'operazione di ripristino. Si noti che eventuali modifiche eseguite sui dati del volume durante il processo di ripristino andranno perse.

## <a name="next-steps"></a>Passaggi successivi
* Informazioni su come [gestire i volumi StorSimple](storsimple-manage-volumes-u2.md).
* Informazioni su come [usare il servizio StorSimple Manager per gestire il dispositivo StorSimple](storsimple-manager-service-administration.md).




<!--HONumber=Nov16_HO3-->


