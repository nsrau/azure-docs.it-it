---
title: Configurare la replica per macchine virtuali con Spazi di archiviazione diretta (S2D) in Azure Site Recovery | Microsoft Docs
description: Questo articolo illustra come configurare la replica di macchine virtuali con S2D da un'area di Azure a un'altra mediante Site Recovery.
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 01/29/2019
ms.author: asgang
ms.openlocfilehash: 6c639d4503b170660abed5767e3571c8a2bf24b9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60790343"
---
# <a name="replicate-azure-virtual-machines-using-storage-spaces-direct-to-another-azure-region"></a>Replicare macchine virtuali di Azure con Spazi di archiviazione diretta in un'altra area di Azure

Questo articolo descrive come abilitare il ripristino di emergenza di macchine virtuali di Azure che eseguono Spazi di archiviazione diretta.

>[!NOTE]
>Per i cluster di Spazi di archiviazione diretta sono supportati solo i punti di ripristino coerenti con l'arresto anomalo del sistema.
>

## <a name="introduction"></a>Introduzione 
[Spazi di archiviazione diretta (S2D)](https://docs.microsoft.com/windows-server/storage/storage-spaces/deploy-storage-spaces-direct) è una risorsa di archiviazione software-defined che offre un modo per creare [cluster guest](https://blogs.msdn.microsoft.com/clustering/2017/02/14/deploying-an-iaas-vm-guest-clusters-in-microsoft-azure) in Azure.  Un cluster guest in Microsoft Azure è un cluster di failover costituito da macchine virtuali IaaS. Consente di eseguire il failover dei carichi di lavoro delle macchine virtuali ospitate nei cluster guest, ottenendo così un contratto di servizio con una disponibilità superiore per le applicazioni rispetto a quanto sarebbe possibile per una singola macchina virtuale di Azure. È utile negli scenari in cui la macchina virtuale ospita un'applicazione critica, come SQL, File server di scalabilità orizzontale e così via.

## <a name="disaster-recovery-of-azure-virtual-machines-using-storage-spaces-direct"></a>Ripristino di emergenza di macchine virtuali di Azure con Spazi di archiviazione diretta
In uno scenario tipico si può avere un cluster guest di macchine virtuali in Azure per una resilienza superiore dell'applicazione, ad esempio File server di scalabilità orizzontale. Anche se questo fornisce all'applicazione una disponibilità più elevata, si vogliono proteggere le applicazioni usando Site Recovery per qualsiasi errore a livello di area. Site Recovery replica i dati da un'area di Azure a un'altra e attiva il cluster nell'area di ripristino di emergenza in caso di failover.

Il diagramma seguente mostra la rappresentazione visiva del failover di macchine virtuali di Azure con Spazi di archiviazione diretta.

![Spazi di archiviazione diretta](./media/azure-to-azure-how-to-enable-replication-s2d-vms/storagespacedirect.png)

 
- Due macchine virtuali di Azure in un cluster di failover Windows, ognuna delle quali con due o più dischi dati.
- S2D sincronizza i dati nel disco dati e presenta le risorse di archiviazione sincronizzate sotto forma di pool di archiviazione.
- Il pool di archiviazione si presenta al cluster di failover come un volume condiviso cluster.
- Il cluster di failover usa il volume condiviso cluster per le unità dati.

**Considerazioni sul ripristino di emergenza**

1. Quando si configura il [cloud di controllo](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness#CloudWitnessSetUp) per il cluster, mantenere il cloud di controllo nell'area di ripristino di emergenza.
2. Se si intende eseguire il failover delle macchine virtuali in una subnet situata in un'area di ripristino di emergenza diversa dall'area di origine, dopo il failover occorre modificare l'indirizzo IP del cluster.  Per modificare l'indirizzo IP del cluster è necessario usare lo [script del piano di ripristino](https://docs.microsoft.com/azure/site-recovery/site-recovery-runbook-automation) ASR.</br>
[Script di esempio](https://github.com/krnese/azure-quickstart-templates/blob/master/asr-automation-recovery/scripts/ASR-Wordpress-ChangeMysqlConfig.ps1) per eseguire il comando all'interno della macchina virtuale usando l'estensione script personalizzata 

### <a name="enabling-site-recovery-for-s2d-cluster"></a>Abilitazione di Site Recovery per il cluster S2D:

1. All'interno dell'insieme di credenziali dei servizi di ripristino fare clic su "+ replica"
1. Selezionare tutti i nodi nel cluster e renderli parte di un [gruppo di coerenza tra più macchine virtuali](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-common-questions#multi-vm-consistency)
1. Selezionare i criteri di replica con la coerenza dell'applicazione disattivata* (è disponibile solo il supporto della coerenza per arresto anomalo del sistema)
1. Abilitare la replica

   ![Protezione di Spazi di archiviazione diretta](./media/azure-to-azure-how-to-enable-replication-s2d-vms/multivmgroup.png)

2. Passare agli elementi replicati per visualizzare lo stato di entrambe le macchine virtuali. 
3. Entrambe le macchine virtuali sono protette e vengono anche visualizzate come parte di un gruppo di coerenza tra più macchine virtuali.

   ![Protezione di Spazi di archiviazione diretta](./media/azure-to-azure-how-to-enable-replication-s2d-vms/storagespacesdirectgroup.PNG)

## <a name="creating-a-recovery-plan"></a>Creazione di un piano di ripristino
Un piano di ripristino supporta la sequenziazione di vari livelli in un'applicazione multilivello durante un failover. La sequenziazione aiuta a mantenere la coerenza delle applicazioni. Quando si crea un piano di ripristino per un'applicazione Web multilivello, completare la procedura descritta in [Creare un piano di ripristino con Site Recovery](site-recovery-create-recovery-plans.md).

### <a name="adding-virtual-machines-to-failover-groups"></a>Aggiunta di macchine virtuali a gruppi di failover

1.  Creare un piano di ripristino aggiungendo le macchine virtuali.
2.  Fare clic su "Personalizza" per raggruppare le VM. Per impostazione predefinita, tutte le VM fanno parte di "Gruppo 1".


### <a name="add-scripts-to-the-recovery-plan"></a>Aggiungere script al piano di ripristino
Per far sì che le applicazioni funzionino correttamente, potrebbe essere necessario eseguire alcune operazioni nelle macchine virtuali di Azure dopo il failover o durante un failover di test. È possibile automatizzare alcune operazioni successive al failover. Ad esempio, qui viene collegato un servizio di bilanciamento del carico e modificato l'IP del cluster.


### <a name="failover-of-the-virtual-machines"></a>Failover delle macchine virtuali 
Entrambi i nodi delle macchine virtuali devono essere sottoposti a failover usando il [piano di ripristino ASR](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans) 

![Protezione di Spazi di archiviazione diretta](./media/azure-to-azure-how-to-enable-replication-s2d-vms/recoveryplan.PNG)

## <a name="run-a-test-failover"></a>Eseguire un failover di test
1.  Nel portale di Azure selezionare l'insieme di credenziali di Servizi di ripristino.
2.  Selezionare il piano di ripristino creato.
3.  Selezionare **Failover di test**.
4.  Per avviare il processo di failover di test, selezionare il punto di recupero e la rete virtuale di Azure.
5.  Quando l'ambiente secondario diventa disponibile, eseguire le convalide.
6.  Al termine delle convalide, per pulire l'ambiente di failover di test, selezionare **Pulisci failover di test**.

Per altre informazioni, vedere [Failover di test in Azure in Site Recovery](site-recovery-test-failover-to-azure.md).

## <a name="run-a-failover"></a>Eseguire un failover

1.  Nel portale di Azure selezionare l'insieme di credenziali di Servizi di ripristino.
2.  Selezionare il piano di ripristino creato per le applicazioni SAP.
3.  Selezionare **Failover**.
4.  Per avviare il processo di failover, selezionare il punto di recupero.

Per altre informazioni, vedere [Failover in Site Recovery](site-recovery-failover.md).
## <a name="next-steps"></a>Passaggi successivi

[Altre informazioni](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-failover-failback) sull'esecuzione dei failback.
