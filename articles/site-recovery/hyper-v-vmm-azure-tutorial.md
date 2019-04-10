---
title: Configurare il ripristino di emergenza in Azure di macchine virtuali Hyper-V locali di cloud VMM con Azure Site Recovery | Microsoft Docs
description: Informazioni su come configurare il ripristino di emergenza in Azure di macchine virtuali Hyper-V locali di cloud System Center VMM con il servizio Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 64559f653ba8a466de7bec10db34383b508e3e4b
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2019
ms.locfileid: "59361285"
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-in-vmm-clouds-to-azure"></a>Configurare il ripristino di emergenza in Azure di macchine virtuali Hyper-V locali di cloud VMM

Questo articolo descrive come abilitare la replica per le macchine virtuali Hyper-V locali gestite da System Center Virtual Machine Manager (VMM), per il ripristino di emergenza in Azure usando il [Azure Site Recovery](site-recovery-overview.md) servizio. Se non si usa VMM, quindi [seguire questa esercitazione](hyper-v-azure-tutorial.md).

Questa è la terza esercitazione di una serie che illustra come configurare il ripristino di emergenza in Azure per le macchine virtuali VMware locali. Nell'esercitazione precedente, abbiamo [preparato l'ambiente Hyper-V on-premises](hyper-v-prepare-on-premises-tutorial.md) per il ripristino di emergenza in Azure. 

In questa esercitazione si apprenderà come:


> [!div class="checklist"]
> * Selezionare l'origine e la destinazione della replica.
> * Configurare l'ambiente di origine, inclusi i componenti locali di Site Recovery, e l'ambiente di destinazione della replica.
> * Configurare il mapping di rete per eseguire il mapping tra le reti di macchine virtuali di VMM e le reti virtuali di Azure.
> * Creare un criterio di replica
> * Abilitare la replica per una macchina virtuale


> [!NOTE]
> Le esercitazioni illustrano il percorso di distribuzione più semplice per uno scenario. Quando possibile, vengono usate le opzioni predefinite e non sono riportati tutti i percorsi e le impostazioni possibili. Per istruzioni dettagliate, vedere l'articolo nella sezione procedure di Site Recovery sommario.

## <a name="before-you-begin"></a>Prima di iniziare

Questa è la terza esercitazione di una serie. In questa esercitazione si presuppone che siano già state completate le attività delle esercitazioni precedenti:

1. [Preparare Azure](tutorial-prepare-azure.md)
2. [Preparare Hyper-V locali](tutorial-prepare-on-premises-hyper-v.md) questa è la terza esercitazione di una serie. In questa esercitazione si presuppone che siano già state completate le attività delle esercitazioni precedenti:


## <a name="select-a-replication-goal"></a>Selezionare un obiettivo di replica

1. In **Insiemi di credenziali dei servizi di ripristino** selezionare l'insieme di credenziali. Abbiamo preparato l'insieme di credenziali **ContosoVMVault** nell'esercitazione precedente.
2. In **Attività iniziali** fare clic su **Site Recovery** e quindi su **Preparare l'infrastruttura**.
3. Nelle **obiettivo di protezione** > **i computer che si trova dove sono?**, selezionare **On-premises**.
4. Nelle **in cui si desidera replicare le macchine?**, selezionare **To Azure**.
5. Nelle **i computer sono virtualizzati?** selezionate **Sì con Hyper-V**.
6. In **Si sta usando System Center VMM per gestire gli host Hyper-V** selezionare **Sì**. Fare quindi clic su **OK**.

    ![Obiettivo di replica](./media/hyper-v-vmm-azure-tutorial/replication-goal.png)


## <a name="confirm-deployment-planning"></a>Confermare la pianificazione della distribuzione

1. Nelle **pianificazione della distribuzione**, se si prevede una distribuzione di grandi dimensioni, scaricare lo strumento Deployment Planner per Hyper-V dal collegamento nella pagina. [Altre informazioni](hyper-v-deployment-planner-overview.md) sulla pianificazione della distribuzione di Hyper-V.
2. Ai fini di questa esercitazione, non occorre deployment planner. Nelle **è stata completata la pianificazione della distribuzione?**, selezionare **verrà faccio tutto in un secondo momento**. Fare quindi clic su **OK**.


## <a name="set-up-the-source-environment"></a>Configurare l'ambiente di origine

Quando si configura l'ambiente di origine, si installa il Provider di Azure Site Recovery nel server VMM e registrare il server nell'insieme di credenziali. Installare l'agente di servizi di ripristino di Azure in ogni host Hyper-V. 

1. In **Preparare l'infrastruttura** fare clic su **Origine**.
2. In **Prepara origine** fare clic su **+ VMM** per aggiungere un server VMM. In **Aggiungi server** verificare che **System Center VMM server** compaia in **Tipo server**.
3. Scaricare il programma di installazione del provider di Microsoft Azure Site Recovery.
4. Scaricare la chiave di registrazione dell'insieme di credenziali, che sarà necessaria durante la configurazione del provider. La chiave è valida per cinque giorni dal momento in cui viene generata.
5. Scaricare il programma di installazione per l'agente di servizi di ripristino di Microsoft Azure.

    ![Download](./media/hyper-v-vmm-azure-tutorial/download-vmm.png)

### <a name="install-the-provider-on-the-vmm-server"></a>Installare il provider nel server VMM

1. Nell'installazione guidata del provider di Azure Site Recovery > **Microsoft Update** acconsentire esplicitamente a usare Microsoft Update per verificare la disponibilità degli aggiornamenti del provider.
2. In **Installazione** accettare il percorso di installazione predefinito per il provider e quindi fare clic su **Installa**. 
3. Dopo l'installazione, in Registrazione guidata di Microsoft Azure Site Recovery > **Impostazioni dell'insieme di credenziali** fare clic su **Sfoglia** e in **File di chiave** selezionare il file di chiave dell'insieme di credenziali scaricato.
4. Specificare la sottoscrizione di Azure Site Recovery e il nome dell'insieme di credenziali (**ContosoVMVault**). Specificare un nome descrittivo per identificare il server VMM nell'insieme di credenziali.
5. In **Impostazioni proxy** selezionare **Connetti direttamente ad Azure Site Recovery senza server proxy**.
6. Accettare il percorso predefinito per il certificato usato per crittografare i dati. I dati crittografati verranno decrittografati durante il failover.
7. In **Sincronizza metadati cloud** selezionare **Sincronizza i metadati cloud nel portale di Site Recovery**. È necessario eseguire questa azione solo una volta in ogni server. Fare quindi clic su **Registra**.
8. Dopo avere registrato il server nell'insieme di credenziali, fare clic su **Fine**.

Al termine della registrazione, i metadati del server vengono recuperati da Azure Site Recovery e il server VMM viene visualizzato in **Infrastruttura di Site Recovery**.

### <a name="install-the-recovery-services-agent-on-hyper-v-hosts"></a>Installare l'agente di servizi di ripristino negli host Hyper-V

Installare l'agente in ogni host Hyper-V contenente macchine virtuali da replicare.

1. In Installazione guidata di Agente servizi di ripristino di Microsoft Azure > **Controllo dei prerequisiti** fare clic su **Avanti**. Gli eventuali prerequisiti mancanti verranno installati automaticamente.
2. In **Impostazioni di installazione** accettare il percorso di installazione e il percorso della cache. L'unità di cache necessita di almeno 5 GB di spazio di archiviazione. Si consiglia un'unità con almeno 600 GB di spazio libero. Fare clic su **Installa**.
3. In **Installazione**, al termine dell'installazione, fare clic su **Chiudi** per completare la procedura guidata.

    ![Installare l'agente](./media/hyper-v-vmm-azure-tutorial/mars-install.png)
    

## <a name="set-up-the-target-environment"></a>Configurare l'ambiente di destinazione

1. Fare clic su **Preparare l'infrastruttura** > **Destinazione**.
2. Selezionare la sottoscrizione e il gruppo di risorse (**ContosoRG**) in cui verranno create le macchine virtuali di Azure dopo il failover.
3. Selezionare il **Resource Manager** modello di distribuzione.

Site Recovery verifica la disponibilità di uno o più account di archiviazione di Azure e reti compatibili.


## <a name="configure-network-mapping"></a>Configurare il mapping di rete

1. In **Infrastruttura di Site Recovery** > **Mapping di rete** > **Mapping di rete** fare clic sull'icona **+Mapping di rete**.
2. In **Aggiungi mapping di rete**, selezionare il server VMM di origine. Selezionare **Azure** come destinazione.
3. Verificare la sottoscrizione e il modello di distribuzione dopo il failover.
4. In **Rete di origine**, selezionare la rete VM di origine locale.
5. In **Rete di destinazione**, selezionare la rete di Azure in cui inserire le macchine virtuali di Azure di replica create dopo il falover. Fare quindi clic su **OK**.

    ![Mapping di rete](./media/hyper-v-vmm-azure-tutorial/network-mapping-vmm.png)

## <a name="set-up-a-replication-policy"></a>Configurare criteri di replica

1. Fare clic su **Preparare l'infrastruttura** > **Impostazioni della replica** > **+Crea e associa**.
2. In **Criteri di creazione e associazione**specificare il nome dei criteri. Utilizziamo **ContosoReplicationPolicy**.
3. Lasciare le impostazioni predefinite e fare clic su **OK**.
    - **Frequenza di copia**: indica che i dati delta (dopo la replica iniziale) verranno replicati ogni 5 minuti.
    - **Conservazione del punto di ripristino** indica per ogni punto di ripristino verrà conservato per due ore.
    - **Frequenza snapshot coerenti con l'app**: indica che i punti di ripristino contenenti snapshot coerenti con l'app verranno creati ogni ora.
    - **Ora di inizio della replica iniziale**: indica che la replica iniziale verrà avviata immediatamente.
    - **Crittografare i dati archiviati in Azure**: l'impostazione predefinita **No** indica che i dati inattivi di Azure non sono crittografati.
4. Dopo aver creato i criteri, fare clic su **OK**. Quando si creano nuovi criteri, questi vengono associati automaticamente al cloud VMM.

## <a name="enable-replication"></a>Abilitare la replica

1. In **Eseguire la replica dell'applicazione** fare clic su **Origine**. 
2. In **Origine** selezionare il cloud VMM. Fare quindi clic su **OK**.
3. In **Destinazione** verificare che sia impostato Azure come destinazione, controllare la sottoscrizione dell'insieme di credenziali e selezionare il modello **Resource Manager**.
4. Selezionare l'account di archiviazione **contosovmsacct1910171607** e la rete di Azure **ContosoASRnet**.
5. In **Macchine virtuali** > **Seleziona** selezionare la macchina virtuale da replicare. Fare quindi clic su **OK**.

   È possibile tenere traccia dello stato del processo **Abilita protezione** in **Processi** > **Site Recovery jobs** (Processi di Site Recovery). Dopo il completamento del processo **Finalizza protezione**, la replica iniziale è completata e la macchina virtuale è pronta per il failover.



## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Eseguire una verifica del ripristino di emergenza](tutorial-dr-drill-azure.md)
