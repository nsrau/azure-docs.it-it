---
title: Calcoli delle valutazioni in Azure Migrate | Microsoft Docs
description: Offre una panoramica dei calcoli delle valutazioni nel servizio Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: raynew
ms.openlocfilehash: a328549307772cbdf470160cc1ad713fe1ee5e05
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67805975"
---
# <a name="assessment-calculations"></a>Calcoli delle valutazioni

Valutazione di Server eseguire la migrazione di Azure valuta i carichi di lavoro locali per la migrazione ad Azure. Questo articolo fornisce informazioni sulle modalità di calcolo delle valutazioni.


[Azure Migrate](migrate-services-overview.md) fornisce un hub centrale per tenere traccia di individuazione, valutazione e migrazione delle App in locale e i carichi di lavoro e le istanze di cloud privati/pubblici, in Azure. L'hub fornisce gli strumenti di Azure Migrate per la valutazione e migrazione, nonché le offerte di fornitori di software indipendenti di terze parti.

## <a name="overview"></a>Panoramica

Una valutazione di valutazione di Azure Migrate Server prevede tre fasi. La valutazione inizia con un'analisi di idoneità, seguita dalla determinazione della dimensione e termina con una stima dei costi mensili. Un computer passa alla fase successiva soltanto se supera quella precedente. Ad esempio, se un computer non supera il controllo di idoneità di Azure, viene contrassegnato come non idoneo per Azure e la dimensione e i costi non vengono calcolati.


## <a name="whats-in-an-assessment"></a>Elementi inclusi nella valutazione

**Proprietà** | **Dettagli**
--- | ---
**Posizione di destinazione** | Area di Azure in cui si vuole eseguire la migrazione.<br/><br/> Azure Migrate supporta attualmente queste aree di destinazione: Australia orientale, Australia sud-orientale, Brasile meridionale, Canada centrale, Canada orientale, India centrale, Stati Uniti centrali, Cina orientale, Cina settentrionale, Asia orientale, Stati Uniti orientali, Stati Uniti orientali 2, Germania centrale, Germania nord-orientale, Giappone orientale, Giappone occidentale, Corea centrale, Corea meridionale, Nord Stati Uniti centrali, Europa settentrionale, Stati Uniti centro-meridionali, Asia sud-orientale, India meridionale, Regno Unito meridionale, Regno Unito occidentale, Stati Uniti Arizona Gov, US Gov Texas, US Gov Virginia, Stati Uniti centro occidentali, Europa occidentale, India occidentale, Stati Uniti occidentali e Stati Uniti occidentali 2.<br/> Per impostazione predefinita, l'area di destinazione è impostata su Stati Uniti occidentali 2.
**Tipo di archiviazione** | Premium/standard HHD/Standard a dischi SSD dischi.<br/><br/> Quando si specifica il tipo di archiviazione come automatico in una valutazione, i dati sulle prestazioni dei dischi (numero di IOPS e velocità effettiva) si basa la raccomandazione del disco.<br/><br/> Se si specifica il tipo di archiviazione come Premium o Standard, consiglia la valutazione un disco SKU all'interno del tipo di archiviazione selezionato.<br/><br/> Se si desidera ottenere una singola istanza VM SLA pari al 99,9%, è possibile impostare il tipo di archiviazione come dischi gestiti Premium. Quindi tutti i dischi nella valutazione verranno generate indicazioni come dischi gestiti Premium. <br/> Azure Migrate supporta solo dischi gestiti per la valutazione della migrazione.<br/> 
**Istanze riservate (RI)** | Specificare questa proprietà se si hanno istanze riservate di in Azure. Le stime di costo nella valutazione prenderà in considerazione gli sconti delle istanze riservate. Le istanze riservate sono attualmente supportate solo per offerte di pagamento a consumo in Azure Migrate.
**Criterio di dimensionamento** | Consente di dimensionare le macchine virtuali. Ridimensionamento può essere basata sulle prestazioni, o come-sia in locale, senza considerare la cronologia delle prestazioni.
**Cronologia delle prestazioni** | La durata da considerare per la valutazione delle prestazioni della macchina virtuale. Questa proprietà è applicabile solo quando il ridimensionamento è basato sulle prestazioni.
**Utilizzo percentile** | Il valore percentile dell'esempio di prestazioni che viene usato per corretto dimensionamento delle macchine virtuali. Questa proprietà è applicabile solo quando il ridimensionamento è basato sulle prestazioni.
**Serie macchina virtuale** | Serie di VM usata per le stime delle dimensioni. Se si ha un ambiente di produzione di cui non si intende eseguire la migrazione a VM serie A in Azure, ad esempio, si può escludere la serie A dall'elenco o dalle serie. Il dimensionamento sarà basato solo sulla serie selezionata.
**Fattore di comfort** | Valutazione di Server eseguire la migrazione di Azure considera un buffer (fattore di comfort) durante la valutazione. che viene applicato ai dati sull'utilizzo delle VM (CPU, memoria, disco e rete). Il fattore di comfort tiene conto di aspetti come utilizzo stagionale, breve cronologia delle prestazioni e probabile aumento dell'utilizzo futuro.<br/><br/> Da una VM con 10 core e un utilizzo del 20%, ad esempio, si ottiene normalmente una VM con 2 core. Con un fattore di comfort pari a 2.0x, invece, il risultato è una VM con 4 core.
**Offerta** | [Offerta di Azure](https://azure.microsoft.com/support/legal/offer-details/) sottoscritta. Azure Migrate stima il costo di conseguenza.
**Valuta** | Valuta di fatturazione. 
**Sconto (%)** | Qualsiasi sconto specifico della sottoscrizione ricevuto oltre all'offerta Azure.<br/> L'impostazione predefinita è 0%.
**Tempo di attività macchina virtuale** | Se non si prevede di eseguire ininterrottamente le macchine virtuali in Azure, è possibile specificare la durata (numero di giorni al mese e numero di ore al giorno) dell'esecuzione. Il costo verrà stimato di conseguenza.<br/> Il valore predefinito è di 31 giorni al mese e di 24 ore al giorno.
**Vantaggio Azure Hybrid** | Specifica se dispone di software assurance e sono idonei [vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Se il valore è impostato su Sì, alle macchine virtuali Windows si applicano i prezzi di Azure per sistemi non Windows. 



## <a name="azure-suitability-analysis"></a>Analisi di idoneità di Azure

Non tutte le macchine sono ideali per l'esecuzione in Azure. Azure eseguire la migrazione di Server Assessment valuta ogni computer locale per la migrazione e Categorizza le macchine in una delle categorie di idoneità seguenti:
- **Idoneo per Azure**: il computer può essere migrato in Azure così com'è, senza modifiche. Eseguirà l'avvio in Azure con il supporto completo di Azure.
- **Idoneo per Azure con condizioni**: il computer può eseguire l'avvio in Azure, ma non ha il supporto completo di Azure. Ad esempio un computer con una versione precedente del sistema operativo Windows Server non è supportato in Azure. È necessario prestare attenzione prima di eseguire la migrazione di tali computer in Azure e seguire le indicazioni di correzione suggerite nella valutazione per risolvere i problemi di idoneità prima di eseguire la migrazione.
- **Non idoneo per Azure**: il computer non eseguirà l'avvio in Azure. Ad esempio, se un computer locale è collegato a un disco più grande di 4 TB, non può essere ospitato in Azure. È necessario seguire le indicazioni di correzione suggerite nella valutazione per risolvere il problema di idoneità prima della migrazione in Azure. Il calcolo della dimensione e dei costi non viene eseguito per i computer contrassegnati come non idonei per Azure.
- **Idoneità sconosciuta**: Azure Migrate non è riuscito a determinare l'idoneità del computer a causa di dati insufficienti disponibili nel server vCenter.



### <a name="machine-properties"></a>Proprietà del computer

Azure Migrate esamina le proprietà seguenti della macchina virtuale in locale per identificare se possa essere eseguita in Azure.

**Proprietà** | **Dettagli** | **Stato di idoneità per Azure**
--- | --- | ---
**Tipo di avvio** | Azure supporta le VM con il tipo di avvio BIOS e non UEFI. | Idoneo con condizioni se il tipo di avvio è UEFI.
**Core** | Il numero di core nei computer deve essere uguale o inferiore al numero massimo di core supportati per una VM di Azure, ovvero 128.<br/><br/> Se è disponibile la cronologia delle prestazioni, Azure Migrate prende in considerazione per il confronto i core utilizzati. Se nelle impostazioni di valutazione è specificato un fattore di comfort, il numero di core utilizzati viene moltiplicato per questo fattore.<br/><br/> In mancanza di una cronologia delle prestazioni, Azure Migrate usa i core allocati senza applicare il fattore di comfort. | Idoneo se è minore o uguale ai limiti.
**Memoria** | La dimensione della memoria del computer deve essere uguale o inferiore alla memoria massima consentita per una macchina virtuale di Azure, ovvero 3892 GB per standard_M128m&nbsp;<sup>2</sup> di Azure serie M. [Altre informazioni](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)<br/><br/> Se è disponibile la cronologia delle prestazioni, Azure Migrate prende in considerazione per il confronto la memoria utilizzata. Se è specificato un fattore di comfort, la memoria utilizzata viene moltiplicata per questo fattore.<br/><br/> In mancanza di una cronologia, si usa la memoria allocata senza applicare il fattore di comfort.<br/><br/> | Idoneo se rientra nei limiti.
**Disco di archiviazione** | Le dimensioni allocate di un disco non devono superare i 4 TB (4096 GB).<br/><br/> I dischi collegati al computer non devono essere più di 65, incluso il disco del sistema operativo. | Idoneo se rientra nei limiti.
**Rete** | A un computer non devono essere collegati più di 32 NIC. | Idoneo se rientra nei limiti.

### <a name="guest-operating-system"></a>Sistema operativo guest
Insieme alle proprietà della macchina virtuale, valutazione Server di Azure Migrate esamina il sistema operativo guest delle macchine, per identificare se possa essere eseguita in Azure.

> [!NOTE]
> Valutazione di Server eseguire la migrazione di Azure Usa il sistema operativo specificato per la macchina virtuale nel Server vCenter per l'analisi. Valutazione di Server eseguire la migrazione di Azure è basato su dispositivo per l'individuazione della macchina virtuale e non può verificare se il sistema operativo in esecuzione nella macchina virtuale è uguale a quello specificato nel Server vCenter.

La logica seguente viene utilizzata dalla valutazione della Server eseguire la migrazione di Azure, per identificare l'idoneità per Azure basata sul sistema operativo.

**Sistema operativo** | **Dettagli** | **Stato di idoneità per Azure**
--- | --- | ---
Windows Server 2016 e tutti i Service Pack | Azure offre supporto completo. | Idoneo per Azure
Windows Server 2012 R2 e tutti i Service Pack | Azure offre supporto completo. | Idoneo per Azure
Windows Server 2012 e tutti i Service Pack | Azure offre supporto completo. | Idoneo per Azure
Windows Server 2008 R2 con tutti i Service Pack | Azure offre supporto completo.| Idoneo per Azure
Windows Server 2008 (32 bit e 64 bit) | Azure offre supporto completo. | Idoneo per Azure
Windows Server 2003, 2003 R2 | Questi sistemi operativi hanno superato la data di fine supporto e hanno bisogno di un [Contratto di supporto personalizzato (CSA)](https://aka.ms/WSosstatement) per il supporto di Azure. | Idoneo per Azure con condizioni, provare ad aggiornare il sistema operativo prima della migrazione in Azure.
Windows 2000, 98, 95, NT, 3.1, MS-DOS | Questi sistemi operativi hanno superato la data di fine supporto, il computer può eseguire l'avvio in Azure, ma Azure non offre alcun supporto del sistema operativo. | Idoneo per Azure con condizioni, si consiglia di aggiornare il sistema operativo prima della migrazione in Azure.
Client Windows 7, 8 e 10 | Azure offre supporto solo con [la sottoscrizione di Visual Studio.](https://docs.microsoft.com/azure/virtual-machines/windows/client-images) | Idoneo per Azure con condizioni
Windows 10 Pro Desktop | Azure fornisce supporto con [i diritti di hosting multi-tenant.](https://docs.microsoft.com/azure/virtual-machines/windows/windows-desktop-multitenant-hosting-deployment) | Idoneo per Azure con condizioni
Windows Vista, XP Professional | Questi sistemi operativi hanno superato la data di fine supporto, il computer può eseguire l'avvio in Azure, ma Azure non offre alcun supporto del sistema operativo. | Idoneo per Azure con condizioni, si consiglia di aggiornare il sistema operativo prima della migrazione in Azure.
Linux | Azure approva questi [sistemi operativi Linux](../virtual-machines/linux/endorsed-distros.md). Altri sistemi operativi Linux possono eseguire l'avvio in Azure, ma è consigliabile aggiornare il sistema operativo a una versione approvata prima della migrazione in Azure. | Idoneo per Azure se la versione è approvata.<br/><br/>Idoneo per Azure con condizioni se la versione non è approvata.
Altri sistemi operativi<br/><br/> ad esempio Oracle Solaris, Apple Mac OS, FreeBSD, ecc. | Azure non approva questi sistemi operativi. Il computer può eseguire l'avvio in Azure, ma Azure non offre alcun supporto del sistema operativo. | Idoneo per Azure con condizioni, si consiglia di installare un sistema operativo supportato prima della migrazione in Azure.  
Sistema operativo specificato come **Altro** nel server vCenter | In questo caso Azure Migrate non può identificare il sistema operativo. | Idoneità sconosciuta. Verificare che il sistema operativo eseguito nella VM sia supportato in Azure.
Sistemi operativi a 32 bit | Il computer può eseguire l'avvio in Azure, ma Azure non offre il supporto completo. | Idoneo per Azure con condizioni, provare ad aggiornare il sistema operativo della macchina virtuale da 32 bit a 64 bit prima di eseguire la migrazione in Azure.

## <a name="sizing"></a>Ridimensionamento

Dopo che un computer è stato contrassegnato come idoneo per Azure, Azure Migrate determina la dimensione della VM e dei relativi dischi per Azure.

- Se la valutazione Usa basato sulle prestazioni, Azure Migrate prende in considerazione la cronologia delle prestazioni del computer per identificare il tipo di dimensione e il disco di macchina virtuale in Azure. Questo metodo è particolarmente utile se è stata una sovrallocazione la macchina virtuale in locale, ma l'utilizzo è basso e si desidera liberà di ridimensionare la macchina virtuale in Azure per risparmiare sui costi.
- Se sta usando un oggetto come in locale della valutazione, valutazione di Azure Migrate Server verrà ridimensionare le macchine virtuali basate sulle impostazioni locali, senza considerare i dati di utilizzo. In questo caso, il ridimensionamento del disco, si basa sul tipo di archiviazione che è specificare nelle proprietà della valutazione (disco Standard o Premium disco).

### <a name="performance-based-sizing"></a>Dimensioni in base alle prestazioni

Per il dimensionamento basato sulle prestazioni, Azure Migrate inizia con i dischi collegati alla macchina virtuale, seguita dalle schede di rete, e quindi esegue il mapping una VM di Azure in base ai requisiti di calcolo della macchina virtuale in locale.

- **Archiviazione**: Azure Migrate tenta di eseguire il mapping di ogni disco collegato alla macchina virtuale con un disco in Azure.
    - Per ottenere l'I/O su disco al secondo (IOPS) effettivo e la velocità effettiva (MBps), Azure Migrate moltiplica l'IOPS e la velocità effettiva del disco per il fattore di comfort. In base ai valori di IOPS effettivo e velocità effettiva, Azure Migrate determina se è necessario eseguire il mapping del disco con un disco standard o premium in Azure.
    - Se Azure Migrate non riesce a trovare un disco con i valori di IOPS e velocità effettiva necessari, contrassegna il computer come non idoneo per Azure. [Altre informazioni](../azure-subscription-service-limits.md#storage-limits) sulle limitazioni di Azure per disco e VM.
    - Se trova un set di dischi adatti, Azure Migrate seleziona quelli che supportano il metodo di ridondanza dell'archiviazione e il percorso specificato nelle impostazioni di valutazione.
    - Se sono presenti più dischi idonei, viene selezionato quello con il costo più basso.
    - Se i dati sulle prestazioni per i dischi non sono disponibili, tutti i dischi vengono mappati ai dischi standard in Azure.

- **Network** (Rete): Azure Migrate tenta di trovare una macchina virtuale di Azure in grado di supportare il numero di schede di rete collegate al computer locale e le prestazioni richieste da queste schede di rete.
    - Per ottenere le prestazioni di rete effettive della VM locale, Azure Migrate aggrega i dati trasmessi al secondo (MBps) dal computer (rete in uscita), in tutte le schede di rete, e applica il fattore di comfort. Questo numero viene usato per trovare una VM di Azure in grado di supportare le prestazioni di rete necessarie.
    - Insieme alle prestazioni di rete considera anche se la VM di Azure può supportare il numero di schede di rete richiesto.
    - Se non sono disponibili dati relativi alle prestazioni di rete, per la determinazione della dimensione della VM viene preso in considerazione solo il numero di schede di rete.

- **Calcolo**: dopo che sono stati calcolati i requisiti di archiviazione e di rete, Azure Migrate prende in considerazione i requisiti di CPU e di memoria per trovare una dimensione di macchina virtuale appropriata in Azure.
    - Azure Migrate analizza i core e la memoria utilizzati e applica il fattore di comfort per ottenere i core e la memoria effettivi. In base al numero risultante, prova a trovare le dimensioni adatte di una macchina virtuale in Azure.
    - Se non si trovano dimensioni adatte, il computer viene contrassegnato come non idoneo per Azure.
    - Se si trovano dimensioni adatte, Azure Migrate esegue i calcoli di archiviazione e di rete. Applica quindi le impostazioni relative alla posizione e al piano tariffario per l'indicazione finale delle dimensioni della macchina virtuale.
    - Se ci sono più dimensioni di VM di Azure idonee, si consiglia di usare quella con il costo più basso.

### <a name="as-on-premises-sizing"></a>Determinazione della dimensione come in locale

Se si usa come in locale, Server Assessment alloca una SKU di VM in Azure in base a dimensioni locale. Analogamente al ridimensionamento del disco, viene esaminato il tipo di archiviazione specificata nelle proprietà di valutazione, Standard o Premium, e di conseguenza viene consigliato il tipo di disco. Il tipo di archiviazione predefinito è dischi Premium.

## <a name="confidence-ratings"></a>Classificazioni di attendibilità
Ogni valutazione basato sulle prestazioni in Azure Migrate è associata a una classificazione di attendibilità compreso nell'intervallo da uno (inferiore) a cinque inizia (più alto).
- La classificazione di attendibilità viene assegnata a una valutazione in base alla disponibilità dei punti dati necessari per calcolare la valutazione.
- La classificazione di attendibilità di una valutazione aiuta a stimare l'affidabilità delle indicazioni relative alla dimensione fornite da Azure Migrate.
- Classificazione di attendibilità non è applicabile per come in locale le valutazioni.
- Per il dimensionamento basato sulle prestazioni, valutazione di Azure Migrate Server deve:
    - I dati di utilizzo per CPU e memoria della macchina virtuale.
    - Per ogni disco collegato alla macchina virtuale, il servizio deve anche avere i dati relativi alle operazioni di I/O al secondo e alla velocità effettiva del disco.
    - Allo stesso modo per ogni scheda di rete collegata a una macchina virtuale, la classificazione di attendibilità deve il / o di rete per eseguire operazioni di ridimensionamento in base alle prestazioni.
    - Se uno qualsiasi dei numeri di utilizzo precedenti non sono disponibile nel Server vCenter, l'indicazione delle dimensioni potrebbe non essere affidabile. 

Di seguito è indicata la classificazione di attendibilità per la valutazione in base alla percentuale dei punti dati disponibili come riportato di seguito:

   **Disponibilità dei punti dati** | **Classificazione di attendibilità**
   --- | ---
   0%-20% | 1 stella
   21%-40% | 2 stelle
   41%-60% | 3 stelle
   61%-80% | 4 stelle
   81%-100% | 5 stelle

### <a name="low-confidence-ratings"></a>Classificazioni di confidenza basso in

Alcuni dei motivi per cui una valutazione è stato possibile ottenere una classificazione di attendibilità bassa:

- Si non analizza l'ambiente per la durata per cui si sta creando la valutazione. Ad esempio, se si crea la valutazione con durata di prestazioni impostata su 1 giorno, è necessario attendere almeno un giorno dopo l'avvio di individuazione per tutti i punti dati ottenere raccolti.
- Alcune macchine virtuali sono state arrestate durante il periodo per il quale viene calcolata la valutazione. Se tutte le macchine virtuali sono state spente per un certo periodo di tempo, valutazione di Azure Migrate Server non è possibile raccogliere i dati sulle prestazioni per quel periodo.
- Sono state create alcune VM all'interno del periodo per il quale viene calcolata la valutazione. Ad esempio, se si crea una valutazione per la cronologia delle prestazioni dell'ultimo mese, ma nell'ambiente sono state create alcune VM solo una settimana fa, la cronologia delle prestazioni delle nuove VM sarà presenti per l'intera durata.

  > [!NOTE]
  > Se la classificazione di attendibilità di una valutazione è inferiore a cinque stelle, è consigliabile attendere almeno un giorno per l'appliance profilare l'ambiente e quindi di ricalcolare la valutazione. In caso contrario, basato sulle prestazioni potrebbe non essere affidabile e si e consiglia di passare la valutazione da utilizzare come in locale.
  
## <a name="monthly-cost-estimation"></a>Stima dei costi mensili

Una volta completati i suggerimenti di ridimensionamento, Azure Migrate calcola i costi di calcolo e archiviazione per dopo la migrazione.

- **Costo di calcolo**: sulla base delle dimensioni consigliate della macchina virtuale di Azure, Azure Migrate usa l'API di fatturazione per calcolare i costi mensili della macchina virtuale.
    - Per il calcolo vengono prese in considerazione le impostazioni relative al sistema operativo, al programma Software Assurance, alle istanze riservate, al tempo di attività della macchina virtuale, alla posizione e alla valuta.
    - Per calcolare il costo di calcolo mensile totale vengono aggregati i costi di tutti i computer.
- **Costo di archiviazione**: Il costo di archiviazione mensile per un computer viene calcolato aggregando il costo mensile di tutti i dischi collegati alla macchina
    - Azure valutazione Server Migrate calcola i costi di archiviazione mensili totali aggregando i costi di archiviazione di tutte le macchine.
    - Attualmente, il calcolo non prende in considerazione le offerte specificate nelle impostazioni di valutazione.

I costi vengono visualizzati nella valuta specificata nelle impostazioni di valutazione.


## <a name="next-steps"></a>Passaggi successivi

Creare una valutazione per [macchine virtuali VMware](tutorial-assess-vmware.md) oppure [macchine virtuali Hyper-V](tutorial-assess-hyper-v.md).
