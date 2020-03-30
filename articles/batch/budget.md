---
title: Analisi dei costi e budget - Azure Batch
description: Informazioni su come ottenere un'analisi dei costi e impostare un budget per le risorse di calcolo sottostanti e le licenze software usate per eseguire i carichi di lavoro Batch.Learn how to get a cost analysis and set a budget for the underlying compute resources and software licenses used to run your Batch workloads.
services: batch
author: LauraBrenner
manager: evansma
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 07/19/2019
ms.author: labrenne
ms.openlocfilehash: 819b5e16f4730e9a1998234288e181772f7c1996
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022716"
---
# <a name="cost-analysis-and-budgets-for-azure-batch"></a>Analisi dei costi e budget per Azure Batch

Non è previsto alcun costo per Azure Batch, ma solo le risorse di calcolo sottostanti e le licenze software usate per eseguire i carichi di lavoro Batch.There's no charge for Azure Batch itself, only the underlying compute resources and software licenses used to run Batch workloads. A livello elevato, i costi vengono sostenuti dalle macchine virtuali (VM) in un pool, dal trasferimento di dati dalla macchina virtuale o da qualsiasi dato di input o output archiviato nel cloud. Diamo un'occhiata ad alcuni componenti chiave di Batch per capire da dove provengono i costi, come impostare un budget per un pool o un account e alcune tecniche per rendere i carichi di lavoro Batch più efficienti in termini di costi.

## <a name="batch-resources"></a>Risorse di batch

Le macchine virtuali sono la risorsa più significativa utilizzata per l'elaborazione batch. Il costo dell'utilizzo delle macchine virtuali per Batch viene calcolato in base al tipo, alla quantità e alla durata di utilizzo. Le opzioni di fatturazione della macchina virtuale includono con pagamento in base al [numero di ot-a-go](https://azure.microsoft.com/offers/ms-azr-0003p/) o [la prenotazione](../cost-management-billing/reservations/save-compute-costs-reservations.md) (pagamento in anticipo). Entrambe le opzioni di pagamento hanno vantaggi diversi a seconda del carico di lavoro di calcolo ed entrambi i modelli di pagamento influiranno sulla fattura in modo diverso.

Quando le applicazioni vengono distribuite nei nodi Batch (VM) usando i [pacchetti dell'applicazione,](batch-application-packages.md)vengono fatturate le risorse di Archiviazione di Azure utilizzate dai pacchetti dell'applicazione. Viene inoltre fatturata l'archiviazione di qualsiasi file di input o di output, ad esempio file di risorse e altri dati di log. In generale, il costo dei dati di archiviazione associati a Batch è molto inferiore al costo delle risorse di calcolo. A ogni macchina virtuale in un pool creato con VirtualMachineConfiguration è associato un disco del sistema operativo che usa dischi gestiti da Azure.Each VM in a pool created with **VirtualMachineConfiguration** has an associated OS disk that uses Azure-managed disks. I dischi gestiti da Azure hanno un costo aggiuntivo e anche altri livelli di prestazioni del disco hanno costi diversi.

I pool batch utilizzano risorse di rete. In particolare, per i pool **VirtualMachineConfiguration** vengono utilizzati servizi di bilanciamento del carico standard che richiedono indirizzi IP statici. I servizi di bilanciamento del carico usati da Batch sono visibili per gli account **di sottoscrizione utente,** ma non per gli account **del servizio batch.** I servizi di bilanciamento del carico standard comportano costi per tutti i dati passati da e verso le macchine virtuali del pool Batch. Selezionare API batch che recuperano i dati dai nodi del pool (ad esempio Ottieni file attività/nodo), pacchetti di applicazioni di attività, file di risorse/output e immagini del contenitore incorrere in costi aggiuntivi.

### <a name="additional-services"></a>Servizi aggiuntivi

I servizi che non includono macchine virtuali e archiviazione possono tener conto del costo dell'account Batch.

Altri servizi comunemente utilizzati con Batch possono includere:Other services commonly used with Batch can include:

- Application Insights
- Data Factory
- Monitoraggio di Azure
- Rete virtuale
- Macchine virtuali con applicazioni grafiche

A seconda dei servizi utilizzati con la soluzione Batch, è possibile che siano costi aggiuntivi. Fare riferimento al [Calcolatore dei prezzi](https://azure.microsoft.com/pricing/calculator/) per determinare il costo di ogni servizio aggiuntivo.

## <a name="cost-analysis-and-budget-for-a-pool"></a>Analisi dei costi e budget per un pool

Tramite il portale di Azure è possibile creare budget e avvisi di spesa per i pool Batch o l'account Batch.Through the Azure portal, you can create budgets and spending alerts for your Batch pool(s) or Batch account. I budget e gli avvisi sono utili per notificare alle parti interessate eventuali rischi di spese eccessive. È possibile che si tratti di un ritardo negli avvisi di spesa e di superare leggermente un budget. In questo esempio verrà visualizzata l'analisi dei costi di un singolo pool Batch.

1. Nel portale di Azure selezionare **Gestione costi e fatturazione** nella barra di spostamento sinistra.
1. Selezionare l'abbonamento nella sezione **I miei abbonamenti**
1. Passare a **Analisi dei costi** nella sezione Gestione **costi** della barra di spostamento sinistra, che mostrerà una visualizzazione simile alla seguente:
1. Selezionare **Aggiungi filtro**. Nel primo elenco a discesa selezionare **Risorsa** ![Selezionare il filtro risorse](./media/batch-budget/resource-filter.png)
1. Nel secondo elenco a discesa selezionare Pool batch. Quando il pool è selezionato, l'analisi dei costi sarà simile all'analisi seguente.
    ![Analisi dei costi di un pool](./media/batch-budget/pool-cost-analysis.png)

L'analisi dei costi risultante mostra il costo del pool e le risorse che contribuiscono a questo costo. In questo esempio, le macchine virtuali usate nel pool sono la risorsa più costosa.

Per creare un budget per il pool, selezionare **Budget: nessuno**, quindi selezionare **Crea nuovo budget >**. Ora utilizzare la finestra per configurare un budget specifico per il pool.

Per altre informazioni sulla configurazione di un budget, vedere Creare e gestire budget di Azure.For more information on configuring a budget, see [Create and manage Azure budgets](../cost-management-billing/costs/tutorial-acm-create-budgets.md).

> [!NOTE]
> Azure Batch è basato sulla tecnologia di Servizi cloud di Azure e di Macchine virtuali di Azure. Quando si sceglie la **configurazione dei servizi cloud**, gli addebiti sono basati sulla struttura dei prezzi di Servizi cloud. Quando si sceglie la **configurazione della macchina virtuale**, gli addebiti sono basati sulla struttura di prezzi di Macchine virtuali. Nell'esempio riportato in questa pagina viene utilizzata la configurazione della **macchina virtuale**.

## <a name="minimize-cost"></a>Riduci al minimo i costi

L'uso di più macchine virtuali e servizi di Azure per lunghi periodi di tempo può essere costoso. Fortunatamente, sono disponibili servizi che consentono di ridurre la spesa e strategie per massimizzare l'efficienza del carico di lavoro.

### <a name="low-priority-virtual-machines"></a>Macchine virtuali con priorità bassa

Le macchine virtuali con priorità bassa riducono il costo dei carichi di lavoro Batch sfruttando la capacità di elaborazione in eccesso in Azure.Low-priority VMs reduce the cost of Batch workloads by taking advantage of surplus computing capacity in Azure. Quando si specificano macchine virtuali con priorità bassa nei pool, Batch usa questo surplus per eseguire il carico di lavoro. È un notevole risparmio sui costi tramite macchine virtuali con priorità bassa al posto delle macchine virtuali dedicate.

Per altre informazioni su come configurare le macchine virtuali con priorità bassa per il carico di lavoro, vedere Usare macchine virtuali con priorità bassa con Batch.Learn more about how to set up low-priority VMs for your workload at [Use low-priority VMs with Batch](batch-low-pri-vms.md).

### <a name="virtual-machine-os-disk-type"></a>Tipo di disco del sistema operativo della macchina virtualeVirtual machine OS disk type

Esistono più [tipi di disco del sistema operativo VM.](../virtual-machines/windows/disks-types.md) La maggior parte delle serie VM hanno dimensioni che supportano sia lo storage premium che quello standard. Quando viene selezionata una dimensione di macchina virtuale 's' per un pool, Batch configura dischi OS SSD premium. Quando viene selezionata la dimensione della macchina virtuale 'non-s', viene utilizzato il tipo di disco rigido standard più economico. Ad esempio, vengono utilizzati dischi OS SSD premium e `Standard_D2s_v3` dischi OS HDD standard per `Standard_D2_v3`.

I dischi OS Premium SSD sono più costosi, ma hanno prestazioni più elevate e le macchine virtuali con dischi premium possono essere avviate leggermente più rapidamente rispetto alle macchine virtuali con dischi ossia HDD standard. Con Batch, il disco del sistema operativo spesso non viene utilizzato molto in quanto le applicazioni e i file di attività si trovano sul disco SSD temporaneo delle macchine virtuali. Pertanto, in molti casi, non è necessario pagare il costo aumentato per l'SSD premium di cui viene eseguito il provisioning quando viene specificata una dimensione della macchina virtuale 's'.

### <a name="reserved-virtual-machine-instances"></a>Istanze di macchine virtuali riservate

Se si intende usare Batch per un lungo periodo di tempo, è possibile risparmiare sul costo delle macchine virtuali usando [Prenotazioni](../cost-management-billing/reservations/save-compute-costs-reservations.md) di Azure per i carichi di lavoro. Un tasso di prenotazione è notevolmente inferiore a quello di una tariffa pay-as-you-go. Le istanze di macchina virtuale utilizzate senza una prenotazione vengono addebitate alla tariffa con pagamento in base al numero di server. Se acquisti una prenotazione, viene applicato lo sconto sulla prenotazione e non ti viene più addebitato il pagamento alle tariffe con pagamento in base al tuo trasferimento.

### <a name="automatic-scaling"></a>Scalabilità automatica

[La scalabilità automatica](batch-automatic-scaling.md) scala dinamicamente il numero di macchine virtuali nel pool Batch in base alle richieste del processo corrente. Ridimensionando il pool in base alla durata di un processo, la scalabilità automatica garantisce che le macchine virtuali siano aumentate e usate solo quando è presente un processo da eseguire. Quando il processo è completo o non sono presenti processi, le macchine virtuali vengono ridimensionate automaticamente per salvare le risorse di calcolo. La scalabilità consente di ridurre il costo complessivo della soluzione Batch usando solo le risorse necessarie.

Per altre informazioni sul ridimensionamento automatico, vedere Ridimensionare automaticamente i nodi di calcolo in un pool Batch di Azure.For more information about automatic scaling, see [Automatically scale compute nodes in an Azure Batch pool.](batch-automatic-scaling.md)

## <a name="next-steps"></a>Passaggi successivi

- Ulteriori informazioni sulle API Batch e sugli strumenti disponibili per la creazione e il monitoraggio delle soluzioni Batch.Learn more about the [Batch APIs and tools](batch-apis-tools.md) available for building and monitoring Batch solutions.  

- Informazioni sulle [macchine virtuali con priorità bassa con Batch](batch-low-pri-vms.md).
