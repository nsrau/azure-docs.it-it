---
title: Calcoli delle valutazioni in Azure Migrate | Microsoft Docs
description: Offre una panoramica dei calcoli delle valutazioni nel servizio Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 06/02/2017
ms.author: raynew
ms.openlocfilehash: db09ff30ff9f3852e84162b8400572e76515230f
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="assessment-calculations"></a>Calcoli delle valutazioni

[Azure Migrate](migrate-overview.md) valuta i carichi di lavoro locali per la migrazione ad Azure. Questo articolo fornisce informazioni sulle modalità di calcolo delle valutazioni.


## <a name="overview"></a>Panoramica

Una valutazione di Azure Migrate si articola in tre fasi. La valutazione inizia con un'analisi di idoneità, seguita dalla determinazione della dimensione e termina con una stima dei costi mensili. Un computer passa alla fase successiva soltanto se supera quella precedente. Ad esempio, se un computer non supera il controllo di idoneità di Azure, viene contrassegnato come non idoneo per Azure e la dimensione e i costi non vengono calcolati. 


## <a name="azure-suitability-analysis"></a>Analisi di idoneità di Azure

Non tutti i computer sono adatti per l'esecuzione nel cloud perché il cloud presenta particolari limitazioni e requisiti. Azure Migrate valuta ogni computer locale per l'idoneità alla migrazione in Azure e classifica i computer in una delle categorie seguenti:
- **Idoneo per Azure**: il computer può essere migrato in Azure così com'è, senza modifiche. Eseguirà l'avvio in Azure con il supporto completo di Azure.
- **Idoneo per Azure con condizioni**: il computer può eseguire l'avvio in Azure, ma non ha il supporto completo di Azure. Ad esempio un computer con una versione precedente del sistema operativo Windows Server non è supportato in Azure. È necessario prestare attenzione prima di eseguire la migrazione di tali computer in Azure e seguire le indicazioni di correzione suggerite nella valutazione per risolvere i problemi di idoneità prima di eseguire la migrazione.
- **Non idoneo per Azure**: il computer non eseguirà l'avvio in Azure. Ad esempio, se un computer locale è collegato a un disco più grande di 4 TB, non può essere ospitato in Azure. È necessario seguire le indicazioni di correzione suggerite nella valutazione per risolvere il problema di idoneità prima della migrazione in Azure. Il calcolo della dimensione e dei costi non viene eseguito per i computer contrassegnati come non idonei per Azure.
- **Idoneità sconosciuta**: Azure Migrate non è riuscito a determinare l'idoneità del computer a causa di dati insufficienti disponibili nel server vCenter. 

Azure Migrate esamina le proprietà del computer e del sistema operativo guest per identificare l'idoneità del computer locale per Azure.

### <a name="machine-properties"></a>Proprietà del computer
Azure Migrate esamina le proprietà seguenti della VM locale per identificare se la VM può essere eseguita in Azure.
 
**Proprietà** | **Dettagli** | **Stato di idoneità per Azure**
--- | --- | ---
**Tipo di avvio** | Azure supporta le VM con il tipo di avvio BIOS e non UEFI. | Idoneo per Azure con condizioni se il tipo di avvio è UEFI. 
**Core** | Il numero di core nei computer deve essere uguale o inferiore al numero massimo di core supportati per una VM di Azure, ovvero 32.<br/><br/> Se è disponibile la cronologia delle prestazioni, Azure Migrate prende in considerazione per il confronto i core utilizzati. Se nelle impostazioni di valutazione è specificato un fattore di comfort, il numero di core utilizzati viene moltiplicato per questo fattore.<br/><br/> In mancanza di una cronologia delle prestazioni, Azure Migrate usa i core allocati senza applicare il fattore di comfort. | Non idoneo se il numero di core è maggiore di 32. 
**Memoria** | La dimensione della memoria del computer deve essere uguale o inferiore alla memoria massima consentita per una VM di Azure, ovvero 448 GB. <br/><br/> Se è disponibile la cronologia delle prestazioni, Azure Migrate prende in considerazione per il confronto la memoria utilizzata. Se è specificato un fattore di comfort, la memoria utilizzata viene moltiplicata per questo fattore.<br/><br/> In mancanza di una cronologia, si usa la memoria allocata senza applicare il fattore di comfort.<br/><br/> | Non idoneo se la dimensione della memoria è maggiore di 448 GB.
**Disco di archiviazione** | Le dimensioni allocate di un disco non devono superare i 4 TB (4096 GB).<br/><br/> I dischi collegati al computer non devono essere più di 65, incluso il disco del sistema operativo. | Non idoneo se un qualsiasi disco è maggiore di 4 TB o se sono presenti più di 65 dischi collegati al computer. 
**Rete** | A un computer non devono essere collegati più di 32 NIC. | Non idoneo se il computer ha più di 32 schede di interfaccia di rete

### <a name="guest-operating-system"></a>Sistema operativo guest 
Insieme alle proprietà della VM, Azure Migrate esamina anche il sistema operativo guest della VM locale per stabilire se la VM può essere eseguita in Azure.

> [!NOTE]
> Azure Migrate prende in considerazione il sistema operativo specificato nel server vCenter per eseguire le analisi seguenti. Poiché il processo di individuazione eseguito da Azure Migrate è basato su appliance, non dispone di un modo per verificare se il sistema operativo eseguito all'interno della VM è uguale a quello specificato nel server vCenter.

Azure Migrate usa la logica seguente per identificare lo stato di idoneità della VM in base al sistema operativo.

**Sistema operativo** | **Dettagli** | **Stato di idoneità per Azure**
--- | --- | ---
Windows Server 2016 e tutti i Service Pack | Azure offre supporto completo. | Idoneo per Azure
Windows Server 2012 R2 e tutti i Service Pack | Azure offre supporto completo. | Idoneo per Azure
Windows Server 2012 e tutti i Service Pack | Azure offre supporto completo. | Idoneo per Azure
Windows Server 2008 R2 con tutti i Service Pack | Azure offre supporto completo.| Idoneo per Azure
Windows Server 2003-2008 R2 | Questi sistemi operativi hanno superato la data di fine supporto e hanno bisogno di un [Contratto di supporto personalizzato (CSA)](https://aka.ms/WSosstatement) per il supporto di Azure. | Idoneo per Azure con condizioni, provare ad aggiornare il sistema operativo prima della migrazione in Azure.
Windows 2000, 98, 95, NT, 3.1, MS-DOS | Questi sistemi operativi hanno superato la data di fine supporto, il computer può eseguire l'avvio in Azure, ma Azure non offre alcun supporto del sistema operativo. | Idoneo per Azure con condizioni, si consiglia di aggiornare il sistema operativo prima della migrazione in Azure.
Client Windows 7, 8 e 10 | Azure offre supporto solo con la sottoscrizione di Visual Studio. | Idoneo per Azure con condizioni
Windows Vista, XP Professional | Questi sistemi operativi hanno superato la data di fine supporto, il computer può eseguire l'avvio in Azure, ma Azure non offre alcun supporto del sistema operativo. | Idoneo per Azure con condizioni, si consiglia di aggiornare il sistema operativo prima della migrazione in Azure.
Linux | Azure approva questi [sistemi operativi Linux](../virtual-machines/linux/endorsed-distros.md). Altri sistemi operativi Linux possono eseguire l'avvio in Azure, ma è consigliabile aggiornare il sistema operativo a una versione approvata prima della migrazione in Azure. | Idoneo per Azure se la versione è approvata.<br/><br/>Idoneo per Azure con condizioni se la versione non è approvata.
Altri sistemi operativi<br/><br/> ad esempio Oracle Solaris, Apple Mac OS, FreeBSD, ecc. | Azure non approva questi sistemi operativi. Il computer può eseguire l'avvio in Azure, ma Azure non offre alcun supporto del sistema operativo. | Idoneo per Azure con condizioni, si consiglia di installare un sistema operativo supportato prima della migrazione in Azure.  
Sistema operativo specificato come *Altro* nel server vCenter | In questo caso Azure Migrate non può identificare il sistema operativo. | Idoneità sconosciuta. Verificare che il sistema operativo eseguito nella VM sia supportato in Azure. 
Sistemi operativi a 32 bit | Il computer può eseguire l'avvio in Azure, ma Azure non offre il supporto completo. | Idoneo per Azure con condizioni, provare ad aggiornare il sistema operativo della macchina virtuale da 32 bit a 64 bit prima di eseguire la migrazione in Azure.

## <a name="sizing"></a>Ridimensionamento

Dopo che un computer è stato contrassegnato come idoneo per Azure, Azure Migrate determina la dimensione della VM e dei relativi dischi per Azure. Se il criterio di determinazione della dimensione specificato nelle proprietà di valutazione è quello basato sulle prestazioni, Azure Migrate prende in considerazione la cronologia delle prestazioni del computer per identificare la dimensione della VM in Azure. Questo metodo è utile in scenari in cui è stata eseguita una sovrallocazione della VM locale ma l'utilizzo è basso e si desidera dimensionare in modo appropriato le VM in Azure per ottimizzare i costi.

> [!NOTE]
> Azure Migrate raccoglie la cronologia delle prestazioni delle VM locali dal server vCenter. Per garantire una determinazione precisa della dimensione, assicurarsi che l'impostazione delle statistiche nel server vCenter sia pari al livello 3 e attendere almeno un giorno prima di iniziare l'individuazione delle VM locali. Se l'impostazione delle statistiche nel server vCenter è inferiore al livello 3, i dati sulle prestazioni per i dischi e la rete non vengono raccolti. 

Se non si vuole considerare la cronologia delle prestazioni e si vuole migrare la VM così com'è in Azure, è possibile specificare il criterio di determinazione della dimensione *come in locale* e Azure Migrate dimensionerà le VM in base alla configurazione locale senza considerare i dati di utilizzo.

### <a name="performance-based-sizing"></a>Dimensioni in base alle prestazioni

Per la determinazione della dimensione in base alle prestazioni, Azure Migrate inizia con i dischi collegati alla VM, continua con le schede di rete e quindi esegue il mapping della VM di Azure in base ai requisiti di calcolo della VM locale. 
 
- **Dischi**: Azure Migrate tenta di eseguire il mapping di ciascun disco collegato al computer con un disco in Azure. 
    
    > [!NOTE]
    > Azure Migrate supporta solo i dischi gestiti per la valutazione.
    
    - Per ottenere l'I/O su disco al secondo (IOPS) effettivo e la velocità effettiva (MBps), Azure Migrate moltiplica l'IOPS e la velocità effettiva del disco per il fattore di comfort. In base ai valori di IOPS effettivo e velocità effettiva, Azure Migrate determina se è necessario eseguire il mapping del disco con un disco standard o premium in Azure.
    - Se Azure Migrate non riesce a trovare un disco con i valori di IOPS e velocità effettiva necessari, contrassegna il computer come non idoneo per Azure. [Altre informazioni](../azure-subscription-service-limits.md#storage-limits) sulle limitazioni di Azure per disco e VM.
    - Se trova un set di dischi adatti, Azure Migrate seleziona quelli che supportano il metodo di ridondanza dell'archiviazione e il percorso specificato nelle impostazioni di valutazione.
    - Se sono presenti più dischi idonei, viene selezionato quello con il costo più basso.
    - Se i dati sulle prestazioni per i dischi non sono disponibili, viene eseguito il mappimg di tutti i dischi a dischi standard in Azure.

- **Schede di rete**: Azure Migrate tenta di trovare una VM di Azure in grado di supportare il numero di schede di rete collegate al computer locale e le prestazioni richieste da queste schede di rete.
    - Per ottenere le prestazioni di rete effettive della VM locale, Azure Migrate aggrega i dati trasmessi al secondo (MBps) dal computer (rete in uscita), in tutte le schede di rete, e applica il fattore di comfort. Questo numero viene usato per trovare una VM di Azure in grado di supportare le prestazioni di rete necessarie.
    - Insieme alle prestazioni di rete considera anche se la VM di Azure può supportare il numero di schede di rete richiesto.
    - Se non sono disponibili dati relativi alle prestazioni di rete, per la determinazione della dimensione della VM viene preso in considerazione solo il numero di schede di rete.

- **Dimensione della VM**: dopo che sono stati calcolati i requisiti di archiviazione e di rete, Azure Migrate prende in considerazione i requisiti di CPU e memoria per trovare una dimensione di VM appropriata in Azure.
    - Azure Migrate analizza i core e la memoria utilizzati e applica il fattore di comfort per ottenere i core e la memoria effettivi. In base al numero risultante, prova a trovare le dimensioni adatte di una macchina virtuale in Azure.
    - Se non si trovano dimensioni adatte, il computer viene contrassegnato come non idoneo per Azure.
    - Se si trovano dimensioni adatte, Azure Migrate esegue i calcoli di archiviazione e di rete. Applica quindi le impostazioni relative alla posizione e al piano tariffario per l'indicazione finale delle dimensioni della macchina virtuale.
    - Se ci sono più dimensioni di VM di Azure idonee, si consiglia di usare quella con il costo più basso.

### <a name="as-on-premises-sizing"></a>Determinazione della dimensione come in locale
Se il criterio di determinazione della dimensione è *come in locale*, Azure Migrate non considera la cronologia delle prestazioni delle VM e alloca le VM e i dischi in base alla dimensione allocata in locale.
- **Archiviazione**: per un disco standard in Azure si consiglia la stessa dimensione del disco locale.
- **Rete**: per ogni scheda di rete si consiglia una scheda di rete in Azure.
- **Calcolo**: Azure Migrate analizza il numero di core e la dimensione della memoria della VM locale e consiglia una VM di Azure con la stessa configurazione. Se ci sono più dimensioni di VM di Azure idonee, si consiglia di usare quella con il costo più basso.
 
### <a name="confidence-rating"></a>Classificazione di attendibilità

Ogni valutazione in Azure Migrate è associata a una classificazione di attendibilità compresa tra 1 e 5 stelle, dove 1 stella corrisponde al livello minimo e 5 stelle al livello massimo. La classificazione di attendibilità viene assegnata a una valutazione in base alla disponibilità dei punti dati necessari per calcolare la valutazione. La classificazione aiuta a stimare l'affidabilità delle indicazioni relative alla dimensione fornite da Azure Migrate. 

La classificazione di attendibilità è utile quando si esegue una *determinazione della dimensione in base alle prestazioni*, in quanto non tutti i punti dati possono essere disponibili. Per una *determinazione della dimensione come in locale*, la classificazione di attendibilità è sempre 5 stelle, perché Azure Migrate ha a disposizione tutti i dati necessari per determinare la dimensione della VM. 

Per una determinazione della dimensione in base alle prestazioni, Azure Migrate deve disporre dei dati sull'utilizzo di CPU e memoria. Per ogni disco collegato alla VM, il servizio deve leggere le operazioni di I/O al secondo in lettura/scrittura e la velocità effettiva per eseguire la determinazione della dimensione in base alle prestazioni. Analogamente, per ogni scheda di rete collegata alla VM, Azure Migrate deve disporre dei dati sull'ingresso/uscita di rete per applicare la determinazione della dimensione in base alle prestazioni. Se una qualsiasi delle cifre sull'utilizzo indicate sopra non è disponibile nel server vCenter, l'indicazione relativa alla dimensione fornita da Azure Migrate potrebbe non essere affidabile. Di seguito è indicata la classificazione di attendibilità per la valutazione in base alla percentuale dei punti dati disponibili:

   **Disponibilità dei punti dati** | **Classificazione di attendibilità**
   --- | ---
   0%-20% | 1 stella
   21%-40% | 2 stelle
   41%-60% | 3 stelle
   61%-80% | 4 stelle
   81%-100% | 5 stelle

Una valutazione può non avere a disposizione tutti i punti dati a causa di uno dei motivi seguenti:
- L'impostazione delle statistiche nel server vCenter non è pari al livello 3 e la valutazione usa il criterio di determinazione della dimensione in base alle prestazioni. Se l'impostazione delle statistiche nel server vCenter è inferiore al livello 3, i dati sulle prestazioni per i dischi e la rete non vengono raccolti da vCenter Server. In questo caso, l'indicazione fornita da Azure Migrate per dischi e rete si basa solo sull'allocazione in locale. Per l'archiviazione, Azure Migrate consiglia dischi standard, in quanto non esiste alcun modo per identificare se il disco ha operazioni di I/O al secondo e velocità effettiva elevate e richiede quindi dischi Premium.
- L'impostazione delle statistiche nel server vCenter è stata impostata sul livello 3 per un breve periodo di tempo, prima di avviare il processo di individuazione. Ad esempio, se si cambia l'impostazione delle statistiche al livello 3 oggi e si avvia l'individuazione usando l'appliance dell'agente di raccolta domani (24 ore dopo) creando una valutazione per un giorno, sono disponibili tutti i punti dati. Se invece si modifica a un mese il periodo di tempo delle prestazioni nelle proprietà della valutazione, la classificazione di attendibilità diminuisce, in quanto i dati sulle prestazioni di dischi e rete per l'ultimo mese non sono disponibili. Per tenere conto dei dati sulle prestazioni dell'ultimo mese, è consigliabile mantenere l'impostazione delle statistiche del server vCenter sul livello 3 per un mese prima di avviare il processo di individuazione. 
- Durante il periodo per cui viene calcolata la valutazione sono state arrestate alcune VM. Se una o più VM sono state spente per un certo periodo di tempo, il server vCenter non avrà i dati sulle prestazioni per questo periodo. 
- All'interno del periodo per cui viene calcolata la valutazione sono state create alcune VM. Questa situazione si verifica, ad esempio, se si crea una valutazione per la cronologia delle prestazioni dell'ultimo mese, ma solo una settimana prima sono state create alcune VM nell'ambiente. In questi casi, la cronologia delle prestazioni delle nuove VM non sarà disponibile per l'intero periodo.

> [!NOTE]
> Se la classificazione di attendibilità di una valutazione è inferiore a 3 stelle, è consigliabile modificare le impostazioni delle statistiche del server vCenter al livello 3, attendere per il periodo che deve essere considerato nella valutazione (1 giorno/1 settimana/1 mese) e quindi eseguire l'individuazione e la valutazione. Se non è possibile applicare la soluzione indicata sopra, la determinazione della dimensione in base alle prestazioni potrebbe non essere affidabile ed è consigliabile passare a quella *come in locale* modificando le proprietà della valutazione.

## <a name="monthly-cost-estimation"></a>Stima dei costi mensili

Dopo aver fornito tutte le indicazioni relative alle dimensioni, Azure Migrate calcola i costi di calcolo e archiviazione post-migrazione.

- **Costo di calcolo**: sulla base delle dimensioni consigliate della macchina virtuale di Azure, Azure Migrate usa l'API di fatturazione per calcolare i costi mensili della macchina virtuale. Per il calcolo vengono prese in considerazione le impostazioni relative al sistema operativo, al programma Software Assurance, alla posizione e alla valuta. Per calcolare il costo di calcolo mensile totale vengono aggregati i costi di tutti i computer.
- **Costo di archiviazione**: il costo di archiviazione mensile di un computer viene calcolato aggregando il costo mensile di tutti i dischi collegati al computer. Azure Migrate calcola i costi di archiviazione mensili totali aggregando i costi di archiviazione di tutti i computer. Attualmente, il calcolo non prende in considerazione le offerte specificate nelle impostazioni di valutazione.

I costi vengono visualizzati nella valuta specificata nelle impostazioni di valutazione. 


## <a name="next-steps"></a>Passaggi successivi

[Creare una valutazione per macchine virtuali VMware locali](tutorial-assessment-vmware.md)
