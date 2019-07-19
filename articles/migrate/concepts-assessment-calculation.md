---
title: Calcoli delle valutazioni in Azure Migrate | Microsoft Docs
description: Offre una panoramica dei calcoli delle valutazioni nel servizio Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/15/2019
ms.author: raynew
ms.openlocfilehash: 87aa48c992b7887ce86c43cac29910dcc57b3e91
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234298"
---
# <a name="assessment-calculations"></a>Calcoli delle valutazioni

[Azure migrate](migrate-services-overview.md) offre un hub centrale per tenere traccia dell'individuazione, della valutazione e della migrazione delle app e dei carichi di lavoro locali e delle istanze di cloud privato/pubblico in Azure. L'hub fornisce strumenti Azure Migrate per la valutazione e la migrazione, nonché offerte di fornitori di software indipendenti (ISV) di terze parti.

Server assessment è uno strumento di Azure Migrate che consente di valutare i server locali per la migrazione ad Azure. Questo articolo fornisce informazioni sulle modalità di calcolo delle valutazioni.

## <a name="whats-in-an-assessment"></a>Elementi inclusi nella valutazione

**Proprietà** | **Dettagli**
--- | ---
**Posizione di destinazione** | Area di Azure in cui si vuole eseguire la migrazione.<br/> Server Assessment supporta attualmente le aree di destinazione seguenti: Australia orientale, Australia sudorientale, Brasile meridionale, Canada centrale, Canada orientale, India centrale, Stati Uniti centrali, Cina orientale, Cina settentrionale, Asia orientale, Stati Uniti orientali, East Uniti, Germania centrale, Germania nord-orientale, Giappone orientale, Giappone occidentale, Corea centrale, Corea meridionale, Nord Stati Uniti centrali, Europa settentrionale, Stati Uniti centro-meridionali, Asia sudorientale, India meridionale, Regno Unito meridionale, Regno Unito occidentale, US Gov Arizona, US Gov Texas, US Gov Virginia, Stati Uniti centro-occidentali, Europa occidentale, India occidentale, Stati Uniti occidentali e Uniti occidentali.
**Tipo di archiviazione** | È possibile usare questa proprietà per specificare il tipo di dischi che si vuole spostare in Azure. <br/><br/> Per il dimensionamento delle sedi locali, è possibile specificare il tipo di archiviazione di destinazione come dischi gestiti da Premium, dischi gestiti da SDD Standard o dischi gestiti da HDD Standard. Per il dimensionamento basato sulle prestazioni, è possibile specificare il tipo di disco di destinazione come dischi automatici, gestiti con gestione Premium, dischi gestiti da HDD Standard o dischi gestiti da SDD Standard.<br/><br/> Quando si specifica il tipo di archiviazione automatico, la preferenza dei dischi è basata sui dati delle relative prestazioni (numero di operazioni di I/O al secondo e velocità effettiva). Se si specifica il tipo di archiviazione Premium o standard, la valutazione indicherà uno SKU del disco all'interno del tipo di archiviazione selezionato. Se si vuole ottenere un contratto di Service per macchine virtuali a istanza singola pari al 99,9%, è possibile specificare il tipo di archiviazione come dischi gestiti Premium. che garantisce che tutti i dischi nella valutazione vengano raccomandati come Managed Disks Premium. Si noti che Azure Migrate supporta solo dischi gestiti per la valutazione della migrazione.
**Istanze riservate (RI)** | Questa proprietà consente di specificare se sono presenti [istanze riservate](https://azure.microsoft.com/pricing/reserved-vm-instances/) in Azure. le stime dei costi nella valutazione vengono quindi effettuate in sconti per il ri. Le istanze riservate sono attualmente supportate solo per l'offerta con pagamento in base al consumo in Azure Migrate.
**Criterio di dimensionamento** | Il criterio da usare per dimensionare correttamente le VM per Azure. È possibile eseguire il ridimensionamento in *base alle prestazioni* o ridimensionare le macchine virtuali *in locale*, senza considerare la cronologia delle prestazioni.
**Cronologia delle prestazioni** | Durata da considerare per la valutazione dei dati sulle prestazioni dei computer. Questa proprietà è applicabile solo quando il criterio di dimensionamento è *basato sulle prestazioni*.
**Utilizzo percentile** | Valore percentile dell'esempio di prestazioni da tenere in considerazione per il dimensionamento corretto. Questa proprietà è applicabile solo quando il ridimensionamento è *basato sulle prestazioni*.
**Serie macchina virtuale** |     È possibile specificare la serie di macchine virtuali che si intende tenere in considerazione per il dimensionamento corretto. Se ad esempio si ha un ambiente di produzione di cui non si intende eseguire la migrazione a macchine virtuali serie A in Azure, è possibile escludere la serie A dall'elenco o dalla serie e il dimensionamento viene eseguito solo nella serie selezionata.
**Fattore di comfort** | Azure Migrate server Assessment considera un buffer (fattore di comfort) durante la valutazione. che viene applicato ai dati sull'utilizzo delle VM (CPU, memoria, disco e rete). Il fattore di comfort tiene conto di aspetti come utilizzo stagionale, breve cronologia delle prestazioni e probabile aumento dell'utilizzo futuro.<br/><br/> Da una VM con 10 core e un utilizzo del 20%, ad esempio, si ottiene normalmente una VM con 2 core. Con un fattore di comfort pari a 2.0x, invece, il risultato è una VM con 4 core.
**Offerta** | [Offerta di Azure](https://azure.microsoft.com/support/legal/offer-details/) sottoscritta. Azure Migrate stima il costo di conseguenza.
**Valuta** | Valuta di fatturazione.
**Sconto (%)** | Qualsiasi sconto specifico della sottoscrizione ricevuto oltre all'offerta Azure.<br/> L'impostazione predefinita è 0%.
**Tempo di attività macchina virtuale** | Se non si prevede di eseguire ininterrottamente le macchine virtuali in Azure, è possibile specificare la durata (numero di giorni al mese e numero di ore al giorno) dell'esecuzione. Il costo verrà stimato di conseguenza.<br/> Il valore predefinito è di 31 giorni al mese e di 24 ore al giorno.
**Vantaggio Azure Hybrid** | Specificare se si dispone di Software Assurance e si è idonei per [vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Se il valore è impostato su Sì, alle macchine virtuali Windows si applicano i prezzi di Azure per sistemi non Windows. Il valore predefinito è Yes.

## <a name="how-are-assessments-calculated"></a>Come vengono calcolate le valutazioni?

Una valutazione in Azure Migrate server assessment viene calcolata usando i metadati raccolti sui server locali. Il calcolo della valutazione viene eseguito in tre fasi. per ogni server, il calcolo della valutazione inizia con l'analisi di idoneità di Azure, seguita dal ridimensionamento e infine dalla stima dei costi mensili. Un server si sposta solo in una fase successiva se passa quello precedente. Se ad esempio un server non supera il controllo di idoneità di Azure, viene contrassegnato come non idoneo per Azure e il ridimensionamento e i costi non vengono eseguiti per lo stesso.

## <a name="azure-suitability-analysis"></a>Analisi di idoneità di Azure

Non tutti i computer sono idonei per l'esecuzione in Azure. Azure Migrate server Assessment valuta ogni computer locale per l'idoneità alla migrazione in Azure e categorizza i computer valutati in una delle categorie di idoneità seguenti:
- **Idoneo per Azure**: il computer può essere migrato in Azure così com'è, senza modifiche. Eseguirà l'avvio in Azure con il supporto completo di Azure.
- **Idoneo per Azure con condizioni**: il computer può eseguire l'avvio in Azure, ma non ha il supporto completo di Azure. Ad esempio un computer con una versione precedente del sistema operativo Windows Server non è supportato in Azure. È necessario prestare attenzione prima di eseguire la migrazione di tali computer in Azure e seguire le indicazioni di correzione suggerite nella valutazione per risolvere i problemi di idoneità prima di eseguire la migrazione.
- **Non idoneo per Azure**: il computer non eseguirà l'avvio in Azure. Ad esempio, se un computer locale ha un disco di dimensioni superiore a 64 TB, non può essere ospitato in Azure. È necessario seguire le indicazioni di correzione suggerite nella valutazione per risolvere il problema di idoneità prima della migrazione in Azure. Il calcolo della dimensione e dei costi non viene eseguito per i computer contrassegnati come non idonei per Azure.
- **Conformità sconosciuta** : Azure migrate non è stato possibile trovare la conformità del computer a causa di metadati insufficienti raccolti dall'ambiente locale.

Azure Migrate server Assessment esamina le proprietà del computer e il sistema operativo guest per identificare la conformità di Azure del computer locale.

### <a name="machine-properties"></a>Proprietà del computer

Server Assessment esamina le seguenti proprietà della macchina virtuale locale per identificare se può essere eseguita in Azure.

**Proprietà** | **Dettagli** | **Stato di idoneità per Azure**
--- | --- | ---
**Tipo di avvio** | Azure supporta le VM con il tipo di avvio BIOS e non UEFI. | Idoneo con condizioni se il tipo di avvio è UEFI.
**Core** | Il numero di core nei computer deve essere uguale o inferiore al numero massimo di core supportati per una VM di Azure, ovvero 128.<br/><br/> Se è disponibile la cronologia delle prestazioni, Azure Migrate prende in considerazione per il confronto i core utilizzati. Se nelle impostazioni di valutazione è specificato un fattore di comfort, il numero di core utilizzati viene moltiplicato per questo fattore.<br/><br/> In mancanza di una cronologia delle prestazioni, Azure Migrate usa i core allocati senza applicare il fattore di comfort. | Idoneo se è minore o uguale ai limiti.
**Memoria** | La dimensione della memoria del computer deve essere uguale o inferiore alla memoria massima consentita per una macchina virtuale di Azure, ovvero 3892 GB per standard_M128m&nbsp;<sup>2</sup> di Azure serie M. [Altre informazioni](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)<br/><br/> Se è disponibile la cronologia delle prestazioni, Azure Migrate prende in considerazione per il confronto la memoria utilizzata. Se è specificato un fattore di comfort, la memoria utilizzata viene moltiplicata per questo fattore.<br/><br/> In mancanza di una cronologia, si usa la memoria allocata senza applicare il fattore di comfort.<br/><br/> | Idoneo se rientra nei limiti.
**Disco di archiviazione** | La dimensione allocata di un disco deve essere 32 TB o less.<br/><br/> I dischi collegati al computer non devono essere più di 65, incluso il disco del sistema operativo. | Idoneo se rientra nei limiti.
**Rete** | A un computer non devono essere collegati più di 32 NIC. | Idoneo se rientra nei limiti.

### <a name="guest-operating-system"></a>Sistema operativo guest
Insieme alle proprietà della macchina virtuale, Azure Migrate server Assessment esamina il sistema operativo guest dei computer, per stabilire se può essere eseguito in Azure.

> [!NOTE]
> Per le macchine virtuali VMware, Azure Migrate server Assessment usa il sistema operativo specificato per la macchina virtuale in server vCenter per eseguire l'analisi del sistema operativo guest. Per le macchine virtuali Linux in esecuzione su VMware, attualmente non identifica la versione del kernel esatta del sistema operativo guest.

La logica seguente viene usata da Azure Migrate server assessment per identificare la conformità di Azure in base al sistema operativo.

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
Altri sistemi operativi<br/><br/> Ad esempio, Oracle Solaris, Apple Mac OS e così via, FreeBSD e così via. | Azure non approva questi sistemi operativi. Il computer può eseguire l'avvio in Azure, ma Azure non offre alcun supporto del sistema operativo. | Idoneo per Azure con condizioni, si consiglia di installare un sistema operativo supportato prima della migrazione in Azure.  
Sistema operativo specificato come **Altro** nel server vCenter | In questo caso Azure Migrate non può identificare il sistema operativo. | Idoneità sconosciuta. Verificare che il sistema operativo eseguito nella VM sia supportato in Azure.
Sistemi operativi a 32 bit | Il computer può eseguire l'avvio in Azure, ma Azure non offre il supporto completo. | Idoneo per Azure con condizioni, provare ad aggiornare il sistema operativo della macchina virtuale da 32 bit a 64 bit prima di eseguire la migrazione in Azure.

## <a name="sizing"></a>Ridimensionamento

Dopo che un computer è stato contrassegnato come pronto per Azure, la valutazione del server esegue il ridimensionamento, che implica l'identificazione della VM di Azure e dello SKU del disco appropriati per la macchina virtuale locale. Le indicazioni sulle dimensioni variano a seconda delle proprietà di valutazione specificate.

- Se la valutazione usa il dimensionamento in *base alle prestazioni*, Azure migrate considera la cronologia delle prestazioni del computer per identificare le dimensioni della VM e il tipo di disco in Azure. Questo metodo è particolarmente utile se la macchina virtuale locale è stata allocata, ma l'utilizzo è basso e si vuole ridimensionare la VM in Azure per ridurre i costi. Questo metodo consente di ottimizzare le dimensioni durante la migrazione.
- Se non si vuole prendere in considerazione i dati sulle prestazioni per le dimensioni delle VM e si vuole portare i computer locali così come sono in Azure, è possibile specificare il criterio di ridimensionamento come *in locale* e la valutazione del server ridimensiona le VM in base all'ambiente locale configurazione senza considerare i dati di utilizzo. Il dimensionamento del disco, in questo caso, verrà eseguito in base al tipo di archiviazione specificato nelle proprietà di valutazione (dischi HDD Standard, dischi SDD Standard o dischi Premium).

### <a name="performance-based-sizing"></a>Dimensioni in base alle prestazioni

Per il dimensionamento basato sulle prestazioni, Azure Migrate server Assessment inizia con i dischi collegati alla VM, seguiti dalle schede di rete e quindi esegue il mapping di uno SKU di VM di Azure in base ai requisiti di calcolo della VM locale. Il dispositivo Azure Migrate profilare l'ambiente locale per raccogliere i dati sulle prestazioni per CPU, memoria, dischi e scheda di rete.

**Raccolta dati sulle prestazioni**

- Per le macchine virtuali VMware, l'appliance Azure Migrate raccoglie un punto di esempio in tempo reale a ogni intervallo di 20 secondi, per le macchine virtuali Hyper-V il punto di esempio in tempo reale viene raccolto a ogni intervallo di 30 secondi.
- Il dispositivo esegue quindi il rollup dei punti di esempio raccolti ogni 10 minuti e invia il valore massimo per gli ultimi 10 minuti a Azure Migrate server assessment.
- Azure Migrate server Assessment archivia tutti i punti di campionamento di 10 minuti per l'ultimo mese e, a seconda delle proprietà di valutazione specificate per la *cronologia delle prestazioni* e l' *utilizzo percentile*, identifica il punto dati appropriato da usare per il dimensionamento corretto. Se ad esempio la cronologia delle prestazioni è impostata su un giorno e l'utilizzo percentile è il 95 ° percentile, USA i punti di campionamento di 10 minuti per l'ultimo giorno, li ordina in ordine crescente e sceglie il valore del 95 ° percentile per il corretto dimensionamento.
- Il valore precedente viene quindi moltiplicato per il fattore di comfort per ottenere i dati di utilizzo delle prestazioni effettivi per ogni metrica (utilizzo della CPU, utilizzo della memoria, IOPS del disco (lettura e scrittura), velocità effettiva del disco (lettura e scrittura), velocità effettiva della rete (in uscita e in uscita)) L'appliance raccoglie.
- Una volta identificato il valore di utilizzo effettivo, le dimensioni di calcolo, archiviazione e rete vengono eseguite come indicato di seguito:

**Dimensioni archiviazione**: Azure Migrate tenta di eseguire il mapping di ogni disco collegato alla macchina virtuale con un disco in Azure.

> [!NOTE]
> Azure Migrate: Server Assessment supporta solo dischi gestiti per la valutazione.

  - Vengono aggiunte le operazioni di i/o di lettura e scrittura di un disco per ottenere i valori totali di IOPS necessari, in modo analogo, di lettura e scrittura per ottenere la velocità effettiva totale di ogni disco
  - Se il tipo di archiviazione è stato specificato come automatico, in base ai valori di IOPS e velocità effettiva validi, Azure Migrate server Assessment rileva quindi se è necessario eseguire il mapping del disco a un disco rigido standard, un'unità SSD standard o un disco Premium in Azure. Se il tipo di archiviazione è impostato su HDD Standard/SSD/Premium, tenterà di trovare uno SKU del disco all'interno del tipo di archiviazione selezionato (HDD Standard/SSD/dischi Premium).
  - Se la valutazione del server non riesce a trovare un disco con le operazioni di i/o al secondo necessarie & velocità effettiva, contrassegna il computer come non adatto per Azure.
  - Se trova un set di dischi appropriati, seleziona quelli che supportano il percorso specificato nelle impostazioni di valutazione.
  - Se sono presenti più dischi idonei, viene selezionato quello con il costo più basso.
  - Se i dati sulle prestazioni per qualsiasi disco non sono disponibili, vengono usati i dati di configurazione del disco (dimensioni del disco) per trovare un disco SSD standard in Azure.

**Dimensionamento rete**: Azure Migrate server Assessment tenta di trovare una macchina virtuale di Azure in grado di supportare il numero di schede di rete collegate al computer locale e le prestazioni richieste da queste schede di rete.
    - Per ottenere le prestazioni di rete effettive della VM locale, server Assessment aggrega i dati trasmessi al secondo (MBps) dal computer (rete in uscita), in tutte le schede di rete e applica il fattore di comfort. Questo numero viene usato per trovare una VM di Azure in grado di supportare le prestazioni di rete necessarie.
    - Insieme alle prestazioni di rete considera anche se la VM di Azure può supportare il numero di schede di rete richiesto.
    - Se non sono disponibili dati relativi alle prestazioni di rete, per la determinazione della dimensione della VM viene preso in considerazione solo il numero di schede di rete.

**Dimensionamento calcolo**: Una volta calcolati i requisiti di archiviazione e di rete, Azure Migrate server Assessment considera i requisiti di CPU e memoria per trovare una dimensione di macchina virtuale adatta in Azure.
    - Azure Migrate esamina i core e la memoria usati in modo efficace per trovare le dimensioni appropriate per la macchina virtuale in Azure.
    - Se non si trovano dimensioni adatte, il computer viene contrassegnato come non idoneo per Azure.
    - Se si trovano dimensioni adatte, Azure Migrate esegue i calcoli di archiviazione e di rete. Applica quindi le impostazioni relative alla posizione e al piano tariffario per l'indicazione finale delle dimensioni della macchina virtuale.
    - Se ci sono più dimensioni di VM di Azure idonee, si consiglia di usare quella con il costo più basso.

### <a name="as-on-premises-sizing"></a>Determinazione della dimensione come in locale

Se si usa come *dimensionamento locale*, server Assessment non considera la cronologia delle prestazioni delle VM e dei dischi e ALLOCA uno SKU di VM in Azure in base alla dimensione allocata in locale. Analogamente, per il ridimensionamento del disco, viene esaminato il tipo di archiviazione specificato in proprietà valutazione (HDD Standard/SSD/Premium) e viene consigliato il tipo di disco di conseguenza. Il tipo di archiviazione predefinito è dischi Premium.

## <a name="confidence-ratings"></a>Classificazioni di confidenza
Ogni valutazione basata sulle prestazioni in Azure Migrate è associata a una classificazione di attendibilità che varia da uno (più basso) a cinque partenze (più alta).
- La classificazione di attendibilità viene assegnata a una valutazione in base alla disponibilità dei punti dati necessari per calcolare la valutazione.
- La classificazione di attendibilità di una valutazione aiuta a stimare l'affidabilità delle indicazioni relative alla dimensione fornite da Azure Migrate.
- La classificazione di attendibilità non è applicabile per come valutazioni locali.
- Per il dimensionamento in base alle prestazioni, Azure Migrate server assessment:
    - I dati di utilizzo per la CPU e la memoria della macchina virtuale.
    - Per ogni disco collegato alla macchina virtuale, il servizio deve anche avere i dati relativi alle operazioni di I/O al secondo e alla velocità effettiva del disco.
    - Analogamente, per ogni scheda di rete collegata a una macchina virtuale, la classificazione di attendibilità richiede l'i/O di rete per eseguire il dimensionamento in base alle prestazioni.
    - Se uno dei numeri di utilizzo riportati sopra non è disponibile in server vCenter, è possibile che l'indicazione relativa alle dimensioni non sia affidabile.

Di seguito è indicata la classificazione di attendibilità per la valutazione in base alla percentuale dei punti dati disponibili come riportato di seguito:

   **Disponibilità dei punti dati** | **Classificazione di attendibilità**
   --- | ---
   0%-20% | 1 stella
   21%-40% | 2 stelle
   41%-60% | 3 stelle
   61%-80% | 4 stelle
   81%-100% | 5 stelle

### <a name="low-confidence-ratings"></a>Classificazione con confidenza bassa

Alcuni dei motivi per cui una valutazione potrebbe ottenere una valutazione di bassa confidenza:

- L'ambiente non è stato profilato per la durata di creazione della valutazione. Se, ad esempio, si crea la valutazione con la durata delle prestazioni impostata su un giorno, è necessario attendere almeno un giorno dopo l'avvio dell'individuazione per tutti i punti dati da raccogliere.
- Alcune macchine virtuali sono state arrestate durante il periodo di calcolo della valutazione. Se una macchina virtuale è stata spenta per un certo periodo di tempo, Azure Migrate server Assessment non può raccogliere i dati sulle prestazioni per tale periodo.
- Alcune macchine virtuali sono state create tra il periodo di calcolo della valutazione. Ad esempio, se si crea una valutazione per la cronologia delle prestazioni dell'ultimo mese, ma alcune macchine virtuali sono state create nell'ambiente solo una settimana fa, la cronologia delle prestazioni delle nuove macchine virtuali non sarà disponibile per l'intera durata.

> [!NOTE]
> Se la classificazione di attendibilità di una valutazione è inferiore a cinque stelle, è consigliabile attendere almeno un giorno per l'appliance per profilare l'ambiente e quindi ricalcolare la valutazione. In caso contrario, il dimensionamento in base alle prestazioni potrebbe non essere affidabile ed è consigliabile passare la valutazione da usare come dimensionamento locale.

## <a name="monthly-cost-estimation"></a>Stima dei costi mensili

Una volta completate le indicazioni sul dimensionamento, Azure Migrate calcola i costi di calcolo e di archiviazione per dopo la migrazione.

- **Costo di calcolo**: sulla base delle dimensioni consigliate della macchina virtuale di Azure, Azure Migrate usa l'API di fatturazione per calcolare i costi mensili della macchina virtuale.
    - Per il calcolo vengono prese in considerazione le impostazioni relative al sistema operativo, al programma Software Assurance, alle istanze riservate, al tempo di attività della macchina virtuale, alla posizione e alla valuta.
    - Per calcolare il costo di calcolo mensile totale vengono aggregati i costi di tutti i computer.
- **Costo di archiviazione**: Il costo di archiviazione mensile per un computer viene calcolato aggregando il costo mensile di tutti i dischi collegati al computer
    - Azure Migrate server Assessment calcola i costi totali di archiviazione mensili aggregando i costi di archiviazione di tutti i computer.
    - Attualmente, il calcolo non prende in considerazione le offerte specificate nelle impostazioni di valutazione.

I costi vengono visualizzati nella valuta specificata nelle impostazioni di valutazione.


## <a name="next-steps"></a>Passaggi successivi

Creare una valutazione per le macchine virtuali [VMware](tutorial-assess-vmware.md) o [Hyper-V](tutorial-assess-hyper-v.md).
