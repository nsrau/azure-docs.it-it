---
title: Valutazioni delle VM di Azure in Azure Migrate server Assessment
description: Informazioni sulle valutazioni in Azure Migrate server Assessment
ms.topic: conceptual
ms.date: 05/27/2020
ms.openlocfilehash: 4020df3ef77e4b8ae0618108f539322092b93079
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91275524"
---
# <a name="server-assessment-overview-migrate-to-azure-vms"></a>Panoramica della valutazione del server (migrazione alle macchine virtuali di Azure)

In questo articolo viene fornita una panoramica delle valutazioni dello strumento [Azure migrate: server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) . Lo strumento può valutare le macchine virtuali VMware locali, le VM Hyper-V e i server fisici per la migrazione ad Azure.

## <a name="whats-an-assessment"></a>Che cos'è una valutazione?

Una valutazione con lo strumento Server Assessment consente di misurare la conformità e stimare l'effetto della migrazione dei server locali in Azure.

> [!NOTE]
> In Azure per enti pubblici esaminare i percorsi di valutazione di [destinazione supportati](migrate-support-matrix.md#supported-geographies-azure-government) . Si noti che le indicazioni sulle dimensioni delle VM nelle valutazioni useranno la serie di macchine virtuali in modo specifico per le aree cloud governative. [Altre](https://azure.microsoft.com/global-infrastructure/services/?regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia&products=virtual-machines) informazioni sui tipi di VM.

## <a name="types-of-assessments"></a>Tipi di valutazioni

È possibile creare due tipi di valutazioni con Azure Migrate: Valutazione server.

**Tipo di valutazione** | **Dettagli**
--- | --- 
**Macchina virtuale di Azure** | Valutazioni per la migrazione dei server locali in macchine virtuali di Azure. <br/><br/> È possibile valutare le [macchine virtuali VMware](how-to-set-up-appliance-vmware.md) locali, le [VM Hyper-V](how-to-set-up-appliance-hyper-v.md) e i [server fisici](how-to-set-up-appliance-physical.md) per la migrazione ad Azure usando questo tipo di valutazione.
**Soluzione Azure VMware** | Valutazioni per la migrazione dei server locali nella [soluzione Azure VMware](../azure-vmware/introduction.md). <br/><br/> È possibile valutare le [macchine virtuali VMware](how-to-set-up-appliance-vmware.md) locali per la migrazione alla soluzione Azure VMware usando questo tipo di valutazione. [Altre informazioni](concepts-azure-vmware-solution-assessment-calculation.md)

Le valutazioni create con server assessment sono uno snapshot temporizzato dei dati. Una valutazione delle VM di Azure in server Assessment offre due opzioni di criteri di ridimensionamento:

**Tipo di valutazione** | **Dettagli** | **Dati**
--- | --- | ---
**Basata sulle prestazioni** | Valutazioni che fanno raccomandazioni in base ai dati sulle prestazioni raccolti | Le indicazioni sulle dimensioni della macchina virtuale sono basate sui dati di utilizzo della CPU e della RAM.<br/><br/> L'indicazione del tipo di disco è basata sulle operazioni di input/output al secondo (IOPS) e sulla velocità effettiva dei dischi locali. I tipi di disco sono Azure HDD Standard, Azure SDD Standard e dischi Premium di Azure.
**Come in locale** | Valutazioni che non utilizzano dati sulle prestazioni per apportare raccomandazioni | Le indicazioni sulle dimensioni della macchina virtuale sono basate sulle dimensioni della macchina virtuale locale.<br/><br> Il tipo di disco consigliato è basato sul tipo di archiviazione selezionato per la valutazione.

## <a name="how-do-i-run-an-assessment"></a>Ricerca per categorie eseguire una valutazione?

Esistono due modi per eseguire una valutazione.

- Valutare i computer usando i metadati del server raccolti da un'appliance Azure Migrate leggera. L'appliance individua i computer locali. Invia quindi i dati sulle prestazioni e i metadati del computer a Azure Migrate.
- Valutare i computer usando i metadati del server importati in un formato con valori delimitati da virgole (CSV).

## <a name="how-do-i-assess-with-the-appliance"></a>Ricerca per categorie valutare con l'appliance?

Se si sta distribuendo un'appliance Azure Migrate per individuare i server locali, seguire questa procedura:

1. Configurare Azure e l'ambiente locale per lavorare con la valutazione del server.
1. Per la prima valutazione, creare un progetto Azure e aggiungervi lo strumento Server assessment.
1. Distribuire un'appliance Azure Migrate leggera. L'appliance individua continuamente computer locali e invia i dati sulle prestazioni e i metadati del computer a Azure Migrate. Distribuire l'appliance come macchina virtuale o computer fisico. Non è necessario installare alcun elemento nei computer che si desidera valutare.

Quando l'appliance inizia l'individuazione del computer, è possibile raccogliere i computer da valutare in un gruppo ed eseguire una valutazione per il gruppo con tipo di valutazione **VM di Azure**.

Per provare questa procedura, seguire le esercitazioni per [VMware](tutorial-prepare-vmware.md), [Hyper-V](tutorial-prepare-hyper-v.md)o [server fisici](tutorial-prepare-physical.md) .

## <a name="how-do-i-assess-with-imported-data"></a>Ricerca per categorie valutare con i dati importati?

Se si sta valutando i server usando un file CSV, non è necessario un dispositivo. Eseguire invece i passaggi seguenti:

1. Configurare Azure per l'uso con la valutazione del server.
1. Per la prima valutazione, creare un progetto Azure e aggiungervi lo strumento Server assessment.
1. Scaricare un modello CSV e aggiungervi i dati del server.
1. Importare il modello in server assessment.
1. Individuare i server aggiunti con l'importazione, raccoglierli in un gruppo ed eseguire una valutazione per il gruppo con tipo di valutazione **VM di Azure**.

## <a name="what-data-does-the-appliance-collect"></a>Quali dati vengono raccolti dal dispositivo?

Se si usa Azure Migrate Appliance per la valutazione, informazioni sui metadati e i dati sulle prestazioni raccolti per [VMware](migrate-appliance.md#collected-data---vmware) e [Hyper-V](migrate-appliance.md#collected-data---hyper-v).

## <a name="how-does-the-appliance-calculate-performance-data"></a>In che modo l'appliance calcola i dati sulle prestazioni?

Se si usa il dispositivo per l'individuazione, raccoglie i dati sulle prestazioni per le impostazioni di calcolo con i passaggi seguenti:

1. L'appliance raccoglie un punto di esempio in tempo reale.

    - **VM VMware**: un punto di esempio viene raccolto ogni 20 secondi.
    - **Macchine virtuali Hyper-V**: un punto di esempio viene raccolto ogni 30 secondi.
    - **Server fisici**: un punto di esempio viene raccolto ogni cinque minuti.

1. L'Appliance combina i punti di esempio per creare un singolo punto dati ogni 10 minuti per i server VMware e Hyper-V e ogni 5 minuti per i server fisici. Per creare il punto dati, l'appliance seleziona i valori di picco di tutti gli esempi. Invia quindi il punto dati ad Azure.
1. Server Assessment archivia tutti i punti dati di 10 minuti per l'ultimo mese.
1. Quando si crea una valutazione, server Assessment identifica il punto dati appropriato da usare per rightsizing. L'identificazione si basa sui valori percentile per la *cronologia delle prestazioni* e l' *utilizzo percentile*.

    - Se ad esempio la cronologia delle prestazioni è di una settimana e l'utilizzo percentile è il 95 ° percentile, server Assessment Ordina i punti di campionamento di 10 minuti per l'ultima settimana. Li ordina in ordine crescente e sceglie il valore del 95 ° percentile per rightsizing.
    - Il valore 95 ° percentile consente di ignorare eventuali outlier, che potrebbero essere inclusi se è stato selezionato il 99 ° percentile.
    - Se si vuole selezionare il picco di utilizzo per il periodo e non si vogliono perdere gli outlier, selezionare il 99 ° percentile per l'utilizzo percentile.

1. Questo valore viene moltiplicato per il fattore di comfort per ottenere i dati di utilizzo delle prestazioni effettivi per le metriche raccolte dal dispositivo:

    - Uso della CPU
    - Utilizzo RAM
    - IOPS del disco (lettura e scrittura)
    - Velocità effettiva del disco (lettura e scrittura)
    - Velocità effettiva di rete (in e in uscita)

## <a name="how-are-azure-vm-assessments-calculated"></a>Come vengono calcolate le valutazioni delle VM di Azure?

Server Assessment usa i metadati e i dati sulle prestazioni dei computer locali per calcolare le valutazioni. Se si distribuisce il dispositivo Azure Migrate, la valutazione usa i dati raccolti dal dispositivo. Tuttavia, se si esegue una valutazione importata utilizzando un file CSV, è necessario fornire i metadati per il calcolo.

I calcoli si verificano in queste tre fasi:

1. **Calcolo della conformità di Azure**: valutare se i computer sono idonei per la migrazione ad Azure.
1. **Calcolare le indicazioni sul dimensionamento**: stima delle dimensioni di calcolo, archiviazione e rete.
1. **Calcolare i costi mensili**: calcolare i costi di calcolo e archiviazione mensili stimati per l'esecuzione delle macchine in Azure dopo la migrazione.

I calcoli sono nell'ordine precedente. Un server del computer si sposta in una fase successiva solo se passa quello precedente. Ad esempio, se un server ha esito negativo, la fase di preparazione di Azure viene contrassegnata come non adatta per Azure. Il dimensionamento e i calcoli dei costi non vengono eseguiti per quel server.

## <a name="whats-in-an-azure-vm-assessment"></a>Che cosa è in una valutazione di VM di Azure?

Ecco cosa è incluso in una valutazione di VM di Azure in server assessment:

**Proprietà** | **Dettagli**
--- | ---
**Posizione di destinazione** | Percorso in cui si desidera eseguire la migrazione. Server Assessment supporta attualmente le aree di Azure di destinazione seguenti:<br/><br/> Australia orientale, Australia sudorientale, Brasile meridionale, Canada centrale, Canada orientale, India centrale, Stati Uniti centrali, Cina orientale, Cina settentrionale, Asia orientale, Stati Uniti orientali, Stati Uniti orientali 2, Germania centrale, Germania nordorientale, Giappone orientale, Giappone occidentale, Corea centrale, Corea meridionale, Stati Uniti centro-settentrionali, Europa settentrionale, Stati Uniti centro-meridionali, Asia sudorientale, Regno Unito occidentale Regno Unito meridionale India meridionale , Stati Uniti centro-occidentali, Europa occidentale, India occidentale, Stati Uniti occidentali e Stati Uniti occidentali 2.
**Disco di archiviazione di destinazione (ridimensionamento)** | Tipo di disco da usare per l'archiviazione in Azure. <br/><br/> Specificare il disco di archiviazione di destinazione come gestito da Premium, gestito da SDD Standard o gestito da HDD Standard.
**Disco di archiviazione di destinazione (dimensionamento basato sulle prestazioni)** | Specifica il tipo di disco di archiviazione di destinazione come automatico, gestito da Premium, gestito da HDD Standard o gestito da SDD Standard.<br/><br/> **Automatico**: l'indicazione del disco è basata sui dati sulle prestazioni dei dischi, ovvero IOPS e velocità effettiva.<br/><br/>**Premium o standard**: la valutazione consiglia uno SKU del disco all'interno del tipo di archiviazione selezionato.<br/><br/> Se si desidera un contratto di servizio (SLA) di VM a istanza singola pari al 99,9%, è consigliabile utilizzare dischi gestiti Premium. Questo uso garantisce che tutti i dischi della valutazione siano consigliati come dischi gestiti Premium.<br/><br/> Azure Migrate supporta solo Managed disks per la valutazione della migrazione.
**Istanze di VM riservate di Azure** | Specifica le [istanze riservate](https://azure.microsoft.com/pricing/reserved-vm-instances/) in modo che le stime dei costi nella valutazione li prendano in considerazione.<br/><br/> Quando si seleziona ' istanze riservate ',' discount (%)' e le proprietà' tempo di esecuzione VM ' non sono applicabili.<br/><br/> Azure Migrate supporta attualmente le istanze di VM riservate di Azure solo per le offerte con pagamento in base al consumo.
**Criteri di dimensionamento** | Usato per dimensionare correttamente la macchina virtuale di Azure.<br/><br/> Usare il ridimensionamento o il dimensionamento in base alle prestazioni.
**Cronologia delle prestazioni** | Utilizzato con il dimensionamento basato sulle prestazioni. Cronologia prestazioni specifica la durata utilizzata per la valutazione dei dati sulle prestazioni.
**Utilizzo percentile** | Utilizzato con il dimensionamento basato sulle prestazioni. L'utilizzo percentile specifica il valore percentile dell'esempio di prestazioni utilizzato per rightsizing.
**Serie VM** | Serie di VM di Azure che si vuole considerare per rightsizing. Se, ad esempio, non si ha un ambiente di produzione in cui sono richieste le macchine virtuali della serie A in Azure, si può escludere la serie A dall'elenco delle serie.
**Fattore di comfort** | Buffer utilizzato durante la valutazione. Viene applicato ai dati della CPU, della RAM, del disco e della rete per le macchine virtuali. In questo modo vengono rilevati problemi come l'utilizzo stagionale, la cronologia delle prestazioni brevi e probabilmente aumenti nell'utilizzo futuro.<br/><br/> Ad esempio, una macchina virtuale con 10 core con utilizzo del 20% normalmente produce una macchina virtuale a due core. Con un fattore di comfort di 2,0, il risultato è una macchina virtuale a quattro core.
**Offerta** | [Offerta di Azure](https://azure.microsoft.com/support/legal/offer-details/) in cui si è iscritti. Valutazione server stima il costo di tale offerta.
**Valuta** | Valuta di fatturazione per l'account.
**Sconto (%)** | Tutti gli sconti specifici della sottoscrizione ricevuti dall'offerta di Azure. L'impostazione predefinita è 0%.
**Tempo di attività macchina virtuale** | Durata in giorni al mese e ore al giorno per le macchine virtuali di Azure che non vengono eseguite in modo continuo. Le stime dei costi sono basate su tale durata.<br/><br/> I valori predefiniti sono 31 giorni al mese e 24 ore al giorno.
**Vantaggio Azure Hybrid** | Specifica se il Software Assurance è idoneo per [vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Se per l'impostazione è impostato il valore predefinito "Sì", per le macchine virtuali Windows vengono considerati i prezzi di Azure per sistemi operativi diversi da Windows.
**Sottoscrizione EA** | Specifica che una sottoscrizione di Enterprise Agreement (EA) viene utilizzata per la stima dei costi. Prende in considerazione lo sconto applicabile alla sottoscrizione. <br/><br/> Lascia le impostazioni per le istanze riservate, sconto (%) e le proprietà del tempo di esecuzione della macchina virtuale con le impostazioni predefinite.


[Esaminare le procedure consigliate per la](best-practices-assessment.md) creazione di una valutazione con server assessment.

## <a name="calculate-readiness"></a>Calcola conformità

Non tutti i computer sono idonei per l'esecuzione in Azure. Una valutazione di VM di Azure valuta tutti i computer locali e assegna loro una categoria di conformità.

- **Pronto per Azure**: il computer può essere migrato così com'è in Azure senza alcuna modifica. Verrà avviato in Azure con il supporto completo di Azure.
- Idoneo **per Azure in modo condizionale**: il computer potrebbe iniziare in Azure, ma potrebbe non avere il supporto completo di Azure. Azure, ad esempio, non supporta un computer in cui è in esecuzione una versione precedente di Windows Server. Prima di eseguire la migrazione di questi computer ad Azure, è necessario prestare attenzione. Per risolvere eventuali problemi di conformità, seguire le indicazioni di correzione suggerite dalla valutazione.
- **Non pronto per Azure**: il computer non verrà avviato in Azure. Se, ad esempio, un disco di un computer locale archivia più di 64 TB, Azure non può ospitare il computer. Seguire le indicazioni di correzione per risolvere il problema prima della migrazione.
- **Conformità sconosciuta**: Azure migrate non è in grado di determinare la conformità del computer a causa di metadati insufficienti.

Per calcolare la conformità, server Assessment esamina le proprietà del computer e le impostazioni del sistema operativo riepilogate nelle tabelle seguenti.

### <a name="machine-properties"></a>Proprietà del computer

Per una valutazione delle VM di Azure, server Assessment esamina le seguenti proprietà di una macchina virtuale locale per determinare se può essere eseguita in macchine virtuali di Azure.

Proprietà | Dettagli | Stato di idoneità per Azure
--- | --- | ---
**Tipo di avvio** | Azure supporta le VM con un tipo di avvio BIOS e non UEFI. | Pronto in modo condizionale se il tipo di avvio è UEFI
**Core** | Ogni computer non deve avere più di 128 core, ovvero il numero massimo supportato da una macchina virtuale di Azure.<br/><br/> Se è disponibile la cronologia delle prestazioni, Azure Migrate prende in considerazione per il confronto i core utilizzati. Se le impostazioni di valutazione specificano un fattore di comfort, il numero di core utilizzati viene moltiplicato per il fattore di comfort.<br/><br/> Se non è presente alcuna cronologia delle prestazioni, Azure Migrate usa i core allocati per applicare il fattore di comfort. | Pronto se il numero di core rientra nel limite
**RAM** | Ogni computer non deve avere più di 3.892 GB di RAM, ovvero la dimensione massima supportata da una VM di Azure M Standard_M128m &nbsp; <sup>2</sup> . [Altre informazioni](../virtual-machines/sizes.md)<br/><br/> Se è disponibile la cronologia delle prestazioni, Azure Migrate considera la RAM utilizzata per il confronto. Se viene specificato un fattore di comfort, la RAM utilizzata viene moltiplicata per il fattore di comfort.<br/><br/> Se non è presente alcuna cronologia, viene usata la RAM allocata per applicare un fattore di comfort.<br/><br/> | Pronto se la quantità di RAM è entro il limite
**Disco di archiviazione** | Le dimensioni allocate di un disco non devono superare 32 TB. Sebbene Azure supporti i dischi da 64 TB con i dischi Ultra SSD di Azure, Azure Migrate: la valutazione del server attualmente verifica la presenza di 32 TB come limite delle dimensioni del disco, perché non supporta ancora Ultra SSD. <br/><br/> Il numero di dischi collegati al computer, incluso il disco del sistema operativo, deve essere 65 o un numero inferiore. | Pronto se il numero e le dimensioni del disco sono entro i limiti
**Rete** | Un computer non deve avere più di 32 interfacce di rete (NIC) connesse. | Pronto se il numero di schede di rete rientra nel limite

### <a name="guest-operating-system"></a>Sistema operativo guest

Per una valutazione delle VM di Azure, insieme alla revisione delle proprietà delle macchine virtuali, la valutazione del server esamina il sistema operativo guest di un computer per determinare se può essere eseguito in Azure.

> [!NOTE]
> Per gestire l'analisi dei guest per le macchine virtuali VMware, server Assessment usa il sistema operativo specificato per la macchina virtuale in server vCenter. Tuttavia, server vCenter non fornisce la versione del kernel per i sistemi operativi delle macchine virtuali Linux. Per individuare la versione, è necessario configurare l' [individuazione delle applicazioni](./how-to-discover-applications.md). Quindi, l'appliance individua le informazioni sulla versione usando le credenziali Guest specificate quando si configura app-Discovery.


Server Assessment usa la logica seguente per identificare la conformità di Azure in base al sistema operativo:

**Sistema operativo** | **Dettagli** | **Stato di idoneità per Azure**
--- | --- | ---
Windows Server 2016 e tutti i Service Pack | Azure offre supporto completo. | Pronto per Azure.
Windows Server 2012 R2 e tutti i Service Pack | Azure offre supporto completo. | Pronto per Azure.
Windows Server 2012 e tutti i Service Pack | Azure offre supporto completo. | Pronto per Azure.
Windows Server 2008 R2 con tutti i Service Pack | Azure offre supporto completo.| Pronto per Azure.
Windows Server 2008 (32 bit e 64 bit) | Azure offre supporto completo. | Pronto per Azure.
Windows Server 2003 e Windows Server 2003 R2 | Questi sistemi operativi hanno superato le date di fine del supporto e necessitano di un [contratto di supporto personalizzato (CSA)](https://aka.ms/WSosstatement) per il supporto in Azure. | Pronto per Azure in modo condizionale. Provare ad aggiornare il sistema operativo prima di eseguire la migrazione ad Azure.
Windows 2000, Windows 98, Windows 95, Windows NT, Windows 3,1 e MS-DOS | Questi sistemi operativi hanno superato le date di fine del supporto. Il computer potrebbe essere avviato in Azure, ma Azure non offre alcun supporto del sistema operativo. | Pronto per Azure in modo condizionale. Si consiglia di aggiornare il sistema operativo prima di eseguire la migrazione ad Azure.
Windows 7, Windows 8 e Windows 10 | Azure fornisce il supporto solo per una [sottoscrizione di Visual Studio.](../virtual-machines/windows/client-images.md) | Pronto per Azure in modo condizionale.
Windows 10 Pro | Azure fornisce supporto con [i diritti di hosting multi-tenant.](../virtual-machines/windows/windows-desktop-multitenant-hosting-deployment.md) | Pronto per Azure in modo condizionale.
Windows Vista e Windows XP Professional | Questi sistemi operativi hanno superato le date di fine del supporto. Il computer potrebbe essere avviato in Azure, ma Azure non offre alcun supporto del sistema operativo. | Pronto per Azure in modo condizionale. Si consiglia di aggiornare il sistema operativo prima di eseguire la migrazione ad Azure.
Linux | Vedere i [sistemi operativi Linux](../virtual-machines/linux/endorsed-distros.md) approvati da Azure. Altri sistemi operativi Linux potrebbero avviarsi in Azure. È tuttavia consigliabile aggiornare il sistema operativo a una versione approvata prima di eseguire la migrazione ad Azure. | Idoneo per Azure se la versione è approvata.<br/><br/>Pronto in modo condizionale se la versione non è approvata.
Altri sistemi operativi come Oracle Solaris, Apple macOS e FreeBSD | Azure non approva questi sistemi operativi. Il computer potrebbe essere avviato in Azure, ma Azure non offre alcun supporto del sistema operativo. | Pronto per Azure in modo condizionale. Prima di eseguire la migrazione ad Azure, è consigliabile installare un sistema operativo supportato.  
Sistema operativo specificato come **Altro** nel server vCenter | In questo caso Azure Migrate non è in grado di identificare il sistema operativo. | Idoneità sconosciuta. Assicurarsi che Azure supporti il sistema operativo in esecuzione all'interno della macchina virtuale.
Sistemi operativi a 32 bit | Il computer potrebbe essere avviato in Azure, ma Azure potrebbe non fornire supporto completo. | Pronto per Azure in modo condizionale. Provare a eseguire l'aggiornamento a un sistema operativo a 64 bit prima di eseguire la migrazione ad Azure.

## <a name="calculating-sizing"></a>Calcolo del dimensionamento

Dopo che il computer è stato contrassegnato come pronto per Azure, la valutazione del server apporta consigli sul dimensionamento nella valutazione delle VM di Azure. Questi consigli identificano la macchina virtuale di Azure e lo SKU del disco. I calcoli di ridimensionamento variano a seconda che si stia usando il dimensionamento locale o basato sulle prestazioni.

### <a name="calculate-sizing-as-is-on-premises"></a>Calcolo delle dimensioni (così come sono in locale)

 Se si usa il dimensionamento in locale, server Assessment non considera la cronologia delle prestazioni delle VM e dei dischi nella valutazione delle VM di Azure.

- **Dimensionamento del calcolo**: server Assessment ALLOCA uno SKU di VM di Azure in base alla dimensione allocata in locale.
- **Dimensionamento del disco e archiviazione**: la valutazione del server esamina il tipo di archiviazione specificato nelle proprietà di valutazione e consiglia il tipo di disco appropriato. I tipi di archiviazione possibili sono HDD Standard, SDD Standard e Premium. Il tipo di archiviazione predefinito è Premium.
- **Dimensionamento della rete**: server Assessment considera la scheda di rete nel computer locale.

### <a name="calculate-sizing-performance-based"></a>Calcolare il dimensionamento (basato sulle prestazioni)

Se si usa il dimensionamento basato sulle prestazioni in una valutazione di VM di Azure, server Assessment apporta le indicazioni di dimensionamento come segue:

- Server Assessment considera la cronologia delle prestazioni del computer per identificare le dimensioni della macchina virtuale e il tipo di disco in Azure.
- Se si importano server utilizzando un file CSV, verranno utilizzati i valori specificati. Questo metodo è particolarmente utile se il computer locale è stato sovraallocato, l'utilizzo è basso e si vuole dimensionare correttamente la VM di Azure per ridurre i costi.
- Se non si vogliono usare i dati sulle prestazioni, reimpostare i criteri di ridimensionamento su così come sono in locale, come descritto nella sezione precedente.

#### <a name="calculate-storage-sizing"></a>Calcolare il dimensionamento dell'archiviazione

Per il ridimensionamento dell'archiviazione in una valutazione di VM di Azure, Azure Migrate tenta di eseguire il mapping di ogni disco collegato al computer a un disco di Azure. Il dimensionamento funziona nel modo seguente:

1. Server Assessment aggiunge le operazioni di i/o in lettura e scrittura di un disco per ottenere il totale di IOPS necessari. Analogamente, vengono aggiunti i valori di velocità effettiva di lettura e scrittura per ottenere la velocità effettiva totale di ogni disco. Nel caso di valutazioni basate sull'importazione, è possibile specificare i IOPS totali, la velocità effettiva totale e il numero totale. di dischi nel file importato senza specificare singole impostazioni del disco. In tal caso, le dimensioni del disco singolo vengono ignorate e i dati forniti vengono usati direttamente per calcolare il dimensionamento e selezionare uno SKU di VM appropriato.

1. Se il tipo di archiviazione è stato specificato come automatico, il tipo selezionato è basato sui valori di IOPS e velocità effettiva effettivi. Server Assessment consente di determinare se eseguire il mapping del disco a un disco HDD Standard, SDD Standard o Premium in Azure. Se il tipo di archiviazione è impostato su uno di questi tipi di dischi, la valutazione del server tenterà di trovare uno SKU del disco all'interno del tipo di archiviazione selezionato.
1. I dischi sono selezionati come segue:
    - Se Server Assessment non riesce a trovare un disco con i IOPS e la velocità effettiva necessari, contrassegna il computer come non idoneo per Azure.
    - Se Server Assessment rileva un set di dischi appropriati, seleziona i dischi che supportano il percorso specificato nelle impostazioni di valutazione.
    - Se sono presenti più dischi idonei, la valutazione del server seleziona il disco con il costo più basso.
    - Se i dati sulle prestazioni per qualsiasi disco non sono disponibili, le dimensioni del disco di configurazione vengono usate per trovare un disco SDD Standard in Azure.

#### <a name="calculate-network-sizing"></a>Calcolare il dimensionamento della rete

Per una valutazione delle VM di Azure, server Assessment tenta di trovare una macchina virtuale di Azure che supporta il numero e le prestazioni richieste delle schede di rete collegate al computer locale.

- Per ottenere le prestazioni di rete effettive della VM locale, server Assessment aggrega la velocità di trasmissione dei dati fuori dal computer (rete in uscita) in tutte le schede di rete. Viene quindi applicato il fattore di comfort. Usa il valore risultante per trovare una macchina virtuale di Azure in grado di supportare le prestazioni di rete necessarie.
- Oltre alle prestazioni di rete, valutazione server considera anche se la macchina virtuale di Azure può supportare il numero di schede di rete necessario.
- Se i dati sulle prestazioni di rete non sono disponibili, valutazione server considera solo il numero di schede di rete per il dimensionamento delle VM.

#### <a name="calculate-compute-sizing"></a>Calcolare il dimensionamento del calcolo

Dopo aver calcolato i requisiti di archiviazione e di rete, valutazione server considera i requisiti della CPU e della RAM per individuare le dimensioni appropriate della macchina virtuale in Azure.

- Azure Migrate esamina i core e la RAM usati in modo efficace per trovare le dimensioni appropriate per la macchina virtuale di Azure.
- Se non si trovano dimensioni adatte, il computer viene contrassegnato come non idoneo per Azure.
- Se si trovano dimensioni adatte, Azure Migrate esegue i calcoli di archiviazione e di rete. Applica quindi le impostazioni relative a posizione e livello di prezzo per la raccomandazione finale sulle dimensioni della macchina virtuale.
- Se ci sono più dimensioni di VM di Azure idonee, si consiglia di usare quella con il costo più basso.

## <a name="confidence-ratings-performance-based"></a>Classificazioni di confidenza (basate sulle prestazioni)

Ogni valutazione delle VM di Azure basata sulle prestazioni in Azure Migrate è associata a una classificazione di attendibilità. La classificazione varia da uno (più basso) a cinque stelle (più alta). La classificazione di attendibilità consente di stimare l'affidabilità delle indicazioni relative alle dimensioni Azure Migrate fornisce.

- La classificazione di attendibilità viene assegnata a una valutazione. La valutazione è basata sulla disponibilità dei punti dati necessari per calcolare la valutazione.
- Per il dimensionamento basato sulle prestazioni, è necessario disporre di una valutazione del server:
    - Dati di utilizzo per la CPU e la RAM della macchina virtuale.
    - I dati di IOPS e velocità effettiva del disco per ogni disco collegato alla VM.
    - I/O di rete per gestire il dimensionamento in base alle prestazioni per ogni scheda di rete collegata a una macchina virtuale.

Se uno di questi numeri di utilizzo non è disponibile, le indicazioni sulle dimensioni potrebbero non essere affidabili.

> [!NOTE]
> Le classificazioni di attendibilità non vengono assegnate per i server valutati con un file CSV importato. Anche le classificazioni non sono applicabili per la valutazione locale.

### <a name="ratings"></a>Classificazioni

Questa tabella mostra le classificazioni di attendibilità della valutazione, che dipendono dalla percentuale di punti dati disponibili:

   **Disponibilità dei punti dati** | **Classificazione di attendibilità**
   --- | ---
   0-20% | 1 stella
   21-40% | 2 stelle
   41-60% | 3 stelle
   61-80% | 4 stelle
   81-100% | 5 stelle

### <a name="low-confidence-ratings"></a>Classificazione con confidenza bassa

Ecco alcuni motivi per cui una valutazione potrebbe ottenere un livello di confidenza basso:

- L'ambiente non è stato profilato per la durata per la quale si sta creando la valutazione. Se, ad esempio, si crea la valutazione con la durata delle prestazioni impostata su un giorno, è necessario attendere almeno un giorno dopo l'avvio dell'individuazione per tutti i punti dati da raccogliere.
- Alcune macchine virtuali sono state arrestate durante il periodo in cui è stata calcolata la valutazione. Se una macchina virtuale viene spenta per un periodo di tempo specifico, server Assessment non può raccogliere i dati sulle prestazioni per tale periodo.
- Alcune macchine virtuali sono state create durante il periodo in cui è stata calcolata la valutazione. Si supponga, ad esempio, che sia stata creata una valutazione per la cronologia delle prestazioni dell'ultimo mese, ma che alcune macchine virtuali siano state create solo una settimana fa. La cronologia delle prestazioni delle nuove macchine virtuali non esisterà per la durata completa.

> [!NOTE]
> Se la classificazione di attendibilità di una valutazione è inferiore a cinque stelle, è consigliabile attendere almeno un giorno per l'appliance per profilare l'ambiente e quindi ricalcolare la valutazione. In caso contrario, il dimensionamento in base alle prestazioni potrebbe non essere affidabile. In tal caso, è consigliabile passare la valutazione al dimensionamento locale.

## <a name="calculate-monthly-costs"></a>Calcola i costi mensili

Una volta completate le indicazioni sul dimensionamento, una valutazione delle VM di Azure in Azure Migrate calcola i costi di calcolo e di archiviazione per dopo la migrazione.

- **Costo di calcolo**: Azure migrate usa le dimensioni consigliate per le macchine virtuali di Azure e l'API di fatturazione di Azure per calcolare il costo mensile per la macchina virtuale.

    Il calcolo prende in considerazione quanto segue:
    - Sistema operativo
    - Software Assurance
    - Istanze riservate
    - Tempo di attività macchina virtuale
    - Location
    - Impostazioni di valuta

    Server Assessment aggrega il costo in tutti i computer per calcolare il costo totale di calcolo mensile.

- **Costo di archiviazione**: il costo mensile di archiviazione per un computer viene calcolato aggregando il costo mensile di tutti i dischi collegati al computer.

    Server Assessment calcola i costi totali di archiviazione mensili aggregando i costi di archiviazione di tutti i computer. Attualmente, il calcolo non prende in considerazione le offerte specificate nelle impostazioni di valutazione.

I costi vengono visualizzati nella valuta specificata nelle impostazioni di valutazione.

## <a name="next-steps"></a>Passaggi successivi

[Rivedere](best-practices-assessment.md) le procedure consigliate per la creazione di valutazioni. 

- Informazioni sull'esecuzione di valutazioni per le [macchine virtuali VMware](tutorial-prepare-vmware.md), le [VM Hyper-V](tutorial-prepare-hyper-v.md)e i [server fisici](tutorial-prepare-physical.md).
- Informazioni sulla valutazione dei server [importati con un file CSV](tutorial-assess-import.md).
- Informazioni sulla configurazione della [visualizzazione delle dipendenze](concepts-dependency-visualization.md).