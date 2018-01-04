---
title: Configurare il ripristino di emergenza per macchine virtuali Hyper-V locale (senza VMM) in Azure con Azure Site Recovery | Documenti Microsoft
description: Informazioni su come configurare il ripristino di emergenza delle macchine virtuali Hyper-V locale (senza VMM) in Azure con il servizio di Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 12/31`/2017
ms.author: raynew
ms.openlocfilehash: 633c14bd25bc8a1419196b2c76ca94c26db68991
ms.sourcegitcommit: 9ea2edae5dbb4a104322135bef957ba6e9aeecde
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/03/2018
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-to-azure"></a>Configurare il ripristino di emergenza di macchine virtuali Hyper-V locali in Azure

Il servizio [Azure Site Recovery](site-recovery-overview.md) favorisce l'attuazione della strategia di ripristino di emergenza gestendo e coordinando le operazioni di replica, failover e failback di computer locali e macchine virtuali di Azure.

In questa esercitazione viene illustrato come configurare il ripristino di emergenza per macchine virtuali Hyper-V locali in Azure. L'esercitazione è pertinente per le macchine virtuali Hyper-V non gestiti da System Center Virtual Machine Manager (VMM). In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Selezionare la replica di origine e destinazione.
> * Configurare l'ambiente di replica di origine, inclusi i componenti, il ripristino del sito locale e l'ambiente di replica di destinazione.
> * Creare un criterio di replica.
> * Abilitare la replica per una macchina virtuale.

Questa è la terza esercitazione di una serie. In questa esercitazione si presuppone che siano già state completate le attività delle esercitazioni precedenti:

1. [Preparare Azure](tutorial-prepare-azure.md)
2. [Preparare locale Hyper-V](tutorial-prepare-on-premises-hyper-v.md)

Prima di iniziare, è utile [esamina l'architettura](concepts-hyper-v-to-azure-architecture.md) per questo scenario di ripristino di emergenza.

## <a name="select-a-replication-goal"></a>Selezionare un obiettivo di replica


1. In **tutti i servizi** > **insiemi di credenziali di servizi di ripristino**, fare clic sul nome dell'insieme di credenziali è preparati nell'esercitazione precedente, **ContosoVMVault**.
2. In **Introduzione**, fare clic su **Site Recovery**. Quindi fare clic su **preparare l'infrastruttura**
3. In **obiettivi della protezione dati** > **dove sono i computer che si trovano**selezionare **locale**.
4. In **in cui si desidera replicare le macchine**selezionare **in Azure**.
5. In **sono computer virtualizzati**selezionare **Sì, con Hyper-V**.
6. In **si utilizza System Center VMM**selezionare **n**. Fare quindi clic su **OK**.

    ![Obiettivo di replica](./media/tutorial-hyper-v-to-azure/replication-goal.png)

## <a name="set-up-the-source-environment"></a>Configurare l'ambiente di origine

Per configurare l'ambiente di origine, aggiungere gli host Hyper-V a un sito Hyper-V, scaricare e installare il Provider di Azure Site Recovery e l'agente di servizi di ripristino di Azure e registrare il sito Hyper-V nell'insieme di credenziali. 

1. In **Preparare l'infrastruttura** fare clic su **Origine**.
2. Fare clic su **+ sito Hyper-V**e specificare il nome del sito è stati creati nell'esercitazione precedente, **ContosoHyperVSite**.
3. Fare clic su **+ Server Hyper-V**.
4. Scaricare il file di programma di installazione del Provider.
5. Scaricare la chiave di registrazione dell'insieme di credenziali, che sarà necessaria durante la configurazione del provider. La chiave è valida per cinque giorni dal momento in cui viene generata.

    ![Scarica provider](./media/tutorial-hyper-v-to-azure/download.png)
    

### <a name="install-the-provider"></a>Installare il Provider

Eseguire il file di programma di installazione del Provider (AzureSiteRecoveryProvider.exe) in ogni host di Hyper-V aggiunti al **ContosoHyperVSite** sito. Programma di installazione installa il Provider di Azure Site Recovery e l'agente di servizi di ripristino, in ogni host Hyper-V.

1. Nella procedura guidata di installazione di Provider di Azure Site Recovery > **Microsoft Update**, optare per utilizzare Microsoft Update per cercare gli aggiornamenti del Provider.
2. In **installazione**, accettare il percorso di installazione predefinito per il Provider e l'agente e fare clic su **installare**.
3. Dopo l'installazione, nella registrazione guidata di Microsoft Azure Site Recovery > **impostazioni insieme di credenziali**, fare clic su **Sfoglia**e in **File di chiave**, selezionare la chiave dell'insieme di credenziali di file che si scaricato. 
4. Specificare la sottoscrizione di Azure Site Recovery, il nome dell'insieme di credenziali (**ContosoVMVault**) e il sito Hyper-V (**ContosoHyperVSite**) a cui appartiene il server Hyper-V.
5. In **le impostazioni del Proxy**selezionare **connettersi direttamente ad Azure Site Recovery senza un proxy**.
6. In **registrazione**, dopo che il server è registrato nell'insieme di credenziali, fare clic su **fine**.

I metadati del server Hyper-V vengono recuperati da Azure Site Recovery e il server viene visualizzato in **S Infrastruttura di Site Recovery** > **Host Hyper-V**. L'operazione può richiedere fino a 30 minuti.


## <a name="set-up-the-target-environment"></a>Configurare l'ambiente di destinazione

Selezionare e verificare le risorse di destinazione. 

1. Fare clic su **Preparare l'infrastruttura** > **Destinazione**.
2. Selezionare la sottoscrizione e il gruppo di risorse **ContosoRG**, in cui le macchine virtuali di Azure verrà create dopo il failover.
3. Selezionare il **Gestione risorse "** modello di distribuzione.

Site Recovery verifica la disponibilità di uno o più account di archiviazione di Azure e reti compatibili.


## <a name="set-up-a-replication-policy"></a>Configurare criteri di replica

1. Fare clic su **Preparare l'infrastruttura** > **Impostazioni della replica** > **+Crea e associa**.
2. In **crea e associa i criteri**, specificare un nome di criterio, **ContosoReplicationPolicy**.
3. Lasciare le impostazioni predefinite e fare clic su **OK**.
    - **Frequenza di copia** indica che delta verranno replicati i dati (dopo la replica iniziale) ogni cinque minuti.
    - **Conservazione del punto di ripristino** indica che le finestre di conservazione per ogni punto di ripristino saranno due due ore.
    - **Frequenza snapshot coerenti con l'app** indica che i punti di ripristino contenenti snapshot coerenti con l'app verranno creati ogni ora.
    - **Ora di inizio della replica iniziale**, indica che la replica iniziale inizierà immediatamente.
4. Dopo la creazione del criterio, fare clic su **OK**. Quando si crea un nuovo criterio associata automaticamente il sito Hyper-V specificato (**ContosoHyperVSite**)

    ![Criteri di replica](./media/tutorial-hyper-v-to-azure/replication-policy.png)


## <a name="enable-replication"></a>Abilitare la replica


1. In **replicare applicazione**, fare clic su **origine**. 
2. In **origine**, selezionare il **ContosoHyperVSite** sito. Fare quindi clic su **OK**.
3. In **destinazione**, verificare di Azure come destinazione, la sottoscrizione dell'insieme di credenziali e **Gestione risorse** modello di distribuzione.
4. Selezionare il **contosovmsacct1910171607** account di archiviazione è creati nell'esercitazione precedente per i dati replicati e **ContosoASRnet** rete, in cui le macchine virtuali di Azure sarà posizionato dopo il failover.
5. In **macchine virtuali** > **selezionare**, selezionare la macchina virtuale da replicare. Fare quindi clic su **OK**.

 È possibile tenere traccia dello stato del processo **Abilita protezione** in **Processi** > **Site Recovery jobs** (Processi di Site Recovery). Dopo il completamento del processo **Finalizza protezione**, la replica iniziale è completata e la macchina virtuale è pronta per il failover.

## <a name="next-steps"></a>Passaggi successivi
[Eseguire un'esercitazione sul ripristino di emergenza](tutorial-dr-drill-azure.md)
