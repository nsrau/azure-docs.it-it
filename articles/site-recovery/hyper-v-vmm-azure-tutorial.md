---
title: Configurare il ripristino di emergenza in Azure di macchine virtuali Hyper-V locali di cloud VMM con Azure Site Recovery | Microsoft Docs
description: Informazioni su come configurare il ripristino di emergenza in Azure di macchine virtuali Hyper-V locali di cloud System Center VMM con il servizio Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 02/14/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 99477757c89fe2df7ae24b7ffe95c8fb7f470c93
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/22/2018
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-in-vmm-clouds-to-azure"></a>Configurare il ripristino di emergenza in Azure di macchine virtuali Hyper-V locali di cloud VMM

Il servizio [Azure Site Recovery](site-recovery-overview.md) favorisce l'attuazione della strategia di ripristino di emergenza gestendo e coordinando le operazioni di replica, failover e failback di computer locali e macchine virtuali di Azure.

In questa esercitazione viene illustrato come configurare il ripristino di emergenza per macchine virtuali Hyper-V locali in Azure. L'esercitazione riguarda le macchine virtuali Hyper-V gestite da System Center Virtual Machine Manager (VMM). In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Selezionare l'origine e la destinazione della replica.
> * Configurare l'ambiente di origine, inclusi i componenti locali di Site Recovery, e l'ambiente di destinazione della replica.
> * Configurare il mapping di rete per eseguire il mapping tra le reti VM di VMM e le reti virtuali di Azure.
> * Creare un criterio di replica
> * Abilitare la replica per una macchina virtuale

Questa è la terza esercitazione di una serie. In questa esercitazione si presuppone che siano già state completate le attività delle esercitazioni precedenti:

1. [Preparare Azure](tutorial-prepare-azure.md)
2. [Preparare Hyper-V in locale](tutorial-prepare-on-premises-hyper-v.md)

Prima di iniziare, è utile [esaminare l'architettura](concepts-hyper-v-to-azure-architecture.md) di questo scenario di ripristino di emergenza.



## <a name="select-a-replication-goal"></a>Selezionare un obiettivo di replica

1. In **Tutti i servizi** > **Insiemi di credenziali dei servizi di ripristino** fare clic sul nome dell'insieme di credenziali usato in queste esercitazioni, **ContosoVMVault**.
2. In **Attività iniziali** fare clic su **Site Recovery**. Fare quindi clic su **Preparare l'infrastruttura**.
3. In **Obiettivo di protezione** > **Dove si trovano le macchine virtuali** selezionare **Locale**.
4. Per **In quale destinazione si vuole eseguire la replica dei computer?** selezionare **In Azure**.
5. In **I computer sono virtualizzati?** selezionare **Sì, con Hyper-V**.
6. In **Are you using System Center VMM** (Si usa System Center VMM) selezionare **Sì**. Fare quindi clic su **OK**.

    ![Obiettivo di replica](./media/hyper-v-vmm-azure-tutorial/replication-goal.png)



## <a name="set-up-the-source-environment"></a>Configurare l'ambiente di origine

Quando si configura l'ambiente di origine, si installano il provider di Azure Site Recovery e l'agente di Servizi di ripristino di Azure e si registrano i server locali nell'insieme di credenziali. 

1. In **Preparare l'infrastruttura** fare clic su **Origine**.
2. In **Prepara origine** fare clic su **+ VMM** per aggiungere un server VMM. In **Aggiungi server** verificare che **System Center VMM server** compaia in **Tipo server**.
3. Scaricare il programma di installazione di Microsoft Azure Site Recovery.
4. Scaricare la chiave di registrazione dell'insieme di credenziali, che sarà necessaria durante la configurazione del provider. La chiave è valida per cinque giorni dal momento in cui viene generata.
5. Scaricare l'agente di Servizi di ripristino.

    ![Download](./media/hyper-v-vmm-azure-tutorial/download-vmm.png)

### <a name="install-the-provider-on-the-vmm-server"></a>Installare il provider nel server VMM

1. Nell'installazione guidata del provider di Azure Site Recovery > **Microsoft Update**, accettare esplicitamente di usare Microsoft Update per verificare la disponibilità degli aggiornamenti del provider.
2. In **Installazione** accettare il percorso di installazione predefinito per il provider, quindi fare clic su **Installa**. 
3. Dopo l'installazione, in Registrazione guidata di Microsoft Azure Site Recovery > **Impostazioni dell'insieme di credenziali** fare clic su **Sfoglia** e in **File di chiave** selezionare il file di chiave dell'insieme di credenziali scaricato.
4. Specificare la sottoscrizione di Azure Site Recovery e il nome dell'insieme di credenziali (**ContosoVMVault**). Specificare un nome descrittivo per identificare il server VMM nell'insieme di credenziali.
5. In **Impostazioni proxy** selezionare **Connetti direttamente ad Azure Site Recovery senza server proxy**.
6. Accettare il percorso predefinito per il certificato usato per crittografare i dati. I dati crittografati verranno decrittografati durante il failover.
7. In **Sincronizza metadati cloud** selezionare **Sincronizza i metadati cloud nel portale di Site Recovery**. È necessario eseguire questa azione solo una volta in ogni server. Fare quindi clic su **Registra**.
8. Dopo avere registrato il server nell'insieme di credenziali, fare clic su **Fine**.

Al termine della registrazione, i metadati del server vengono recuperati da Azure Site Recovery e il server VMM viene visualizzato in **Infrastruttura di Site Recovery**.

### <a name="install-the-recovery-services-agent"></a>Installare l'agente di Servizi di ripristino

Installare l'agente in ogni host Hyper-V contenente le macchine virtuali da replicare.

1. In Installazione guidata di Agente servizi di ripristino di Microsoft Azure > **Verifica dei prerequisiti**, fare clic su **Avanti**. Gli eventuali prerequisiti mancanti verranno installati automaticamente.
2. In **Impostazioni di installazione** accettare il percorso di installazione e il percorso della cache. L'unità di cache necessita di almeno 5 GB di spazio di archiviazione. Si consiglia un'unità con almeno 600 GB di spazio libero. Fare clic su **Installa**.
3. In **Installazione**, al termine dell'installazione, fare clic su **Chiudi** per completare la procedura guidata.

    ![Installare l'agente](./media/hyper-v-vmm-azure-tutorial/mars-install.png)
    

## <a name="set-up-the-target-environment"></a>Configurare l'ambiente di destinazione

1. Fare clic su **Preparare l'infrastruttura** > **Destinazione**.
2. Selezionare la sottoscrizione e il gruppo di risorse (**ContosoRG**) in cui verranno create le VM di Azure dopo il failover.
3. Selezionare il modello di distribuzione **Resource Manager**.

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
2. In **Criteri di creazione e associazione** specificare il nome dei criteri, ovvero **ContosoReplicationPolicy**.
3. Lasciare l'impostazione predefinita e fare clic su **OK**.
    - **Frequenza di copia** indica che i dati differenziali (dopo la replica iniziale) verranno replicati ogni 5 minuti.
    - **Conservazione del punto di ripristino** indica un intervallo di conservazione di due ore per ogni punto di ripristino.
    - **Frequenza snapshot coerenti con l'app** indica che verranno creati ogni ora punti di ripristino contenenti snapshot coerenti con l'app.
    - **Ora di inizio della replica iniziale** indica che la replica iniziale verrà avviata immediatamente.
    - **Crittografare i dati archiviati in Azure** - l'impostazione predefinita **Off** indica che i dati inattivi in Azure non sono crittografati.
4. Dopo aver creato i criteri, fare clic su **OK**. Quando si creano nuovi criteri, questi vengono associati automaticamente al cloud VMM.

## <a name="enable-replication"></a>Abilitare la replica

1. In **Eseguire la replica dell'applicazione** fare clic su **Origine**. 
2. In **Origine** selezionare il cloud VMM. Fare quindi clic su **OK**.
3. In **Destinazione** verificare che sia impostato Azure come destinazione, controllare la sottoscrizione dell'insieme di credenziali e selezionare il modello **Resource Manager**.
4. Selezionare l'account di archiviazione **contosovmsacct1910171607** e la rete di Azure **ContosoASRnet**.
5. In **Macchine virtuali** > **Seleziona** selezionare la macchina virtuale da replicare. Fare quindi clic su **OK**.

 È possibile tenere traccia dello stato del processo **Abilita protezione** in **Processi** > **Site Recovery jobs** (Processi di Site Recovery). Dopo il completamento del processo **Finalizza protezione**, la replica iniziale è completata e la macchina virtuale è pronta per il failover.


## <a name="next-steps"></a>Passaggi successivi
[Eseguire un'esercitazione sul ripristino di emergenza](tutorial-dr-drill-azure.md)
