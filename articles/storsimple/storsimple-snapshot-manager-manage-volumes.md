---
title: StorSimple Snapshot Manager e volumi | Documentazione Microsoft
description: Illustra come usare lo snap-in MMC StorSimple Snapshot Manager per visualizzare e gestire i volumi e per configurare i backup.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 78896323-e57c-431e-bbe2-0cbde1cf43a2
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 04/18/2016
ms.author: alkohli
ms.openlocfilehash: 309fa85d0a4d877522a89dd8f1e6e71fb2074744
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94964967"
---
# <a name="use-storsimple-snapshot-manager-to-view-and-manage-volumes"></a>Utilizzare StorSimple Snapshot Manager per visualizzare e gestire i volumi
## <a name="overview"></a>Panoramica
È possibile usare il nodo **Volumi** di StorSimple Snapshot Manager (nel riquadro **Ambito**) per selezionare i volumi e visualizzare le relative informazioni. I volumi vengono presentati come unità che corrispondono ai volumi montati dall'host. Il nodo **Volumi** indica i volumi locali e i tipi di volume supportati da StorSimple, compresi i volumi individuati tramite l'uso di iSCSI e di un dispositivo. 

Per altre informazioni sui volumi supportati, passare al [supporto per più tipi di volume](storsimple-what-is-snapshot-manager.md#support-for-multiple-volume-types).

![Elenco volumi nel riquadro dei Risultati](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Volume_node.png)

Il nodo **Volumi** consente inoltre di ripetere l'analisi o eliminare i volumi dopo l'individuazione da parte di StorSimple Snapshot Manager. 

Questo tutorial spiega come montare, inizializzare e formattare volumi e quindi usare StorSimple Snapshot Manager per:

* Visualizzare informazioni sui volumi 
* Eliminare volumi
* Ripetere la scansione dei volumi 
* Configurare un volume di base e una copia di backup
* Configurare un volume con mirroring dinamico e una copia di backup

> [!NOTE]
> Tutte le azioni del nodo **Volumi** sono disponibili anche nel menu **Azioni**.
> 
> 

## <a name="mount-volumes"></a>Montare i volumi
Usare la procedura seguente per montare, inizializzare e formattare volumi StorSimple. Questa procedura usa Gestione disco, un'utilità di sistema per la gestione dei dischi rigidi e dei volumi o delle partizioni corrispondenti. Per altre informazioni su Gestione disco, vedere [Gestione Disco](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770943(v=ws.11)) nel sito Web Microsoft TechNet.

#### <a name="to-mount-volumes"></a>Montare volumi
1. Sul computer, avviare iSCSI Microsoft Initiator.
2. Specificare uno degli indirizzi IP di interfaccia come portale di destinazione o indirizzo IP di individuazione e connettersi al dispositivo. Dopo che il dispositivo è connesso, i volumi sono accessibili nel sistema di Windows. Per altre informazioni sull'uso dell'iniziatore iSCSI Microsoft, vedere la sezione "Connecting to an iSCSI target device" (Connessione a un dispositivo di destinazione iSCSI) in [Installing and Configuring Microsoft iSCSI Initiator][1] (Installazione e configurazione dell'iniziatore iSCSI Microsoft).
3. Per avviare Gestione Disco, usare una delle opzioni seguenti:
   
   * DigitareDiskmgmt.msc nella casella **Esegui**.
   * Avviare Server Manager, espandere il nodo **Archiviazione**, quindi selezionare **Gestione disco**.
   * Avviare **Strumenti di amministrazione**, espandere il nodo **Gestione computer** e quindi selezionare **Gestione disco**. 
     
     > [!NOTE]
     > È necessario usare i privilegi di amministratore per eseguire Gestione disco.
     > 
     > 
4. Portare i volumi online:
   
   1. In Gestione disco fare clic con il pulsante destro del mouse su un volume contrassegnato con **Offline**.
   2. Fare clic su **Riattiva disco specificato**. Dopo la riattivazione, il disco deve essere contrassegnato con **Online**.
5. Inizializzare il/i volume/i:
   
   1. Fare clic con il pulsante destro del mouse sui volumi individuati.
   2. Scegliere **Inizializza disco** dal menu.
   3. Nella finestra di dialogo **Inizializza disco** selezionare i dischi da inizializzare, quindi fare clic su **OK**.
6. Formattare volumi semplici:
   
   1. Fare clic con il pulsante destro del mouse su un volume da formattare.
   2. Scegliere **Nuovo volume semplice**.
   3. Usare la procedura guidata Nuovo Volume Semplice per formattare il volume:
      
      * Specificare le dimensioni del volume.
      * Specificare una lettera di unità.
      * Selezionare il file system NTFS.
      * Specificare una dimensione unità di allocazione pari a 64 KB.
      * Eseguire una formattazione veloce.
7. Formattare volumi con più partizioni. Per istruzioni, vedere la sezione "Partizioni e Volumi" in [Esecuzione Gestione Disco](/previous-versions/tn-archive/dd163556(v=technet.10)).

## <a name="view-information-about-your-volumes"></a>Visualizzare informazioni sui volumi
Utilizzare la procedura seguente per visualizzare informazioni sui volumi locali e di Azure StorSimple di Azure.

#### <a name="to-view-volume-information"></a>Per visualizzare le informazioni del volume
1. Fare clic sull’icona del desktop per avviare StorSimple Snapshot Manager. 
2. Nel riquadro **Ambito** fare clic sul nodo **Volumi**. Viene visualizzato un elenco di volumi locali e montati, compresi tutti i volumi StorSimple di Azure, nel **riquadro** Risultati. Le colonne nel **riquadro** Risultati sono configurabili. Fare clic con il pulsante destro del mouse sul nodo **volumi** , selezionare **Visualizza**, quindi selezionare **Aggiungi/Rimuovi colonne**.
   
    ![Configurare le colonne](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_View_volumes.png)
   
   | Colonna risultati | Description |
   |:--- |:--- |
   |  Nome |La colonna **Nome** contiene la lettera di unità assegnata a ogni volume individuato. |
   |  Dispositivo |La **colonna** dispositivo contiene l'indirizzo IP del dispositivo connesso al computer. |
   |  Nome Dispositivo Volume |La colonna **Nome volume dispositivo** contiene il nome del volume del dispositivo cui appartiene il volume selezionato. Questo è il nome del volume definito nel portale di Azure per il volume specifico. |
   |  Percorsi di accesso |Nella colonna **Percorsi di accesso** viene visualizzato il percorso di accesso al volume. Si tratta del punto di montaggio o lettera di unità in cui il volume è accessibile nel computer. |

## <a name="delete-a-volume"></a>Eliminare un volume
Utilizzare la procedura seguente per eliminare un volume da StorSimple Snapshot Manager.

> [!NOTE]
> Non è possibile eliminare un volume se fa parte di un gruppo di volumi. L'opzione Elimina non è disponibile per i volumi che sono membri di un gruppo di volumi. Per eliminare il volume, è necessario eliminare l'intero gruppo di volumi.

#### <a name="to-delete-a-volume"></a>Per eliminare un volume
1. Fare clic sull’icona del desktop per avviare StorSimple Snapshot Manager.
2. Nel riquadro **Ambito** fare clic sul nodo **Volumi**. 
3. Nel riquadro **Risultati** fare clic con il pulsante destro del mouse sul volume da eliminare.
4. Scegliere **Elimina** dal menu. 
   
    ![Eliminare un volume](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Delete_volume.png) 
5. Viene visualizzata la finestra di dialogo **Elimina volume**. Nella casella di testo digitare **Conferma**, quindi fare clic su **OK**.
6. Come impostazione predefinita, StorSimple Snapshot Manager esegue il backup di un volume prima di eliminarlo. Questa precauzione consente di proteggere dalla perdita di dati se l'eliminazione è accidentale. StorSimple Snapshot Manager, consente di visualizzare uno **Snapshot automatico** messaggio di stato durante l'esecuzione del backup del volume. 
   
    ![Messaggio di snapshot automatico](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Automatic_snap.png) 

## <a name="rescan-volumes"></a>Ripetere la scansione dei volumi
Per ripetere l'analisi dei volumi connessi a StorSimple Snapshot Manager, seguire questa procedura.

#### <a name="to-rescan-the-volumes"></a>Ripetere l'analisi dei volumi
1. Fare clic sull’icona del desktop per avviare StorSimple Snapshot Manager.
2. Nel riquadro **Ambito** fare clic con il pulsante destro del mouse su **Volumi**, quindi scegliere **Ripeti analisi dei volumi**.
   
    ![Ripetere la scansione dei volumi](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Rescan_volumes.png)
   
    Questa procedura consente di sincronizzare l'elenco di volumi con StorSimple Snapshot Manager. Le modifiche, ad esempio creazione o eliminazione di volumi, verranno mostrate nei risultati.

## <a name="configure-and-back-up-a-basic-volume"></a>Configurare ed eseguire il backup di un volume di base
Utilizzare la procedura seguente per configurare il backup di un volume di base e avviare immediatamente un backup o creare un criterio per i backup programmati.

### <a name="prerequisites"></a>Prerequisiti
Prima di iniziare:

* Assicurarsi che il dispositivo StorSimple, dispositivi e il computer siano configurati correttamente. Per altre informazioni, vedere [Distribuire un dispositivo StorSimple locale](./storsimple-8000-deployment-walkthrough-u2.md).
* Installare e configurare Snapshot StorSimple Manager. Per altre informazioni, vedere l'argomento relativo alla [distribuzione di StorSimple Snapshot Manager](storsimple-snapshot-manager-deployment.md).

#### <a name="to-configure-backup-of-a-basic-volume"></a>Per configurare il backup di un volume di base
1. Creare un volume di base nel dispositivo StorSimple.
2. Montare, inizializzare e formattare il volume come descritto in [Montare i volumi](#mount-volumes). 
3. Fare clic sull’icona StorSimple Snapshot Manager sul desktop. Verrà visualizzata la finestra di StorSimple Snapshot Manager. 
4. Nel riquadro **Ambito** fare clic con il pulsante destro del mouse su **Volumi**, quindi scegliere **Ripeti analisi dei volumi**. Al termine dell'analisi, nel riquadro **Risultati** deve essere visualizzato un elenco di volumi. 
5. Nel riquadro **Risultati** fare clic con il pulsante destro del mouse sul volume, quindi selezionare **Crea gruppo di volumi**. 
   
    ![Crea gruppo di volumi](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Create_volume_group.png) 
6. Nella finestra di dialogo **Crea gruppo di volumi** digitare un nome per il gruppo di volumi, assegnare il volume al gruppo, quindi fare clic su **OK**.
7. Nel riquadro **Ambito** espandere il nodo **Gruppi di volumi**. Il nuovo gruppo di volumi dovrebbe essere visualizzato nel nodo **gruppi di volumi** . 
8. Fare clic sul nome del gruppo di volumi.
   
   * Per avviare un processo di backup interattivo (su richiesta), fare clic su **Esegui backup**. 
   * Per pianificare un backup automatico, fare clic su **Crea criteri di backup**. Nella pagina **Generale** selezionare un gruppo di volumi dall'elenco. Nella pagina **Pianificazione** immettere i dettagli sulla pianificazione. Al termine, fare clic su **OK**. 
9. Per verificare che il backup sia stato avviato, espandere il nodo **Processi** nel riquadro **Ambito**, quindi fare clic sul nodo **In esecuzione**. L'elenco dei processi in esecuzione viene visualizzato nel riquadro **Risultati**. 

## <a name="configure-and-back-up-a-dynamic-mirrored-volume"></a>Configurare ed eseguire il backup di un volume con mirroring dinamico
Per configurare il backup di un volume con mirroring dinamico, seguire questa procedura:

* Passaggio 1: Usare Gestione disco per creare un volume con mirroring dinamico. 
* Passaggio 2: Usare StorSimple Snapshot Manager per configurare il backup.

### <a name="prerequisites"></a>Prerequisiti
Prima di iniziare:

* Assicurarsi che il dispositivo StorSimple, dispositivi e il computer siano configurati correttamente. Per altre informazioni, vedere [Distribuire un dispositivo StorSimple locale](storsimple-8000-deployment-walkthrough-u2.md).
* Installare e configurare Snapshot StorSimple Manager. Per altre informazioni, vedere l'argomento relativo alla [distribuzione di StorSimple Snapshot Manager](storsimple-snapshot-manager-deployment.md).
* Configurare due volumi sul dispositivo StorSimple. Negli esempi i volumi disponibili sono **Disco 1** e **Disco 2**. 

### <a name="step-1-use-disk-management-to-create-a-dynamic-mirrored-volume"></a>Passaggio 1: Utilizzare Gestione Disco per creare un volume con mirroring dinamico
Gestione disco è un'utilità di sistema per la gestione dei dischi rigidi e i volumi o partizioni in essi contenuti. Per altre informazioni su Gestione disco, vedere [Gestione Disco](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770943(v=ws.11)) nel sito Web Microsoft TechNet.

#### <a name="to-create-a-dynamic-mirrored-volume"></a>Per creare un volume con mirroring dinamico
1. Per avviare Gestione Disco, usare una delle opzioni seguenti: 
   
   * Aprire la casella **Esegui**, digitare **Diskmgmt.msc**, quindi premere INVIO.
   * Avviare Server Manager, espandere il nodo **Archiviazione**, quindi selezionare **Gestione disco**. 
   * Avviare **Strumenti di amministrazione**, espandere il nodo **Gestione computer** e quindi selezionare **Gestione disco**. 
2. Assicurarsi che siano disponibili due volumi nel dispositivo StorSimple. Nell'esempio i volumi disponibili sono **Disco 1** e **Disco 2**. 
3. Nella finestra Gestione disco fare clic con il pulsante destro su **Disco 1**, quindi scegliere **Nuovo volume con mirroring** nella colonna destra del riquadro inferiore. 
   
    ![Nuovo volume con mirroring](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_New_mirrored_volume.png) 
4. Nella pagina della **Creazione guidata nuovo volume con mirroring** fare clic su **Avanti**.
5. Nella pagina **Seleziona dischi** selezionare **Disco 2** nel riquadro **Selezionato**, fare clic su **Aggiungi**, quindi fare clic su **Avanti**. 
6. Nella pagina **Assegna lettera o percorso unità** accettare le impostazioni predefinite e quindi fare clic su **Avanti**. 
7. Nella pagina **Formatta volume** selezionare **64 K** nella casella **Dimensioni unità di allocazione**. Selezionare la casella di controllo **Esegui formattazione veloce**, quindi fare clic su **Avanti**. 
8. Nella pagina **Completamento della Creazione guidata nuovo volume con mirroring** esaminare le impostazioni, quindi fare clic su **Fine**. 
9. Viene visualizzato un messaggio per indicare che il disco di base verrà convertito in un disco dinamico. Fare clic su **Sì**.
   
    ![Messaggi di conversione del disco dinamico](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Disk_management_msg.png) 
10. In Gestione disco, verificare che Disco 1 e Disco 2 vengano visualizzati come volumi con mirroring dinamico. Nella colonna dello stato dovrebbe essere visualizzata la dicitura **Dinamico** e le barre di capacità dovrebbero diventare rosse a indicare un volume con mirroring. 
    
    ![Dischi dinamici con mirroring di Gestione disco](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Verify_dynamic_disks_2.png) 

### <a name="step-2-use-storsimple-snapshot-manager-to-configure-backup"></a>Passaggio 2: Utilizzare StorSimple Snapshot Manager per configurare il backup
Utilizzare la procedura seguente per configurare un volume con mirroring dinamico, avviare immediatamente un backup o creare un criterio per i backup pianificati.

#### <a name="to-configure-backup-of-a-dynamic-mirrored-volume"></a>Per configurare il backup di un volume con mirroring dinamico
1. Fare clic sull’icona StorSimple Snapshot Manager sul desktop. Verrà visualizzata la finestra di StorSimple Snapshot Manager. 
2. Nel riquadro **Ambito** fare clic con il pulsante destro del mouse su **Volumi**, quindi scegliere **Ripeti analisi dei volumi**. Al termine dell'analisi, nel riquadro **Risultati** deve essere visualizzato un elenco di volumi. Il volume con mirroring dinamico viene elencato come un singolo volume. 
3. Nel riquadro **Risultati** fare clic con il pulsante destro del mouse sul volume con mirroring dinamico, quindi scegliere **Crea gruppo di volumi**. 
4. Nella finestra di dialogo **Crea gruppo di volumi** digitare un nome per il gruppo di volumi, assegnare il volume con mirroring dinamico al gruppo, quindi fare clic su **OK**. 
5. Nel riquadro **Ambito** espandere il nodo **Gruppi di volumi**. Il nuovo gruppo di volumi dovrebbe essere visualizzato nel nodo **Gruppi di volumi**. 
6. Fare clic sul nome del gruppo di volumi. 
   
   * Per avviare un processo di backup interattivo (su richiesta), fare clic su **Esegui backup**. 
   * Per pianificare un backup automatico, fare clic su **Crea criteri di backup**. Nella pagina **Generale** selezionare il gruppo di volumi nell'elenco. Nella pagina **Pianificazione** immettere i dettagli sulla pianificazione. Al termine, fare clic su **OK**. 
7. È possibile monitorare il processo di backup durante l'esecuzione. Nel riquadro **Ambito** espandere il nodo **Processi**, quindi fare clic su **In esecuzione**. I dettagli del processo vengono visualizzati nel riquadro **Risultati**. Al termine del processo di backup, i dettagli vengono trasferiti nella lista **ultime 24 ore** . 

## <a name="next-steps"></a>Passaggi successivi
* Informazioni su come [usare StorSimple Snapshot Manager per amministrare la soluzione di StorSimple](storsimple-snapshot-manager-admin.md).
* Informazioni su come [usare StorSimple Snapshot Manager per creare e gestire gruppi di volumi](storsimple-snapshot-manager-manage-volume-groups.md).

<!--Reference links-->
[1]: /previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee338480(v=ws.10)