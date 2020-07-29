---
title: Analisi dei costi e budget
description: Informazioni su come ottenere un'analisi dei costi e impostare un budget per le risorse di calcolo sottostanti e le licenze software usate per eseguire i carichi di lavoro di Batch.
ms.topic: how-to
ms.date: 07/19/2019
ms.openlocfilehash: 13c8cc508a4940b5e21570104527c40988879919
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83725772"
---
# <a name="cost-analysis-and-budgets-for-azure-batch"></a>Analisi dei costi e budget per Azure Batch

Non sono previsti addebiti per Azure Batch, ma solo per le risorse di calcolo sottostanti e le licenze software usate per eseguire i carichi di lavoro di Batch. A un livello elevato, i costi addebitati dipenderanno dalle macchine virtuali (VM) in un pool, dal trasferimento dei dati dalla macchina virtuale o da eventuali dati di input o output archiviati nel cloud. Verranno ora esaminati alcuni componenti chiave di Batch per comprendere la provenienza dei costi, come impostare un budget per un pool o un account e per scoprire alcune tecniche per rendere i carichi di lavoro di Batch più convenienti.

## <a name="batch-resources"></a>Risorse di Batch

Le macchine virtuali sono la risorsa più significativa usata per l'elaborazione batch. Il costo di uso delle macchine virtuali per Batch viene calcolato in base al tipo, alla quantità e alla durata dell'uso. Le opzioni di fatturazione delle macchine virtuali includono il [pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0003p/) o la [prenotazione](../cost-management-billing/reservations/save-compute-costs-reservations.md) (pagamento anticipato). Entrambe le opzioni di pagamento hanno vantaggi diversi a seconda del carico di lavoro di calcolo, mentre entrambi i modelli di pagamento influiranno sulla fattura in modo diverso.

Quando si distribuiscono applicazioni ai nodi Batch (VM) tramite [pacchetti dell'applicazione](batch-application-packages.md), vengono inoltre addebitati i costi per le risorse di Archiviazione di Azure usate dai pacchetti dell'applicazione. Viene addebitata anche l'archiviazione di tutti i file di input o di output, ad esempio file di risorse e altri dati di log. In generale, il costo dei dati di archiviazione associati a Batch è molto inferiore al costo delle risorse di calcolo. Ogni macchina virtuale in un pool creato con **VirtualMachineConfiguration** dispone di un disco del sistema operativo associato che usa dischi gestiti di Azure. I dischi gestiti di Azure presentano costi aggiuntivi e anche altri livelli di prestazioni del disco hanno costi diversi.

I pool di Batch usano le risorse di rete. In particolare, per i pool **VirtualMachineConfiguration** vengono usati servizi di bilanciamento del carico standard, che richiedono indirizzi IP statici. I servizi di bilanciamento del carico usati da Batch sono visibili per gli account di **sottoscrizione utente**, ma non sono visibili per account del **servizio Batch**. I servizi di bilanciamento del carico standard comportano addebiti per tutti i dati trasmessi da e verso le macchine virtuali del pool di Batch; le API Batch che recuperano i dati dai nodi del pool (ad esempio Get Task/Node File), i pacchetti dell'applicazione di attività, i file di risorse/output e le immagini del contenitore comporteranno addebiti.

### <a name="additional-services"></a>Servizi aggiuntivi

I servizi che non includono macchine virtuali e spazio di archiviazione possono essere specificati nel costo dell'account Batch.

Altri servizi comunemente usati con Batch possono includere:

- Application Insights
- Data Factory
- Monitoraggio di Azure
- Rete virtuale
- Macchine virtuali con applicazioni grafiche

A seconda dei servizi usati con la soluzione Batch, è possibile che vengano addebitati costi aggiuntivi. Per determinare il costo di ogni servizio aggiuntivo, fare riferimento al [Calcolatore dei prezzi](https://azure.microsoft.com/pricing/calculator/).

## <a name="cost-analysis-and-budget-for-a-pool"></a>Analisi dei costi e budget per un pool

Grazie alla portale di Azure, è possibile creare budget e avvisi di spesa per i pool di Batch o l'account Batch. I budget e gli avvisi sono utili per notificare agli stakeholder eventuali rischi di overspending. È possibile che si verifichi un ritardo negli avvisi di spesa e che venga leggermente superato un budget. In questo esempio viene illustrata l'analisi dei costi di un singolo pool di Batch.

1. Nel portale di Azure selezionare **Gestione costi + Fatturazione** dalla barra di spostamento sinistra.
1. Selezionare la sottoscrizione nella sezione **Sottoscrizioni personali**.
1. Andare a **Analisi dei costi** nella sezione **Gestione costi** della barra di spostamento a sinistra, in cui sarà disponibile una vista simile alla seguente:
1. Selezionare **Aggiungi filtro**. Nel primo elenco a discesa selezionare **Risorsa** ![Selezionare il filtro di risorse](./media/batch-budget/resource-filter.png)
1. Nel secondo elenco a discesa selezionare il pool di Batch. Una volta selezionato il pool, l'analisi dei costi sarà simile a quella riportata di seguito.
    ![Analisi dei costi di un pool](./media/batch-budget/pool-cost-analysis.png)

L'analisi dei costi risultante mostra il costo del pool, nonché le risorse che contribuiscono a questo costo. In questo esempio, le macchine virtuali usate nel pool sono la risorsa più costosa.

Per creare un budget per il pool, selezionare **Budget: None** (Budget: nessuno), quindi selezionare **Crea un nuovo budget >** . Usare la finestra per configurare un budget specifico per il pool.

Per altre informazioni sulla configurazione di un budget, vedere la sezione [Creazione e gestione dei budget di Azure](../cost-management-billing/costs/tutorial-acm-create-budgets.md).

> [!NOTE]
> Azure Batch è basato sulla tecnologia di Servizi cloud di Azure e di Macchine virtuali di Azure. Scegliendo la **configurazione di Servizi cloud**, i costi verranno addebitati in base alla struttura dei prezzi di Servizi cloud. Scegliendo la **configurazione della macchina virtuale**, i costi verranno addebitati in base alla struttura dei prezzi di Macchine virtuali. Nell'esempio riportato in questa pagina viene usata la **configurazione della macchina virtuale**.

## <a name="minimize-cost"></a>Ridurre i costi

L'uso di più macchine virtuali e servizi di Azure per periodi prolungati di tempo può essere costoso. Fortunatamente, sono disponibili servizi che consentono di ridurre la spesa, nonché strategie per massimizzare l'efficienza del carico di lavoro.

### <a name="low-priority-virtual-machines"></a>Macchine virtuali con priorità bassa

Le macchine virtuali con priorità bassa riducono il costo dei carichi di lavoro di Batch sfruttando la capacità di calcolo in eccesso in Azure. Quando si specificano le macchine virtuali con priorità bassa nei pool, Batch può usare questa capacità in eccesso per eseguire il carico di lavoro. Si verifica un notevole risparmio di costi usando macchine virtuali con priorità bassa al posto di macchine virtuali dedicate.

Per altre informazioni su come configurare le macchine virtuali con priorità bassa per il carico di lavoro, vedere [Usare macchine virtuali con priorità bassa con Batch](batch-low-pri-vms.md).

### <a name="virtual-machine-os-disk-type"></a>Tipo di disco del sistema operativo per macchina virtuale

Sono presenti più [tipi di disco del sistema operativo per macchina virtuale](../virtual-machines/windows/disks-types.md). La maggior parte delle serie di macchine virtuali presentano dimensioni che supportano sia l'archiviazione standard che l'archiviazione premium. Quando si seleziona la dimensione della macchina virtuale "s" per un pool, Batch configura i dischi del sistema operativo SSD Premium. Quando si seleziona la dimensione della macchina virtuale "non-s", viene usato il tipo di disco HDD standard più economico. Ad esempio, i dischi del sistema operativo SSD Premium vengono usati per `Standard_D2s_v3`, mentre i dischi del sistema operativo HDD standard vengono usati per `Standard_D2_v3`.

I dischi del sistema operativo SSD Premium sono più costosi, ma offrono prestazioni migliori e le macchine virtuali con dischi Premium possono iniziare in modo leggermente più rapido rispetto alle macchine virtuali con dischi del sistema operativo HDD standard. Con Batch, il disco del sistema operativo spesso non viene usato molto perché le applicazioni e i file delle attività si trovano nel disco SSD temporaneo delle macchine virtuali. Pertanto, in molti casi, non è necessario pagare il costo maggiore per l'unità SSD Premium di cui viene effettuato il provisioning quando si specifica la dimensione della macchina virtuale "s".

### <a name="reserved-virtual-machine-instances"></a>Istanze di macchine virtuale riservate

Se si prevede di usare Batch per un lungo periodo di tempo, è possibile risparmiare sui costi delle macchine virtuali usando le [prenotazioni di Azure](../cost-management-billing/reservations/save-compute-costs-reservations.md) per i carichi di lavoro. Una tariffa di prenotazione è notevolmente inferiore rispetto a una tariffa con pagamento in base al consumo. Per le istanze di macchine virtuali usate senza prenotazione viene addebitata una tariffa con pagamento in base al consumo. Se si acquista una prenotazione, viene applicato lo sconto di prenotazione e non vengono più addebitate le tariffe con pagamento in base al consumo.

### <a name="automatic-scaling"></a>Ridimensionamento automatico

Il [ridimensionamento automatico](batch-automatic-scaling.md) ridimensiona in modo dinamico il numero di macchine virtuali nel pool di Batch in base alle esigenze del processo corrente. Ridimensionando il pool in base alla durata di un processo, il ridimensionamento automatico garantisce che le macchine virtuali vengano aumentate e usate solo quando è presente un processo da eseguire. Al termine del processo o se non sono presenti processi, le macchine virtuali vengono automaticamente ridimensionate per ridurre le risorse di calcolo. Il ridimensionamento consente di ridurre il costo complessivo della soluzione Batch usando solo le risorse necessarie.

Per altre informazioni sul ridimensionamento automatico di un'applicazione, vedere [Ridimensionare automaticamente i nodi di calcolo in un pool di Azure Batch](batch-automatic-scaling.md).

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sulle [API e gli strumenti di Batch](batch-apis-tools.md) disponibili per la compilazione e il monitoraggio di soluzioni Batch.  

- Informazioni sulle [macchine virtuali con priorità bassa in Batch](batch-low-pri-vms.md).
