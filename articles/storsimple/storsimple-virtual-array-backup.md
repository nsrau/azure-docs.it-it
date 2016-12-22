---
title: Esercitazione sul backup dell&quot;array virtuale Microsoft Azure StorSimple | Documentazione Microsoft
description: Si illustra come eseguire il backup delle condivisioni e dei volumi di StorSimple Virtual Array.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: e3cdcd9e-33b1-424d-82aa-b369d934067e
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/21/2016
ms.author: alkohli
translationtype: Human Translation
ms.sourcegitcommit: 5c31ac5284f3ba8fecc1e9f33838a778254658e5
ms.openlocfilehash: 2e49565f2696e345d83fcff4d60a4f9a2b9ff39e

---
# <a name="back-up-your-storsimple-virtual-array"></a>Backup dell'array virtuale StorSimple

## <a name="overview"></a>Panoramica

StorSimple Virtual Array è un dispositivo virtuale locale di archiviazione cloud ibrido che può essere configurato come file server o server iSCSI. L'array virtuale consente all'utente di creare backup pianificati e manuali di tutte le condivisioni o di tutti i volumi nel dispositivo. Quando viene configurato come file server, consente anche il ripristino a livello di elemento. In questa esercitazione viene descritto come creare backup pianificati e manuali ed eseguire il ripristino a livello di elemento per recuperare un file eliminato nell'array virtuale.

Le informazioni contenute in questa esercitazione si applicano solo all'array virtuale StorSimple. Per informazioni sulla serie 8000, vedere l'articolo sulla [creazione di un backup per un dispositivo serie 8000](storsimple-manage-backup-policies-u2.md)

## <a name="back-up-shares-and-volumes"></a>Backup di condivisioni e volumi

I backup garantiscono la protezione temporizzata, migliorano la recuperabilità e riducono al minimo i tempi di ripristino per le condivisioni e i volumi. È possibile eseguire il backup di una condivisione o di un volume sul dispositivo StorSimple in due modi: **Pianificato** o **Manuale**. Ognuno di questi metodi è descritto nelle sezioni seguenti.

## <a name="change-the-backup-start-time"></a>Modificare l'ora di inizio dei backup

> [!NOTE]
> In questa versione, i backup pianificati vengono creati secondo un criterio predefinito che esegue il backup ogni giorno in un orario specificato di tutte le condivisioni o di tutti i volumi nel dispositivo. Al momento non è possibile creare criteri personalizzati per i backup pianificati.


L'array virtuale StorSimple dispone di un criterio di backup predefinito che si avvia in un orario specifico (22:30) ed esegue il backup di tutte le condivisioni o di tutti i volumi nel dispositivo una volta al giorno. È possibile modificare l'ora di avvio del backup, ma la frequenza e il periodo di conservazione (che specificano il numero di backup da conservare) non possono essere modificati. Questi backup riguardano l'intero dispositivo virtuale. Ciò potrebbe potenzialmente influire sulle prestazioni del dispositivo, interessando i carichi di lavoro distribuiti al suo interno. È pertanto consigliabile pianificare queste operazioni in orari di scarso traffico.

 Eseguire la procedura seguente nel [portale di Azure ](https://portal.azure.com/) per modificare l'ora di inizio predefinita del backup.

#### <a name="to-change-the-start-time-for-the-default-backup-policy"></a>Per modificare l'ora di inizio del criterio di backup predefinito

1. Passare a **Dispositivi**. Verrà visualizzato l'elenco dei dispositivi registrati con il servizio Gestione dispositivi StorSimple. 
   
    ![passare a Dispositivi](./media/storsimple-virtual-array-backup/changebuschedule1.png)

2. Selezionare e fare clic sul dispositivo. Viene visualizzato il pannello **Impostazioni**. Passare a **Gestisci > Criteri di backup**.
   
    ![selezionare il dispositivo](./media/storsimple-virtual-array-backup/changebuschedule2.png)

3. Nel pannello **Criteri di backup** l'ora di inizio predefinita è 22:30. È possibile specificare la nuova ora di inizio per la pianificazione giornaliera nel fuso orario del dispositivo.
   
    ![passare a Criteri di backup](./media/storsimple-virtual-array-backup/changebuschedule5.png)

4. Fare clic su **Salva**.

### <a name="take-a-manual-backup"></a>Creazione di un backup manuale

Oltre ai backup pianificati, è possibile eseguire un backup manuale (su richiesta) dei dati del dispositivo in qualsiasi momento.

#### <a name="to-create-a-manual-backup"></a>Per creare un backup manuale

1. Passare a **Dispositivi**. Selezionare il dispositivo e fare clic con il pulsante destro del mouse su **...** all'estrema destra nella riga selezionata. Dal menu di scelta rapida selezionare **Esegui backup**.
   
    ![passare a Esegui backup](./media/storsimple-virtual-array-backup/takebackup1m.png)

2. Nel pannello **Esegui backup** fare clic su **Esegui backup**. In questo modo verrà eseguito il backup di tutte le condivisioni sul file server o di tutti i volumi sul server iSCSI. 
   
    ![avvio di backup](./media/storsimple-virtual-array-backup/takebackup2m.png)
   
    Viene avviato un backup su richiesta e visualizzata una notifica indicante che il processo di backup è in corso.
   
    ![avvio di backup](./media/storsimple-virtual-array-backup/takebackup3m.png) 
   
    Al termine del processo, viene visualizzata una nuova notifica. Quindi si avvia il processo di backup.
   
    ![processo di backup creato](./media/storsimple-virtual-array-backup/takebackup4m.png)

3. Per tenere traccia dell'avanzamento dei backup ed esaminare i dettagli del processo, fare clic sulla notifica. In questo modo si passa a **Dettagli processo**.
   
     ![dettagli del processo di backup](./media/storsimple-virtual-array-backup/takebackup5m.png)

4. Al termine del backup, passare a **Gestione > Catalogo di backup**. Verrà visualizzato uno snapshot cloud di tutte le condivisioni (o i volumi) sul dispositivo.
   
    ![Backup completato](./media/storsimple-virtual-array-backup/takebackup19m.png) 

## <a name="view-existing-backups"></a>Visualizzare i backup esistenti
Per visualizzare i backup esistenti, eseguire la procedura seguente nel portale di Azure.

#### <a name="to-view-existing-backups"></a>Per visualizzare i backup esistenti

1. Passare al pannello **Dispositivi**. Selezionare e fare clic sul dispositivo. Nel pannello **Impostazioni** passare a **Gestione > Catalogo di backup**.
   
    ![passare a Catalogo di backup](./media/storsimple-virtual-array-backup/viewbackups1.png)
2. Specificare i criteri seguenti da usare per il filtro:
   
    - **Intervallo di tempo**: può essere **Ultima ora**, **Ultime 24 ore**, **Ultimi 7 giorni**, **Ultimi 30 giorni**, **Ultimo anno** e **Data personalizzata**.
    
    - **Dispositivi**: selezionare dall'elenco dei file server o server iSCSI registrati con il servizio Gestione dispositivi StorSimple.
   
    - **Avviato**: può essere **Pianificato** automaticamente tramite criteri di backup o avviato **Manualmente** dall'utente.
   
    ![Filtro backup](./media/storsimple-virtual-array-backup/viewbackups2.png)

3. Fare clic su **Apply**. Viene visualizzato l'elenco filtrato dei backup nel pannello **Catalogo di backup**. Si noti che è possibile visualizzare solo 100 elementi di backup alla volta.
   
    ![Catalogo di backup aggiornato](./media/storsimple-virtual-array-backup/viewbackups3.png)

## <a name="next-steps"></a>Passaggi successivi

Scoprire di più su come [amministrazione StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).




<!--HONumber=Nov16_HO4-->


