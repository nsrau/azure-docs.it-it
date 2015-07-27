<properties 
   pageTitle="Usare StorSimple Snapshot Manager per visualizzare e gestire volumi | Microsoft Azure"
   description="Descrive come utilizzare lo snap-in MMC StorSimple Snapshot Manager per visualizzare e gestire i volumi."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="adinah"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="07/09/2015"
   ms.author="v-sharos" />

# Utilizzare StorSimple Snapshot Manager per visualizzare e gestire i volumi

## Panoramica

È possibile utilizzare StorSimple Snapshot Manager **nodo**Volumi (nel**riquadro**ambito) per selezionare volumi e visualizzare le relative informazioni. I volumi vengono presentati come unità che corrispondono ai volumi montati dall'host. Il**nodo**dei volumi Mostra volumi locali e tipi di volume supportati da Azure StorSimple, compresi i volumi individuati tramite l'utilizzo di iSCSI e di un dispositivo.

Per ulteriori informazioni sui volumi supportati, consultare[Supporto per più tipi di volume](storsimple-what-is-snapshot-manager.md#support-for-multiple-volume-types).

![Elenco volumi nel riquadro dei Risultati](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Volume_node.png)

**Figura 1: Gestione Nodo dei Volumi StorSimple Snapshot**

Il**nodo dei volumi**ti permette di scansionare o eliminare i volumi dopo l’individuazione da parte di StorSimple Snapshot Manager.

In questo tutorial viene spiegato come montare, inizializzare e formattare volumi e quindi utilizzare Gestione Snapshot StorSimple per:

- Visualizzare informazioni sui volumi 
- Eliminare volumi
- Ripetere la scansione dei volumi 
- Configurare un volume di base e una copia di backup
- Configurare un volume con mirroring dinamico e una copia di backup

>[AZURE.NOTE]Tutte i nodi del**Volume**inoltre sono disponibili nel pannello**azioni**.
 
## Montare i volumi

Utilizzare la seguente procedura per montare, inizializzare e formattare volumi su StorSimple di Azure.

#### Montare volumi

1. Sul computer, avviare iSCSI Microsoft Initiator.

2. Specificare uno degli indirizzi IP di interfaccia come portale di destinazione o indirizzo IP di individuazione e connettersi al dispositivo. Dopo che il dispositivo è connesso, i volumi sono accessibili nel sistema di Windows. Per ulteriori informazioni sull'utilizzo dell'iniziatore iSCSI Microsoft, vedere la sezione "Connessione a un dispositivo di destinazione iSCSI" in[Installazione e Configurazione di iSCSI Initiator][1].

3. Per avviare lo snap-in Gestione disco, utilizzare una delle opzioni seguenti:

    - Digitare diskmgmt. msc nella**casella**Esegui.

    - Avviare Server Manager, espandere il nodo **Archiviazione**, quindi selezionare **Gestione disco**.

    - Avviare **Strumenti di amministrazione**, espandere il nodo **Gestione computer **, quindi selezionare **Gestione disco**.

    >[AZURE.NOTE]È necessario utilizzare i privilegi di amministratore per eseguire Gestione disco.
 
4. Portare i volumi online:

   1. In Gestione disco, fare doppio clic su un volume contrassegnato**Offline**.

   2. Fare clic su**Riattiva disco specificato**. Il disco deve essere contrassegnato come**Online**dopo la riattivazione.

5. Inizializzare il/i volume/i

   1. Cliccare col tasto destro del mouse sui volumi individuati.

   2. Nel menu, selezionare**Inizializza Disco**.

   3. Nella finestra di dialogo**Inizializza disco**, selezionare i dischi che si desidera inizializzare e quindi fare clic su**OK**.

6. Formattare volumi semplici:

   1. Fare doppio clic su un volume che si desidera formattare.

   2. Nel menu, selezionare**Nuovo Volume Semplice**.

   3. Utilizzare la procedura guidata Nuovo Volume Semplice per formattare il volume:

      - Specificare le dimensioni del volume.
      - Specificare una lettera di unità.
      - Selezionare il file system NTFS.
      - Specificare una dimensione unità di allocazione pari a 64 KB.
      - Eseguire una formattazione veloce.

7. Formattare volumi con più partizioni. Per istruzioni, vedere la sezione "Partizioni e Volumi" in[Esecuzione Gestione Disco](https://msdn.microsoft.com/library/dd163556.aspx).

## Visualizza informazioni sui tuoi volumi

Utilizzare la procedura seguente per visualizzare informazioni sui volumi locali e di Azure StorSimple di Azure.

#### Per visualizzare le informazioni del volume

1. Fare clic sull’icona del desktop per avviare StorSimple Snapshot Manager. 

2. Nel riquadro **Ambito**, fare clic sul nodo **Volumi**. Viene visualizzato un elenco di volumi locali e montati, compresi tutti i volumi StorSimple di Azure, nel**riquadro**Risultati. Le colonne nel**riquadro**Risultati sono configurabili. (Pulsante destro del mouse il**nodo volumi**, seleziona**visualizza**quindi selezionare**Aggiungi/Rimuovi Colonne**.)

    ![Configurare le colonne](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_View_volumes.png)

    Colonna risultati | Descrizione 
    :--------------|:-------------
    Nome | Il**colonna**Nome, contiene la lettera di unità assegnata a ogni volume individuato.
    Dispositivo | La**colonna**dispositivo contiene l'indirizzo IP del dispositivo connesso al computer.
    Nome Dispositivo Volume | La**Colonna**Nome Dispositivo Volume contiene il nome del volume del dispositivo a cui appartiene il volume selezionato. Questo è il nome del volume definito nel portale di gestione di Azure per il volume specifico.
    Percorsi di accesso | La**colonna**Percorsi di Accesso mostra il percorso di accesso al volume. Si tratta del punto di montaggio o lettera di unità in cui il volume è accessibile nel computer.
 
## Eliminare un volume

Utilizzare la procedura seguente per eliminare un volume da StorSimple Snapshot Manager.

>[AZURE.NOTE]Non è possibile eliminare un volume se fa parte di un gruppo di volumi. (L'opzione di eliminazione non è disponibile per i volumi che sono membri di un gruppo di volumi). È necessario eliminare l'intero gruppo di volumi per eliminare il volume.<br>
#### Per eliminare un volume

1. Fare clic sull’icona del desktop per avviare StorSimple Snapshot Manager.

2. Nel riquadro **Ambito**, fare clic sul nodo **Volumi**.

3. Nel**riquadro**Risultati, cliccare col tasto destro del mouse sul volume che si desidera eliminare.

4. Nel menu, fare clic su**Elimina**.

    ![Eliminare un volume](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Delete_volume.png)

5. Apparirà la finestra di dialogo**Elimina Volume**. Digitare **conferma** nella casella di testo e quindi fare clic su **OK**.

6. Come impostazione predefinita, StorSimple Snapshot Manager esegue il backup di un volume prima di eliminarlo. Questa precauzione consente di proteggere dalla perdita di dati se l'eliminazione è accidentale. StorSimple Snapshot Manager, consente di visualizzare uno**Snapshot automatico**messaggio di stato durante l'esecuzione del backup del volume.

    ![Messaggio di snapshot automatico](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Automatic_snap.png)

## Ripetere la scansione dei volumi

Utilizzare la procedura seguente per ripetere l’analisi dei volumi connessi a StorSimple Snapshot Manager .

#### Ripetere l'analisi dei volumi

1. Fare clic sull’icona del desktop per avviare StorSimple Snapshot Manager.

2. Nel**riquadro**Ambito, clicca col tasto destro su**Volumi**quindi fare clic su**Ripeti analisi dei volumi**.

    ![Ripetere la scansione dei volumi](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Rescan_volumes.png)
 
    Questa procedura consente di sincronizzare l'elenco di volumi con StorSimple Snapshot Manager. Le modifiche, ad esempio creazione o eliminazione di volumi, verranno mostrate nei risultati.

## Configurare ed eseguire il backup di un volume di base

Utilizzare la procedura seguente per configurare il backup di un volume di base e avviare immediatamente un backup o creare un criterio per i backup programmati.

### Prerequisiti

Prima di iniziare:

- Assicurarsi che il dispositivo StorSimple, dispositivi e il computer siano configurati correttamente. Per ulteriori informazioni, vedere[distribuire dispositivo StorSimple locale](storsimple-deployment-walkthrough.md).

- Installare e configurare Snapshot StorSimple Manager. Per ulteriori informazioni, vedere [Distribuire Snapshot StorSimple Manager](storsimple-snapshot-manager-deployment.md).

#### Per configurare il backup di un volume di base

1. Creare un volume di base nel dispositivo StorSimple.

2. Montare, inizializzare e formattare il volume come descritto in[Montare Volumi](#mount-volumes).

3. Fare clic sull’icona StorSimple Snapshot Manager sul desktop. Verrà visualizzata la finestra di gestione StorSimple Snapshot Manager.

4. Nel**riquadro**Ambito, Cliccare con il tasto destro il nodo**Volumi**e quindi selezionare**Ripeti analisi dei volumi**. Al termine dell'analisi, un elenco di volumi dovrebbe apparire nel riquadro **Risultati**.

5. Nel**riquadro**Risultati, cliccare con il tasto destro del mouse sul volume e quindi selezionare**Crea gruppo di volumi**.

    ![Crea gruppo di volumi](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Create_volume_group.png)

6. Nella**finestra di dialogo **Crea gruppo di volumi, digitare un nome per il gruppo di volumi, assegnare i volumi e quindi fare clic su**OK**.

7. Nel riquadro **Ambito**, espandere il nodo **Gruppi di volumi**. Il nuovo gruppo di volumi dovrebbe essere visualizzato sotto il nodo **Gruppi di volumi**.

8. Fare clic sul nome del gruppo di volumi.

    - Per avviare un processo di backup interattivo (su richiesta), fare clic su**Esegui Backup**. 

    - Per pianificare un backup automatico, fare clic su**Crea criteri di Backup**. Nella**pagina**Generale, selezionare un gruppo di volumi dall'elenco. Nella pagina **Programmazione**, immettere i dettagli del programma. Al termine, fare clic su**OK**.

9. Per verificare che il processo di backup è stato avviato, espandere il**nodo**processi nel**riquadro**Ambito e quindi scegliere il**nodo**In Esecuzione. Verrà visualizzato l'elenco dei processi in esecuzione nel**riquadro**Risultati.

## Configurare ed eseguire il backup di un volume con mirroring dinamico

Completare i passaggi seguenti per configurare il backup per un volume con mirroring dinamico:

- Passaggio 1: Utilizzare Gestione Disco per creare un volume con mirroring dinamico 

- Passaggio 2: Utilizzare StorSimple Snapshot Manager per configurare il backup

### Prerequisiti

Prima di iniziare:

- Assicurarsi che il dispositivo StorSimple, dispositivi e il computer siano configurati correttamente. Per ulteriori informazioni, vedere[distribuire dispositivo StorSimple locale](storsimple-deployment-walkthrough.md).

- Installare e configurare Snapshot StorSimple Manager. Per ulteriori informazioni, vedere [Distribuire Snapshot StorSimple Manager](storsimple-snapshot-manager-deployment.md).

- Configurare due volumi sul dispositivo StorSimple. (Negli esempi, i volumi disponibili sono**disco 1**e**disco 2**.)

### Passaggio 1: Utilizzare Gestione Disco per creare un volume con mirroring dinamico

Gestione disco è un'utilità di sistema per la gestione dei dischi rigidi e i volumi o partizioni in essi contenuti. Per ulteriori informazioni su Gestione Disco, vedere[Gestione Disco](https://technet.microsoft.com/library/cc770943.aspx)sul Sito Web Microsoft TechNet.

#### Per creare un volume con mirroring dinamico

1. Per avviare Gestione Disco, utilizzare una delle opzioni seguenti: 

   - Aprire il**eseguire**digitare**diskmgmt. msc**quindi premere INVIO.

   - Avviare Server Manager, espandere il nodo **Archiviazione**, quindi selezionare **Gestione disco**.

   - Avviare **Strumenti di amministrazione**, espandere il nodo **Gestione computer **, quindi selezionare **Gestione disco**.

2. Assicurarsi che siano disponibili due volumi nel dispositivo StorSimple. (Nell'esempio, i volumi disponibili sono**disco 1**e**disco 2**.) 

3. Nella finestra Gestione disco, nella colonna a destra del riquadro inferiore, cliccare col tasto destro del mouse su**disco 1**e selezionare**Nuovo Volume con mirroring**.

    ![Nuovo Volume con mirroring](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_New_mirrored_volume.png)

4. Nella**pagina**Nuovo Volume con mirroring della procedura guidata, fare clic su**Avanti**.

5. Nella pagina**Seleziona dischi**selezionare**disco 2**nel**riquadro**selezionati, fare clic su**Aggiungi**e quindi fare clic su**Avanti**.

6. Nella pagina**Assegna lettera di unità o percorso**, accettare le impostazioni predefinite e quindi fare clic su**Avanti**.

7. Nella pagina**Formatta Volume**nella casella**dimensioni unità di allocazione**selezionare**64 KB**. Selezionare **Esegui formattazione veloce**, quindi fare clic su**Avanti**.

8. Nella pagina**completamento del nuovo Volume con mirroring**, esaminare le impostazioni e quindi fare clic su**Fine**.

9. Viene visualizzato un messaggio per indicare che il disco di base verrà convertito in un disco dinamico. Fare clic su **Sì**.

    ![Messaggi di conversione del disco dinamico](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Disk_management_msg.png)

10. In Gestione disco, verificare che il disco 1 e disco 2 vengono mostrati come i volumi dinamici con mirroring. (* * Dinamico * * dovrebbe essere visualizzato nella colonna di stato e il colore della barra di capacità dovrebbe cambia in rosso per indicare un volume con mirroring.)

    ![Gestione Disco dischi dinamici con mirroring di](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Verify_dynamic_disks_2.png)
 
### Passaggio 2: Utilizzare StorSimple Snapshot Manager per configurare il backup

Utilizzare la procedura seguente per configurare un volume con mirroring dinamico, avviare immediatamente un backup o creare un criterio per i backup pianificati.

#### Per configurare il backup di un volume con mirroring dinamico

1. Fare clic sull’icona StorSimple Snapshot Manager sul desktop. Verrà visualizzata la finestra di gestione StorSimple Snapshot Manager. 

2. Nel**riquadro**ambito, cliccare col destro il nodo**volumi**e selezionare**Ripeti analisi dei volumi**. Al termine dell’analisi, un elenco di volumi dovrebbe apparire nel riquadro **Risultati**. Il volume con mirroring dinamico viene elencato come un singolo volume.

3. Nel**riquadro**risultati, cliccare col tasto destro del mouse sul volume con mirroring dinamico e quindi fare clic su**Crea gruppo di volumi**.

4. Nella finestra di dialogo **Crea gruppo di volumi**digitare un nome per il gruppo di volumi, assegnare il volume con mirroring dinamico al gruppo e quindi fare clic su**OK**.

5. Nel riquadro **Ambito**, espandere il nodo **Gruppi di volumi**. Il nuovo gruppo di volumi dovrebbe essere visualizzato sotto il nodo **Gruppi di volumi**.

6. Fare clic sul nome del gruppo di volumi.

    - Per avviare un processo di backup interattivo (su richiesta), fare clic su**Esegui Backup**. 

    - Per pianificare un backup automatico, fare clic su**Crea criteri di Backup**. Nella Pagina**Generale**, selezionare il gruppo di volumi dall'elenco. Nella pagina **Programmazione**, immettere i dettagli del programma. Al termine, fare clic su**OK**.

7. È possibile monitorare il processo di backup durante l'esecuzione. Nel**riquadro**ambito, espandere il**nodo**processi e quindi fare clic su**in esecuzione**, vengono visualizzati i dettagli del processo nel**riquadro**risultati. Al termine del processo di backup, i dettagli vengono trasferiti nella lista**ultime 24 ore**.

## Passaggi successivi

[Usare StorSimple Snapshot Manager per gestire i gruppi di volumi](storsimple-snapshot-manager-manage-volume-groups.md).

<!--Reference links-->
[1]: https://msdn.microsoft.com/library/ee338480(v=ws.10).aspx

<!---HONumber=July15_HO3-->