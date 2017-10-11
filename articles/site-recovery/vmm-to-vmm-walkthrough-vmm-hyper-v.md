---
title: Configurare VMM e Hyper-V per la replica in un sito secondario con Azure Site Recovery | Microsoft Docs
description: Descrive come configurare i server di System Center VMM e gli host Hyper-V per la replica in un sito VMM secondario.
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: d0389e3b-3737-496c-bda6-77152264dd98
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2017
ms.author: raynew
ms.openlocfilehash: 73bd1790c56ac52166f638de2e80c2a2cfb87e53
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2017
---
# <a name="step-4-set-up-vmm-and-hyper-v-for-hyper-v-vm-replication-to-a-secondary-site"></a>Passaggio 4: Configurare VMM e Hyper-V per la replica di VM Hyper-V in un sito secondario 

Dopo aver predisposto la rete, configurare i server di System Center Virtual Machine Manager (VMM) e gli host Hyper-V per la replica di macchine virtuali Hyper-V in un sito secondario tramite [Azure Site Recovery](site-recovery-overview.md) nel portale di Azure. 

È possibile inviare commenti nella parte inferiore di questo articolo oppure nel [forum sui servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).



## <a name="prepare-vmm-servers"></a>Preparare i server VMM 

Preparare la distribuzione:


1. Assicurarsi che i server VMM siano conformi ai [requisiti di supporto](site-recovery-support-matrix-to-sec-site.md#on-premises-servers) e ai [prerequisiti di distribuzione](vmm-to-vmm-walkthrough-prerequisites.md).
2. Verificare che i server VMM siano connessi a Internet e abbiano accesso a questi URL.
    
    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]
    
    - Se sono presenti regole del firewall basate sull'indirizzo IP, verificare che consentano la comunicazione con Azure.
    - Consentire gli [intervalli IP del data center di Azure ](https://www.microsoft.com/download/confirmation.aspx?id=41653) e la porta HTTPS (443).
    - Consentire gli intervalli di indirizzi IP per l'area di Azure della sottoscrizione e per gli Stati Uniti occidentali (usati per il controllo di accesso e la gestione delle identità).
3. Assicurarsi che il server VMM sia [pronto per il mapping di rete](vmm-to-vmm-walkthrough-network.md#prepare-for-network-mapping)


## <a name="prepare-hyper-v-hostsclusters"></a>Preparare gli host/cluster Hyper-V

1. Assicurarsi che gli host/cluster Hyper-V siano conformi ai [requisiti di supporto](site-recovery-support-matrix-to-sec-site.md#on-premises-servers) e ai [prerequisiti di distribuzione](vmm-to-vmm-walkthrough-prerequisites.md).
2. Verificare i requisiti per le [VM Hyper-V](site-recovery-support-matrix-to-sec-site.md#support-for-replicated-machine-os-versions)
3. Verificare i requisiti di [rete](site-recovery-support-matrix-to-sec-site.md#network-configuration) e [archiviazione](site-recovery-support-matrix-to-sec-site.md#storage).
4. Verificare che gli host Hyper-V siano connessi a Internet e abbiano accesso a questi URL.
    
    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]
    
    - Se sono presenti regole del firewall basate sull'indirizzo IP, verificare che consentano la comunicazione con Azure.
    - Consentire gli [intervalli IP del data center di Azure ](https://www.microsoft.com/download/confirmation.aspx?id=41653) e la porta HTTPS (443).
    - Consentire gli intervalli di indirizzi IP per l'area di Azure della sottoscrizione e per gli Stati Uniti occidentali (usati per il controllo di accesso e la gestione delle identità).

## <a name="prepare-for-single-server-deployment"></a>Prepararsi per una distribuzione a server singolo


Se è disponibile un unico server VMM, è possibile replicare le macchine virtuali degli host Hyper-V presenti nel cloud VMM in [Azure](hyper-v-site-walkthrough-overview.md) o in un cloud VMM secondario, come descritto in questo documento. La prima opzione è consigliabile perché la replica tra cloud non è uniforme.

Se si desidera replicare tra cloud, è possibile replicare con un singolo server VMM autonomo o con un singolo server VMM distribuito in un cluster di Windows esteso

### <a name="replicate-with-a-standalone-vmm-server"></a>Eseguire la replica con un server VMM autonomo

In questo scenario distribuire il singolo server VMM come macchina virtuale nel sito primario e replicare questa VM in un sito secondario con Site Recovery e Hyper-V Replica.

1. **Configurare VMM in una VM Hyper-V**. È consigliabile condividere il percorso dell'istanza di SQL Server usata da VMM nella stessa macchina virtuale. In questo modo, infatti, deve essere creata una sola macchina virtuale, con un conseguente risparmio di tempo. Se si vuole usare un'istanza remota di SQL Server e si verifica un'interruzione, è necessario ripristinare l'istanza prima di ripristinare VMM.
2. **Assicurarsi che il server VMM abbia almeno due cloud configurati**. Un cloud conterrà le VM da replicare e l’altro cloud verrà usato come posizione secondaria. Il cloud che contiene le VM da proteggere deve essere conforme ai [prerequisiti](#prerequisites).
3. Configurare Site Recovery come descritto in questo articolo. Creare e registrare il server VMM in un insieme di credenziali, impostare un criterio di replica e abilitare la replica. I nomi VMM di origine e di destinazione saranno uguali. Specificare che la replica iniziale venga eseguita tramite la rete.
4. Se si configura il mapping di rete, verrà eseguito il mapping della rete VM relativa al cloud primario alla rete VM relativa al cloud secondario.
5. Nella console di gestione Hyper-V, abilitare la Replica Hyper-V nell'host Hyper-V che contiene le VM VMM e abilitare la replica sulla VM. Assicurarsi di non aggiungere la macchina virtuale VMM ai cloud protetti mediante Ripristino sito, per garantire che le impostazioni di Replica Hyper-V non siano sottoposte a override da Ripristino sito.
6. Se si creano piani di ripristino per il failover, si userà lo stesso server VMM per l'origine e la destinazione.
7. In caso di interruzione totale, eseguire il failover e il ripristino come riportato di seguito:

   1. Nella console di gestione Hyper-V nel sito secondario, eseguire un failover non pianificato per eseguire il failover della macchina virtuale VMM primaria al sito secondario.
   2. Verificare che la macchina virtuale VMM sia attiva e in esecuzione e, nell'insieme di credenziali, eseguire un failover non pianificato per eseguire il failover delle macchine virtuali dal cloud primario a quello secondario. Eseguire il commit del failover e selezionare un punto di ripristino alternativo, se necessario.
   3. Al termine del failover non pianificato, è di nuovo possibile accedere a tutte le risorse dal sito primario.
   4. Quando il sito primario sarà nuovamente disponibile, abilitare la replica inversa per la macchina virtuale VMM nella console di gestione Hyper-V nel sito secondario. Verrà avviata la replica per la macchina virtuale dal sito secondario al sito primario.
   5. Nella console di gestione Hyper-V nel sito secondario, eseguire un failover pianificato per eseguire il failover della macchina virtuale VMM al sito primario. Eseguire il commit del failover. Viene abilitata la replica inversa per avviare nuovamente la replica della macchina virtuale VMM dal sito primario a quello secondario.
   6. Nell'insieme di credenziali dei Servizi di ripristino abilitare la replica inversa per le VM del carico di lavoro per avviarne la replica dal sito secondario a quello primario.
   7. Nell'insieme di credenziali dei Servizi di ripristino eseguire un failover pianificato per eseguire il failback delle VM del carico di lavoro nel sito primario. Eseguire il commit del failover per completare le operazioni. Abilitare la replica inversa per avviare la replica delle VM del carico di lavoro dal sito primario a quello secondario.

### <a name="replicate-with-a-stretched-vmm-cluster"></a>Eseguire la replica con un cluster VMM con estensione

Anziché distribuire un server VMM autonomo come VM che replica in un sito secondario, è possibile garantire la disponibilità elevata di VMM distribuendolo come VM in un cluster di failover di Windows, assicurando così flessibilità al carico di lavoro e protezione da errori hardware. Per effettuare la distribuzione usando Site Recovery, la macchina virtuale di VMM deve essere distribuita in un cluster esteso in siti geograficamente separati. A tale scopo, seguire questa procedura:

1. Installare VMM su una macchina virtuale in un cluster di failover di Windows e selezionare l'opzione per eseguire il server come disponibilità elevata durante l'installazione.
2. L'istanza di SQL Server usata da VMM deve essere replicata con gruppi di disponibilità AlwaysOn di SQL Server in modo che sia disponibile una replica del database nel sito secondario.
3. Seguire le istruzioni in questo articolo per creare un insieme di credenziali, registrare il server e configurare la protezione. È necessario registrare ogni server VMM nel cluster nell'insieme di credenziali di Servizi di ripristino. A tale scopo, installare il Provider in un nodo attivo e registrare il server VMM. Quindi installare il provider sugli altri nodi.
4. Se si verifica un'interruzione, il server VMM e il database SQL Server corrispondente sono sottoposti a failover e l'accesso viene eseguito dal sito secondario.



## <a name="next-steps"></a>Passaggi successivi

Vedere [Passaggio 5: Configurare un insieme di credenziali](vmm-to-vmm-walkthrough-create-vault.md).