---
title: Configurare il ripristino di emergenza di Hyper-V (con VMM) con Azure Site Recovery
description: Informazioni su come configurare il ripristino di emergenza di macchine virtuali Hyper-V locali in Cloud System Center VMM in Azure usando Site Recovery.
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: fdf6d9674305ca13af51f3f7b97e0a40568738b6
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73953961"
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-in-vmm-clouds-to-azure"></a>Configurare il ripristino di emergenza in Azure di macchine virtuali Hyper-V locali di cloud VMM

Questo articolo descrive come abilitare la replica per le VM Hyper-V locali gestite da System Center Virtual Machine Manager (VMM) per il ripristino di emergenza in Azure usando il servizio [Azure Site Recovery](site-recovery-overview.md) . Se non si usa VMM, [seguire questa esercitazione](hyper-v-azure-tutorial.md) .

Questa è la terza esercitazione di una serie che mostra come configurare il ripristino di emergenza in Azure per macchine virtuali VMware locali. Nell'esercitazione precedente è stato [preparato l'ambiente Hyper-V locale per il](hyper-v-prepare-on-premises-tutorial.md) ripristino di emergenza in Azure.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Selezionare l'origine e la destinazione della replica.
> * Configurare l'ambiente di replica di origine, inclusi i componenti di Site Recovery locali e l'ambiente di replica di destinazione.
> * Configurare il mapping di rete per eseguire il mapping tra le reti VM VMM e le reti virtuali di Azure.
> * Creare un criterio di replica.
> * Abilitare la replica per una macchina virtuale.

> [!NOTE]
> Le esercitazioni illustrano il percorso di distribuzione più semplice per uno scenario. Quando possibile, vengono usate le opzioni predefinite e non sono riportati tutti i percorsi e le impostazioni possibili. Per istruzioni dettagliate, esaminare gli articoli nella sezione **Guide pratiche** della [documentazione di Site Recovery](https://docs.microsoft.com/azure/site-recovery).

> [!WARNING]
> Il supporto per il ripristino di emergenza di macchine virtuali Hyper-V con System Center VMM sarà deprecato nel prossimo futuro. Prima di procedere, è consigliabile leggere i dettagli relativi a elementi [deprecati](scvmm-site-recovery-deprecation.md) .


## <a name="before-you-begin"></a>Prima di iniziare

Questa è la terza esercitazione di una serie. Si presuppone che siano già state completate le attività delle esercitazioni precedenti:

1. [Preparare Azure](tutorial-prepare-azure.md)
2. [Preparare Hyper-V in locale](tutorial-prepare-on-premises-hyper-v.md)

## <a name="select-a-replication-goal"></a>Selezionare un obiettivo di replica

1. Nel portale di Azure passare a **Insiemi di credenziali dei servizi di ripristino** e selezionare l'insieme di credenziali. L'insieme di credenziali **ContosoVMVault** è stato preparato nell'esercitazione precedente.
2. In **Attività iniziali** selezionare **Site Recovery** e quindi **Preparare l'infrastruttura**.
3. In **Obiettivo di protezione** > **Dove si trovano le macchine virtuali?** selezionare **Locale**.
4. In **In quale destinazione si vuole eseguire la replica dei computer?** selezionare **In Azure**.
5. In **I computer sono virtualizzati?** selezionare **Sì, con Hyper-V**.
6. In si **Usa System Center VMM per gestire gli host Hyper-V?** selezionare **Sì**.
7.  Selezionare **OK**.

    ![Obiettivo di replica](./media/hyper-v-vmm-azure-tutorial/replication-goal.png)

## <a name="confirm-deployment-planning"></a>Confermare la pianificazione della distribuzione

1. Se si sta pianificando una distribuzione di grandi dimensioni, in **Pianificazione della distribuzione** scaricare Deployment Planner per Hyper-V usando il collegamento presente nella pagina. [Altre informazioni](hyper-v-deployment-planner-overview.md) sulla pianificazione della distribuzione di Hyper-V.
2. Per questa esercitazione non è necessario Deployment Planner. In **È stata completata la pianificazione della distribuzione?** selezionare **Operazione da completare in seguito** e quindi selezionare **OK**.

## <a name="set-up-the-source-environment"></a>Configurare l'ambiente di origine

Quando si configura l'ambiente di origine, è necessario installare il provider di Azure Site Recovery nel server VMM e registrare il server nell'insieme di credenziali. Si installa l'agente di servizi di ripristino di Azure in ogni host Hyper-V.

1. In **preparare l'infrastruttura**selezionare **origine**.
2. In **prepara origine**selezionare **+ VMM** per aggiungere un server VMM. In **Aggiungi server** verificare che **System Center VMM server** compaia in **Tipo server**.
3. Scaricare il programma di installazione del provider di Microsoft Azure Site Recovery.
4. Scaricare la chiave di registrazione dell'insieme di credenziali, Questa chiave è necessaria quando si esegue l'installazione del provider. La chiave è valida per cinque giorni dal momento in cui viene generata.
5. Scaricare il programma di installazione per l'agente di Servizi di ripristino di Microsoft Azure.

    ![Download del provider, della chiave di registrazione e dell'agente](./media/hyper-v-vmm-azure-tutorial/download-vmm.png)

### <a name="install-the-provider-on-the-vmm-server"></a>Installare il provider nel server VMM

1. Nell'installazione guidata del provider di Azure Site Recovery > **Microsoft Update**, accettare esplicitamente di usare Microsoft Update per verificare la disponibilità degli aggiornamenti del provider.
2. In **installazione**accettare il percorso di installazione predefinito per il provider e selezionare **Installa**.
3. Dopo l'installazione, nella Microsoft Azure Site Recovery registrazione guidata > **Impostazioni**dell'insieme di credenziali selezionare **Sfoglia**e in **file di chiave**selezionare il file di chiave dell'insieme di credenziali scaricato.
4. Specificare la sottoscrizione di Azure Site Recovery e il nome dell'insieme di credenziali (**ContosoVMVault**). Specificare un nome descrittivo per identificare il server VMM nell'insieme di credenziali.
5. In **Impostazioni proxy** selezionare **Connetti direttamente ad Azure Site Recovery senza server proxy**.
6. Accettare il percorso predefinito per il certificato usato per crittografare i dati. I dati crittografati verranno decrittografati durante il failover.
7. In **Sincronizza metadati cloud** selezionare **Sincronizza i metadati cloud nel portale di Site Recovery**. Questa azione deve essere eseguita solo una volta in ogni server. Selezionare quindi **Registra**.
8. Dopo aver registrato il server nell'insieme di credenziali, selezionare **fine**.

Al termine della registrazione, i metadati del server vengono recuperati da Azure Site Recovery e il server VMM viene visualizzato in **Infrastruttura di Site Recovery**.

### <a name="install-the-recovery-services-agent-on-hyper-v-hosts"></a>Installare l'agente di servizi di ripristino negli host Hyper-V

Installare l'agente in ogni host Hyper-V contenente le macchine virtuali che si desidera replicare.

1. Nell'installazione guidata di Servizi di ripristino di Microsoft Azure agente > **controllo dei prerequisiti**selezionare **Avanti**. Eventuali prerequisiti mancanti verranno installati automaticamente.
2. In **impostazioni di installazione**accettare il percorso di installazione e il percorso della cache. L'unità di cache necessita di almeno 5 GB di spazio di archiviazione. Si consiglia un'unità con almeno 600 GB di spazio libero. Quindi, selezionare **Installa**.
3. In **installazione**, al termine dell'installazione, fare clic su **Chiudi** per terminare la procedura guidata.

    ![Installare l'agente](./media/hyper-v-vmm-azure-tutorial/mars-install.png)

## <a name="set-up-the-target-environment"></a>Configurare l'ambiente di destinazione

1. Selezionare **Preparare l'infrastruttura** > **Destinazione**.
2. Selezionare la sottoscrizione e il gruppo di risorse (**ContosoRG**) in cui verranno create le macchine virtuali di Azure dopo il failover.
3. Selezionare il modello di distribuzione **Gestione risorse** .

Site Recovery verifica la disponibilità di uno o più account di archiviazione di Azure e reti compatibili.

## <a name="configure-network-mapping"></a>Configurare il mapping di rete

1. In **Site Recovery infrastruttura** > **mapping di rete** > **mapping**di rete, selezionare l'icona **+ mapping di rete** .
2. In **Aggiungi mapping di rete**, selezionare il server VMM di origine. Selezionare **Azure** come destinazione.
3. Verificare la sottoscrizione e il modello di distribuzione dopo il failover.
4. In **Rete di origine**, selezionare la rete VM di origine locale.
5. In **Rete di destinazione**, selezionare la rete di Azure in cui inserire le macchine virtuali di Azure di replica create dopo il falover. Quindi selezionare **OK**.

    ![Mapping di rete](./media/hyper-v-vmm-azure-tutorial/network-mapping-vmm.png)

## <a name="set-up-a-replication-policy"></a>Configurare criteri di replica

1. Selezionare **Preparare l'infrastruttura** > **Impostazioni della replica** >  **+Crea e associa**.
2. In **Criteri di creazione e associazione**specificare il nome dei criteri. Il nome usato al momento è **ContosoReplicationPolicy**.
3. Lasciare le impostazioni predefinite e selezionare **OK**.
    - **Frequenza di copia** indica che, dopo la replica iniziale, i dati Delta vengono replicati ogni cinque minuti.
    - La **conservazione dei punti di ripristino** indica che ogni punto di ripristino verrà mantenuto per due ore.
    - **Frequenza snapshot coerenti con l'app**: indica che i punti di ripristino contenenti snapshot coerenti con l'app verranno creati ogni ora.
    - **Ora di inizio della replica iniziale**: indica che la replica iniziale verrà avviata immediatamente.
    - **Crittografare i dati archiviati in Azure** è impostato sul valore predefinito (disattivato) e indica che i dati**inattivi**in Azure non sono crittografati.
4. Dopo aver creato i criteri, selezionare **OK**. Quando si creano nuovi criteri, questi vengono associati automaticamente al cloud VMM.

## <a name="enable-replication"></a>Abilitare la replica

1. In **Eseguire la replica dell'applicazione** selezionare **Origine**.
2. In **Origine** selezionare il cloud VMM. Quindi selezionare **OK**.
3. In **destinazione**verificare la destinazione (Azure), la sottoscrizione dell'insieme di credenziali e selezionare il modello di **Gestione risorse** .
4. Selezionare l'account di archiviazione **contosovmsacct1910171607** e la rete **ContosoASRnet** di Azure.
5. In **Macchine virtuali** > **Seleziona** selezionare la macchina virtuale di cui si vuole eseguire la replica. Quindi selezionare **OK**.

   È possibile tenere traccia dello stato del processo **Abilita protezione** in **Processi** > **Site Recovery jobs** (Processi di Site Recovery). Al termine del processo **Finalizza protezione**, la replica iniziale è completa e la macchina virtuale è pronta per il failover.

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Eseguire un'esercitazione sul ripristino di emergenza](tutorial-dr-drill-azure.md)
