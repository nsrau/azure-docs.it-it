---
title: Ripristinare un volume StorSimple dal backup | Microsoft Docs
description: Viene illustrato come utilizzare la pagina del catalogo di backup del servizio StorSimple Manager per ripristinare un volume StorSimple da un set di backup.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''

ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 08/17/2016
ms.author: alkohli

---
# Ripristinare un volume StorSimple da un set di backup
[!INCLUDE [storsimple-version-selector-restore-from-backup](../../includes/storsimple-version-selector-restore-from-backup.md)]

## Overview
Nella pagina **Catalogo di backup** vengono visualizzati tutti i set di backup creati quando si eseguono backup manuali o automatizzati. È possibile utilizzare questa pagina per elencare tutti i backup per un criterio di backup o un volume, selezionare o eliminare i backup o utilizzare un backup per ripristinare o clonare un volume.

 ![Pagina catalogo di backup](./media/storsimple-restore-from-backup-set/HCS_BackupCatalog.png)

Questa esercitazione illustra come usare la pagina **Catalogo backup** per ripristinare un volume sul dispositivo da un set di backup.

## Come utilizzare il catalogo di backup
La pagina **Catalogo di backup** fornisce una query che consente di limitare la selezione del set di backup. È possibile filtrare i set di backup che vengono recuperati in base ai parametri seguenti:

* **Dispositivo**: il dispositivo in cui è stato creato il set di backup.
* **Criterio di backup** o **Volume**: il criterio di backup o volume associato a questo set di backup.
* **Da** e **A**: intervallo di data e ora di creazione del set di backup.

I set di backup filtrati vengono quindi catalogati in base ai seguenti attributi:

* **Nome**: nome del criterio di backup o volume associato al set di backup.
* **Dimensioni**: dimensione effettiva del set di backup.
* **Creato il**: data e ora di creazione dei backup.
* **Tipo**: i set di backup possono essere snapshot in locale o del cloud. Uno snapshot locale è un backup di tutti i dati di volume archiviati localmente sul dispositivo, mentre uno snapshot del cloud si riferisce al backup dei dati di volume che risiedono nel cloud. Gli snapshot in locale forniscono un accesso più rapido, mentre gli snapshot del cloud vengono scelti per la resilienza dei dati.
* **Avviato da**: i backup possono essere avviati automaticamente in base a una pianificazione o manualmente dall'utente. Per pianificare i backup, è possibile utilizzare un criterio di backup. In alternativa, è possibile utilizzare l’opzione **Esegui backup** per eseguire un backup interattivo.

## Come ripristinare il volume di StorSimple da un backup
È possibile usare la pagina **Catalogo di backup** per ripristinare il volume StorSimple da un backup specifico.

> [!WARNING]
> Il ripristino da un backup sostituisce i volumi esistenti dal backup. Ciò può comportare la perdita dei dati scritti dopo l'esecuzione del backup.
> 
> 

Prima di avviare un ripristino su un volume, assicurarsi che il volume sia offline. È necessario portare offline il volume prima nell'host e poi sul dispositivo. Seguire la procedura illustrata in [Portare un volume offline](storsimple-manage-volumes.md#take-a-volume-offline). Eseguire i passaggi seguenti per ripristinare un volume da un set di backup.

### Per effettuare il ripristino da un set di backup
1. Nella pagina del servizio StorSimple Manager, fare clic sulla scheda **Catalogo di backup**.
   
    ![Catalogo di backup](./media/storsimple-restore-from-backup-set/HCS_Restore.png)
2. Procedura di selezione di un set di backup:
   
   1. Selezionare un dispositivo appropriato.
   2. Nell'elenco a discesa, scegliere il volume o il criterio di backup per il backup che si desidera selezionare.
   3. Specificare l'intervallo di tempo.
   4. Fare clic sull'icona del segno di spunta ![icona del segno di spunta](./media/storsimple-restore-from-backup-set/HCS_CheckIcon.png) per eseguire la query.
      
      I backup associati al volume selezionato o al criterio di backup dovrebbero essere visualizzati nell'elenco dei set di backup.
3. Espandere il set di backup per visualizzare i volumi associati. Questi volumi devono essere disconnessi nell'host e nel dispositivo prima di poterli ripristinare. Seguire la procedura illustrata in [Portare un volume offline](storsimple-manage-volumes.md#take-a-volume-offline).
   
   > [!IMPORTANT]
   > Assicurarsi, in primo luogo, di aver disattivato tutti i volumi sull'host, prima di disattivarli sul dispositivo. Se non si eseguono i volumi offline nell'host, si potrebbe verificare un danneggiamento dei dati.
   > 
   > 
4. Selezionare un set di backup. Fare clic su **Ripristina** nella parte inferiore della pagina.
5. Verrà richiesto di confermare.
   
    ![Pagina di conferma](./media/storsimple-restore-from-backup-set/HCS_ConfirmRestore.png)
6. Esaminare le informazioni di ripristino e fare clic sull'icona di segno di spunta![icona del segno di spunta](./media/storsimple-restore-from-backup-set/HCS_CheckIcon.png). In questo modo viene avviato un processo di ripristino che è possibile visualizzare accedendo alla pagina **Processi**.
7. Dopo aver completato il ripristino, è possibile verificare che i contenuti dei volumi siano sostituiti dai volumi dal backup.

![Video disponibile](./media/storsimple-restore-from-backup-set/Video_icon.png) **Video disponibile**

Per guardare un video che illustra come è possibile utilizzare le funzionalità di copia e ripristino di StorSimple per ripristinare file eliminati, fare clic [qui](https://azure.microsoft.com/documentation/videos/storsimple-recover-deleted-files-with-storsimple/).

## Passaggi successivi
* Informazioni su come [gestire i volumi StorSimple](storsimple-manage-volumes.md).
* Informazioni su come [usare il servizio StorSimple Manager per gestire il dispositivo StorSimple](storsimple-manager-service-administration.md).

<!---HONumber=AcomDC_0824_2016-->