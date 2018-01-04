---
title: Configurare il ripristino di emergenza delle macchine virtuali Hyper-V in locale nei cloud VMM in Azure con Azure Site Recovery | Documenti Microsoft
description: Informazioni su come configurare il ripristino di emergenza delle macchine virtuali Hyper-V locale nel cloud di System Center VMM in Azure, con il servizio di Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 12/31/2017
ms.author: raynew
ms.openlocfilehash: 5f364c6f8a88ad53c12909f0e9f10afcce5ef8af
ms.sourcegitcommit: 9ea2edae5dbb4a104322135bef957ba6e9aeecde
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/03/2018
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-in-vmm-clouds-to-azure"></a>Configurare il ripristino di emergenza delle macchine virtuali Hyper-V in locale nei cloud VMM in Azure

Il servizio [Azure Site Recovery](site-recovery-overview.md) favorisce l'attuazione della strategia di ripristino di emergenza gestendo e coordinando le operazioni di replica, failover e failback di computer locali e macchine virtuali di Azure.

In questa esercitazione viene illustrato come configurare il ripristino di emergenza per macchine virtuali Hyper-V locali in Azure. L'esercitazione è pertinente per le macchine virtuali Hyper-V gestiti da System Center Virtual Machine Manager (VMM). In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Selezionare la replica di origine e destinazione.
> * Configurare l'ambiente di replica di origine, inclusi i componenti, il ripristino del sito locale e l'ambiente di replica di destinazione.
> * Configurare il mapping di rete, eseguire il mapping tra le reti VM di VMM e reti virtuali di Azure.
> * Creare un criterio di replica
> * Abilitare la replica per una macchina virtuale

Questa è la terza esercitazione di una serie. In questa esercitazione si presuppone che siano già state completate le attività delle esercitazioni precedenti:

1. [Preparare Azure](tutorial-prepare-azure.md)
2. [Preparare locale Hyper-V](tutorial-prepare-on-premises-hyper-v.md)

Prima di iniziare, è utile [esamina l'architettura](concepts-hyper-v-to-azure-architecture.md) per questo scenario di ripristino di emergenza.



## <a name="select-a-replication-goal"></a>Selezionare un obiettivo di replica

1. In **tutti i servizi** > **insiemi di credenziali di servizi di ripristino**, fare clic sul nome dell'insieme di credenziali è utilizzare queste esercitazioni **ContosoVMVault**.
2. In **Introduzione**, fare clic su **Site Recovery**. Quindi fare clic su **preparare l'infrastruttura**
3. In **obiettivi della protezione dati** > **dove sono i computer che si trovano**selezionare **locale**.
4. In **in cui si desidera replicare le macchine**selezionare **in Azure**.
5. In **sono computer virtualizzati**selezionare **Sì, con Hyper-V**.
6. In **si utilizza System Center VMM**selezionare **Sì**. Fare quindi clic su **OK**.

    ![Obiettivo di replica](./media/tutorial-hyper-v-vmm-to-azure/replication-goal.png)



## <a name="set-up-the-source-environment"></a>Configurare l'ambiente di origine

Quando si configura l'ambiente di origine, si installano il provider di Azure Site Recovery e l'agente di Servizi di ripristino di Azure e si registrano i server locali nell'insieme di credenziali. 

1. In **Preparare l'infrastruttura** fare clic su **Origine**.
2. In **Prepara origine** fare clic su **+ VMM** per aggiungere un server VMM. In **Aggiungi server** verificare che **System Center VMM server** compaia in **Tipo server**.
3. Scaricare il programma di installazione per il Provider di Microsoft Azure Site Recovery.
4. Scaricare la chiave di registrazione dell'insieme di credenziali, che sarà necessaria durante la configurazione del provider. La chiave è valida per cinque giorni dal momento in cui viene generata.
5. Scaricare l'agente di servizi di ripristino.

    ![Download](./media/tutorial-hyper-v-vmm-to-azure/download-vmm.png)

### <a name="install-the-provider-on-the-vmm-server"></a>Installare il provider nel server VMM

1. Nella procedura guidata di installazione di Provider di Azure Site Recovery > **Microsoft Update**, optare per utilizzare Microsoft Update per cercare gli aggiornamenti del Provider.
2. In **installazione**, accettare il percorso di installazione predefinito per il Provider e fare clic su **installare**. 
3. Dopo l'installazione, nella registrazione guidata di Microsoft Azure Site Recovery > **impostazioni insieme di credenziali**, fare clic su **Sfoglia**e in **file di chiave**, selezionare la chiave dell'insieme di credenziali di file che si scaricato.
4. Specificare la sottoscrizione di Azure Site Recovery e il nome dell'insieme di credenziali (**ContosoVMVault**). Specificare un nome descrittivo per il server VMM, per identificarla nell'insieme di credenziali.
5. In **le impostazioni del Proxy**selezionare **connettersi direttamente ad Azure Site Recovery senza un proxy**.
6. Accettare il percorso predefinito per il certificato utilizzato per crittografare i dati. I dati crittografati verranno decrittografati durante il failover.
7. In **Sincronizza metadati cloud**selezionare **sincronizzazione metadati cloud nel portale di Site Recovery**. È necessario eseguire questa azione solo una volta in ogni server. Quindi fare clic su **registrare**.
8. Dopo avere registrato il server nell'insieme di credenziali, fare clic su **Fine**.

Al termine della registrazione, i metadati del server vengono recuperati da Azure Site Recovery e il server VMM viene visualizzato in **Infrastruttura di Site Recovery**.

### <a name="install-the-recovery-services-agent"></a>Installare l'agente di servizi di ripristino

Installare l'agente in ogni host Hyper-V contenente le macchine virtuali da replicare.

1. Nell'installazione guidata agente di servizi di Microsoft Azure Recovery > **controllo dei prerequisiti**, fare clic su **Avanti**. Verranno installati automaticamente gli eventuali prerequisiti mancanti.
2. In **le impostazioni di installazione**, accettare il percorso di installazione e il percorso della cache. L'unità di cache necessita di almeno 5 GB di spazio di archiviazione. Si consiglia un'unità con almeno 600 GB di spazio libero. Fare clic su **Installa**.
3. In **Installazione**, al termine dell'installazione, fare clic su **Chiudi** per completare la procedura guidata.

    ![Installare l'agente](./media/tutorial-hyper-v-vmm-to-azure/mars-install.png)
    

## <a name="set-up-the-target-environment"></a>Configurare l'ambiente di destinazione

1. Fare clic su **Preparare l'infrastruttura** > **Destinazione**.
2. Selezionare la sottoscrizione e il gruppo di risorse (**ContosoRG**) in cui le macchine virtuali di Azure verrà create dopo il failover.
3. Selezionare il **Gestione risorse "** modello di distribuzione.

Site Recovery verifica la disponibilità di uno o più account di archiviazione di Azure e reti compatibili.


## <a name="configure-network-mapping"></a>Configurare il mapping di rete

1. In **Infrastruttura di Site Recovery** > **Mapping di rete** > **Mapping di rete** fare clic sull'icona **+Mapping di rete**.
2. In **Aggiungi mapping di rete**, selezionare il server VMM di origine. Selezionare **Azure** come destinazione.
3. Verificare la sottoscrizione e il modello di distribuzione dopo il failover.
4. In **Rete di origine**, selezionare la rete VM di origine locale.
5. In **Rete di destinazione**, selezionare la rete di Azure in cui inserire le macchine virtuali di Azure di replica create dopo il falover. Fare quindi clic su **OK**.

    ![Mapping di rete](./media/tutorial-hyper-v-vmm-to-azure/network-mapping-vmm.png)

## <a name="set-up-a-replication-policy"></a>Configurare criteri di replica

1. Fare clic su **Preparare l'infrastruttura** > **Impostazioni della replica** > **+Crea e associa**.
2. In **crea e associa i criteri**, specificare un nome di criterio, **ContosoReplicationPolicy**.
3. Lasciare le impostazioni predefinite e fare clic su **OK**.
    - **Frequenza di copia** indica che delta verranno replicati i dati (dopo la replica iniziale) ogni cinque minuti.
    - **Conservazione del punto di ripristino** indica che le finestre di conservazione per ogni punto di ripristino saranno due due ore.
    - **Frequenza snapshot coerenti con l'app** indica che i punti di ripristino contenenti snapshot coerenti con l'app verranno creati ogni ora.
    - **Ora di inizio della replica iniziale**, indica che la replica iniziale inizierà immediatamente.
    - **Crittografare i dati archiviati in Azure** -il valore predefinito **Off** impostazione indica che i dati in Azure non crittografati a riposo.
4. Dopo la creazione del criterio, fare clic su **OK**. Quando si creano nuovi criteri, questi vengono associati automaticamente al cloud VMM.

## <a name="enable-replication"></a>Abilitare la replica

1. In **replicare applicazione**, fare clic su **origine**. 
2. In **origine**, selezionare il cloud VMM. Fare quindi clic su **OK**.
3. In **destinazione**, verificare di Azure come destinazione, la sottoscrizione dell'insieme di credenziali e selezionare il **Gestione risorse** modello.
4. Selezionare il **contosovmsacct1910171607** account di archiviazione e **ContosoASRnet** rete di Azure.
5. In **macchine virtuali** > **selezionare**, selezionare la macchina virtuale da replicare. Fare quindi clic su **OK**.

 È possibile tenere traccia dello stato del processo **Abilita protezione** in **Processi** > **Site Recovery jobs** (Processi di Site Recovery). Dopo il completamento del processo **Finalizza protezione**, la replica iniziale è completata e la macchina virtuale è pronta per il failover.


## <a name="next-steps"></a>Passaggi successivi
[Eseguire un'esercitazione sul ripristino di emergenza](tutorial-dr-drill-azure.md)
