---
title: Catalogo di backup di StorSimple Snapshot Manager | Microsoft Docs
description: Descrive come utilizzare lo snap-in MMC StorSimple Snapshot Manager per visualizzare e gestire il catalogo di backup.
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: 
ms.assetid: 6abdbfd2-22ce-45a5-aa15-38fae4c8f4ec
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: v-sharos
ms.openlocfilehash: b97753e6f1b67e3c8d247281c5e5208033a56eca
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="use-storsimple-snapshot-manager-to-manage-the-backup-catalog"></a>Uso di StorSimple Snapshot Manager per gestire il catalogo di backup

## <a name="overview"></a>Panoramica
La funzione principale di StorSimple Snapshot Manager è quella di consentire la creazione di copie di backup coerenti con l'applicazione dei volumi StorSimple sotto forma di snapshot. Gli snapshot vengono quindi elencati in un file XML denominato *catalogo di backup*. Il catalogo di backup consente di organizzare gli snapshot per gruppo di volumi e quindi per snapshot locali o cloud.

In questa esercitazione viene descritto come utilizzare il nodo del **catalogo di backup** per completare le attività seguenti:

* Ripristino di un volume
* Clonazione di un volume o di un gruppo di volumi
* Eliminazione di un backup
* Recupero di un file
* Ripristino del database di StorSimple Snapshot Manager

È possibile visualizzare il catalogo di backup espandendo il nodo del **catalogo di backup** nel riquadro **Ambito**, quindi espandendo il gruppo di volumi.

* Se si fa clic sul nome del gruppo di volumi, nel riquadro **Risultati** viene mostrato il numero di snapshot locali e cloud disponibili per il gruppo di volumi. 
* Se si fa clic su **Snapshot locale** o **Snapshot cloud** nel riquadro **Risultati** vengono mostrate le informazioni seguenti per ciascun snapshot di backup (a seconda delle impostazioni di **Visualizza**):
  
  * **Nome** : l'ora di creazione dello snapshot.
  * **Tipo** : se si tratta di uno snapshot locale o cloud.
  * **Proprietario** : il proprietario del contenuto. 
  * **Disponibile** : se lo snapshot è attualmente disponibile. **True** indica che lo snapshot è disponibile e può essere ripristinato, **False** indica che lo snapshot non è più disponibile. 
  * **Importato** : se il backup è stato importato. **True** indica che il backup è stato importato dal servizio Gestione dispositivi StorSimple nel momento in cui il dispositivo è stato configurato in StorSimple Snapshot Manager, **False** indica che non è stato importato, ma è stato creato da StorSimple Snapshot Manager. (È possibile identificare facilmente un gruppo di volumi importati perché viene aggiunto un suffisso che identifica il dispositivo da cui è stato importato il gruppo di volumi.)
    
    ![Catalogo di backup](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Backup_catalog.png)
* Se si espande **Snapshot locale** o **Snapshot cloud**, quindi si fa clic sul nome di un singolo snapshot, nel riquadro **Risultati** vengono mostrate le informazioni seguenti sullo snapshot selezionato:
  
  * **Nome** : il volume identificato dalla lettera di unità. 
  * **Nome locale** : il nome locale dell'unità (se disponibile). 
  * **Dispositivo** : il nome del dispositivo su cui risiede il volume. 
  * **Disponibile** : se lo snapshot è attualmente disponibile. **True** indica che lo snapshot è disponibile e può essere ripristinato, **False** indica che lo snapshot non è più disponibile. 

## <a name="restore-a-volume"></a>Ripristino di un volume
Utilizzare la procedura seguente per ripristinare un volume dal backup.

#### <a name="prerequisites"></a>Prerequisiti
Se questa operazione non è già stata eseguita, creare un volume e un gruppo di volumi, quindi eliminare il volume. Per impostazione predefinita, StorSimple Snapshot Manager esegue il backup di un volume prima di consentirne l’eliminazione. Questa precauzione permette di evitare la perdita di dati se il volume viene eliminato involontariamente o se i dati devono essere recuperati per qualsiasi motivo. 

Durante la creazione del backup precauzionale, in StorSimple Snapshot Manager viene visualizzato il messaggio seguente.

![Messaggio di snapshot automatico](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Automatic_snap.png) 

> [!IMPORTANT]
> Non è possibile eliminare un volume che fa parte di un gruppo di volumi. L'opzione di eliminazione non è disponibile. <br>
> 
> 

#### <a name="to-restore-a-volume"></a>Per ripristinare un volume
1. Fare clic sull’icona del desktop per avviare StorSimple Snapshot Manager. 
2. Nel riquadro **Ambito** espandere il nodo del **catalogo di backup**, espandere un gruppo di volumi, quindi fare clic su **Snapshot locali** o **Snapshot cloud**. Nel riquadro **Risultati** , viene visualizzato un elenco di snapshot di backup.
3. Individuare il backup che si desidera ripristinare, fare clic su di esso con il pulsante destro del mouse, quindi fare clic su **Ripristina**.
   
    ![Ripristino del catalogo di backup](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Restore_BU_catalog.png) 
4. Nella pagina di conferma esaminare i dettagli digitare **Conferma**, quindi fare clic su **OK**. StorSimple Snapshot Manager usa il backup per ripristinare il volume.
   
    ![Ripristino del messaggio di conferma](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Restore_volume_msg.png) 
5. È possibile monitorare l'azione di ripristino durante l'esecuzione. Nel riquadro **Ambito** espandere il nodo **Processi**, quindi fare clic su **In esecuzione**. Nel riquadro **Risultati** , vengono visualizzati i dettagli del processo. Al termine del processo di ripristino, i dettagli del processo vengono trasferiti all’elenco **Ultime 24 ore** .

## <a name="clone-a-volume-or-volume-group"></a>Clonazione di un volume o di un gruppo di volumi
Utilizzare la procedura seguente per creare un duplicato (clone) di un volume o di un gruppo di volumi.

#### <a name="to-clone-a-volume-or-volume-group"></a>Per clonare un volume o un gruppo di volumi
1. Fare clic sull’icona del desktop per avviare StorSimple Snapshot Manager.
2. Nel riquadro **Ambito** espandere il nodo del **catalogo di backup**, espandere un gruppo di volumi, quindi fare clic su **Snapshot cloud**. Nel riquadro **Risultati** , viene visualizzato un elenco dei backup.
3. Individuare il volume o il gruppo di volumi che si desidera clonare, fare clic con il pulsante destro del mouse sul nome del volume o del gruppo di volumi, quindi fare clic su **Clona**. Viene visualizzata la finestra di dialogo **Clona snapshot cloud** .
   
    ![Clonazione di uno snapshot cloud](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Clone.png) 
4. Completare la finestra di dialogo **Clona snapshot cloud** nel modo seguente: 
   
   1. Nella casella di testo **Nome** , digitare un nome per il volume clonato. Questo nome verrà visualizzato nel nodo **Volumi** . 
   2. (Facoltativo) Selezionare **Unità**, quindi selezionare la lettera di un’unità dall'elenco a discesa.
   3. (Facoltativo) Selezionare **Cartella (NTFS)**, quindi digitare il percorso di una cartella o fare clic su Sfoglia e selezionare un percorso per la cartella. 
   4. Fare clic su **Crea**.
5. Al termine del processo di clonazione, è necessario inizializzare il volume clonato. Avviare Server Manager, quindi avviare Gestione disco. Per istruzioni dettagliate, vedere [Montaggio volumi](storsimple-snapshot-manager-manage-volumes.md#mount-volumes). Al termine dell'inizializzazione, il volume verrà elencato sotto il nodo **Volumi** nel riquadro **Ambito**. Se non viene visualizzato il volume elencato, aggiornare l'elenco dei volumi (fare clic con il pulsante destro del mouse sul nodo **Volumi**, quindi fare clic su **Aggiorna**).

## <a name="delete-a-backup"></a>Eliminazione di un backup
Utilizzare la procedura seguente per eliminare uno snapshot dal catalogo di backup. 

> [!NOTE]
> L’eliminazione di uno snapshot comporta la rimozione dei dati di cui è stato eseguito il backup associati allo snapshot. Tuttavia, il processo di pulizia dei dati dal cloud potrebbe richiedere alcuni minuti.<br>


#### <a name="to-delete-a-backup"></a>Per eliminare un backup
1. Fare clic sull’icona del desktop per avviare StorSimple Snapshot Manager.
2. Nel riquadro **Ambito** espandere il nodo del **catalogo di backup**, espandere un gruppo di volumi, quindi fare clic su **Snapshot locali** o **Snapshot cloud**. Nel riquadro **Risultati** , viene visualizzato un elenco di snapshot.
3. Fare clic con il pulsante destro del mouse sullo snapshot che si desidera eliminare, quindi fare clic su **Elimina**.
4. Quando viene visualizzato il messaggio di conferma, fare clic su **OK**.

## <a name="recover-a-file"></a>Recupero di un file
Se un file viene accidentalmente eliminato da un volume, è possibile recuperare il file utilizzando uno snapshot che predata l’eliminazione per creare un clone del volume, quindi copiando il file dal volume clonato sul volume originale.

#### <a name="prerequisites"></a>Prerequisiti
Prima di iniziare, assicurarsi di disporre di un backup corrente del gruppo di volumi. Quindi, eliminare un file archiviato in uno dei volumi del gruppo. Infine, utilizzare la procedura seguente per ripristinare il file eliminato dal backup. 

#### <a name="to-recover-a-deleted-file"></a>Per recuperare un file eliminato
1. Fare clic sull’icona StorSimple Snapshot Manager sul desktop. Viene visualizzata la finestra della console di StorSimple Snapshot Manager. 
2. Nel riquadro **Ambito** espandere il nodo del **catalogo di backup** e passare a uno snapshot che contiene il file eliminato. In genere, è consigliabile selezionare uno snapshot creato poco prima dell'eliminazione.
3. Individuare il volume che si desidera clonare, fare clic con il pulsante destro del mouse, quindi fare clic su **Clona**. Viene visualizzata la finestra di dialogo **Clona snapshot cloud** .
   
    ![Clonazione di uno snapshot cloud](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Clone.png) 
4. Completare la finestra di dialogo **Clona snapshot cloud** nel modo seguente: 
   
   1. Nella casella di testo **Nome** , digitare un nome per il volume clonato. Questo nome verrà visualizzato nel nodo **Volumi** . 
   2. (Facoltativo) Selezionare **Unità**, quindi selezionare la lettera di un’unità dall'elenco a discesa. 
   3. (Facoltativo) Selezionare **Cartella (NTFS)**, quindi digitare il percorso di una cartella o fare clic su **Sfoglia** e selezionare un percorso per la cartella. 
   4. Fare clic su **Create**. 
5. Al termine del processo di clonazione, è necessario inizializzare il volume clonato. Avviare Server Manager, quindi avviare Gestione disco. Per istruzioni dettagliate, vedere [Montaggio volumi](storsimple-snapshot-manager-manage-volumes.md#mount-volumes). Al termine dell'inizializzazione, il volume verrà elencato sotto il nodo **Volumi** nel riquadro **Ambito**. 
   
    Se non viene visualizzato il volume elencato, aggiornare l'elenco dei volumi (fare clic con il pulsante destro del mouse sul nodo **Volumi**, quindi fare clic su **Aggiorna**).
6. Aprire la cartella NTFS che contiene il volume clonato, espandere il nodo **Volumi** , quindi aprire il volume clonato. Individuare il file che si desidera recuperare e copiarlo sul volume principale.
7. Dopo aver ripristinato il file, è possibile eliminare la cartella NTFS che contiene il volume clonato.

## <a name="restore-the-storsimple-snapshot-manager-database"></a>Ripristino del database di StorSimple Snapshot Manager
È consigliabile eseguire regolarmente il backup del database di StorSimple Snapshot Manager sul computer host. Se si verifica un'emergenza o viene riscontrato un errore nel computer host per qualsiasi motivo, è possibile ripristinarlo dal backup. La creazione del backup del database è un processo manuale.

#### <a name="to-back-up-and-restore-the-database"></a>Per eseguire il backup e il ripristino del database
1. Arrestare il servizio di gestione di Microsoft StorSimple:
   
   1. Avviare Server Manager.
   2. Nel dashboard di Server Manager, selezionare **Servizi** dal menu **Strumenti**.
   3. Nella finestra **Servizi** selezionare **Microsoft StorSimple Management Service**.
   4. Nel riquadro a destra, sotto **Microsoft StorSimple Management Service**, fare clic su **Arresta il servizio**.
2. Nel computer host, passare a C:\ProgramData\Microsoft\StorSimple\BACatalog. 
   
   > [!NOTE]
   > ProgramData è una cartella nascosta.
   > 
   > 
3. Individuare il file XML di catalogo, copiare il file e archiviare la copia in un luogo sicuro o nel cloud. Se viene riscontrato un errore nell’host, è possibile utilizzare questo file di backup per recuperare i criteri di backup creati in StorSimple Snapshot Manager.
   
    ![File del catalogo di backup di Azure StorSimple](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_bacatalog.png)
4. Riavviare il servizio di gestione di Microsoft StorSimple: 
   
   1. Nel dashboard di Server Manager, selezionare **Servizi** dal menu **Strumenti**.
   2. Nella finestra **Servizi** selezionare **Microsoft StorSimple Management Service**.
   3. Nel riquadro a destra, sotto **Microsoft StorSimple Management Service**, fare clic su **Riavvia il servizio**.
5. Nel computer host, passare a C:\ProgramData\Microsoft\StorSimple\BACatalog. 
6. Eliminare il file XML del catalogo e sostituirlo con la versione di backup creata. 
7. Fare clic sull'icona di StorSimple Snapshot Manager del desktop per avviare StorSimple Snapshot Manager. 

## <a name="next-steps"></a>Passaggi successivi
* Ulteriori informazioni sull’ [utilizzo di StorSimple Snapshot Manager per amministrare la soluzione di StorSimple](storsimple-snapshot-manager-admin.md).
* Ulteriori informazioni sulle [attività e sui flussi di lavoro di StorSimple Snapshot Manager](storsimple-snapshot-manager-admin.md#storsimple-snapshot-manager-tasks-and-workflows).

