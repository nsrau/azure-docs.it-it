---
title: Eseguire la migrazione di macchine virtuali AWS ad Azure con il servizio Azure Site Recovery | Microsoft Docs
description: Questo articolo descrive come eseguire la migrazione di macchine virtuali Windows in esecuzione in Amazon Web Services (AWS) ad Azure usando Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/27/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: cd3229773b19b9f6c4d9ff76402f1841a4810bc7
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2018
ms.locfileid: "52851129"
---
# <a name="migrate-amazon-web-services-aws-vms-to-azure"></a>Eseguire la migrazione di macchine virtuali Amazon Web Services (AWS) ad Azure

Questa esercitazione illustra come eseguire la migrazione di macchine virtuali Amazon Web Services (AWS) a macchine virtuali di Azure usando Azure Site Recovery. Quando si esegue la migrazione di istanze EC2 di AWS ad Azure, le macchine virtuali vengono trattate come computer fisici locali. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Verificare i prerequisiti
> * Preparare le risorse di Azure
> * Preparare le istanze EC2 di AWS per la migrazione
> * Distribuire un server di configurazione
> * Abilitare la replica per le macchine virtuali
> * Eseguire un test del failover per verificare che tutto funzioni correttamente
> * Eseguire un unico failover in Azure

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/pricing/free-trial/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti
- Assicurarsi che nelle macchine virtuali di cui si vuole eseguire la migrazione sia in esecuzione una versione supportata del sistema operativo. Le versioni supportate includono: 
  - Windows Server 2016 
  - Windows Server 2012 R2
  - Windows Server 2012 
  - Versione a 64 bit di Windows Server 2008 R2 SP1 o versione successiva
  - Red Hat Enterprise Linux 6.7 (solo istanze virtualizzate di HVM) con un driver Citrix PV o AWS PV. Le istanze che eseguono driver RedHat PV *non* sono supportate.
 - Il servizio Mobility deve essere installato in ogni macchina virtuale da replicare. 

    > [!IMPORTANT]
    > Site Recovery installa il servizio automaticamente quando si abilita la replica per la macchina virtuale. Per l'installazione automatica è necessario preparare un account nelle istanze EC2 che Site Recovery userà per accedere alla macchina virtuale. È possibile usare un account di dominio o locale. 
    > - Per le macchine virtuali Linux l'account deve essere un account radice nel server Linux di origine. 
    > - Per le macchine virtuali Windows se non si usa un account di dominio, disabilitare il controllo Accesso utente remoto nel computer locale:
    >
    >      Nel Registro di sistema in **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System** aggiungere la voce DWORD **LocalAccountTokenFilterPolicy** e impostare il valore su **1**.

- È necessario avere un'istanza EC2 separata da usare come server di configurazione di Site Recovery. L'istanza deve eseguire Windows Server 2012 R2.

## <a name="prepare-azure-resources"></a>Preparare le risorse di Azure

Sono necessarie alcune risorse pronte in Azure per l'uso da parte delle istanze EC2 migrate. Le risorse necessarie includono un account di archiviazione, un insieme di credenziali e una rete virtuale.

### <a name="create-a-storage-account"></a>Creare un account di archiviazione

Le immagini delle macchine replicate sono archiviate in Archiviazione di Azure. Le macchine virtuali di Azure vengono create dall'archiviazione quando si esegue il failover da locale ad Azure.

1. Nel menu a sinistra nel [portale di Azure](https://portal.azure.com) scegliere **Crea una risorsa** > **Archiviazione** > **Account di archiviazione**.
2. Immettere un nome per l'account di archiviazione. Per queste esercitazioni viene usato il nome **awsmigrated2017**. Il nome deve:
    - Essere univoco in Azure
    - Avere una lunghezza compresa tra 3 e 24 caratteri
    - Contenere solo numeri e lettere minuscole
3. Mantenere i valori predefiniti per **Modello di distribuzione**, **Tipologia account**, **Prestazioni** e **Trasferimento sicuro obbligatorio**.
5. Selezionare il valore predefinito **RA-GRS** per **Replica**.
6. Selezionare la sottoscrizione da usare per questa esercitazione.
7. Per **Gruppo di risorse** selezionare **Crea nuovo**. In questo esempio viene usato **migrationRG** per il nome del gruppo di risorse.
8. Per **Posizione** selezionare **Europa occidentale**.
9. Selezionare **Crea** per creare l'account di archiviazione.

### <a name="create-a-vault"></a>Creare un insieme di credenziali

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Tutti i servizi**. Cercare e quindi selezionare **Insiemi di credenziali dei servizi di ripristino**.
2. Nella pagina degli insiemi di credenziali di Servizi di ripristino di Azure selezionare **Aggiungi**.
3. Per **Nome** immettere **myVault**.
4. In **Sottoscrizione** selezionare la sottoscrizione che si vuole usare.
4. Per **Gruppo di risorse** selezionare **Usa esistente** e quindi selezionare **migrationRG**.
5. Per **Posizione** selezionare **Europa occidentale**.
5. Selezionare **Aggiungi al dashboard** per poter accedere rapidamente al nuovo insieme di credenziali dal dashboard.
7. Al termine, selezionare **Crea**.

Per visualizzare il nuovo insieme di credenziali, passare a **Dashboard** > **Tutte le risorse**. Il nuovo insieme di credenziali viene visualizzato anche nella pagina principale **Insiemi di credenziali dei servizi di ripristino**.

### <a name="set-up-an-azure-network"></a>Configurare una rete di Azure

Le macchine virtuali di Azure create dopo la migrazione (failover) vengono aggiunte a questa rete di Azure.

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Crea una risorsa** > **Rete** >
   **Rete virtuale**.
3. Per **Nome** immettere **myMigrationNetwork**.
4. Lasciare il valore predefinito per **Spazio degli indirizzi**.
5. In **Sottoscrizione** selezionare la sottoscrizione che si vuole usare.
6. Per **Gruppo di risorse** selezionare **Usa esistente** e quindi selezionare **migrationRG**.
7. Per **Posizione** selezionare **Europa occidentale**.
8. In **Subnet** lasciare i valori predefiniti per **Nome** e **Intervallo IP**.
9. Lasciare l'opzione **Endpoint di servizio** disabilitata.
10. Al termine, selezionare **Crea**.

## <a name="prepare-the-infrastructure"></a>Preparare l'infrastruttura

Nella pagina dell'insieme di credenziali nel portale di Azure selezionare **Site Recovery** nella sezione **Riquadro attività iniziale** e quindi selezionare **Preparare l'infrastruttura**. Completare questi passaggi.

### <a name="1-protection-goal"></a>1: Obiettivo di protezione

Nella pagina **Obiettivo di protezione** selezionare i valori seguenti:

|    |  |
|---------|-----------|
| Dove si trovano le macchine virtuali? |Selezionare **Locale**.|
| In quale destinazione si vuole eseguire la replica dei computer? |Selezionare **To Azure** (In Azure).|
| I computer sono virtualizzati? |Selezionare **Non virtualizzato/Altro**.|

Al termine, selezionare **OK** per passare alla sezione successiva.

### <a name="2-prepare-source"></a>2: Preparare l'origine

Nella pagina **Prepara origine** selezionare **+ Server di configurazione**.

1. Usare un'istanza EC2 che esegue Windows Server 2012 R2 per creare un server di configurazione e registrarlo con l'insieme di credenziali di ripristino.
2. Configurare il proxy nella macchina virtuale dell'istanza EC2 usata come server di configurazione in modo che possa accedere agli [URL del servizio](site-recovery-support-matrix-to-azure.md).
3. Scaricare il [programma di installazione unificata di Microsoft Azure Site Recovery](https://aka.ms/unifiedinstaller_wus). È possibile scaricarlo nel computer locale e quindi copiarlo nella macchina virtuale usata come server di configurazione.
4. Selezionare il pulsante **Scarica** per scaricare la chiave di registrazione dell'insieme di credenziali. Copiare il file scaricato nella macchina virtuale usata come server di configurazione.
5. Nella macchina virtuale fare clic con il pulsante destro del mouse sul programma di installazione scaricato per l'installazione unificata di Microsoft Azure Site Recovery e quindi scegliere **Esegui come amministratore**.

    1. In **Prima di iniziare** selezionare **Install the configuration server and process server** (Installare il server di configurazione e il server di elaborazione) e quindi selezionare **Avanti**.
    2. In **Third-Party Software License** (Licenza software di terze parti) selezionare **I accept the third-party license agreement** (Accetto il contratto di licenza di terze parti) e quindi selezionare **Avanti**.
    3. In **Registrazione** fare clic su **Sfoglia** e passare al percorso in cui si trova il file della chiave di registrazione dell'insieme di credenziali. Selezionare **Avanti**.
    4. In **Impostazioni Internet** selezionare **Connetti direttamente ad Azure Site Recovery senza server proxy** e quindi selezionare **Avanti**.
    5. Nella pagina **Controllo dei prerequisiti** vengono eseguiti i controlli di diversi elementi. Al termine, fare clic su **Avanti**.
    6. In **MySQL Configuration** (Configurazione MySQL) specificare le password richieste e quindi selezionare **Avanti**.
    7. In **Dettagli ambiente** selezionare **No**. Non è necessario proteggere le macchine VMware. Quindi selezionare **Avanti**.
    8. In **Percorso di installazione** selezionare **Avanti** per accettare il valore predefinito.
    9. In **Selezione rete** selezionare **Avanti** per accettare il valore predefinito.
    10. In **Riepilogo** selezionare **Installa**.
    11. **Stato dell'installazione** visualizza informazioni sullo stato del processo di installazione. Al termine, selezionare **Fine**. Viene visualizzata una finestra di messaggio per il riavvio. Selezionare **OK**. Viene poi visualizzata una finestra con un messaggio sulla passphrase di connessione al server di configurazione. Copiare la passphrase negli Appunti e salvarla in una posizione sicura.
6. Nella macchina virtuale eseguire cspsconfigtool.exe per creare uno o più account di gestione nel server di configurazione. Assicurarsi che gli account di gestione abbiano le autorizzazioni di amministratore per le istanze EC2 di cui si vuole eseguire la migrazione.

Al termine della configurazione del server di configurazione, tornare al portale e selezionare il server appena creato come **Server di configurazione**. Selezionare **OK** per passare al passaggio 3: Preparare la destinazione.

### <a name="3-prepare-target"></a>3: Preparare la destinazione

In questa sezione vengono immesse informazioni sulle risorse create nella sezione precedente [Preparare le risorse di Azure](#prepare-azure-resources) in questa esercitazione.

1. In **Sottoscrizione** selezionare la sottoscrizione di Azure usata nell'esercitazione sulla [preparazione di Azure](tutorial-prepare-azure.md).
2. Selezionare **Resource Manager** come modello di distribuzione.
3. Site Recovery verifica la disponibilità di uno o più account di archiviazione di Azure e reti compatibili. Dovrebbe trattarsi delle risorse create nella sezione precedente [Preparare le risorse di Azure](#prepare-azure-resources) in questa esercitazione.
4. Al termine, fare clic su **OK**.


### <a name="4-prepare-replication-settings"></a>4: Preparare le impostazioni di replica

Prima di abilitare la replica è necessario creare i criteri di replica.

1. Selezionare **+ Replicate and Associate** (+ Replica e associa).
2. In **Nome** immettere **myReplicationPolicy**.
3. Lasciare le altre impostazioni predefinite e quindi selezionare **OK** per creare i criteri. I nuovi criteri vengono associati automaticamente al server di configurazione.

### <a name="5-select-deployment-planning"></a>5: Selezionare la pianificazione di distribuzione

In **È stata completata la pianificazione della distribuzione?** selezionare **Operazione da completare in seguito** e quindi selezionare **OK**.

Quando si è terminato con tutte le cinque sezioni in **Preparare l'infrastruttura**, selezionare **OK**.


## <a name="enable-replication"></a>Abilitare la replica

Abilitare la replica per ogni macchina virtuale di cui si vuole eseguire la migrazione. Quando viene abilitata la replica, Site Recovery installa automaticamente il servizio Mobility.

1. Accedere al [portale di Azure](htts://portal.azure.com).
1. Nella pagina dell'insieme di credenziali in **Riquadro attività iniziale** selezionare **Site Recovery**.
2. In **Per macchine locali e macchine virtuali di Azure** selezionare **Passaggio 1: Eseguire la replica dell'applicazione**. Completare le pagine della procedura guidata con le informazioni seguenti. Selezionare **OK** in ogni pagina al termine:
    - 1: Configurare l'origine

    |  |  |
    |-----|-----|
    | Origine: | Selezionare **Locale**.|
    | Percorso di origine:| Immettere il nome dell'istanza EC2 del server di configurazione.|
    |Tipo di computer: | Selezionare **Computer fisici**.|
    | Server di elaborazione: | Selezionare il server di configurazione dall'elenco a discesa.|

    - 2: Configurare la destinazione

    |  |  |
    |-----|-----|
    | Destinazione: | Lasciare il valore predefinito.|
    | Sottoscrizione: | Selezionare la sottoscrizione in uso.|
    | Gruppo di risorse di post-failover:| Usare il gruppo di risorse creato in [Preparare le risorse di Azure](#prepare-azure-resources).|
    | Modello di distribuzione post-failover: | Selezionare **Gestione risorse**.|
    | Account di archiviazione: | Selezionare l'account di archiviazione creato in [Preparare le risorse di Azure](#prepare-azure-resources).|
    | Rete di Azure: | Selezionare **Configurare ora per le macchine virtuali selezionate**.|
    | Rete di Azure per il post-failover: | Scegliere la rete creata in [Preparare le risorse di Azure](#prepare-azure-resources).|
    | Subnet: | Selezionare il **valore predefinito** nell'elenco a discesa.|

    - 3: Selezionare i computer fisici

      Selezionare **Computer fisico** e quindi immettere i valori per **Nome**, **Indirizzo IP** e **Tipo di sistema operativo** dell'istanza EC2 di cui si vuole eseguire la migrazione. Selezionare **OK**.

    - 4: Configurare le proprietà

      Selezionare l'account creato nel server di configurazione e quindi selezionare **OK**.

    - 5: Configurare le impostazioni di replica

      Verificare che i criteri di replica selezionati nell'elenco a discesa siano **myReplicationPolicy** e quindi selezionare **OK**.

3. Al termine della procedura guidata, selezionare **Abilita replica**.

Per tenere traccia dell'avanzamento del processo **Abilita replica**, passare a **Monitoraggio e report** > **Processi** > **Processi di Site Recovery**. Dopo l'esecuzione del processo **Finalizza protezione** la macchina virtuale è pronta per il failover.        

Quando viene abilitata la replica per una macchina virtuale, le modifiche diventeranno effettive e verranno visualizzate nel portale dopo 15 minuti o più.

## <a name="run-a-test-failover"></a>Eseguire un failover di test

Quando si esegue un failover di test, si verificano gli eventi seguenti:

- Viene eseguito un controllo dei prerequisiti per verificare che tutte le condizioni necessarie per il failover siano in atto.
- Il failover elabora i dati, in modo che sia possibile creare una macchina virtuale di Azure. Selezionando il punto di recupero più recente, viene creato un punto di recupero dai dati.
- Una macchina virtuale di Azure viene creata usando i dati elaborati nel passaggio precedente.

Nel portale eseguire il failover di test:

1. Nella pagina dell'insieme di credenziali passare a **Elementi protetti** > **Elementi replicati**. Selezionare la macchina virtuale e quindi selezionare **Failover di test**.
2. Selezionare un punto di recupero in cui eseguire il failover:
    - **Ultima elaborazione**: viene eseguito il failover della macchina virtuale al punto di recupero più recente elaborato da Site Recovery. Viene visualizzato il timestamp. Con questa opzione, non viene dedicato alcun tempo all'elaborazione dei dati, quindi si ottiene un RTO (Recovery Time Objective) basso.
    - **Coerente con l'app più recente**: questa opzione esegue il failover su tutte le macchine virtuali al punto di ripristino coerente con l'app. Viene visualizzato il timestamp.
    - **Personalizzazione**: selezionare qualsiasi punto di ripristino.

3. In **Failover di test** selezionare la rete di Azure di destinazione a cui vengono connesse le macchine virtuali di Azure dopo il failover. Selezionare la rete creata in [Preparare le risorse di Azure](#prepare-azure-resources).
4. Selezionare **OK** per iniziare il failover. Per verificare lo stato dell'operazione, selezionare la macchina virtuale per visualizzarne le proprietà. Oppure è possibile selezionare il progetto **Failover di test** nella pagina per l'insieme di credenziali. A tale scopo, selezionare **Monitoraggio e report** > **Processi** >  **Processi di Site Recovery**.
5. Al termine del failover, la macchina virtuale di Azure di replica viene visualizzata nel portale di Azure. Per visualizzare la macchina virtuale, selezionare **Macchine virtuali**. Assicurarsi che la macchina virtuale sia delle dimensioni appropriate, che sia connessa alla rete corretta e che sia in esecuzione.
6. Sarà ora possibile connettersi alla macchina virtuale replicata in Azure.
7. Per eliminare le macchine virtuali di Azure create durante il failover di test, selezionare **Pulisci failover di test** nel piano di ripristino. Fare clic su **Note** per registrare e salvare eventuali osservazioni associate al failover di test.

In alcuni scenari, il failover richiede ulteriore elaborazioni. Per il completamento dell'elaborazione sono richiesti dagli 8 ai 10 minuti.

## <a name="migrate-to-azure"></a>Eseguire la migrazione ad Azure

Eseguire un failover per le istanze EC2 per eseguirne la migrazione alle macchine virtuali di Azure:

1. In **Elementi protetti** > **Elementi replicati** selezionare le istanze AWS e quindi selezionare **Failover**.
2. In **Failover** selezionare un **Punto di ripristino** in cui eseguire il failover. Selezionare l'ultimo punto di ripristino e avviare il failover. Nella pagina **Processi** è possibile seguire lo stato del failover.
1. Assicurarsi che la macchina virtuale venga visualizzata in **Elementi replicati**.
2. Fare clic con il pulsante destro del mouse su ogni macchina virtuale e quindi selezionare **Completa la migrazione**. Vengono eseguite le operazioni seguenti:

    - Il processo di migrazione viene completato, viene arrestata la replica per la macchina virtuale AWS e viene arrestata la fatturazione di Site Recovery per la macchina virtuale.
    - In questo passaggio vengono eliminati i dati di replica, ma non le macchine virtuali di cui è stata eseguita la migrazione. 

    ![Completare la migrazione](./media/migrate-tutorial-aws-azure/complete-migration.png)

> [!WARNING]
> *Non annullare un failover in corso*. Prima dell'avvio del failover, la replica della macchina virtuale viene arrestata. Se si annulla un failover in corso, il failover viene arrestato ma non verrà eseguita di nuovo la replica della macchina virtuale.  


## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come eseguire la migrazione di istanze EC2 AWS a macchine virtuali di Azure. Per altre informazioni sulle macchine virtuali di Azure, passare alle esercitazioni per le macchine virtuali di Windows.

> [!div class="nextstepaction"]
> [Esercitazioni per le macchine virtuali di Windows in Azure](../virtual-machines/windows/tutorial-manage-vm.md)
