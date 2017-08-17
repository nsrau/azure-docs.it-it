---
title: Distribuzione di StorSimple Snapshot Manager | Microsoft Docs
description: "Informazioni su come scaricare e installare Snapshot StorSimple Manager, uno snap-in MMC per la gestione delle funzionalità di protezione e backup dei dati di StorSimple."
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: 
ms.assetid: f0128f57-519e-49ec-9187-23575809cdbe
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: v-sharos
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: cde355381b0d726a1ab340bc4230b2dc8f6e2c56
ms.contentlocale: it-it
ms.lasthandoff: 07/08/2017


---
# <a name="deploy-the-storsimple-snapshot-manager-mmc-snap-in"></a>Distribuire lo snap-in di StorSimple Snapshot Manager

## <a name="overview"></a>Panoramica
StorSimple Snapshot Manager è uno snap-in Microsoft Management Console (MMC) che semplifica la protezione dei dati e la gestione dei backup in un ambiente di Microsoft Azure StorSimple. Con StorSimple Snapshot Manager è possibile gestire Microsoft Azure StorSimple in locale e archiviare nella cloud come se fosse un sistema di archiviazione completamente integrato, semplificando i processi di backup e ripristino e riducendo i costi di conseguenza. 

In questa esercitazione vengono descritti i requisiti di configurazione, nonché le procedure per installare, rimuovere e aggiornare StorSimple Snapshot Manager.

> [!NOTE]
> * Non è possibile usare Gestione Snapshot StorSimple per gestire gli array virtuali di Microsoft Azure StorSimple (noti anche come dispositivi virtuali StorSimple locali).
> * Se si prevede di installare l'aggiornamento 2 di StorSimple sul dispositivo StorSimple, assicurarsi di scaricare la versione più recente di StorSimple Snapshot Manager e installarla **prima di installare l'aggiornamento 2 di StorSimple**. La versione più recente di StorSimple Snapshot Manager è compatibile con le versioni precedenti e funziona con tutte le versioni rilasciate di Microsoft Azure StorSimple. Se si utilizza la versione precedente di StorSimple Snapshot Manager, è necessario aggiornarla (non è necessario disinstallare la versione precedente prima di installare la nuova versione).


## <a name="storsimple-snapshot-manager-installation"></a>Installazione di StorSimple Snapshot Manager
StorSimple Snapshot Manager può essere installato nei computer che eseguono il sistema operativo Windows Server 2008 R2 SP1, Windows Server 2012 o Windows Server 2012 R2. Nei server che eseguono Windows 2008 R2, è necessario installare Windows Server 2008 SP1 e Windows Management Framework 3.0.

Prima di installare o aggiornare lo StorSimple Snapshot Manager per Microsoft Management Console (MMC), assicurarsi che il dispositivo di Microsoft Azure StorSimple e l'host siano configurati correttamente.

## <a name="configure-prerequisites"></a>Configurazione dei prerequisiti
I passaggi seguenti forniscono una panoramica generale delle attività di configurazione che è necessario completare prima di installare StorSimple Snapshot Manager. Per informazioni complete sulla configurazione  e sull’installazione di Microsoft Azure StorSimple, inclusi i requisiti di sistema e istruzioni dettagliate, vedere [Distribuire dispositivo StorSimple in locale](storsimple-8000-deployment-walkthrough-u2.md).

> [!IMPORTANT]
> Prima di iniziare, consultare [Elenco di controllo configurazione della distribuzione](storsimple-8000-deployment-walkthrough-u2.md#deployment-configuration-checklist) e i [Prerequisiti di distribuzione](storsimple-8000-deployment-walkthrough-u2.md#deployment-prerequisites) in [Distribuire un dispositivo StorSimple locale](storsimple-8000-deployment-walkthrough-u2.md).
> <br>
> 
> 

### <a name="before-you-install-storsimple-snapshot-manager"></a>Prima di installare StorSimple Snapshot Manager
1. Disimballare, montare e collegare il dispositivo Microsoft Azure StorSimple, come descritto in[installare il dispositivo StorSimple 8100](storsimple-8100-hardware-installation.md) o [installare il dispositivo StorSimple 8600](storsimple-8600-hardware-installation.md).
2. Assicurarsi che il computer host esegua uno dei seguenti sistemi operativi:
   
   * Windows Server 2008 R2 (nei server che eseguono Windows 2008 R2, è necessario inoltre installare Windows Server 2008 SP1 e Windows Management Framework 3.0)
   * Windows Server 2012
   * Windows Server 2012 R2
     
     Per un dispositivo virtuale StorSimple, l'host deve essere una macchina virtuale di Microsoft Azure.
3. Assicurarsi che siano soddisfatti i requisiti di configurazione Microsoft Azure StorSimple. Per ulteriori informazioni, vedere [Prerequisiti di distribuzione](storsimple-8000-deployment-walkthrough-u2.md#deployment-prerequisites).
4. Connettere il dispositivo all'host ed eseguire la configurazione iniziale. Per ulteriori informazioni, vedere [passaggi di distribuzione](storsimple-8000-deployment-walkthrough-u2.md#deployment-steps).
5. Creare i volumi nel dispositivo, assegnarli all'host e verificare che l'host sia in grado di montarli e utilizzarli. StorSimple Snapshot Manager supporta i tipi di volumi seguenti:
   
   * Volumi di base
   * Volumi semplici
   * Volumi dinamici
   * Volumi dinamici con mirroring (RAID 1)
   * Volumi condivisi del cluster
     
     Per informazioni sulla creazione di volumi nel dispositivo StorSimple o nel dispositivo virtuale StorSimple, vedere [Passaggio 6: Creare un volume](storsimple-8000-deployment-walkthrough-u2.md#step-6-create-a-volume) in [Distribuire dispositivo StorSimple in locale](storsimple-8000-deployment-walkthrough-u2.md).

## <a name="install-a-new-storsimple-snapshot-manager"></a>Installare un nuovo StorSimple Snapshot Manager
Prima di installare StorSimple Snapshot Manager, assicurarsi che i volumi creati nel dispositivo StorSimple o nel dispositivo virtuale StorSimple siano montati, inizializzati e formattati come descritto in [Prerequisiti di configurazione](#configure-prerequisites).

> [!IMPORTANT]
> * Per un dispositivo virtuale StorSimple, l'host deve essere una macchina virtuale di Microsoft Azure. 
> * L'host deve eseguire Windows 2008 R2, Windows Server 2012 o Windows Server 2012 R2. Se il server esegue Windows Server 2008 R2, è necessario installare anche Windows Server 2008 SP1 e Windows Management Framework 3.0.
> * Prima di poter connettere il dispositivo di StorSimple Snapshot Manager, è necessario configurare una connessione iSCSI dall'host al dispositivo StorSimple.

Seguire questi passaggi per completare una nuova installazione di StorSimple Snapshot Manager. Se si installa un aggiornamento, passare a [Aggiorna o reinstalla StorSimple Snapshot Manager](#upgrade-or-reinstall-storsimple-snapshot-manager).

* Passaggio 1: Installare StorSimple Snapshot Manager 
* Passaggio 2: Connettere StorSimple Snapshot Manager a un dispositivo 
* Passaggio 3: Verificare la connessione al dispositivo 

### <a name="step-1-install-storsimple-snapshot-manager"></a>Passaggio 1: Installare StorSimple Snapshot Manager
Utilizzare la procedura seguente per installare StorSimple Snapshot Manager.

#### <a name="to-install-storsimple-snapshot-manager"></a>Per installare StorSimple Snapshot Manager
1. Scaricare il software StorSimple Snapshot Manager (Vai al [StorSimple Snapshot Manager](https://www.microsoft.com/download/details.aspx?id=44220) in Microsoft Download Center) e salvarlo localmente nell'host.
2. In File Explorer fare clic sul pulsante destro del mouse sulla cartella compressa, quindi fare clic su **Estrai tutto**.
3. Nella finestra **Estrazione cartelle compresse**, nel riquadro **Selezionare una destinazione ed estrarre i file** digitare o selezionare il percorso in cui si desidera estrarre i file.
   
    > [!IMPORTANT]
    > È necessario installare StorSimple Snapshot Manager sull'unità C.
    
4. Selezionare la casella di controllo **Mostra i file estratti al termine dell'operazione**,quindi fare clic su **Estrai**.
   
    ![Estrarre la finestra di dialogo file](./media/storsimple-snapshot-manager-deployment/HCS_SSM_extract_files.png) 
5. Al termine dell'estrazione, verrà aperta la cartella di destinazione. Fare doppio clic sull'icona del programma di installazione dell’applicazione che viene visualizzata nella cartella di destinazione.
6. Quando compare il messaggio **Installazione completata** fare clic su **Chiudi**. Sul desktop dovrebbe essere visualizzata l'icona di StorSimple Snapshot Manager.
   
    ![icona del desktop](./media/storsimple-snapshot-manager-deployment/HCS_SSM_desktop_icon.png) 

### <a name="step-2-connect-storsimple-snapshot-manager-to-a-device"></a>Passaggio 2: Connettere StorSimple Snapshot Manager a un dispositivo
Attenersi alla seguente procedura per la connessione di StorSimple Snapshot Manager a un dispositivo StorSimple.

#### <a name="to-connect-storsimple-snapshot-manager-to-a-device"></a>Per connettere StorSimple Snapshot Manager a un dispositivo
1. Fare clic sull’icona StorSimple Snapshot Manager sul desktop. Verrà visualizzata la finestra di StorSimple Snapshot Manager. La finestra contiene un riquadro **Ambito**, un riquadro **Risultati** e un riquadro **Azioni**. 
   
    ![Interfaccia utente di StorSimple Snapshot Manager](./media/storsimple-snapshot-manager-deployment/HCS_SSM_gui_panes.png)
   
   * Il riquadro **Ambito** (riquadro a sinistra) contiene un elenco di nodi organizzati in una struttura ad albero. È possibile espandere alcuni nodi per selezionare una vista o dati specifici relativi a tale nodo. Fare clic sull'icona di freccia per espandere o comprimere un nodo. Fare doppio clic su un elemento nel riquadro **Ambito** per visualizzare un elenco di azioni disponibili per quell'elemento.
   * Il riquadro **Risultati** (riquadro centrale) contiene informazioni dettagliate sul nodo, vista o dati selezionati nel riquadro **Ambito**.
   * Il pannello **Azioni** elenca le operazioni che è possibile eseguire sul nodo, visualizzazione o i dati selezionati nel riquadro **Ambito**.
     
     Per una descrizione completa dell'interfaccia utente di Gestione snapshot StorSimple, vedere [interfaccia utente di StorSimple Snapshot Manager](storsimple-use-snapshot-manager.md).
2. Nel riquadro **Ambito**, fare clic con il pulsante destro del mouse sul nodo **Dispositivi**, quindi fare clic su **Configura un dispositivo**. Viene visualizzata la finestra di dialogo **Configura un dispositivo** .
   
    ![Configura un dispositivo](./media/storsimple-snapshot-manager-deployment/HCS_SSM_config_device.png) 
3. Nella casella di riepilogo **Dispositivo** , selezionare l'indirizzo IP del dispositivo StorSimple di Microsoft Azure o del dispositivo virtuale. Nella casella di testo **Password**, digitare la password di StorSimple Snapshot Manager creata per il dispositivo nel portale di Azure. Fare clic su **OK**.
4. Gestione snapshot StorSimple esegue la ricerca del dispositivo identificato. Se il dispositivo è disponibile, StorSimple Snapshot Manager aggiunge una connessione. È possibile [verificare la connessione al dispositivo](#to-verify-the-connection) per verificare che la connessione sia stata aggiunta correttamente.
   
    Se il dispositivo non è disponibile per qualsiasi motivo, Gestione snapshot StorSimple restituisce un messaggio di errore. Fare clic su **OK** per chiudere il messaggio di errore e quindi fare clic su **Annulla** per chiudere la finestra di dialogo **Configurare un dispositivo**.
5. Quando si connette a un dispositivo, StorSimple Snapshot Manager importa ogni gruppo di volumi configurato per il dispositivo, purché il gruppo di volumi abbia associati i backup. I gruppi di volumi che non dispongono di backup associati non vengono importati. Inoltre, non vengono importati i criteri di backup creati per un gruppo di volumi. Per visualizzare i gruppi importati, fare doppio clic sul nodo **Gruppi di volumi** nel riquadro **Ambito** e fare clic su **Toggle imported groups** (Attiva o disattiva i gruppi importati).

### <a name="step-3-verify-the-connection-to-the-device"></a>Passaggio 3: Verificare la connessione al dispositivo
Utilizzare la procedura seguente per verificare che StorSimple Snapshot Manager sia connesso al dispositivo StorSimple.

#### <a name="to-verify-the-connection"></a>Per verificare la connessione
1. Nel riquadro **Ambito** fare clic sul nodo **Dispositivi**.
   
    ![Stato del dispositivo StorSimple Snapshot Manager](./media/storsimple-snapshot-manager-deployment/HCS_SSM_Device_status.png) 
2. Controllare il pannello **Risultati** : 
   
   * Se viene visualizzato un indicatore verde sull'icona del dispositivo e **Disponibile** appare nella colonna **Stato**, allora il dispositivo è connesso. 
   * Se viene visualizzato un indicatore rosso sull'icona del dispositivo e Non disponibile viene visualizzato nella colonna **Stato** , allora il dispositivo non è connesso. 
   * Se **Aggiornamento** viene visualizzato nella colonna **Stato**, allora StorSimple Snapshot Manager sta recuperando i gruppi di volumi e i relativi backup per un dispositivo connesso.

## <a name="upgrade-or-reinstall-storsimple-snapshot-manager"></a>Aggiorna o reinstalla StorSimple Snapshot Manager
È necessario disinstallare StorSimple Snapshot Manager completamente prima di aggiornare o reinstallare il software. 

Prima di reinstallare StorSimple Snapshot Manager, eseguire il backup del database di StorSimple Snapshot Manager esistente nel computer host. Ciò consente di salvare le informazioni di configurazione e criteri di backup in modo che sia possibile ripristinare facilmente questi dati dal backup.

Se si aggiorna o reinstalla StorSimple Snapshot Manager, attenersi alla seguente procedura:

* Passaggio 1: Disinstallare StorSimple Snapshot Manager 
* Passaggio 2: Eseguire il backup del database di StorSimple Snapshot Manager 
* Passaggio 3: Reinstallare StorSimple Snapshot Manager e ripristinare il database 

### <a name="step-1-uninstall-storsimple-snapshot-manager"></a>Passaggio 1: Disinstallare StorSimple Snapshot Manager
Utilizzare la procedura seguente per disinstallare StorSimple Snapshot Manager

#### <a name="to-uninstall-storsimple-snapshot-manager"></a>Per disinstallare StorSimple Snapshot Manager
1. Nel computer host, aprire il **Pannello di controllo** fare clic su **Programmi** quindi fare clic su **Programmi e funzionalità**.
2. Nel riquadro sinistro fare clic su **Disinstalla o modifica programma**.
3. Fare clic con il pulsante destro del mouse su **StorSimple Snapshot Manager**, quindi fare clic su **Disisntalla**.
4. Verrà avviato il programma di installazione di StorSimple Snapshot Manager. Fare clic su **Modifica installazione** quindi fare clic su **Disinstalla**.
   
   > [!NOTE]
   > Se sono presenti processi MMC in esecuzione in background, ad esempio StorSimple Snapshot Manager o Disk Management, la disinstallazione non riesce e verrà visualizzato un messaggio di chiudere tutte le istanze di MMC prima di tentare di disinstallare il programma. Selezionare **Chiudere automaticamente le applicazioni e tentare di riavviarle al termine dell'installazione** quindi fare clic su **OK**.
   > 
   > 
5. Al termine del processo di disinstallazione, viene visualizzato il messaggio **Installazione completata**. Fare clic su **Close**.

### <a name="step-2-back-up-the-storsimple-snapshot-manager-database"></a>Passaggio 2: Eseguire il backup del database di StorSimple Snapshot Manager
Utilizzare la procedura seguente per creare e salvare una copia del database di StorSimple Snapshot Manager.

#### <a name="to-back-up-the-database"></a>Per eseguire il backup del database
1. Arrestare il servizio di gestione Microsoft StorSimple:
   
   1. Avviare Server Manager.
   2. Nel Dashboard di Server Manager, selezionare **Servizi** dal menu **Strumenti**.
   3. Nella pagina **Servizi** selezionare **Microsoft StorSimple Management Service**.
   4. Nel riquadro a destra, sotto **Microsoft StorSimple Management Service**, fare clic su **Arresta il servizio**.
      
        ![Arrestare il servizio Gestione dispositivi StorSimple](./media/storsimple-snapshot-manager-deployment/HCS_SSM_stop_service.png)
2. Passare a C:\ProgramData\Microsoft\StorSimple\BACatalog. 
   
   > [!NOTE]
   > ProgramData è una cartella nascosta.
  
3. Individuare il file XML di catalogo, copiare il file e archiviare la copia in un luogo sicuro o nel cloud.
   
    ![File catalogo di backup di StorSimple](./media/storsimple-snapshot-manager-deployment/HCS_SSM_bacatalog.png)
4. Riavviare il servizio di gestione di Microsoft StorSimple: 
   
   1. Nel Dashboard di Server Manager, selezionare **Servizi** dal menu **Strumenti**.
   2. Nella pagina **Servizi** selezionare **Microsoft StorSimple Management Service**.
   3. Nel riquadro a destra, sotto **Microsoft StorSimple Management Service**, fare clic su **Riavvia il servizio**. 

### <a name="step-3-reinstall-storsimple-snapshot-manager-and-restore-the-database"></a>Passaggio 3: Reinstallare StorSimple Snapshot Manager e ripristinare il database
Per reinstallare StorSimple Snapshot Manager, seguire i passaggi in [Installare un nuovo StorSimple Snapshot Manager](#install-a-new-storsimple-snapshot-manager). Quindi, utilizzare la procedura seguente per ripristinare il database di StorSimple Snapshot Manager.

#### <a name="to-restore-the-database"></a>Per ripristinare il database
1. Arrestare il servizio di gestione Microsoft StorSimple:
   
   1. Avviare Server Manager.
   2. Nel Dashboard di Server Manager, selezionare **Servizi** dal menu **Strumenti**.
   3. Nella pagina **Servizi** selezionare **Microsoft StorSimple Management Service**.
   4. Nel riquadro a destra, sotto **Microsoft StorSimple Management Service**, fare clic su **Arresta il servizio**.
2. Passare a C:\ProgramData\Microsoft\StorSimple\BACatalog.
   
   > [!NOTE]
   > ProgramData è una cartella nascosta.
   > 
   > 
3. Eliminare il file XML di catalogo e sostituirlo con la versione salvata in precedenza.
4. Riavviare il servizio di gestione di Microsoft StorSimple: 
   
   1. Nel Dashboard di Server Manager, selezionare **Servizi** dal menu **Strumenti**.
   2. Nella pagina **Servizi** selezionare **Microsoft StorSimple Management Service**.
   3. Nel riquadro a destra, sotto **Microsoft StorSimple Management Service**, fare clic su **Riavvia il servizio**.

## <a name="next-steps"></a>Passaggi successivi
* Per ulteriori informazioni su StorSimple Snapshot Manager, passare a [Cos'è StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md).
* Per ulteriori informazioni sull'interfaccia utente di Gestione snapshot StorSimple, passare a [Interfaccia utente di StorSimple Snapshot Manager](storsimple-use-snapshot-manager.md).
* Per ulteriori informazioni sull'utilizzo di gestione Snapshot StorSimple, passare a [utilizzare StorSimple Snapshot Manager per amministrare la soluzione di StorSimple](storsimple-snapshot-manager-admin.md).


