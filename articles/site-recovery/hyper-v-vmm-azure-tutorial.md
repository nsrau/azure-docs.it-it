---
title: Configurare il ripristino di emergenza per macchine virtuali Hyper-V locali in cloud VMM in Azure con Site Recovery | Microsoft Docs
description: Informazioni su come configurare il ripristino di emergenza di macchine virtuali Hyper-V locali in cloud System Center VMM ad Azure usando Site Recovery.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 8c3c9347a027cccfaef6def84bfdc4c83555e98a
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2019
ms.locfileid: "65966484"
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-in-vmm-clouds-to-azure"></a>Configurare il ripristino di emergenza in Azure di macchine virtuali Hyper-V locali di cloud VMM

Questo articolo descrive come abilitare la replica per le macchine virtuali Hyper-V locali gestite da System Center Virtual Machine Manager (VMM), per il ripristino di emergenza in Azure usando il [Azure Site Recovery](site-recovery-overview.md) servizio. Se non si usa VMM, [seguire questa esercitazione](hyper-v-azure-tutorial.md) invece.

Questa è la terza esercitazione di una serie che mostra come configurare il ripristino di emergenza in Azure per macchine virtuali VMware locali. Nell'esercitazione precedente, abbiamo [preparato l'ambiente Hyper-V on-premises](hyper-v-prepare-on-premises-tutorial.md) per il ripristino di emergenza in Azure.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Selezionare l'origine e la destinazione della replica.
> * Configurare l'ambiente di origine, inclusi i componenti di Site Recovery in locale e l'ambiente di replica di destinazione.
> * Configurare il mapping di rete per eseguire il mapping tra le reti VM VMM e reti virtuali di Azure.
> * Creare un criterio di replica.
> * Abilitare la replica per una macchina virtuale.

> [!NOTE]
> Le esercitazioni mostrano il percorso di distribuzione più semplice per uno scenario. Quando possibile, vengono usate le opzioni predefinite e non sono riportati tutti i percorsi e le impostazioni possibili. Per istruzioni dettagliate, vedere gli articoli nel **guide pratiche** sezione il [documentazione di Site Recovery](https://docs.microsoft.com/en-us/azure/site-recovery).

## <a name="before-you-begin"></a>Prima di iniziare

Questa è la terza esercitazione di una serie. Si presuppone di aver già completato le attività nelle esercitazioni precedenti:

1. [Preparare Azure](tutorial-prepare-azure.md)
2. [Preparare Hyper-V in locale](tutorial-prepare-on-premises-hyper-v.md)

## <a name="select-a-replication-goal"></a>Selezionare un obiettivo di replica

1. Nel portale di Azure, passare a **insiemi di credenziali dei servizi di ripristino** e selezionare l'insieme di credenziali. L'insieme di credenziali **ContosoVMVault** è stato preparato nell'esercitazione precedente.
2. Nelle **Guida introduttiva**, selezionare **Site Recovery**, quindi selezionare **preparare l'infrastruttura**.
3. Nelle **obiettivo di protezione** > **i computer che si trova dove sono?**, selezionare **On-premises**.
4. In **In quale destinazione si vuole eseguire la replica dei computer?** selezionare **In Azure**.
5. Nelle **i computer sono virtualizzati?**, selezionare **Sì con Hyper-V**.
6. Nelle **si usa System Center VMM per gestire gli host Hyper-V?**, selezionare **Yes**.
7.  Selezionare **OK**.

    ![Obiettivo di replica](./media/hyper-v-vmm-azure-tutorial/replication-goal.png)

## <a name="confirm-deployment-planning"></a>Confermare la pianificazione della distribuzione

1. Se si sta pianificando una distribuzione di grandi dimensioni, in **Pianificazione della distribuzione** scaricare Deployment Planner per Hyper-V usando il collegamento presente nella pagina. [Altre informazioni](hyper-v-deployment-planner-overview.md) sulla pianificazione della distribuzione di Hyper-V.
2. Per questa esercitazione, non occorre Deployment Planner. Nella **è stata completata la pianificazione della distribuzione?**, selezionare **verrà faccio tutto in un secondo momento**, quindi selezionare **OK**.

## <a name="set-up-the-source-environment"></a>Configurare l'ambiente di origine

Quando si configura l'ambiente di origine, si installa il Provider di Azure Site Recovery nel server VMM e registrare il server nell'insieme di credenziali. Installare l'agente di servizi di ripristino di Azure in ogni host Hyper-V.

1. Nelle **preparare l'infrastruttura**, selezionare **origine**.
2. Nelle **Prepara origine**, selezionare **+ VMM** per aggiungere un server VMM. In **Aggiungi server** verificare che **System Center VMM server** compaia in **Tipo server**.
3. Scaricare il programma di installazione del provider di Microsoft Azure Site Recovery.
4. Scaricare la chiave di registrazione dell'insieme di credenziali. Questa chiave è necessaria quando si esegue l'installazione del Provider. La chiave è valida per cinque giorni dal momento in cui viene generata.
5. Scaricare il programma di installazione per l'agente di servizi di ripristino di Microsoft Azure.

    ![Scarica Provider, la chiave di registrazione e dell'agente](./media/hyper-v-vmm-azure-tutorial/download-vmm.png)

### <a name="install-the-provider-on-the-vmm-server"></a>Installare il provider nel server VMM

1. Nell'installazione guidata del provider di Azure Site Recovery > **Microsoft Update** acconsentire esplicitamente a usare Microsoft Update per verificare la disponibilità degli aggiornamenti del provider.
2. Nelle **installazione**, accettare il percorso di installazione predefinito per il Provider e selezionare **installare**.
3. Dopo l'installazione, in registrazione guidata di Microsoft Azure Site Recovery > **delle impostazioni dell'insieme di credenziali**, selezionare **Sfoglia**e in **file di chiave**, selezionare la chiave dell'insieme di credenziali di file che è stato scaricato.
4. Specificare la sottoscrizione di Azure Site Recovery e il nome dell'insieme di credenziali (**ContosoVMVault**). Specificare un nome descrittivo per identificare il server VMM nell'insieme di credenziali.
5. In **Impostazioni proxy** selezionare **Connetti direttamente ad Azure Site Recovery senza server proxy**.
6. Accettare il percorso predefinito per il certificato usato per crittografare i dati. I dati crittografati verranno decrittografati durante il failover.
7. In **Sincronizza metadati cloud** selezionare **Sincronizza i metadati cloud nel portale di Site Recovery**. Questa azione deve essere eseguita una sola volta in ogni server. Selezionare quindi **Registra**.
8. Dopo che il server è registrato nell'insieme di credenziali, selezionare **fine**.

Al termine della registrazione, i metadati del server vengono recuperati da Azure Site Recovery e il server VMM viene visualizzato in **Infrastruttura di Site Recovery**.

### <a name="install-the-recovery-services-agent-on-hyper-v-hosts"></a>Installare l'agente di servizi di ripristino negli host Hyper-V

Installare l'agente in ogni host Hyper-V contenenti le macchine virtuali che si vuole replicare.

1. Nell'installazione guidata di Microsoft Azure Recovery Services Agent > **controllo dei prerequisiti**, selezionare **successivo**. Gli eventuali prerequisiti mancanti verranno installati automaticamente.
2. Nelle **impostazioni di installazione**, accettare il percorso di installazione e il percorso della cache. L'unità di cache necessita di almeno 5 GB di spazio di archiviazione. Si consiglia un'unità con almeno 600 GB di spazio libero. Quindi, selezionare **Installa**.
3. Nelle **installazione**, al termine dell'installazione, selezionare **Chiudi** per completare la procedura guidata.

    ![Installare l'agente](./media/hyper-v-vmm-azure-tutorial/mars-install.png)

## <a name="set-up-the-target-environment"></a>Configurare l'ambiente di destinazione

1. Selezionare **Preparare l'infrastruttura** > **Destinazione**.
2. Selezionare la sottoscrizione e il gruppo di risorse (**ContosoRG**) in cui verranno create le macchine virtuali di Azure dopo il failover.
3. Selezionare il **Resource Manager** modello di distribuzione.

Site Recovery verifica la disponibilità di uno o più account di archiviazione di Azure e reti compatibili.

## <a name="configure-network-mapping"></a>Configurare il mapping di rete

1. Nelle **infrastruttura di Site Recovery** > **mapping di rete** > **Mapping di rete**, selezionare il **+Mappingdirete** icona.
2. In **Aggiungi mapping di rete**, selezionare il server VMM di origine. Selezionare **Azure** come destinazione.
3. Verificare la sottoscrizione e il modello di distribuzione dopo il failover.
4. In **Rete di origine**, selezionare la rete VM di origine locale.
5. In **Rete di destinazione**, selezionare la rete di Azure in cui inserire le macchine virtuali di Azure di replica create dopo il falover. Quindi selezionare **OK**.

    ![Mapping di rete](./media/hyper-v-vmm-azure-tutorial/network-mapping-vmm.png)

## <a name="set-up-a-replication-policy"></a>Configurare criteri di replica

1. Selezionare **preparare l'infrastruttura** > **le impostazioni di replica** > **+ crea e associa**.
2. In **Criteri di creazione e associazione**specificare il nome dei criteri. Il nome usato al momento è **ContosoReplicationPolicy**.
3. Lasciare le impostazioni predefinite e selezionare **OK**.
    - **Frequenza di copia** indica che, dopo la replica iniziale, i dati delta verranno replicati ogni cinque minuti.
    - **Conservazione del punto di ripristino** indica che ogni punto di ripristino verrà conservato per due ore.
    - **Frequenza snapshot coerenti con l'app**: indica che i punti di ripristino contenenti snapshot coerenti con l'app verranno creati ogni ora.
    - **Ora inizio replica iniziale** indica che la replica iniziale inizierà immediatamente.
    - **Crittografare i dati archiviati in Azure** è impostata sul valore predefinito (**disattivato**) e indica che i dati inattivi in Azure non sono crittografati.
4. Dopo aver creato i criteri, selezionare **OK**. Quando si crea un nuovo criterio, associata automaticamente nel cloud VMM.

## <a name="enable-replication"></a>Abilita replica

1. Nelle **applicazione di replicare**, selezionare **origine**.
2. In **Origine** selezionare il cloud VMM. Quindi selezionare **OK**.
3. Nelle **destinazione**, verificare la sottoscrizione di destinazione (Azure), l'insieme di credenziali e selezionare il **Resource Manager** modello.
4. Selezionare il **contosovmsacct1910171607** account di archiviazione e il **ContosoASRnet** rete di Azure.
5. Nelle **macchine virtuali** > **selezionare**, selezionare la macchina virtuale che si vuole replicare. Quindi selezionare **OK**.

   È possibile tenere traccia dello stato del processo **Abilita protezione** in **Processi** > **Site Recovery jobs** (Processi di Site Recovery). Dopo il **Finalizza protezione** al termine del processo, la replica iniziale è stata completata e la macchina virtuale è pronta per il failover.

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Eseguire un'esercitazione sul ripristino di emergenza](tutorial-dr-drill-azure.md)
