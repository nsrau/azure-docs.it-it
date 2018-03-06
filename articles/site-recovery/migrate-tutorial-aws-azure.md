---
title: Eseguire la migrazione di macchine virtuali da AWS ad Azure con Azure Site Recovery | Microsoft Docs
description: Questo articolo descrive come eseguire la migrazione di macchine virtuali Windows in esecuzione in Amazon Web Services (AWS) ad Azure usando Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 02/27/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 59a09b5d67391f2b48d338d721369f14ed6b4ede
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2018
---
# <a name="migrate-amazon-web-services-aws-vms-to-azure"></a>Eseguire la migrazione di macchine virtuali Amazon Web Services (AWS) ad Azure

Questa esercitazione illustra come eseguire la migrazione di macchine virtuali Amazon Web Services (AWS) a macchine virtuali di Azure usando Site Recovery. Quando viene eseguita la migrazione di istanze EC2 ad Azure, le macchine virtuali vengono trattate come computer fisici locali. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Preparare le risorse di Azure
> * Preparare le istanze EC2 di AWS per la migrazione
> * Distribuire un server di configurazione
> * Abilitare la replica per le macchine virtuali
> * Eseguire un test del failover per verificare che tutto funzioni correttamente
> * Eseguire un unico failover in Azure

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/pricing/free-trial/) prima di iniziare.


## <a name="prepare-azure-resources"></a>Preparare le risorse di Azure

Per usare le istanze EC2 sono necessarie alcune risorse pronte in Azure. Le risorse necessarie includono un account di archiviazione, un insieme di credenziali e una rete virtuale.

### <a name="create-a-storage-account"></a>Creare un account di archiviazione

Le immagini delle macchine replicate sono archiviate nell'archiviazione di Azure. Le macchine virtuali di Azure vengono create dall'archiviazione quando si esegue il failover da locale ad Azure.

1. Nel menu [Portale di Azure](https://portal.azure.com) scegliere **Crea una risorsa** > **Archiviazione** > **Account di archiviazione**.
2. Immettere un nome per l'account di archiviazione. Per queste esercitazioni viene usato il nome **awsmigrated2017**. Il nome deve essere univoco in Azure, avere una lunghezza compresa tra 3 e 24 caratteri e può contenere solo numeri e lettere minuscole.
3. Mantenere i valori predefiniti per **Modello di distribuzione**, **Tipologia account**, **Prestazioni** e **Trasferimento sicuro obbligatorio**.
5. Selezionare il valore predefinito **RA-GRS** per **Replica**.
6. Selezionare la sottoscrizione da usare per questa esercitazione.
7. Per **Gruppo di risorse** selezionare **Crea nuovo**. In questo esempio viene usato il nome **migrationRG**.
8. Selezionare **Europa occidentale** come posizione.
9. Fare clic su **Crea** per creare l'account di archiviazione.

### <a name="create-a-vault"></a>Creare un insieme di credenziali

1. Nel riquadro di spostamento a sinistra del [portale di Azure](https://portal.azure.com) fare clic su **Tutti i servizi** e cercare e selezionare **Insiemi di credenziali dei servizi di ripristino**.
2. Nella pagina Insiemi di credenziali dei servizi di ripristino fare clic su **+ Aggiungi** nella parte superiore sinistra della pagina.
3. Per **Nome** digitare *myVault*.
4. Per **Sottoscrizione** selezionare la sottoscrizione appropriata.
4. Per **Gruppo di risorse** selezionare **Usa esistente** e *migrationRG*.
5. In **Posizione** selezionare *Europa occidentale*.
5. Per accedere rapidamente al nuovo insieme di credenziali dal dashboard, selezionare **Aggiungi al dashboard**.
7. Al termine dell'operazione fare clic su **Crea**.

Il nuovo insieme di credenziali viene visualizzato in **Dashboard** > **Tutte le risorse** e nella pagina principale **Insiemi di credenziali dei servizi di ripristino**.

### <a name="set-up-an-azure-network"></a>Configurare una rete di Azure

Le macchine virtuali di Azure create dopo la migrazione (failover) vengono aggiunte a questa rete.

1. Nel [portale di Azure](https://portal.azure.com) fare clic su **Crea una risorsa** > **Rete** >
   **Rete virtuale**.
3. Per **Nome** digitare *myMigrationNetwork*.
4. Lasciare il valore predefinito per **Spazio degli indirizzi**.
5. Per **Sottoscrizione** selezionare la sottoscrizione appropriata.
6. Per **Gruppo di risorse** selezionare **Usa esistente** e scegliere *migrationRG* dalla casella a discesa.
7. Per **Posizione** selezionare **Europa occidentale**.
8. Lasciare i valori predefiniti per **Subnet**, sia **Nome** che **Intervallo IP**.
9. Lasciare **Endpoint di servizio** disabilitata.
10. Al termine dell'operazione fare clic su **Crea**.


## <a name="prepare-the-ec2-instances"></a>Preparare le istanze EC2

È necessario avere una o più macchine virtuali di cui eseguire la migrazione. Le istanze EC2 devono eseguire una versione a 64 bit di Windows Server 2008 R2 SP1 o successiva, Windows Server 2012, Windows Server 2012 R2, Windows Server 2016 o Red Hat Enterprise Linux 6.7 (solo istanze virtualizzate HVM). Il server deve avere solo driver Citrix PV o AWS PV. Le istanze che eseguono driver RedHat PV non sono supportate.

Il servizio Mobility deve essere installato in ogni VM da replicare. Site Recovery installa il servizio automaticamente quando si abilita la replica per la macchina virtuale. Per l'installazione automatica è necessario preparare un account nelle istanze EC2 che Site Recovery userà per accedere alla macchina virtuale.

È possibile usare un account di dominio o locale. Per le macchine virtuali Linux l'account deve essere un account radice nel server Linux di origine. Per le macchine virtuali Windows se non si usa un account di dominio, disabilitare il controllo Accesso utente remoto nel computer locale:

  - Nel Registro di sistema in **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System** aggiungere la voce DWORD **LocalAccountTokenFilterPolicy** e impostare il valore su 1.

È necessario avere anche un'istanza EC2 separata da usare come server di configurazione di Site Recovery. L'istanza deve eseguire Windows Server 2012 R2.


## <a name="prepare-the-infrastructure"></a>Preparare l'infrastruttura

Nella pagina del portale per l'insieme di credenziali selezionare **Site Recovery** dalla sezione **Riquadro attività iniziale** e fare clic su **Preparare l'infrastruttura**.

### <a name="1-protection-goal"></a>1 Obiettivo di protezione

Nella pagina **Obiettivo di protezione** selezionare i valori seguenti:

|    |  |
|---------|-----------|
| Dove si trovano le macchine virtuali? | **Locale**|
| In quale destinazione si vuole eseguire la replica dei computer? |**In Azure**|
| I computer sono virtualizzati? | **Non virtualizzato/Altro**|

Al termine, fare clic su **OK** per passare alla sezione successiva.

### <a name="2-source-prepare"></a>2 Preparazione dell'origine

Nella pagina **Prepara origine** fare clic su **+ Server di configurazione**.

1. Usare un'istanza EC2 che esegue Windows Server 2012 R2 per creare un server di configurazione e registrarlo con l'insieme di credenziali di ripristino.

2. Configurare il proxy nella macchina virtuale dell'istanza EC2 usata come server di configurazione in modo che possa accedere agli [URL del servizio](site-recovery-support-matrix-to-azure.md).

3. Scaricare il programma di [installazione unificata di Microsoft Azure Site Recovery](http://aka.ms/unifiedinstaller_wus). È possibile scaricarlo nel computer locale e quindi copiarlo nella macchina virtuale usata come server di configurazione.

4. Fare clic sul pulsante **Scarica** per scaricare la chiave di registrazione dell'insieme di credenziali. Copiare il file scaricato nella macchina virtuale usata come server di configurazione.

5. Nella macchina virtuale fare clic con il pulsante destro del mouse sul programma di installazione scaricato per l'**installazione unificata di Microsoft Azure Site Recovery** e selezionare **Esegui come amministratore**.

    1. In **Prima di iniziare** selezionare **Install the configuration server and process server** (Installare il server di configurazione e il server di elaborazione) e quindi fare clic su **Avanti**.
    2. In **Third-Party Software License** (Licenza software di terze parti) selezionare **I accept the third-party license agreement** (Accetto il contratto di licenza di terze parti), quindi fare clic su **Avanti**.
    3. In **Registration** fare clic su Sfoglia e passare al percorso in cui si trova il file della chiave di registrazione dell'insieme di credenziali e fare clic su **Avanti**.
    4. In **Impostazioni Internet** selezionare **Connetti direttamente ad Azure Site Recovery senza server proxy**, quindi fare clic su **Avanti**.
    5. Nella pagina **Controllo dei prerequisiti** vengono eseguiti i controlli di diversi elementi. Al termine, fare clic su **Avanti**.
    6. In **MySQL Configuration** (Configurazione MySQL) specificare le password richieste e quindi fare clic su **Avanti**.
    7. In **Dettagli ambiente** selezionare **No** poiché non è necessario proteggere i computer VMware e quindi fare clic su **Avanti**.
    8. In **Percorso di installazione** fare clic su **Avanti** per accettare il valore predefinito.
    9. In **Selezione rete** fare clic su **Avanti** per accettare il valore predefinito.
    10. In **Riepilogo** fare clic su **Installa**.
    11. **Stato dell'installazione** visualizza informazioni sullo stato di avanzamento del processo di installazione. Al termine, fare clic su **Fine**. Nella finestra popup in cui viene comunicato che può essere necessario un riavvio fare clic su **OK**. Nella finestra popup relativa alla passphrase di connessione del server di configurazione copiare la passphrase negli Appunti e salvarla in un percorso sicuro.

6. Nella macchina virtuale eseguire **cspsconfigtool.exe** per creare uno o più account di gestione nel server di configurazione. Assicurarsi che gli account di gestione abbiano le autorizzazioni di amministratore per le istanze EC2 di cui si vuole eseguire la migrazione.

Al termine della configurazione del server di configurazione, tornare al portale e selezionare il server appena creato come **Server di configurazione** e fare clic su *OK** per passare al passaggio 3 Preparazione della destinazione.

### <a name="3-target-prepare"></a>3 Preparazione della destinazione

In questa sezione vengono immesse informazioni sulle risorse create nella sezione precedente di questa esercitazione [Preparare le risorse di Azure](#prepare-azure-resources).

1. In **Sottoscrizione** selezionare la sottoscrizione di Azure usata nell'esercitazione sulla [preparazione di Azure](tutorial-prepare-azure.md).
2. Selezionare **Resource Manager** come modello di distribuzione.
3. Site Recovery verifica la disponibilità di uno o più account di archiviazione di Azure e reti compatibili. Si tratta delle risorse create nella sezione precedente di questa esercitazione [Preparare le risorse di Azure](#prepare-azure-resources)
4. Al termine, fare clic su **OK**.


### <a name="4-replication-settings-prepare"></a>4 Preparazione delle impostazioni di replica

Prima di abilitare la replica è necessario creare i criteri di replica

1. Fare clic su **+ Replicate and Associate** (+ Replica e associa).
2. In **Nome** digitare **myReplicationPolicy**.
3. Lasciare le altre impostazioni predefinite e fare clic su **OK** per creare i criteri. I nuovi criteri vengono associati automaticamente al server di configurazione.

### <a name="5-deployment-planning-select"></a>5 Selezione della pianificazione della distribuzione

In **È stata completata la pianificazione della distribuzione?** selezionare **Operazione da completare in seguito** dalla casella a discesa e quindi fare clic su **OK**.

Dopo aver completato i passaggi di tutte e cinque le sezioni di **Preparare l'infrastruttura** fare clic su **OK**.


## <a name="enable-replication"></a>Abilitare la replica

Abilitare la replica per ogni macchina virtuale di cui si vuole eseguire la migrazione. Quando viene abilitata la replica, Site Recovery installa automaticamente il servizio Mobility.

1. Aprire il [portale di Azure](htts://portal.azure.com).
1. Nella pagina dell'insieme di credenziali in **Riquadro attività iniziale** fare clic su **Site Recovery**.
2. In **Per macchine locali e macchine virtuali di azure** fare clic su **Passaggio 1: Eseguire la replica dell'applicazione**. Nelle pagine della procedura guidata inserire le informazioni seguenti e fare clic su **OK** in ogni pagina:
    - 1 Configurazione dell'origine:

    |  |  |
    |-----|-----|
    | Origine: | **Locale**|
    | Percorso di origine:| Nome dell'istanza EC2 del server di configurazione.|
    |Tipo di computer: | **Computer fisici**|
    | Server di elaborazione: | Selezionare il server di configurazione dall'elenco a discesa.|

    - 2 Configurazione della destinazione

    |  |  |
    |-----|-----|
    | Destinazione: | Lasciare il valore predefinito.|
    | Sottoscrizione: | Selezionare la sottoscrizione in uso.|
    | Gruppo di risorse di post-failover:| Usare il gruppo di risorse creato nella sezione [Preparare le risorse di Azure](#prepare-azure-resources).|
    | Modello di distribuzione post-failover: | Scegliere **Resource Manager**|
    | Account di archiviazione: | Scegliere l'account di archiviazione creato nella sezione [Preparare le risorse di Azure](#prepare-azure-resources).|
    | Rete di Azure: | Scegliere **Configurare ora per le macchine virtuali selezionate**|
    | Rete di Azure per il post-failover: | Scegliere la rete creata nella sezione [Preparare le risorse di Azure](#prepare-azure-resources).|
    | Subnet: | Selezionare il **valore predefinito** dalla casella a discesa.|

    - 3 Selezione dei computer fisici

        Fare clic su **+ Computer fisico** e quindi immettere **Nome**, **Indirizzo IP** e **Tipo di sistema operativo** dell'istanza EC2 di cui si vuole eseguire la migrazione e quindi fare clic su **OK**.

    - 4 Configurazione delle proprietà

        Selezionare l'account creato nel server di configurazione dalla casella a discesa e fare clic su **OK**.

    - 5 Configurazione delle impostazioni di replica

        Verificare che i criteri di replica selezionati nella casella a discesa siano **myReplicationPolicy** e quindi fare clic su **OK**.

3. Al termine della procedura guidata, fare clic su **Abilita replica**.


È possibile tenere traccia dell'avanzamento del processo di **abilitazione della protezione** in **Monitoraggio e report** > **Processi** > **Processi di Site Recovery**. Dopo l'esecuzione del processo **Finalizza protezione** la macchina virtuale è pronta per il failover.        

Quando viene abilitata la replica per una macchina virtuale, le modifiche diventeranno effettive e verranno visualizzate nel portale dopo 15 minuti o più.

## <a name="run-a-test-failover"></a>Eseguire un failover di test

Quando si esegue un failover di test, si verifica quanto segue:

1. Viene eseguito un controllo dei prerequisiti per verificare che tutte le condizioni necessarie per il failover siano in atto.
2. Il failover elabora i dati, in modo che sia possibile creare una macchina virtuale di Azure. Selezionando il punto di recupero più recente, viene creato un punto di recupero dai dati.
3. Una macchina virtuale di Azure viene creata usando i dati elaborati nel passaggio precedente.

Nel portale eseguire il failover di test come descritto di seguito:

1. Nella pagina dell'insieme di credenziali passare a **Elementi protetti** > **Elementi replicati**> fare clic sulla macchina virtuale > **+ Failover di test**.

2. Selezionare un punto di ripristino da usare per il failover:
    - **Elaborato più recente (RTO basso)**: esegue il failover della macchina virtuale nel punto di ripristino più recente elaborato da Site Recovery. Viene visualizzato il timestamp. Con questa opzione, non viene impiegato alcun tempo di elaborazione dati, pertanto viene fornito un RTO (Recovery Time Objective) basso.
    - **Coerente con l'app più recente**: questa opzione esegue il failover su tutte le macchine virtuali al punto di ripristino coerente con l'app. Viene visualizzato il timestamp.
    - **Personalizzazione**: selezionare qualsiasi punto di ripristino.
3. In **Failover di test** selezionare la rete di Azure di destinazione a cui vengono connesse le macchine virtuali di Azure dopo il failover. Selezionare la rete creata nella sezione [Preparare le risorse di Azure](#prepare-azure-resources).
4. Fare clic su **OK** per iniziare il failover. È possibile tenere traccia dell'avanzamento facendo clic sulla macchina virtuale per visualizzarne le proprietà. In alternativa, fare clic sul processo **Failover di test** nella pagina dell'insieme di credenziali in **Monitoraggio e report** > **Processi** >
   **Processi di Site Recovery**.
5. Al termine del failover, la macchina virtuale di Azure di replica viene visualizzata nel portale di Azure in **Macchine virtuali**. Verificare che la macchina virtuale sia delle dimensioni appropriate, che sia connessa alla rete corretta e che sia in esecuzione.
6. Sarà ora possibile connettersi alla macchina virtuale replicata in Azure.
7. Per eliminare le macchine virtuali di Azure create durante il failover di test, fare clic su **Pulisci failover di test** nel piano di ripristino. Fare clic su **Note** per registrare e salvare eventuali osservazioni associate al failover di test.

In alcuni scenari il failover richiede un'altra elaborazione il cui completamento richiede da 8 a 10 minuti.


## <a name="migrate-to-azure"></a>Eseguire la migrazione ad Azure

Eseguire un failover per le istanze EC2 per eseguirne la migrazione alle macchine virtuali di Azure.

1. In **Elementi protetti** > **Elementi replicati** fare clic sulle istanze AWS > **Failover**.
2. In **Failover** selezionare un **Punto di ripristino** in cui eseguire il failover. Selezionare l'ultimo punto di ripristino.
3. Selezionare **Arrestare la macchina prima di iniziare il failover** se si vuole provare ad arrestare le macchine virtuali di origine tramite Site Recovery prima di attivare il failover. Il failover continua anche se l'arresto ha esito negativo. Nella pagina **Processi** è possibile seguire lo stato del failover.
4. Verificare che la macchina virtuale venga visualizzata in **Elementi replicati**.
5. Fare clic con il pulsante destro del mouse su ogni macchina virtuale e scegliere **Completa la migrazione**. Il processo di migrazione viene completato, viene arrestata la replica per la macchina virtuale AWS e viene arrestata la fatturazione di Site Recovery per la macchina virtuale.

    ![Completare la migrazione](./media/migrate-tutorial-aws-azure/complete-migration.png)

> [!WARNING]
> **Non annullare un failover in corso**: prima dell'avvio del failover, la replica della macchina virtuale viene arrestata. Se si annulla un failover in corso, il failover viene arrestato ma non viene eseguita di nuovo la replica della macchina virtuale.  




## <a name="next-steps"></a>Passaggi successivi

In questo argomento si è appreso come eseguire la migrazione di istanze EC2 AWS a macchine virtuali di Azure. Per altre informazioni sulle macchine virtuali di Azure, passare alle esercitazioni per le macchine virtuali di Windows.

> [!div class="nextstepaction"]
> [Esercitazioni per le macchine virtuali di Windows in Azure](../virtual-machines/windows/tutorial-manage-vm.md)
