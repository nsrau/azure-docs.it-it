---
title: File di inclusione
description: File di inclusione
services: virtual-machines-windows, azure-resource-manager
author: genlin
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 04/14/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: 136c7e497b24db99b230884514d4a286bbeb64be
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58114856"
---
Quando si crea una macchina virtuale (VM), si riavviano VM arrestate (deallocate) o si ridimensiona una VM, Microsoft Azure alloca risorse di calcolo alla sottoscrizione. Microsoft continua a investire in funzionalità e infrastrutture aggiuntive per garantire che tutti i tipi di VM supportino le richieste dei clienti. In alcuni casi possono, tuttavia, verificarsi errori di allocazione delle risorse a causa di un aumento senza precedenti delle richieste di servizi di Azure in aree specifiche. Questo problema può verificarsi quando si tenta di creare o avviare le VM in un'area e le VM visualizzano il codice di errore e il messaggio seguenti:

**Codice errore**: AllocationFailed o ZonalAllocationFailed

**Messaggio di errore**: "Allocazione non riuscita. Non è disponibile una capacità sufficiente per la dimensione di VM richiesta in quest'area. Altre informazioni su come aumentare la possibilità di eseguire correttamente l'allocazione sono disponibili in http://aka.ms/allocation-guidance"

Questo articolo illustra le cause di alcuni dei più comuni errori di allocazione e suggerisce possibili soluzioni alternative.

Se il problema di Azure non viene risolto in questo articolo, visitare il [forum di Azure su MSDN e Overflow dello Stack](https://azure.microsoft.com/support/forums/). È possibile pubblicare il problema in questi forum o in @AzureSupport su Twitter. È anche possibile inviare una richiesta di supporto tecnico di Azure selezionando Ottieni supporto nel sito del [supporto tecnico di Azure](https://azure.microsoft.com/support/options/).

Fino a quando il tipo preferito di VM è disponibile nella propria area preferita, è consigliabile che i clienti che hanno problemi di distribuzione tengano in considerazione le indicazioni nella tabella seguente come soluzione alternativa temporanea. 

Identificare lo scenario più adatto al caso e quindi ripetere la richiesta di allocazione usando la soluzione alternativa suggerita corrispondente per aumentare la possibilità di eseguire correttamente l'allocazione. In alternativa, è sempre possibile riprovare in un secondo momento. Questo avviene perché nel cluster, nell'area o nella zona è stato liberato un numero di risorse sufficiente a soddisfare la richiesta. 


## <a name="resize-a-vm-or-add-vms-to-an-existing-availability-set"></a>Ridimensionare una VM o aggiungere VM a un set di disponibilità esistente

### <a name="cause"></a>Causa

La richiesta di ridimensionamento di una VM o di aggiunta di una VM a un set di disponibilità esistente deve essere eseguita nel cluster originale che ospita il set di disponibilità esistente. La dimensione di VM richiesta è supportata dal cluster, ma è possibile che il cluster non disponga di una capacità sufficiente. 

### <a name="workaround"></a>Soluzione alternativa

Se la VM può far parte di un set di disponibilità diverso, creare una VM in un altro set di disponibilità (nella stessa area). La nuova VM può quindi essere aggiunta alla stessa rete virtuale.

Arrestare (deallocare) tutte le VM nello stesso set di disponibilità, quindi riavviarle tutte.
Per arrestare: Fare clic su Gruppi di risorse > [il proprio gruppo di risorse] > Risorse > [il proprio set di disponibilità] > Macchine virtuali > [la propria macchina virtuale] > Arresta.
Dopo l'arresto di tutte le VM, selezionare la prima e fare clic su Avvia.
Questo passaggio garantisce che venga eseguito un nuovo tentativo di allocazione e che si possa selezionare un nuovo cluster con capacità sufficiente.

## <a name="restart-partially-stopped-deallocated-vms"></a>Riavviare VM arrestate (deallocate) parzialmente

### <a name="cause"></a>Causa

La deallocazione parziale significa che una o più VM in un set di disponibilità sono state arrestate (deallocate), ma non tutte. Quando si dealloca una VM, vengono rilasciate le risorse associate. Riavviare le VM in un set di disponibilità parzialmente deallocato equivale ad aggiungere VM a un set di disponibilità esistente. Pertanto, è necessario tentare la richiesta di allocazione al cluster originale che ospita il set di disponibilità esistente che può non avere una capacità sufficiente.

### <a name="workaround"></a>Soluzione alternativa

Arrestare (deallocare) tutte le VM nello stesso set di disponibilità, quindi riavviarle tutte.
Per arrestare: Fare clic su Gruppi di risorse > [il proprio gruppo di risorse] > Risorse > [il proprio set di disponibilità] > Macchine virtuali > [la propria macchina virtuale] > Arresta.
Dopo l'arresto di tutte le VM, selezionare la prima e fare clic su Avvia.
Questo passaggio garantisce che venga eseguito un nuovo tentativo di allocazione e che si possa selezionare un nuovo cluster con capacità sufficiente.

## <a name="restart-fully-stopped-deallocated-vms"></a>Riavviare VM arrestate (deallocate) completamente

### <a name="cause"></a>Causa

La deallocazione completa significa che sono state arrestate (deallocate) tutte le VM in un set di disponibilità. La richiesta di allocazione per il riavvio di queste VM viene eseguita in tutti i cluster che supportano le dimensioni desiderate nell'ambito dell'area o della zona. Modificare la richiesta di allocazione in base ai suggerimenti in questo articolo, quindi ripetere la richiesta per aumentare la possibilità di eseguire correttamente l'allocazione. 

### <a name="workaround"></a>Soluzione alternativa

Se si usano dimensioni o serie di VM precedenti, ad esempio Dv1, DSv1, Av1, D15v2 o DS15v2, si consiglia di passare a versioni più recenti. Per informazioni specifiche sulle dimensioni di VM, vedere queste raccomandazioni.
Se non è possibile usare una dimensione di VM diversa, provare a distribuire in un'area diversa all'interno della stessa area geografica. Per altre informazioni sulle dimensioni di VM disponibili in ogni area, vedere https://aka.ms/azure-regions

Se si usano le zone di disponibilità, provare un'altra zona all'interno dell'area con capacità disponibile per la dimensione di VM richiesta.

Se la richiesta di allocazione è di grandi dimensioni (oltre 500 core), consultare le informazioni disponibili nelle sezioni seguenti per suddividere la richiesta in distribuzioni di dimensioni minori.

## <a name="allocation-failures-for-older-vm-sizes-av1-dv1-dsv1-d15v2-ds15v2-etc"></a>Errori di allocazione per dimensioni di VM precedenti (Av1, Dv1, DSv1, D15v2, DS15v2 e così via)

Con l'espansione dell'infrastruttura di Azure, viene distribuito hardware di nuova generazione progettato per supportare i tipi di macchina virtuale più recenti. Alcune delle serie di VM precedenti non vengono eseguite nell'infrastruttura di generazione più recente. Per questo motivo, è possibile che i clienti riscontrino occasionalmente errori di allocazione per queste SKU legacy. Per evitare questo problema, consigliamo ai clienti che usano macchine virtuali serie legacy di valutare il passaggio a macchine virtuali più recenti equivalenti per i motivi seguenti: Queste macchine virtuali sono ottimizzate per l'hardware più recente e consentiranno di sfruttare un rapporto prezzo/prestazioni più vantaggioso. 

|Dimensioni/Serie VM legacy|Dimensioni/Serie VM più recenti consigliate|Altre informazioni|
|----------------------|----------------------------|--------------------|
|Serie Av1|[Serie Av2](../articles/virtual-machines/windows/sizes-general.md#av2-series)|https://azure.microsoft.com/blog/new-av2-series-vm-sizes/
|Serie Dv1 o DSv1 (da D1 a D5)|[Serie Dv3 o Dsv3](../articles/virtual-machines/windows/sizes-general.md#dsv3-series-1)|https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/
|Serie Dv1 o DSv1 (da D11 a D14)|[Serie Ev3 o Esv3](../articles/virtual-machines/windows/sizes-memory.md#esv3-series)|
|D15v2 o DS15v2|Se si usa il modello di distribuzione di Resource Manager per sfruttare i vantaggi offerti dalle dimensioni maggiori della VM, provare a passare a E16v3/E16sv3 o E32v3/E32sv3. Queste VM sono progettate per l'hardware di generazione più recente. Se si usa il modello di distribuzione di Resource Manager per assicurarsi che l'istanza di VM sia isolata all'hardware dedicato a un singolo cliente, provare a passare alle nuove dimensioni di VM isolate, E64i_v3 o E64is_v3, progettate per l'hardware di generazione più recente. |https://azure.microsoft.com/blog/new-isolated-vm-sizes-now-available/


## <a name="allocation-failures-for-large-deployments-more-than-500-cores"></a>Errori di allocazione per distribuzioni di grandi dimensioni (oltre 500 core)

Ridurre il numero di istanze delle dimensioni di VM richieste e quindi ripetere l'operazione di distribuzione. Inoltre, per distribuzioni più grandi, può essere utile valutare i [set di scalabilità di macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-machine-scale-sets/). Il numero di istanze di VM può essere aumentato o ridotto automaticamente in base alla richiesta o a una pianificazione definita e si hanno maggiori possibilità di eseguire correttamente l'allocazione perché le distribuzioni possono essere distribuite in più cluster. 

## <a name="background-information"></a>Informazioni generali
### <a name="how-allocation-works"></a>Come funziona l'allocazione
I server nei data center di Azure sono partizionati in cluster. In genere, viene eseguita una richiesta di allocazione in più cluster, ma è possibile che determinati vincoli nella richiesta di allocazione impongano alla piattaforma Azure di eseguire la richiesta in un solo cluster. In questo articolo, si fa riferimento a questa operazione con l'espressione "bloccata su un cluster". Il diagramma 1 riportato di seguito illustra il caso di un'allocazione normale tentata in più cluster. Il diagramma 2 illustra il caso di un'allocazione bloccata sul cluster 2 perché è quello che ospita il servizio cloud CS_1 o il set di disponibilità esistente.
![Diagramma di allocazione](./media/virtual-machines-common-allocation-failure/Allocation1.png)

### <a name="why-allocation-failures-happen"></a>Perché si verificano gli errori di allocazione
Quando una richiesta di allocazione è bloccata su un cluster, la probabilità di non riuscire a trovare risorse disponibili è più alta, perché il pool di risorse disponibili è più ridotto. Inoltre, se la richiesta di allocazione è bloccata su un cluster, ma il tipo di risorsa richiesto non è supportato da quel cluster, la richiesta non viene eseguita correttamente anche se nel cluster ci sono risorse disponibili. Il diagramma 3 seguente illustra un'allocazione bloccata non riuscita perché nel solo cluster candidato non sono presenti risorse disponibili. Il diagramma 4 illustra un'allocazione bloccata non riuscita perché il solo cluster candidato non supporta le dimensioni della VM richieste, anche se nel cluster ci sono risorse disponibili.

![Errore di allocazione bloccata](./media/virtual-machines-common-allocation-failure/Allocation2.png)


