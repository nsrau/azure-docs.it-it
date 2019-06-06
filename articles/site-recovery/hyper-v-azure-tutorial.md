---
title: Configurare il ripristino di emergenza per macchine virtuali Hyper-V locali (senza VMM) in Azure con Azure Site Recovery | Microsoft Docs
description: Informazioni su come configurare il ripristino di emergenza per macchine virtuali Hyper-V locali (senza VMM) in Azure con Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 05/30/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 9d45e0f0c759685f9d35285ee7718585d5961333
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66399404"
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-to-azure"></a>Configurare il ripristino di emergenza di macchine virtuali Hyper-V locali in Azure

Il servizio [Azure Site Recovery](site-recovery-overview.md) favorisce l'attuazione della strategia di ripristino di emergenza gestendo e coordinando le operazioni di replica, failover e failback di computer locali e macchine virtuali di Azure.

Questa è la terza esercitazione di una serie. Descrive come configurare il ripristino di emergenza di macchine virtuali Hyper-V locali in Azure. L'esercitazione si applica alle macchine virtuali Hyper-V non gestite da System Center Virtual Machine Manager (VMM).

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Selezionare l'origine e la destinazione della replica.
> * Configurare l'ambiente di replica di origine, inclusi i componenti di Site Recovery locali e l'ambiente di replica di destinazione.
> * Creare un criterio di replica.
> * Abilitare la replica per una macchina virtuale.

> [!NOTE]
> Le esercitazioni mostrano il percorso di distribuzione più semplice per uno scenario. Quando possibile, vengono usate le opzioni predefinite e non sono riportati tutti i percorsi e le impostazioni possibili. Per istruzioni dettagliate, esaminare gli articoli nella sezione **Guide pratiche** della [documentazione di Site Recovery](https://docs.microsoft.com/azure/site-recovery).

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
6. In **Are you using System Center VMM to manage your Hyper-V hosts** (Si usa System Center VMM per gestire gli host Hyper-V?) selezionare **No**.
7. Selezionare **OK**.

    ![Obiettivo di replica](./media/hyper-v-azure-tutorial/replication-goal.png)

## <a name="confirm-deployment-planning"></a>Confermare la pianificazione della distribuzione

1. Se si sta pianificando una distribuzione di grandi dimensioni, in **Pianificazione della distribuzione** scaricare Deployment Planner per Hyper-V usando il collegamento presente nella pagina. [Altre informazioni](hyper-v-deployment-planner-overview.md) sulla pianificazione della distribuzione di Hyper-V.
2. Per questa esercitazione non è necessario Deployment Planner. In **È stata completata la pianificazione della distribuzione?** selezionare **Operazione da completare in seguito** e quindi selezionare **OK**.

    ![Pianificazione della distribuzione](./media/hyper-v-azure-tutorial/deployment-planning.png)

## <a name="set-up-the-source-environment"></a>Configurare l'ambiente di origine

Per configurare l'ambiente di origine, creare un sito Hyper-V e aggiungere al sito gli host Hyper-V contenenti le macchine virtuali di cui si vuole eseguire la replica. Scaricare e installare quindi il provider di Azure Site Recovery e l'agente di Servizi di ripristino di Azure in ogni host e quindi registrare il sito Hyper-V nell'insieme di credenziali.

1. In **Preparare l'infrastruttura** selezionare **Origine**.
2. In **Prepara origine** fare clic su **+ Sito Hyper-V**.
3. In **Creare il sito Hyper-V** specificare il nome del sito. Il nome del sito usato al momento è **ContosoHyperVSite**.

    ![Sito di Hyper-V](./media/hyper-v-azure-tutorial/hyperv-site.png)

4. Dopo la creazione del sito in **Prepara origine** > **Passaggio 1: Selezionare il sito Hyper-V** selezionare il sito creato.
5. Selezionare **+ Hyper-V Server**.

    ![Server Hyper-V](./media/hyper-v-azure-tutorial/hyperv-server.png)

6. Scaricare il programma di installazione del provider di Microsoft Azure Site Recovery.
7. Scaricare la chiave di registrazione dell'insieme di credenziali, Questa chiave è necessaria per installare il provider. La chiave è valida per cinque giorni dal momento in cui viene generata.

    ![Scaricare il provider e la chiave di registrazione](./media/hyper-v-azure-tutorial/download.png)
    

### <a name="install-the-provider"></a>Installare il provider

Installare il file di installazione scaricato (AzureSiteRecoveryProvider.exe) in ogni host Hyper-V che si vuole aggiungere al sito Hyper-V. Il file installa il provider di Azure Site Recovery e l'agente di Servizi di ripristino in ogni host Hyper-V.

1. Eseguire il file di installazione.
2. Nell'installazione guidata del provider di Azure Site Recovery > **Microsoft Update** acconsentire esplicitamente a usare Microsoft Update per verificare la disponibilità degli aggiornamenti del provider.
3. In **Installazione** accettare il percorso di installazione predefinito per il provider e l'agente e quindi selezionare **Installa**.
4. Dopo l'installazione, in Registrazione guidata di Microsoft Azure Site Recovery > **Impostazioni dell'insieme di credenziali** selezionare **Sfoglia** e in **File di chiave** selezionare il file di chiave dell'insieme di credenziali scaricato.
5. Specificare la sottoscrizione di Azure Site Recovery, il nome dell'insieme di credenziali (**ContosoVMVault**) e il sito Hyper-V (**ContosoHyperVSite**) a cui appartiene il server Hyper-V.
6. In **Impostazioni proxy** selezionare **Connetti direttamente ad Azure Site Recovery senza server proxy**.
7. Dopo avere registrato il server nell'insieme di credenziali, in **Registrazione** fare clic su **Fine**.

I metadati del server Hyper-V vengono recuperati da Azure Site Recovery e il server viene visualizzato in **S Infrastruttura di Site Recovery** > **Host Hyper-V**. Il processo potrebbe richiedere fino a 30 minuti.

#### <a name="install-the-provider-on-a-hyper-v-core-server"></a>Installare il provider in un server Hyper-V Core

Se è in esecuzione un server Hyper-V Core, scaricare il file di installazione e completare questi passaggi:

1. Estrarre i file da AzureSiteRecoveryProvider.exe in una directory locale eseguendo questo comando:

    `AzureSiteRecoveryProvider.exe /x:. /q`
 
2. Eseguire `.\setupdr.exe /i`. I risultati vengono registrati in %Programdata%\ASRLogs\DRASetupWizard.log.

3. Registrare il server eseguendo questo comando:

    ```
    cd  C:\Program Files\Microsoft Azure Site Recovery Provider\DRConfigurator.exe" /r /Friendlyname "FriendlyName of the Server" /Credentials "path to where the credential file is saved"
    ```

## <a name="set-up-the-target-environment"></a>Configurare l'ambiente di destinazione

Selezionare e verificare le risorse di destinazione:

1. Selezionare **Preparare l'infrastruttura** > **Destinazione**.
2. Selezionare la sottoscrizione e il gruppo di risorse (**ContosoRG**) in cui verranno create le macchine virtuali di Azure dopo il failover.
3. Selezionare il modello di distribuzione **Resource Manager**.

Site Recovery verifica la disponibilità di uno o più account di archiviazione di Azure e reti compatibili.

## <a name="set-up-a-replication-policy"></a>Configurare criteri di replica

1. Selezionare **Preparare l'infrastruttura** > **Impostazioni della replica** >  **+Crea e associa**.
2. In **Criteri di creazione e associazione**specificare il nome dei criteri. Il nome usato al momento è **ContosoReplicationPolicy**.
3. Per questa esercitazione verranno usate le impostazioni predefinite:
    - **Frequenza di copia**: indica la frequenza di replica dei dati delta (dopo la replica iniziale). La frequenza predefinita è ogni 5 minuti.
    - **Conservazione del punto di ripristino** indica che i punti di ripristino verranno conservati per due ore.
    - **Frequenza snapshot coerenti con l'app**: indica che i punti di ripristino contenenti snapshot coerenti con l'app verranno creati ogni ora.
    - **Ora di inizio della replica iniziale**: indica che la replica iniziale verrà avviata immediatamente.
4. Dopo aver creato i criteri, selezionare **OK**. Quando si creano nuovi criteri, questi vengono associati automaticamente al sito Hyper-V. In questa esercitazione si tratta di **ContosoHyperVSite**.

    ![Criteri di replica](./media/hyper-v-azure-tutorial/replication-policy.png)

## <a name="enable-replication"></a>Abilitare la replica

1. In **Eseguire la replica dell'applicazione** selezionare **Origine**.
2. In **Origine** selezionare il sito **ContosoHyperVSite**. Quindi selezionare **OK**.
3. In **Destinazione** verificare la destinazione (Azure), la sottoscrizione dell'insieme di credenziali e il modello di distribuzione di **Resource Manager**.
4. Se si usano le impostazioni dell'esercitazione, selezionare l'account di archiviazione **contosovmsacct1910171607** creato nell'esercitazione precedente per i dati replicati. Selezionare anche la rete **ContosoASRnet**, in cui si troveranno le macchine virtuali di Azure dopo il failover.
5. In **Macchine virtuali** > **Seleziona** selezionare la macchina virtuale di cui si vuole eseguire la replica. Quindi selezionare **OK**.

   È possibile tenere traccia dello stato del processo **Abilita protezione** in **Processi** > **Site Recovery jobs** (Processi di Site Recovery). Al termine del processo **Finalizza protezione**, la replica iniziale è completa e la macchina virtuale è pronta per il failover.

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Eseguire un'esercitazione sul ripristino di emergenza](tutorial-dr-drill-azure.md)
