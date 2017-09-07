---
title: Configurare l'origine e la destinazione per la replica Hyper-V in Azure (con System Center VMM) con Azure Site Recovery| Microsoft Docs
description: Viene riepilogata la procedura necessaria per configurare le impostazioni di origine e destinazione per la replica in Archiviazione di Azure di macchine virtuali Hyper-V presenti in cloud VMM con Azure Site Recovery
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 5edb6d87-25a5-40fe-b6f1-ddf7b55a6b31
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/25/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: c72f839d0a1288dccb7deb3e44fc2b20d64818f0
ms.contentlocale: it-it
ms.lasthandoff: 08/21/2017

---

# <a name="step-8-set-up-the-source-and-target-for-hyper-v-with-vmm-replication-to-azure"></a>Passaggio 8: Configurare l'origine e la destinazione per la replica Hyper-V (con VMM) in Azure

Dopo aver [creato un insieme di credenziali delle chiavi](vmm-to-azure-walkthrough-create-vault.md) e aver specificato cosa si vuole replicare, usare le informazioni in questo articolo per configurare le impostazioni di origine e destinazione per la replica in Azure di macchine virtuali Hyper-V locali presenti in cloud di System Center Virtual Machine Manager (VMM) usando il servizio [Azure Site Recovery](site-recovery-overview.md) nel portale di Azure.

Inserire commenti e domande nella parte inferiore di questo articolo oppure nel [forum sui servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="set-up-the-source-environment"></a>Configurare l'ambiente di origine

S1. Fare clic su **Preparare l'infrastruttura** > **Origine**.

    ![Set up source](./media/vmm-to-azure-walkthrough-source-target/set-source1.png)

2. In **Prepara origine** fare clic su **+ VMM** per aggiungere un server VMM.

    ![Impostare l'origine](./media/vmm-to-azure-walkthrough-source-target/set-source2.png)

3. In **Aggiungi server** verificare che in **Tipo di server** sia visualizzato **System Center VMM server** (Server System Center VMM) e che il server VMM sia conforme a [prerequisiti e requisiti URL](#prerequisites).
4. Scaricare il file di installazione del provider di Azure Site Recovery.
5. Scaricare la chiave di registrazione, che sarà necessaria durante l'installazione. La chiave è valida per cinque giorni dal momento in cui viene generata.

    ![Impostare l'origine](./media/vmm-to-azure-walkthrough-source-target/set-source3.png)

## <a name="install-the-provider-on-the-vmm-server"></a>Installare il provider nel server VMM

1. Eseguire il file di installazione del provider in ogni server VMM.
2. In **Microsoft Update** è possibile acconsentire esplicitamente agli aggiornamenti in modo che gli aggiornamenti del provider vengano installati in base ai criteri di Microsoft Update.
3. In **Installazione** accettare o modificare il percorso predefinito di installazione del provider e quindi fare clic su **Installa**.

    ![Percorso di installazione](./media/vmm-to-azure-walkthrough-source-target/provider2.png)
4. Al termine dell'installazione fare clic su **Registra** per registrare il server VMM nell'insieme di credenziali.
5. Nella pagina **Impostazioni dell'insieme di credenziali** fare clic su **Sfoglia** per selezionare il file di chiave dell'insieme di credenziali. Specificare la sottoscrizione di Azure Site Recovery e il nome dell'insieme di credenziali.

    ![Server registration](./media/vmm-to-azure-walkthrough-source-target/provider10.png)
6. In **Connessione Internet**specificare la modalità di connessione a Site Recovery tramite Internet del provider in esecuzione nel server VMM.

   * Per fare in modo che il provider si connetta direttamente, selezionare **Connetti direttamente ad Azure Site Recovery senza server proxy**.
   * Se per il proxy esistente è necessaria l'autenticazione o si vuole usare un proxy personalizzato, selezionare **Connetti ad Azure Site Recovery usando un server proxy**.
   * Se si usa un proxy personalizzato è necessario specificare l'indirizzo, la porta e le credenziali.
   * Se si usa un proxy, è necessario che gli URL descritti nei [prerequisiti](#on-premises-prerequisites) siano già consentiti.
   * Se si usa un proxy personalizzato, un account RunAs di VMM (DRAProxyAccount) verrà creato automaticamente con le credenziali del proxy specificate. Configurare il server proxy in modo che l'account possa eseguire correttamente l'autenticazione. Le impostazioni dell'account RunAs di VMM possono essere modificate nella console VMM. In **Impostazioni** espandere **Sicurezza** > **Account RunAs** e quindi modificare la password di DRAProxyAccount. È necessario riavviare il servizio VMM per rendere effettiva l'impostazione.

     ![Internet](./media/vmm-to-azure-walkthrough-source-target/provider13.png)
7. Accettare o modificare il percorso del certificato SSL generato automaticamente per la crittografia dei dati. Questo certificato viene usato se si abilita la crittografia dei dati per un cloud protetto da Azure nel portale di Azure Site Recovery. Conservare il certificato in una posizione sicura, Se la crittografia dei dati è abilitata, quando si esegue un failover in Azure è necessario eseguire la decrittografia.
8. In **Nome server**specificare un nome descrittivo per identificare il server VMM nell'insieme di credenziali. In una configurazione cluster specificare il nome del ruolo relativo al cluster VMM.
9. Abilitare **Sincronizza i metadati cloud** per sincronizzare i metadati relativi a tutti i cloud presenti nel server VMM con l'insieme di credenziali. È necessario eseguire questa azione solo una volta in ogni server. Se non si vogliono sincronizzare tutti i cloud, è possibile lasciare deselezionata questa opzione e sincronizzare ogni cloud singolarmente nelle proprietà del cloud nella console VMM. Fare clic su **Register** per completare il processo.

    ![Server registration](./media/vmm-to-azure-walkthrough-source-target/provider16.png)
10. Verrà avviata la registrazione. Al termine della registrazione, il server verrà visualizzato in **Infrastruttura di Site Recovery** > **Server VMM**.


## <a name="install-the-azure-recovery-services-agent-on-hyper-v-hosts"></a>Installare l'agente di Servizi di ripristino di Azure negli host Hyper-V

1. Dopo aver configurato il provider è necessario scaricare il file di installazione per l'agente di Servizi di ripristino di Azure. Eseguire l'installazione in ogni server Hyper-V nel cloud VMM.

    ![Siti Hyper-V](./media/vmm-to-azure-walkthrough-source-target/hyperv-agent1.png)
2. In **Controllo dei prerequisiti** fare clic su **Avanti**. Gli eventuali prerequisiti mancanti verranno installati automaticamente.

    ![Prerequisiti per l'agente di Servizi di ripristino di Azure](./media/vmm-to-azure-walkthrough-source-target/hyperv-agent2.png)
3. In **Impostazioni di installazione** accettare o modificare il percorso di installazione e il percorso della cache. È possibile configurare la cache in un'unità con almeno 5 GB di spazio di archiviazione disponibile ma è consigliabile usare un disco della cache con almeno 600 GB di spazio disponibile. Fare clic su **Installa**.
4. Al termine dell'installazione fare clic su **Chiudi** per completare l'operazione.

    ![Registrare l'Agente di Servizi di ripristino di Microsoft Azure](./media/vmm-to-azure-walkthrough-source-target/hyperv-agent3.png)

### <a name="command-line-installation"></a>Installazione dalla riga di comando
È possibile installare l'agente di Servizi di ripristino di Microsoft Azure dalla riga di comando con questo comando:

     marsagentinstaller.exe /q /nu

### <a name="set-up-internet-proxy-access-to-site-recovery-from-hyper-v-hosts"></a>Impostare l'accesso tramite proxy Internet a Site Recovery da host Hyper-V

L'agente di servizi di ripristino in esecuzione negli host Hyper-V richiede l'accesso ad Azure tramite Internet per la replica delle macchine virtuali. Se si accede a Internet tramite un proxy, configurarlo come indicato di seguito:

1. Aprire lo snap-in di MMC di Backup di Microsoft Azure nell'host Hyper-V. Per impostazione predefinita, un collegamento a Backup di Microsoft Azure è disponibile sul desktop oppure in: C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin.
2. Nello snap-in fare clic su **Modifica proprietà**.
3. Nella scheda **Configurazione proxy** specificare le informazioni del server proxy.

    ![Registrare l'Agente di Servizi di ripristino di Microsoft Azure](./media/vmm-to-azure-walkthrough-source-target/mars-proxy.png)
4. Assicurarsi che l'agente possa raggiungere gli URL indicati nei [prerequisiti](#on-premises-prerequisites).

## <a name="set-up-the-target-environment"></a>Configurare l'ambiente di destinazione
Specificare l'account di archiviazione di Azure da usare per la replica e la rete di Azure a cui le macchine virtuali di Azure dovranno connettersi dopo il failover.

1. Fare clic su **Preparare l'infrastruttura** > **Destinazione**, selezionare la sottoscrizione e il gruppo di risorse in cui si vuole creare il failover delle macchine virtuali. Scegliere il modello di distribuzione che si vuole usare in Azure (classica o con Resource Manager) per il failover delle macchine virtuali.

    ![Archiviazione](./media/vmm-to-azure-walkthrough-source-target/enablerep3.png)

2. Site Recovery verifica la disponibilità di uno o più account di archiviazione di Azure e reti compatibili.

    ![Archiviazione](./media/vmm-to-azure-walkthrough-source-target/compatible-storage.png)

3. Per creare un account di archiviazione con Resource Manager, se non è già stato fatto, fare clic su **+ Account di archiviazione** per eseguire l'operazione inline.  Nel pannello **Crea account di archiviazione** specificare il nome, il tipo, la sottoscrizione e la località dell'account. L'account deve trovarsi nella stessa località dell'insieme di credenziali di Servizi di ripristino.

   ![Archiviazione](./media/vmm-to-azure-walkthrough-source-target/gs-createstorage.png)


   * Per creare un account di archiviazione con il modello di distribuzione classica, usare il portale di Azure. [Altre informazioni](../storage/common/storage-create-storage-account.md)
   * Se si usa un account di archiviazione Premium per i dati replicati, configurare un account di archiviazione Standard aggiuntivo per l'archiviazione dei log di replica in cui vengono acquisite le modifiche in corso ai dati locali.
5. Per creare una rete di Azure con Resource Manager, se non è già stato fatto, fare clic su **+ Rete** per eseguire l'operazione inline. Nel pannello **Crea rete virtuale** specificare il nome, l'intervallo di indirizzi, i dettagli della subnet, la sottoscrizione e la località della rete. La rete deve trovarsi nella stessa località dell'insieme di credenziali di Servizi di ripristino.

   ![Rete](./media/vmm-to-azure-walkthrough-source-target/gs-createnetwork.png)

   Per creare una rete con il modello di distribuzione classica, usare il portale di Azure. [Altre informazioni](../virtual-network/virtual-networks-create-vnet-classic-pportal.md).





## <a name="next-steps"></a>Passaggi successivi

Andare al [Passaggio 9: Configurare il mapping di rete](vmm-to-azure-walkthrough-network-mapping.md)

