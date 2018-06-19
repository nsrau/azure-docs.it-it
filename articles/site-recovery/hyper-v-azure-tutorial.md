---
title: Configurare il ripristino di emergenza per macchine virtuali Hyper-V locali (senza VMM) con Azure Site Recovery | Microsoft Docs
description: Informazioni su come configurare il ripristino di emergenza per macchine virtuali Hyper-V locali (senza VMM) con il servizio Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 05/21/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 9ee5478412b02615efec983dd0b99c12fc2d9213
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2018
ms.locfileid: "34643584"
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-to-azure"></a>Configurare il ripristino di emergenza di macchine virtuali Hyper-V locali in Azure

Il servizio [Azure Site Recovery](site-recovery-overview.md) favorisce l'attuazione della strategia di ripristino di emergenza gestendo e coordinando le operazioni di replica, failover e failback di computer locali e macchine virtuali di Azure.

In questa esercitazione viene illustrato come configurare il ripristino di emergenza per macchine virtuali Hyper-V locali in Azure. L'esercitazione riguarda le macchine virtuali Hyper-V non gestite da System Center Virtual Machine Manager (VMM). In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Selezionare l'origine e la destinazione della replica.
> * Configurare l'ambiente di origine, inclusi i componenti locali di Site Recovery, e l'ambiente di destinazione della replica.
> * Creare un criterio di replica.
> * Abilitare la replica per una macchina virtuale.

Questa è la terza esercitazione di una serie. In questa esercitazione si presuppone che siano già state completate le attività delle esercitazioni precedenti:

1. [Preparare Azure](tutorial-prepare-azure.md)
2. [Preparare Hyper-V in locale](tutorial-prepare-on-premises-hyper-v.md)

Prima di iniziare, è utile [esaminare l'architettura](concepts-hyper-v-to-azure-architecture.md) di questo scenario di ripristino di emergenza.

## <a name="select-a-replication-goal"></a>Selezionare un obiettivo di replica


1. In **Tutti i servizi** > **Insiemi di credenziali dei servizi di ripristino** selezionare l'insieme di credenziali **ContosoVMVault**, preparato nell'esercitazione precedente.
2. In **Attività iniziali** fare clic su **Site Recovery** e quindi su **Preparare l'infrastruttura**.
3. In **Obiettivo di protezione** > **Dove si trovano le macchine virtuali** selezionare **Locale**.
4. Nella casella **In quale destinazione si vuole eseguire la replica dei computer** selezionare **In Azure**.
5. In **Are you using System Center VMM to manage your Hyper-V hosts** (Si usa System Center VMM per gestire gli host Hyper-V), selezionare **No**. Fare quindi clic su **OK**.

    ![Obiettivo di replica](./media/hyper-v-azure-tutorial/replication-goal.png)

## <a name="confirm-deployment-planning"></a>Confermare la pianificazione della distribuzione

Quando si pianifica una distribuzione di grandi dimensioni, è consigliabile assicurarsi di completare la [pianificazione della distribuzione per la replica Hyper-V](hyper-v-deployment-planner-overview.md). Ai fini di questa esercitazione, in **È stata completata la pianificazione della distribuzione?** selezionare **Operazione da completare in seguito** nell'elenco a discesa.

![Pianificazione della distribuzione](./media/hyper-v-azure-tutorial/deployment-planning.png)

## <a name="set-up-the-source-environment"></a>Configurare l'ambiente di origine

Per configurare l'ambiente di origine, creare un sito Hyper-V e aggiungere gli host Hyper-V al sito. Scaricare e installare quindi il provider di Azure Site Recovery e l'agente di Servizi di ripristino di Azure in ogni host e registrare il sito Hyper-V nell'insieme di credenziali. 

1. In **Preparare l'infrastruttura** fare clic su **Origine**.
2. Fare clic su **+ Sito Hyper-V** e specificare il nome del sito **ContosoHyperVSite**, creato nell'esercitazione precedente.

    ![Sito di Hyper-V](./media/hyper-v-azure-tutorial/hyperv-site.png)

3. Dopo la creazione del sito, fare clic su **+Server Hyper-V**.

    ![Server Hyper-V](./media/hyper-v-azure-tutorial/hyperv-server.png)

4. Scaricare il file di installazione del provider.
6. Scaricare la chiave di registrazione dell'insieme di credenziali, che sarà necessaria durante la configurazione del provider. La chiave è valida per cinque giorni dal momento in cui viene generata.

    ![Scaricare il provider](./media/hyper-v-azure-tutorial/download.png)
    

### <a name="install-the-provider"></a>Installare il provider

Eseguire il file di installazione del provider (AzureSiteRecoveryProvider.exe) in ogni host Hyper-V aggiunto al sito **ContosoHyperVSite**. Il file installa il provider di Azure Site Recovery e l'agente di Servizi di ripristino in ogni host Hyper-V.

1. Nell'installazione guidata del provider di Azure Site Recovery > **Microsoft Update**, accettare esplicitamente di usare Microsoft Update per verificare la disponibilità degli aggiornamenti del provider.
2. In **Installazione** accettare il percorso di installazione predefinito per il provider e l'agente, quindi fare clic su **Installa**.
3. Dopo l'installazione, in Registrazione guidata di Microsoft Azure Site Recovery > **Impostazioni dell'insieme di credenziali** fare clic su **Sfoglia** e in **File di chiave** selezionare il file di chiave dell'insieme di credenziali scaricato. 
4. Specificare la sottoscrizione di Azure Site Recovery, il nome dell'insieme di credenziali (**ContosoVMVault**) e il sito Hyper-V (**ContosoHyperVSite**) a cui appartiene il server Hyper-V.
5. In **Impostazioni proxy** selezionare **Connetti direttamente ad Azure Site Recovery senza server proxy**.
6. Dopo avere registrato il server nell'insieme di credenziali, in **Registrazione** fare clic su **Fine**.

I metadati del server Hyper-V vengono recuperati da Azure Site Recovery e il server viene visualizzato in **S Infrastruttura di Site Recovery** > **Host Hyper-V**. Il processo potrebbe richiedere fino a 30 minuti.


## <a name="set-up-the-target-environment"></a>Configurare l'ambiente di destinazione

Selezionare e verificare le risorse di destinazione. 

1. Fare clic su **Preparare l'infrastruttura** > **Destinazione**.
2. Selezionare la sottoscrizione e il gruppo di risorse **ContosoRG** in cui verranno create le VM di Azure dopo il failover.
3. Selezionare il modello di distribuzione **Resource Manager**.

Site Recovery verifica la disponibilità di uno o più account di archiviazione di Azure e reti compatibili.


## <a name="set-up-a-replication-policy"></a>Configurare criteri di replica

> [!NOTE]
> Per i criteri di replica da Hyper-V ad Azure l'opzione relativa alla frequenza di copia ogni 15 minuti verrà ritirata a favore delle impostazioni della frequenza di copia ogni cinque e 30 minuti. I criteri di replica che usano una frequenza di copia pari a 15 minuti verranno aggiornati automaticamente in modo che usino l'impostazione della frequenza di copia ogni 5 minuti. Le opzioni relative alla frequenza di copia ogni cinque e 30 minuti garantiscono prestazioni di replica e obiettivi del punto di recupero migliori rispetto alla frequenza di copia ogni 15 minuti, con un impatto minimo sull'utilizzo della larghezza di banda e sul volume di trasferimento dei dati.

1. Fare clic su **Preparare l'infrastruttura** > **Impostazioni della replica** > **+Crea e associa**.
2. In **Creare e associare i criteri** specificare il nome **ContosoReplicationPolicy**.
3. Lasciare le impostazioni predefinite e fare clic su **OK**.
    - **Frequenza di copia**: indica che i dati delta (dopo la replica iniziale) verranno replicati ogni 5 minuti.
    - **Conservazione del punto di ripristino**: indica che i periodi di conservazione di ogni punto di ripristino avranno una durata di due ore.
    - **Frequenza snapshot coerenti con l'app**: indica che i punti di ripristino contenenti snapshot coerenti con l'app verranno creati ogni ora.
    - **Ora di inizio della replica iniziale** indica che la replica iniziale verrà avviata immediatamente.
4. Dopo aver creato i criteri, fare clic su **OK**. Quando si creano nuovi criteri, questi vengono associati automaticamente al sito Hyper-V specificato, ovvero **ContosoHyperVSite**.

    ![Criteri di replica](./media/hyper-v-azure-tutorial/replication-policy.png)


## <a name="enable-replication"></a>Abilitare la replica


1. In **Eseguire la replica dell'applicazione** fare clic su **Origine**. 
2. In **Origine** selezionare il sito **ContosoHyperVSite**. Fare quindi clic su **OK**.
3. In **Destinazione**, verificare che sia impostato Azure come destinazione e controllare la sottoscrizione dell'insieme di credenziali e il modello di distribuzione **Resource Manager**.
4. Selezionare l'account di archiviazione **contosovmsacct1910171607** creato nell'esercitazione precedente per i dati replicati e la rete **ContosoASRnet** in cui si troveranno le macchine virtuali di Azure dopo il failover.
5. In **Macchine virtuali** > **Seleziona** selezionare la macchina virtuale da replicare. Fare quindi clic su **OK**.

 È possibile tenere traccia dello stato del processo **Abilita protezione** in **Processi** > **Site Recovery jobs** (Processi di Site Recovery). Dopo il completamento del processo **Finalizza protezione**, la replica iniziale è completata e la macchina virtuale è pronta per il failover.

## <a name="next-steps"></a>Passaggi successivi
[Eseguire un'esercitazione sul ripristino di emergenza](tutorial-dr-drill-azure.md)
