<properties 
   pageTitle="StorSimple Snapshot Manager e volumi | Microsoft Azure"
   description="Illustra come usare lo snap-in MMC Gestione snapshot StorSimple per visualizzare e gestire i volumi e per configurare i backup."
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
   ms.date="09/21/2015"
   ms.author="v-sharos" />

# Utilizzare StorSimple Snapshot Manager per visualizzare e gestire i volumi

## Panoramica

È possibile usare il nodo **Volumes** di StorSimple Snapshot Manager (nel riquadro **ambito**) per selezionare i volumi e visualizzare le relative informazioni. I volumi vengono presentati come unità che corrispondono ai volumi montati dall'host. Il nodo **Volumes** indica i volumi locali e i tipi di volume supportati da Azure StorSimple, compresi i volumi individuati tramite l'uso di iSCSI e di un dispositivo.

Per altre informazioni sui volumi supportati, passare al [supporto per più tipi di volume](storsimple-what-is-snapshot-manager.md#support-for-multiple-volume-types).

![Elenco volumi nel riquadro dei Risultati](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Volume_node.png)

Il nodo **Volumi** consente inoltre di ripetere l'analisi o eliminare i volumi dopo l'individuazione da parte di Gestione snapshot StorSimple.

Questo tutorial spiega come montare, inizializzare e formattare volumi e quindi usare StorSimple Snapshot Manager per:

- Visualizzare informazioni sui volumi 
- Eliminare volumi
- Ripetere la scansione dei volumi 
- Configurare un volume di base e una copia di backup
- Configurare un volume con mirroring dinamico e una copia di backup

>[AZURE.NOTE]Tutte le azioni del nodo **Volumi** sono disponibili anche nel riquadro **Azioni**.
 
## Montare i volumi

Utilizzare la seguente procedura per montare, inizializzare e formattare volumi su StorSimple di Azure.

#### Montare volumi

1. Sul computer, avviare iSCSI Microsoft Initiator.

2. Specificare uno degli indirizzi IP di interfaccia come portale di destinazione o indirizzo IP di individuazione e connettersi al dispositivo. Dopo che il dispositivo è connesso, i volumi sono accessibili nel sistema di Windows. Per altre informazioni sull'uso dell'iniziatore iSCSI Microsoft, vedere la sezione "Stabilire una connessione a un dispositivo di destinazione iSCSI" in[Installare e configurare l'iniziatore iSCSI Microsoft][1].

3. Per avviare lo snap-in Gestione disco, utilizzare una delle opzioni seguenti:

    - Digitare Diskmgmt.msc nella casella **Esegui**.

    - Avviare Server Manager, espandere il nodo **Archiviazione** e quindi selezionare **Gestione disco**.

    - Avviare **Strumenti di amministrazione**, espandere il nodo **Gestione computer ** e quindi selezionare **Gestione disco**.

    >[AZURE.NOTE]È necessario utilizzare i privilegi di amministratore per eseguire Gestione disco.
 
4. Portare i volumi online:

   1. In Gestione disco fare clic con il pulsante destro del mouse su un volume contrassegnato come **Offline**.

   2. Scegliere **Riattiva disco specificato**. Il disco deve essere contrassegnato come **Online** dopo la riattivazione.

5. Inizializzare il/i volume/i

   1. Cliccare col tasto destro del mouse sui volumi individuati.

   2. Scegliere **Inizializza disco** dal menu.

   3. Nella finestra di dialogo **Inizializza disco** selezionare i dischi che si desidera inizializzare e quindi fare clic su**OK**.

6. Formattare volumi semplici:

   1. Fare doppio clic su un volume che si desidera formattare.

   2. Scegliere **Nuovo volume semplice** dal menu.

   3. Utilizzare la procedura guidata Nuovo Volume Semplice per formattare il volume:

      - Specificare le dimensioni del volume.
      - Specificare una lettera di unità.
      - Selezionare il file system NTFS.
      - Specificare una dimensione unità di allocazione pari a 64 KB.
      - Eseguire una formattazione veloce.

7. Formattare volumi con più partizioni. Per istruzioni, vedere la sezione su partizioni e volumi nell'argomento relativo all'[implementazione di Gestione disco](https://msdn.microsoft.com/library/dd163556.aspx).

## Visualizza informazioni sui tuoi volumi

Utilizzare la procedura seguente per visualizzare informazioni sui volumi locali e di Azure StorSimple di Azure.

#### Per visualizzare le informazioni del volume

1. Per avviare StorSimple Snapshot Manager, fare clic sull'icona del desktop. 

2. Nel riquadro **Ambito** fare clic sul nodo **Volumi**. Nel riquadro **Risultati** viene visualizzato un elenco di volumi locali e montati, compresi tutti i volumi di Azure StorSimple. Le colonne nel riquadro **Risultati** sono configurabili. Fare clic con il pulsante destro del mouse sul nodo **Volumi**, scegliere **Visualizza** e quindi **Aggiungi/Rimuovi colonne**.

    ![Configurare le colonne](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_View_volumes.png)

    Colonna risultati | Descrizione 
    :--------------|:-------------
    Nome | La colonna **Nome** contiene la lettera di unità assegnata a ogni volume individuato.
    Dispositivo | La colonna **Dispositivo** contiene l'indirizzo IP del dispositivo connesso al computer host.
    Nome Dispositivo Volume | La colonna **Nome volume dispositivo** contiene il nome del volume del dispositivo a cui appartiene il volume selezionato. Questo è il nome del volume definito nel portale di gestione di Azure per il volume specifico.
    Percorsi di accesso | La colonna **Percorsi di accesso** mostra il percorso di accesso al volume. Si tratta del punto di montaggio o lettera di unità in cui il volume è accessibile nel computer.
 
## Eliminare un volume

Utilizzare la procedura seguente per eliminare un volume da StorSimple Snapshot Manager.

>[AZURE.NOTE]Non è possibile eliminare un volume se fa parte di un gruppo di volumi. (L'opzione di eliminazione non è disponibile per i volumi che sono membri di un gruppo di volumi). È necessario eliminare l'intero gruppo di volumi per eliminare il volume. <br>
#### Per eliminare un volume

1. Per avviare StorSimple Snapshot Manager, fare clic sull'icona del desktop.

2. Nel riquadro **Ambito** fare clic sul nodo **Volumi**.

3. Nel riquadro **Risultati** fare clic con il pulsante destro del mouse sul volume che si desidera eliminare.

4. Scegliere **Elimina** dal menu.

    ![Eliminare un volume](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Delete_volume.png)

5. Verrà visualizzata la finestra di dialogo **Elimina volume**. Digitare **Conferma** nella casella di testo e quindi fare clic su **OK**.

6. Come impostazione predefinita, StorSimple Snapshot Manager esegue il backup di un volume prima di eliminarlo. Questa precauzione consente di proteggere dalla perdita di dati se l'eliminazione è accidentale. Gestione snapshot StorSimple visualizza un messaggio di stato di **Snapshot automatico** durante l'esecuzione del backup del volume.

    ![Messaggio di snapshot automatico](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Automatic_snap.png)

## Ripetere la scansione dei volumi

Per ripetere l'analisi dei volumi connessi a StorSimple Snapshot Manager, seguire questa procedura.

#### Ripetere l'analisi dei volumi

1. Per avviare StorSimple Snapshot Manager, fare clic sull'icona del desktop.

2. Nel riquadro **Ambito** fare clic con il pulsante destro del mouse su **Volumi** e quindi scegliere **Ripeti analisi dei volumi**.

    ![Ripetere la scansione dei volumi](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Rescan_volumes.png)
 
    Questa procedura consente di sincronizzare l'elenco di volumi con StorSimple Snapshot Manager. Le modifiche, ad esempio creazione o eliminazione di volumi, verranno mostrate nei risultati.

## Configurare ed eseguire il backup di un volume di base

Utilizzare la procedura seguente per configurare il backup di un volume di base e avviare immediatamente un backup o creare un criterio per i backup programmati.

### Prerequisiti

Prima di iniziare:

- Assicurarsi che il dispositivo StorSimple, dispositivi e il computer siano configurati correttamente. Per altre informazioni, vedere [Distribuire un dispositivo StorSimple locale](storsimple-deployment-walkthrough.md).

- Installare e configurare Snapshot StorSimple Manager. Per altre informazioni, vedere l'argomento relativo alla [distribuzione di Gestione snapshot StorSimple](storsimple-snapshot-manager-deployment.md).

#### Per configurare il backup di un volume di base

1. Creare un volume di base nel dispositivo StorSimple.

2. Montare, inizializzare e formattare il volume come descritto in [Montare i volumi](#mount-volumes).

3. Fare clic sull’icona StorSimple Snapshot Manager sul desktop. Verrà visualizzata la finestra di gestione StorSimple Snapshot Manager.

4. Nel riquadro **Ambito** fare clic con il pulsante destro del mouse sul nodo **Volumi** e quindi scegliere **Ripeti analisi dei volumi**. Al termine dell'analisi, nel riquadro **Risultati** dovrebbe venire visualizzato un elenco di volumi.

5. Nel riquadro **Risultati** fare clic con il pulsante destro del mouse sul volume e quindi scegliere **Crea gruppo di volumi**.

    ![Crea gruppo di volumi](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Create_volume_group.png)

6. Nella finestra di dialogo **Crea gruppo di volumi** digitare un nome per il gruppo di volumi, assegnare i volumi e quindi fare clic su **OK**.

7. Nel riquadro **Ambito** espandere il nodo **Gruppi di volumi**. Il nuovo gruppo di volumi dovrebbe essere visualizzato nel nodo **Gruppi di volumi**.

8. Fare clic sul nome del gruppo di volumi.

    - Per avviare un processo di backup interattivo (su richiesta), fare clic su **Esegui backup**. 

    - Per pianificare un backup automatico, fare clic su **Crea criteri di backup**. Nella pagina **Generale** selezionare un gruppo di volumi dall'elenco. Nella pagina **Pianificazione** immettere i dettagli della pianificazione. Al termine, fare clic su **OK**.

9. Per verificare che il processo di backup sia stato avviato, espandere il nodo **Processi** nel riquadro **Ambito** e quindi fare clic sul nodo **In esecuzione**. Nel riquadro **Risultati** verrà visualizzato l'elenco dei processi in esecuzione .

## Configurare ed eseguire il backup di un volume con mirroring dinamico

Per configurare il backup di un volume con mirroring dinamico, seguire questa procedura:

- Passaggio 1: Usare Gestione disco per creare un volume con mirroring dinamico. 

- Passaggio 2: Usare Gestione snapshot StorSimple per configurare il backup.

### Prerequisiti

Prima di iniziare:

- Assicurarsi che il dispositivo StorSimple, dispositivi e il computer siano configurati correttamente. Per altre informazioni, vedere [Distribuire un dispositivo StorSimple locale](storsimple-deployment-walkthrough.md).

- Installare e configurare Snapshot StorSimple Manager. Per altre informazioni, vedere l'argomento relativo alla [distribuzione di Gestione snapshot StorSimple](storsimple-snapshot-manager-deployment.md).

- Configurare due volumi sul dispositivo StorSimple. Negli esempi i volumi disponibili sono **disco 1** e **disco 2**.

### Passaggio 1: Utilizzare Gestione Disco per creare un volume con mirroring dinamico

Gestione disco è un'utilità di sistema per la gestione dei dischi rigidi e i volumi o partizioni in essi contenuti. Per altre informazioni su Gestione disco, vedere [Gestione Disco](https://technet.microsoft.com/library/cc770943.aspx) nel sito Web Microsoft TechNet.

#### Per creare un volume con mirroring dinamico

1. Per avviare Gestione Disco, utilizzare una delle opzioni seguenti: 

   - Aprire la casella **Esegui**, digitare **Diskmgmt.msc** e premere INVIO.

   - Avviare Server Manager, espandere il nodo **Archiviazione** e quindi selezionare **Gestione disco**.

   - Avviare **Strumenti di amministrazione**, espandere il nodo **Gestione computer ** e quindi selezionare **Gestione disco**.

2. Assicurarsi che siano disponibili due volumi nel dispositivo StorSimple. Nell'esempio i volumi disponibili sono**disco 1** e **disco 2**. 

3. Nella finestra Gestione disco, nella colonna di destra del riquadro inferiore, fare clic con il pulsante destro del mouse sul **disco 1** e scegliere **uovo volume con mirroring**.

    ![Nuovo Volume con mirroring](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_New_mirrored_volume.png)

4. Nella pagina **uovo volume con mirroring** della procedura guidata fare clic su **Avanti**.

5. Nella pagina **Seleziona dischi** selezionare il **disco 2** nel riquadro **Selezionati**, fare clic su **Aggiungi** e quindi su **Avanti**.

6. Nella pagina **Assegna lettera o percorso unità** accettare le impostazioni predefinite e quindi fare clic su **Avanti**.

7. Nella pagina **Formatta volume**, nella casella **Dimensioni unità di allocazione**, selezionare**64 KB**. Selezionare la casella di controllo **Esegui formattazione veloce** e quindi fare clic su **Avanti**.

8. Nella pagina **Completamento della Creazione guidata nuovo volume con mirroring** esaminare le impostazioni e quindi fare clic su **Fine**.

9. Viene visualizzato un messaggio per indicare che il disco di base verrà convertito in un disco dinamico. Fare clic su **Sì**.

    ![Messaggi di conversione del disco dinamico](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Disk_management_msg.png)

10. In Gestione disco, verificare che il disco 1 e il disco 2 vengano visualizzati come volumi con mirroring dinamico. Nella colonna dello stato dovrebbe essere visualizzata la dicitura **Dinamico** e le barre di capacità dovrebbero diventare rosse a indicare un volume con mirroring.

    ![Gestione Disco dischi dinamici con mirroring di](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Verify_dynamic_disks_2.png)
 
### Passaggio 2: Utilizzare StorSimple Snapshot Manager per configurare il backup

Utilizzare la procedura seguente per configurare un volume con mirroring dinamico, avviare immediatamente un backup o creare un criterio per i backup pianificati.

#### Per configurare il backup di un volume con mirroring dinamico

1. Fare clic sull’icona StorSimple Snapshot Manager sul desktop. Verrà visualizzata la finestra di gestione StorSimple Snapshot Manager. 

2. Nel riquadro **Ambito** fare clic con il pulsante destro del mouse sul nodo **Volumi** e quindi scegliere **Ripeti analisi dei volumi**. Al termine dell'analisi, nel riquadro **Risultati** dovrebbe venire visualizzato un elenco di volumi. Il volume con mirroring dinamico viene elencato come un singolo volume.

3. Nel riquadro **Risultati** fare clic con il pulsante destro del mouse sul volume con mirroring dinamico e quindi scegliere **Crea gruppo di volumi**.

4. Nella finestra di dialogo **Crea gruppo di volumi** digitare un nome per il gruppo di volumi, assegnare il volume con mirroring dinamico al gruppo e quindi fare clic su **OK**.

5. Nel riquadro **Ambito** espandere il nodo **Gruppi di volumi**. Il nuovo gruppo di volumi dovrebbe essere visualizzato nel nodo **Gruppi di volumi**.

6. Fare clic sul nome del gruppo di volumi.

    - Per avviare un processo di backup interattivo (su richiesta), fare clic su **Esegui backup**. 

    - Per pianificare un backup automatico, fare clic su **Crea criteri di backup**. Nella pagina **Generale** selezionare il gruppo di volumi dall'elenco. Nella pagina **Pianificazione** immettere i dettagli della pianificazione. Al termine, fare clic su **OK**.

7. È possibile monitorare il processo di backup durante l'esecuzione. Nel riquadro **Ambito** espandere il nodo **Processi** e quindi fare clic su **In esecuzione**. Nel riquadro **Risultati** verranno visualizzati i dettagli del processo. Al termine del processo di backup, i dettagli verranno trasferiti nell'elenco dei processi delle **Ultime 24 ore**.

## Passaggi successivi

- Imparare a [usare Gestione Snapshot StorSimple per amministrare la soluzione di StorSimple](storsimple-snapshot-manager-admin.md).
- Imparare a [usare Gestione snapshot StorSimple per creare e gestire gruppi di volumi](storsimple-snapshot-manager-manage-volume-groups.md).

<!--Reference links-->
[1]: https://msdn.microsoft.com/library/ee338480(v=ws.10).aspx

<!---HONumber=Sept15_HO4-->