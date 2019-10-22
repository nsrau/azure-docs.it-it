---
title: Analisi dei costi e budget-Azure Batch
description: Informazioni su come ottenere un'analisi dei costi e impostare un budget per il carico di lavoro batch.
services: batch
author: laurenhughes
manager: gwallace
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 07/19/2019
ms.author: lahugh
ms.openlocfilehash: 6ccf530fe2164b3d9b1936648ffe9057c334efd6
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "70094214"
---
# <a name="cost-analysis-and-budgets-for-azure-batch"></a>Analisi dei costi e budget per Azure Batch

Non sono previsti addebiti per Azure Batch, ma solo le risorse di calcolo sottostanti e le licenze software usate per eseguire i carichi di lavoro di batch. A un livello elevato, i costi sono sostenuti dalle macchine virtuali (VM) in un pool, dal trasferimento dei dati dalla macchina virtuale o da eventuali dati di input o output archiviati nel cloud. Verranno ora esaminati alcuni componenti chiave di batch per comprendere la provenienza dei costi, come impostare un budget per un pool o un account e alcune tecniche per rendere i carichi di lavoro di batch più convenienti.

## <a name="batch-resources"></a>Risorse batch

Le macchine virtuali sono la risorsa più significativa utilizzata per l'elaborazione batch. Il costo di utilizzo delle macchine virtuali per batch viene calcolato in base al tipo, alla quantità e alla durata dell'utilizzo. Le opzioni di fatturazione della VM includono [pagamento in base](https://azure.microsoft.com/offers/ms-azr-0003p/) al consumo o [prenotazione](../billing/billing-save-compute-costs-reservations.md) (pagamento anticipato). Entrambe le opzioni di pagamento hanno vantaggi diversi a seconda del carico di lavoro di calcolo, mentre entrambi i modelli di pagamento avranno effetto sulla fattura in modo diverso.

Quando le applicazioni vengono distribuite in nodi di batch (VM) usando i [pacchetti dell'applicazione](batch-application-packages.md), vengono fatturate le risorse di archiviazione di Azure utilizzate dai pacchetti dell'applicazione. Viene addebitata anche l'archiviazione di tutti i file di input o di output, ad esempio file di risorse e altri dati di log. In generale, il costo dei dati di archiviazione associati a batch è molto inferiore al costo delle risorse di calcolo. Ogni macchina virtuale in un pool creato con **VirtualMachineConfiguration** dispone di un disco del sistema operativo associato che usa dischi gestiti di Azure. I dischi gestiti di Azure presentano costi aggiuntivi e anche altri livelli di prestazioni del disco hanno costi diversi.

I pool di batch usano le risorse di rete. In particolare, per i pool di **VirtualMachineConfiguration** vengono usati i bilanciamento del carico standard, che richiedono indirizzi IP statici. I servizi di bilanciamento del carico usati da batch sono visibili per gli account di **sottoscrizione utente** , ma non sono visibili per gli account del **servizio batch** . I bilanciamento del carico standard comportano addebiti per tutti i dati passati da e verso le VM del pool di batch; Selezionare le API batch che recuperano i dati dai nodi del pool (ad esempio Ottieni attività/file nodo), i pacchetti dell'applicazione di attività, i file di risorse/output e le immagini del contenitore comporteranno addebiti.

### <a name="additional-services"></a>Servizi aggiuntivi

I servizi che non includono macchine virtuali e spazio di archiviazione possono rappresentare il costo dell'account batch.

Altri servizi comunemente usati con batch possono includere:

- Application Insights
- Data Factory
- Monitoraggio di Azure
- Rete virtuale
- Macchine virtuali con applicazioni grafiche

A seconda dei servizi usati con la soluzione batch, è possibile che vengano addebitati costi aggiuntivi. Fare riferimento al [calcolatore dei prezzi](https://azure.microsoft.com/pricing/calculator/) per determinare il costo di ogni servizio aggiuntivo.

## <a name="cost-analysis-and-budget-for-a-pool"></a>Analisi dei costi e budget per un pool

Grazie alla portale di Azure, è possibile creare budget e avvisi di spesa per i pool di batch o l'account batch. I budget e gli avvisi sono utili per notificare agli stakeholder i rischi di overspending. È possibile che si verifichi un ritardo negli avvisi di spesa e che venga leggermente superato un budget. In questo esempio viene illustrata l'analisi dei costi di un singolo pool di batch.

1. Nella portale di Azure selezionare **Gestione costi e fatturazione** dalla barra di spostamento a sinistra.
1. Selezionare la sottoscrizione nella sezione **sottoscrizioni personali**
1. Passare a **analisi costi** nella sezione **Gestione costi** della barra di spostamento a sinistra, in cui verrà visualizzata una visualizzazione simile alla seguente:
1. Selezionare **Aggiungi filtro**. Nel primo elenco a discesa selezionare **resource** ![Select il filtro delle risorse ](./media/batch-budget/resource-filter.png)
1. Nel secondo elenco a discesa selezionare il pool di batch. Quando il pool è selezionato, l'analisi dei costi sarà simile a quella riportata di seguito.
    ![Cost l'analisi di un pool ](./media/batch-budget/pool-cost-analysis.png)

L'analisi dei costi risultante Mostra il costo del pool e le risorse che contribuiscono a questo costo. In questo esempio, le macchine virtuali usate nel pool sono la risorsa più costosa.

Per creare un budget per il pool, selezionare **budget: nessuno**, quindi selezionare **crea nuovo budget >** . Usare ora la finestra per configurare un budget specifico per il pool.

Per altre informazioni sulla configurazione di un budget, vedere [creare e gestire i budget di Azure](../cost-management/tutorial-acm-create-budgets.md).

> [!NOTE]
> Azure Batch è basato sulla tecnologia di Servizi cloud di Azure e di Macchine virtuali di Azure. Quando si sceglie la **configurazione di servizi cloud**, l'addebito viene addebitato in base alla struttura dei prezzi dei servizi cloud. Quando si sceglie **configurazione macchina virtuale**, l'addebito viene addebitato in base alla struttura dei prezzi delle macchine virtuali. Nell'esempio riportato in questa pagina viene utilizzata la **configurazione della macchina virtuale**.

## <a name="minimize-cost"></a>Ridurre i costi

L'uso di più macchine virtuali e servizi di Azure per periodi prolungati di tempo può essere costoso. Fortunatamente, sono disponibili servizi che consentono di ridurre la spesa, nonché strategie per massimizzare l'efficienza del carico di lavoro.

### <a name="low-priority-virtual-machines"></a>Macchine virtuali con priorità bassa

Le macchine virtuali con priorità bassa riducono il costo dei carichi di lavoro di batch sfruttando la capacità di calcolo in eccesso in Azure. Quando si specificano le macchine virtuali con priorità bassa nei pool, batch usa questa eccedenza per eseguire il carico di lavoro. Si verifica un notevole risparmio di costi usando macchine virtuali con priorità bassa al posto di VM dedicate.

Per altre informazioni su come configurare le macchine virtuali con priorità bassa per il carico di lavoro, vedere [usare macchine virtuali con priorità bassa con batch](batch-low-pri-vms.md).

### <a name="virtual-machine-os-disk-type"></a>Tipo di disco del sistema operativo della macchina virtuale

Sono disponibili più [tipi di dischi del sistema operativo VM](../virtual-machines/windows/disks-types.md). Per la maggior parte delle serie VM sono disponibili dimensioni che supportano archiviazione standard e Premium. Quando si selezionano le dimensioni della macchina virtuale per un pool, batch configura i dischi del sistema operativo SSD Premium. Quando viene selezionata la dimensione della macchina virtuale "non s", viene usato il tipo di disco HDD standard più economico. Ad esempio, i dischi del sistema operativo SSD Premium vengono usati per la `Standard_D2s_v3` e i dischi del sistema operativo HDD standard vengono usati per `Standard_D2_v3`.

SSD Premium dischi del sistema operativo sono più costosi, ma hanno prestazioni migliori e le macchine virtuali con dischi Premium possono iniziare leggermente più rapidamente rispetto alle VM con dischi del sistema operativo HDD standard. Con batch, il disco del sistema operativo spesso non viene usato molto perché le applicazioni e i file delle attività si trovano nel disco SSD temporaneo delle macchine virtuali. Pertanto, in molti casi, non è necessario pagare il costo maggiore per l'unità SSD Premium di cui viene effettuato il provisioning quando si specifica la dimensione della VM a.

### <a name="reserved-virtual-machine-instances"></a>Istanze di macchine virtuali riservate

Se si prevede di usare batch per un lungo periodo di tempo, è possibile risparmiare sui costi delle macchine virtuali usando le [prenotazioni di Azure](../billing/billing-save-compute-costs-reservations.md) per i carichi di lavoro. Una tariffa di prenotazione è notevolmente inferiore rispetto a una tariffa con pagamento in base al consumo. Per le istanze di macchine virtuali usate senza prenotazione viene addebitata una tariffa con pagamento in base al consumo. Se si acquista una prenotazione, viene applicato lo sconto di prenotazione e non vengono più addebitate le tariffe con pagamento in base al consumo.

### <a name="automatic-scaling"></a>Ridimensionamento automatico

Il [ridimensionamento automatico ridimensiona](batch-automatic-scaling.md) in modo dinamico il numero di macchine virtuali nel pool di batch in base alle esigenze del processo corrente. Ridimensionando il pool in base alla durata di un processo, il ridimensionamento automatico garantisce che le macchine virtuali vengano aumentate e usate solo quando è presente un processo da eseguire. Al termine del processo o se non sono presenti processi, le macchine virtuali vengono automaticamente ridimensionate per ridurre le risorse di calcolo. La scalabilità consente di ridurre il costo complessivo della soluzione batch usando solo le risorse necessarie.

Per altre informazioni sul ridimensionamento automatico, vedere [ridimensionare automaticamente i nodi di calcolo in un pool di Azure batch](batch-automatic-scaling.md).

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sulle [API e gli strumenti di batch](batch-apis-tools.md) disponibili per la compilazione e il monitoraggio di soluzioni batch.  

- Informazioni sulle [macchine virtuali con priorità bassa con batch](batch-low-pri-vms.md).
