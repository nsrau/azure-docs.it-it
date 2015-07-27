<properties 
   pageTitle="Distribuzione Gestione snapshot StorSimple | Microsoft Azure"
   description="Viene descritto come scaricare e installare Gestione Snapshot StorSimple."
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

# Distribuzione Gestione snapshot StorSimple

## Panoramica

Gestione Snapshot StorSimple è uno snap-in Microsoft Management Console (MMC) che semplifica la protezione dei dati e la gestione dei backup in un ambiente di Microsoft Azure StorSimple. Con StorSimple Snapshot Manager è possibile gestire Microsoft Azure StorSimple in locale e archiviare nella cloud come se fosse un sistema di archiviazione completamente integrato, semplificando i processi di backup e ripristino e riducendo i costi di conseguenza.

In questa esercitazione vengono descritti i requisiti di configurazione, nonché le procedure per installare, rimuovere e aggiornare StorSimple Snapshot Manager.

## Installazione di StorSimple Snapshot Manager

StorSimple Snapshot Manager può essere installato nei computer che eseguono il sistema operativo Windows Server ® 2008 R2 SP1, Windows Server 2012 o Windows Server 2012 R2.

>[AZURE.NOTE]Nei server che eseguono Windows 2008 R2, è necessario installare Windows Server 2008 SP1 e Windows Management Framework 3.0.

Prima di installare o aggiornare lo StorSimple Snapshot Manager per Microsoft Management Console (MMC), assicurarsi che il dispositivo di Microsoft Azure StorSimple e l'host siano configurati correttamente.

## Configurazione dei prerequisiti

I passaggi seguenti forniscono una panoramica generale delle attività di configurazione che è necessario completare prima di installare StorSimple Snapshot Manager. Per informazioni complete sulla configurazione e sull’installazione di Microsoft Azure StorSimple, inclusi i requisiti di sistema e istruzioni dettagliate, vedere [Distribuire dispositivo StorSimple in locale](storsimple-deployment-walkthrough.md).

>[AZURE.IMPORTANT]Prima di iniziare, esaminare l’[elenco di controllo di pre-installazione](storsimple-deployment-walkthrough.md#pre-installation-checklist) e [i prerequisiti di distribuzione](storsimple-deployment-walkthrough.md#deployment-prerequisites) in [Distribuire un dispositivo StorSimple locale](storsimple-deployment-walkthrough.md).<br>
 
### Prima di installare StorSimple Snapshot Manager

1. Disimballare, montare e collegare il dispositivo Microsoft Azure StorSimple, come descritto in[installare il dispositivo StorSimple 8100](storsimple-8100-hardware-installation.md)o[installare il dispositivo StorSimple 8600](storsimple-8600-hardware-installation.md).

2. Assicurarsi che il computer host esegua uno dei seguenti sistemi operativi:

    - Windows Server 2008 R2 (nei server che eseguono Windows 2008 R2, è necessario inoltre installare Windows Server 2008 SP1 e Windows Management Framework 3.0)
    - Windows Server 2012
    - Windows Server 2012 R2
 
    > [AZURE.NOTE]Per un dispositivo virtuale StorSimple, l'host deve essere una macchina virtuale di Microsoft Azure.

3. Assicurarsi che siano soddisfatti i requisiti di configurazione Microsoft Azure StorSimple. Per ulteriori informazioni, vedere[Prerequisiti di distribuzione](storsimple-deployment-walkthrough.md#deployment-prerequisites).

4. Connettere il dispositivo all'host ed eseguire la configurazione iniziale. Per ulteriori informazioni, vedere[Passaggi di distribuzione](storsimple-deployment-walkthrough.md#deployment-steps).

5. Creare i volumi nel dispositivo, assegnarli all'host e verificare che l'host sia in grado di montarli e utilizzarli. StorSimple Snapshot Manager supporta i tipi di volumi seguenti:

    - Volumi di base
    - Volumi semplici
    - Volumi dinamici
    - Volumi dinamici con mirroring (RAID 1)
    - Volumi condivisi del cluster
 
    Per informazioni sulla creazione di volumi nel dispositivo StorSimple o nel dispositivo virtuale StorSimple, vedere "passaggio 6: Creare un volume" in[Distribuire dispositivo StorSimple in locale](storsimple-deployment-walkthrough.md).

## Installare un nuovo StorSimple Snapshot Manager

Prima di installare StorSimple Snapshot Manager, assicurarsi che i volumi creati nel dispositivo StorSimple o nel dispositivo virtuale StorSimple siano montati, inizializzati e formattati come descritto in[Prerequisiti di configurazione](#configure-prerequisites).

>[AZURE.IMPORTANT]
>
>- Per un dispositivo virtuale StorSimple, l'host deve essere una macchina virtuale di Microsoft Azure. 
>
>- L'host deve eseguire Windows 2008 R2, Windows Server 2012 o Windows Server 2012 R2. Se il server esegue Windows Server 2008 R2, è necessario installare anche Windows Server 2008 SP1 e Windows Management Framework 3.0.
>
>- Prima di poter connettere il dispositivo di StorSimple Snapshot Manager, è necessario configurare una connessione iSCSI dall'host al dispositivo StorSimple.

Seguire questi passaggi per completare una nuova installazione di StorSimple Snapshot Manager. Se si installa un aggiornamento, passare a [Aggiorna o reinstalla StorSimple Snapshot Manager](#upgrade-or-reinstall-storsimple-snapshot-manager).

- Passaggio 1: Installare StorSimple Snapshot Manager 
- Passaggio 2: Connettere StorSimple Snapshot Manager a un dispositivo 
- Passaggio 3: Verificare la connessione al dispositivo 

###Passaggio 1: Installare StorSimple Snapshot Manager

Utilizzare la procedura seguente per installare StorSimple Snapshot Manager.

#### Per installare StorSimple Snapshot Manager

1. Scaricare il software StorSimple Snapshot Manager (Vai al[StorSimple Snapshot Manager](https://www.microsoft.com/download/details.aspx?id=44220)in Microsoft Download Center) e salvarlo localmente nell'host.

2. In FileExplorer fare clic sul pulsante destro del mouse sulla cartella compressa, quindi fare clic su**Estrai tutto**.

3. Nella finestra**Estrarre cartelle compresse (zippate) **, **Selezionare una destinazione ed estrarre la casella o il tipo di file,**digitare o selezionare il percorso in cui si desidera estrarre i file.

    >[AZURE.IMPORTANT]È necessario installare StorSimple Snapshot Manager sull'unità C.
 
4. Selezionare la casella di controllo**Mostra i file estratti al termine dell'operazione**,quindi fare clic su**Estrarre**.

    ![Estrarre la finestra di dialogo file](./media/storsimple-snapshot-manager-deployment/HCS_SSM_extract_files.png)

4. Al termine dell'estrazione, verrà aperta la cartella di destinazione. Fare doppio clic sull'icona del programma di installazione dell’applicazione che viene visualizzata nella cartella di destinazione.

5. Quando compare il messaggio **Installazione completata**fare clic su**Chiudi**. Sul desktop dovrebbe essere visualizzata l'icona di StorSimple Snapshot Manager.

    ![icona del desktop](./media/storsimple-snapshot-manager-deployment/HCS_SSM_desktop_icon.png)

### Passaggio 2: Connettere StorSimple Snapshot Manager a un dispositivo

Attenersi alla seguente procedura per la connessione di StorSimple Snapshot Manager a un dispositivo StorSimple.

#### Per connettere StorSimple Snapshot Manager a un dispositivo

1. Fare clic sull’icona StorSimple Snapshot Manager sul desktop. Verrà visualizzata la finestra di gestione StorSimple Snapshot Manager. La finestra contiene un riquadro **Ambito**, un riquadro **Risultati** e un riquadro **Azioni**. 

    ![Interfaccia utente di gestione Snapshot StorSimple](./media/storsimple-snapshot-manager-deployment/HCS_SSM_gui_panes.png)

    - Il riquadro**Ambito** (riquadro a sinistra) contiene un elenco di nodi organizzati in una struttura ad albero. È possibile espandere alcuni nodi per selezionare una vista o dati specifici relativi a tale nodo. Fare clic sull'icona di freccia per espandere o comprimere un nodo. Fare doppio clic su un elemento nel riquadro **Ambito** per visualizzare un elenco di azioni disponibili per quell'elemento. 

    - Il riquadro **Risultati** (riquadro centrale) contiene informazioni dettagliate sul nodo, la visualizzazione o i dati selezionati nel riquadro**Ambito**.

    - Il pannello **Azioni** elenca le operazioni che è possibile eseguire sul nodo, visualizzazione o i dati selezionati nel riquadro **Ambito**.

    Per una descrizione completa dell'interfaccia utente Gestione Snapshot StorSimple, vedere[interfaccia utente di gestione Snapshot StorSimple](storsimple-use-snapshot-manager.md).

2. Nel riquadro **Ambito**, fare clic con il pulsante destro del mouse sul nodo **Dispositivi**, quindi fare clic su **Configura un dispositivo**. Viene visualizzata la finestra di dialogo **Configura un dispositivo**.

    ![Configurare un dispositivo](./media/storsimple-snapshot-manager-deployment/HCS_SSM_config_device.png)

3. Nel **dispositivo** casella di riepilogo, selezionare l'indirizzo IP del dispositivo Microsoft Azure StorSimple o del dispositivo virtuale. Nel casella di testo **Password** digitare la password di Gestione snapshot StorSimple creata per il dispositivo nel portale di gestione di Microsoft Azure. Fare clic su **OK**.

4. Gestione snapshot StorSimple esegue la ricerca del dispositivo identificato. Se il dispositivo è disponibile, gestione Snapshot StorSimple aggiunge una connessione. È possibile[verificare la connessione al dispositivo](#to-verify-the-connection)per verificare che la connessione sia stata aggiunta correttamente.

    Se il dispositivo non è disponibile per qualsiasi motivo, gestione Snapshot StorSimple restituisce un messaggio di errore. Fare clic su **OK** per chiudere il messaggio di errore, quindi fare clic su **Annulla** per chiudere la finestra di dialogo **Configura un dispositivo**.

5. Quando si connette a un dispositivo, gestione Snapshot StorSimple importa ogni gruppo di volumi configurato per il dispositivo, purché il gruppo di volumi abbia associati i backup. I gruppi di volumi che non dispongono di backup associati non vengono importati. Inoltre, non vengono importati i criteri di backup creati per un gruppo di volumi. Per visualizzare i gruppi importati, fare doppio clic su**gruppi di volumi**nodo nel riquadro dell’**ambito** e fare clic su**attivare o disattivare i gruppi importati**.

### Passaggio 3: Verificare la connessione al dispositivo

Utilizzare la procedura seguente per verificare che Gestione Snapshot StorSimple sia connessa al dispositivo StorSimple.

#### Per verificare la connessione

1. Nel **riquadro dell’ambito** fare clic sui**dispositivi**nodo.

    ![Stato del dispositivo StorSimple Snapshot Manager](./media/storsimple-snapshot-manager-deployment/HCS_SSM_Device_status.png)

2. Controllare il pannello **Risultati**:

   - Se viene visualizzato un indicatore verde sull'icona del dispositivo e**disponibile**viene visualizzato nella**stato**colonna, allora il dispositivo è connesso. 

   - Se viene visualizzato un indicatore rosso sull'icona del dispositivo e non disponibile viene visualizzato nella**stato**colonna, allora il dispositivo non è connesso.

   - Se**aggiornamento**viene visualizzato nella**stato**colonna, allora Gestione Snapshot StorSimple sta recuperando i gruppi di volumi e i relativi backup per un dispositivo connesso.

## Aggiornare o reinstallare Gestione Snapshot StorSimple

È necessario disinstallare Gestione Snapshot StorSimple completamente prima di aggiornare o reinstallare il software.

Prima di reinstallare Gestione Snapshot StorSimple, eseguire il backup del database di gestione Snapshot StorSimple esistente nel computer host. Ciò consente di salvare le informazioni di configurazione e criteri di backup in modo che sia possibile ripristinare facilmente questi dati dal backup.

Se si aggiorna o reinstalla Gestione Snapshot StorSimple, attenersi alla seguente procedura:

- Passaggio 1: Disinstallare Gestione Snapshot StorSimple 
- Passaggio 2: Il backup del database di gestione Snapshot StorSimple 
- Passaggio 3: Reinstallare Gestione Snapshot StorSimple e ripristinare il database 

### Passaggio 1: Disinstallare Gestione Snapshot StorSimple

Utilizzare la procedura seguente per disinstallare Gestione Snapshot StorSimple.

#### Per disinstallare Gestione Snapshot StorSimple

1. Nel computer host, aprire il**Pannello di controllo**fare clic su**programmi**quindi fare clic su**programmi e funzionalità**.

2. Nel riquadro sinistro fare clic su**Disinstalla o modifica programma**.

3. Fare doppio clic su**Gestione Snapshot StorSimple**quindi fare clic su**Disinstalla**.

4. Verrà avviato il programma di installazione di gestione Snapshot StorSimple. Fare clic su**Modifica installazione**quindi fare clic su**Disinstalla**.

    >[AZURE.NOTE]Se sono presenti processi MMC in esecuzione in background, ad esempio Gestione Snapshot StorSimple o Gestione disco, la disinstallazione non riesce e verrà visualizzato un messaggio di chiudere tutte le istanze di MMC prima di tentare di disinstallare il programma. Selezionare**automaticamente chiudere le applicazioni e tentare di riavviarle al termine dell'installazione**quindi fare clic su**OK**.
 
5. Al termine del processo di disinstallazione, viene visualizzato il messaggio**Installazione completata** Fare clic su **Close**.

### Passaggio 2: Il backup del database di gestione Snapshot StorSimple

Utilizzare la procedura seguente per creare e salvare una copia del database di gestione Snapshot StorSimple.

#### Per eseguire il backup del database

1. Arrestare il servizio di gestione di Microsoft StorSimple:

   1. Avviare Server Manager.

   2. Nel Dashboard di Server Manager, selezionare **Servizi** dal menu **Strumenti**.

   3. Nella pagina **Servizi**, selezionare **Servizio di gestione Microsoft StorSimple**.

   4. Nel riquadro a destra, sotto **Servizio di gestione Microsoft StorSimple**, fare clic su **Arresta il servizio**.

        ![Stop the StorSimple Manager service](./media/storsimple-snapshot-manager-deployment/HCS_SSM_stop_service.png)

2. Passare a c:\\programdata\\microsoft\\storsimple\\bacatalog.

    >[AZURE.NOTE]ProgramData è una cartella nascosta.

3. Individuare il file XML di catalogo, copiare il file e archiviare la copia in un luogo sicuro o nel cloud.

    ![File catalogo di backup di StorSimple](./media/storsimple-snapshot-manager-deployment/HCS_SSM_bacatalog.png)

4. Riavviare il servizio di gestione di Microsoft StorSimple:

    1. Nel Dashboard di Server Manager, selezionare **Servizi** dal menu **Strumenti**.

    2. Nella pagina**servizi**, seleziona il**servizio di gestione di Microsoft StorSimple**e.

    3. Nel riquadro a destra, sotto **Servizio di gestione Microsoft StorSimple**, fare clic su **Riavvia il servizio**.

### Passaggio 3: Reinstallare Gestione Snapshot StorSimple e ripristinare il database

Per reinstallare Gestione Snapshot StorSimple, seguire i passaggi in[installare una nuova gestione Snapshot StorSimple](#install-a-new-storsimple-snapshot-manager). Quindi, utilizzare la procedura seguente per ripristinare il database di gestione Snapshot StorSimple.

#### Per ripristinare il database

1. Arrestare il servizio di gestione di Microsoft StorSimple:

    1. Avviare Server Manager.

    2. Nel Dashboard di Server Manager, selezionare **Servizi** dal menu **Strumenti**.

    3. Nella pagina **Servizi**, selezionare **Servizio di gestione Microsoft StorSimple**.

    4. Nel riquadro a destra, sotto **Servizio di gestione Microsoft StorSimple**, fare clic su **Arresta il servizio**.

2. Passare a c:\\programdata\\microsoft\\storsimple\\bacatalog.

     >[AZURE.NOTE]ProgramData è una cartella nascosta.

3. Eliminare il file XML di catalogo e sostituirlo con la versione salvata in precedenza.

4. Riavviare il servizio di gestione di Microsoft StorSimple:

    1. Nel Dashboard di Server Manager, selezionare **Servizi** dal menu **Strumenti**.

    2. Nella pagina **Servizi**, selezionare **Servizio di gestione Microsoft StorSimple**.

    3. Nel riquadro a destra, sotto **Servizio di gestione Microsoft StorSimple**, fare clic su **Riavvia il servizio**.

## Passaggi successivi

Per ulteriori informazioni su StorSimple Snapshot Manager, passare a[che cos'è Gestione Snapshot StorSimple?](storsimple-what-is-snapshot-manager.md)

Per ulteriori informazioni sull'interfaccia utente Gestione Snapshot StorSimple, passare a[interfaccia utente di gestione Snapshot StorSimple](storsimple-use-snapshot-manager.md)

<!---HONumber=July15_HO3-->