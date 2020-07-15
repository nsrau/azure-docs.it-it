---
title: Configurare il ripristino di emergenza Hyper-V (con VMM) usando Azure Site Recovery
description: Informazioni su come configurare il ripristino di emergenza in Azure di macchine virtuali Hyper-V locali di cloud System Center VMM con Site Recovery.
ms.topic: tutorial
ms.date: 03/19/2020
ms.custom: MVC
ms.openlocfilehash: f32103adce184a67cec9e5a778ac1d1e6f330f4d
ms.sourcegitcommit: e995f770a0182a93c4e664e60c025e5ba66d6a45
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86130239"
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-in-vmm-clouds-to-azure"></a>Configurare il ripristino di emergenza in Azure di macchine virtuali Hyper-V locali di cloud VMM

Questa esercitazione illustra come abilitare la replica per le macchine virtuali Hyper-V locali gestite da System Center Virtual Machine Manager (VMM) in Azure usando [Azure Site Recovery](site-recovery-overview.md). Se non si usa VMM, [seguire questa esercitazione](hyper-v-azure-tutorial.md).

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Selezionare l'origine e la destinazione della replica.
> * Configurare l'ambiente di replica di origine, inclusi i componenti di Site Recovery locali e l'ambiente di replica di destinazione.
> * Configurare il mapping di rete tra le reti di macchine virtuali di VMM e le reti virtuali di Azure.
> * Creare un criterio di replica.
> * Abilitare la replica per una macchina virtuale.

> [!NOTE]
> Le esercitazioni mostrano il percorso di distribuzione più semplice per uno scenario. Quando possibile, vengono usate le opzioni predefinite e non sono riportati tutti i percorsi e le impostazioni possibili. Per istruzioni dettagliate, esaminare gli articoli nella sezione **Guide pratiche** della [documentazione di Site Recovery](./index.yml).

## <a name="prerequisites"></a>Prerequisiti

Per questa esercitazione si presuppone che l'utente abbia già completato le esercitazioni seguenti:

1. [Preparare Azure](tutorial-prepare-azure.md)
1. [Preparare i server Hyper-V locali](hyper-v-prepare-on-premises-tutorial.md)

## <a name="select-a-replication-goal"></a>Selezionare un obiettivo di replica

1. Nel portale di Azure passare a **Insiemi di credenziali di Servizi di ripristino** e selezionare l'insieme di credenziali **ContosoVMVault** creato nell'esercitazione [Preparare Azure](tutorial-prepare-azure.md#create-a-recovery-services-vault).
1. In **Attività iniziali** selezionare **Site Recovery** > **Preparare l'infrastruttura** e configurare le impostazioni seguenti:
    1. In **Obiettivo di protezione** > **Dove si trovano le macchine virtuali?** selezionare **Locale**.
    1. In **In quale destinazione si vuole eseguire la replica dei computer?** selezionare **In Azure**.
    1. In **I computer sono virtualizzati?** selezionare **Sì, con Hyper-V**.
    1. In **Are you using System Center VMM to manage your Hyper-V hosts** (Si usa System Center VMM per gestire gli host Hyper-V?) selezionare **Sì**.
1. Selezionare **OK**.

   ![Obiettivo di replica](./media/hyper-v-vmm-azure-tutorial/replication-goal.png)

## <a name="confirm-deployment-planning"></a>Confermare la pianificazione della distribuzione

1. Se si sta pianificando una distribuzione di grandi dimensioni, in **Pianificazione della distribuzione** scaricare Deployment Planner per Hyper-V usando il collegamento presente nella pagina. [Altre informazioni](hyper-v-deployment-planner-overview.md) sulla pianificazione della distribuzione di Hyper-V.
1. Per questa esercitazione non è necessario Deployment Planner. In **È stata completata la pianificazione della distribuzione?** selezionare **Operazione da completare in seguito** e quindi selezionare **OK**.

## <a name="set-up-the-source-environment"></a>Configurare l'ambiente di origine

Quando si configura l'ambiente di origine, è necessario installare il provider di Azure Site Recovery nel server VMM e registrare il server nell'insieme di credenziali. Installare l'agente di Servizi di ripristino di Azure in ognuno degli host Hyper-V.

1. **Preparare l'infrastruttura**. Selezionare **Origine**.
1. **Preparare l'origine**. Selezionare **+ VMM** per aggiungere un server VMM. In **Aggiungi server** verificare che **System Center VMM server** compaia in **Tipo server**.
1. Scaricare il programma di installazione del provider di Microsoft Azure Site Recovery.
1. Scaricare la chiave di registrazione dell'insieme di credenziali, che sarà necessaria durante la configurazione del provider. La chiave è valida per cinque giorni dal momento in cui viene generata.
1. Scaricare il programma di installazione del provider dell'agente di Servizi di ripristino di Microsoft Azure.

   ![Scaricare il provider, la chiave di registrazione e l'agente](./media/hyper-v-vmm-azure-tutorial/download-vmm.png)

### <a name="install-the-provider-on-the-vmm-server"></a>Installare il provider nel server VMM

1. Nell'installazione guidata del provider di Azure Site Recovery, in **Microsoft Update** acconsentire esplicitamente a usare Microsoft Update per verificare la disponibilità di aggiornamenti del provider.
1. **Installazione**. Accettare il percorso di installazione predefinito per il provider e l'agente e quindi selezionare **Installa**.
1. Dopo l'installazione, in Registrazione guidata di Microsoft Azure Site Recovery selezionare **Impostazioni dell'insieme di credenziali**, **Sfoglia** e in **File di chiave** selezionare il file di chiave dell'insieme di credenziali scaricato.
1. Specificare la sottoscrizione di Azure Site Recovery e il nome dell'insieme di credenziali (**ContosoVMVault**). Specificare un nome descrittivo per identificare il server VMM nell'insieme di credenziali.
1. **Impostazioni proxy**. Selezionare **Connetti direttamente ad Azure Site Recovery senza server proxy**.
1. Accettare il percorso predefinito per il certificato usato per crittografare i dati. I dati crittografati verranno decrittografati durante il failover.
1. **Sincronizza i metadati cloud**. Selezionare **Sincronizza i metadati del cloud con il portale di Site Recovery**. È necessario eseguire questa azione solo una volta in ogni server. Selezionare quindi **Registra**.
1. Dopo avere registrato il server nell'insieme di credenziali, fare clic su **Fine**.

Al termine della registrazione, i metadati del server vengono recuperati da Azure Site Recovery e il server VMM viene visualizzato in **Infrastruttura di Site Recovery**.

### <a name="install-the-recovery-services-agent-on-hyper-v-hosts"></a>Installare l'agente di Servizi di ripristino negli host Hyper-V

Installare l'agente in ogni host Hyper-V contenente le macchine virtuali da replicare.

Nell'Installazione guidata di Agente di Servizi di ripristino di Microsoft Azure configurare queste impostazioni:

1. **Controllo dei prerequisiti**. Selezionare **Avanti**. Gli eventuali prerequisiti mancanti verranno installati automaticamente.
1. **Impostazioni di installazione**. Accettare il percorso di installazione e il percorso della cache. L'unità di cache necessita di almeno 5 GB di spazio di archiviazione. Si consiglia un'unità con almeno 600 GB di spazio libero. Quindi, selezionare **Installa**.
1. **Installazione**. Al termine dell'installazione, selezionare **Chiudi** per chiudere la procedura guidata.

   ![Installare Agent](./media/hyper-v-vmm-azure-tutorial/mars-install.png)

## <a name="set-up-the-target-environment"></a>Configurare l'ambiente di destinazione

1. Selezionare **Preparare l'infrastruttura** > **Destinazione**.
1. Selezionare la sottoscrizione e il gruppo di risorse (**ContosoRG**) in cui verranno create le macchine virtuali di Azure dopo il failover.
1. Selezionare il modello di distribuzione **Resource Manager**.

Site Recovery verifica la disponibilità di uno o più account di archiviazione di Azure e reti compatibili.

## <a name="configure-network-mapping"></a>Configurare il mapping di rete

1. **Infrastruttura di Site Recovery** > **Mapping di rete** > **Mapping di rete**. Selezionare l'icona **+ Mapping di rete**.
1. **Aggiungi mapping di rete**. Selezionare il server **System Center VMM di origine**. Per la **Destinazione** selezionare Azure.
1. Verificare la sottoscrizione e il modello di distribuzione dopo il failover.
1. **Rete di origine**. Selezionare la rete delle macchine virtuali di origine locali.
1. **Rete di destinazione**. Selezionare la rete di Azure in cui collocare le macchine virtuali di Azure di replica create dopo il failover. Quindi selezionare **OK**.

   ![Mapping di rete](./media/hyper-v-vmm-azure-tutorial/network-mapping-vmm.png)

## <a name="set-up-a-replication-policy"></a>Configurare criteri di replica

1. Selezionare **Preparare l'infrastruttura** > **Impostazioni della replica** >  **+Crea e associa**.
1. In **Criteri di creazione e associazione**specificare il nome dei criteri. Il nome usato al momento è **ContosoReplicationPolicy**.
1. Accettare le impostazioni predefinite e fare clic su **OK**:
   - **Frequenza di copia**: indica che dopo la replica iniziale i dati differenziali verranno replicati ogni 5 minuti.
   - **Conservazione del punto di ripristino**: indica che ogni punto di ripristino verrà conservato per due ore.
   - **Frequenza snapshot coerenti con l'app**: indica che i punti di ripristino contenenti snapshot coerenti con l'app verranno creati ogni ora.
   - **Ora di inizio della replica iniziale**: indica che la replica iniziale verrà avviata immediatamente.
   - **Crittografare i dati archiviati in Azure**: l'impostazione predefinita **No** indica che i dati inattivi di Azure non sono crittografati.
1. Dopo aver creato i criteri, selezionare **OK**. Quando si creano nuovi criteri, questi vengono associati automaticamente al cloud VMM.

## <a name="enable-replication"></a>Abilitare la replica

1. **Eseguire la replica dell'applicazione**. Selezionare **Origine**.
1. **Source**. Selezionare il cloud VMM. Quindi selezionare **OK**.
1. **Destinazione**. Verificare la destinazione (Azure), controllare la sottoscrizione dell'insieme di credenziali e selezionare il modello **Resource Manager**.
1. Selezionare l'account di archiviazione **contosovmsacct1910171607** e la rete di Azure **ContosoASRnet**.
1. **Macchine virtuali** > **Seleziona**. Selezionare la macchina virtuale da replicare. Quindi selezionare **OK**.

   È possibile tenere traccia dello stato del processo **Abilita protezione** in **Processi** > **Site Recovery jobs** (Processi di Site Recovery). Al termine del processo **Finalizza protezione**, la replica iniziale è completa e la macchina virtuale è pronta per il failover.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Eseguire un'esercitazione sul ripristino di emergenza](tutorial-dr-drill-azure.md)
