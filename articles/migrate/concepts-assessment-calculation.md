---
title: Valutazioni nella valutazione del server di migrazione di AzureAssessments in Azure Migrate Server Assessment
description: Informazioni sulle valutazioni in Azure Migrate Server Assessment
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 2f76ea5f195be2914cdcdb4de9e93af38504d66e
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81769930"
---
# <a name="assessments-in-azure-migrate-server-assessment"></a>Valutazioni in Azure Migrate: valutazione del server

Questo articolo offre una panoramica delle valutazioni nello strumento [Azure Migrate: Server Assessment.This](migrate-services-overview.md#azure-migrate-server-assessment-tool) article provides an overview of assessments in the Azure Migrate: Server Assessment tool. Lo strumento può valutare le macchine virtuali VMware locali, le macchine virtuali Hyper-V e i server fisici per la migrazione ad Azure.The tool can assess on-premises VMware virtual machines, Hyper-VV VMs, and physical servers for migration to Azure.

## <a name="whats-an-assessment"></a>Cos'è una valutazione?

Una valutazione con lo strumento di valutazione dei server misura la conformità e stima l'effetto della migrazione dei server locali in Azure.An assessment with the Server Assessment tool measures the readiness and estimates the effect of migrating on-premises servers to Azure.

> [!NOTE]
> In Azure per enti pubblici esaminare i percorsi di valutazione [delle destinazione supportati.](migrate-support-matrix.md#supported-geographies-azure-government) Si noti che i consigli sulle dimensioni delle macchine virtuali nelle valutazioni utilizzeranno la serie VM in modo specifico per le aree di Government Cloud.Note that VM size recommendations in assessments will use the VM series specifically for Government Cloud regions. [Altre informazioni](https://azure.microsoft.com/global-infrastructure/services/?regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia&products=virtual-machines) sui tipi di macchine virtuali.

## <a name="types-of-assessments"></a>Tipi di valutazioni

Le valutazioni create con la valutazione del server sono uno snapshot temporizzato dei dati. Server Assessment fornisce due tipi di valutazioni.

**Tipo di valutazione** | **Dettagli** | **Dati**
--- | --- | ---
**Basata sulle prestazioni** | Valutazioni che formulano raccomandazioni in base ai dati sulle prestazioni raccolti | La raccomandazione sulle dimensioni della macchina virtuale si basa sui dati relativi all'utilizzo della CPU e della RAM.<br/><br/> La raccomandazione del tipo di disco si basa sulle operazioni di input/output al secondo (IOPS) e sulla velocità effettiva dei dischi locali. I tipi di disco sono dischi hdD standard di Azure, SSD standard di Azure e Azure Premium.Disk types are Azure Standard HDD, Azure Standard SSD, and Azure Premium disks.
**Così com'è locale** | Valutazioni che non utilizzano i dati sulle prestazioni per formulare raccomandazioni | La raccomandazione per le dimensioni della macchina virtuale si basa sulle dimensioni della macchina virtuale locale.<br/><br> Il tipo di disco consigliato si basa sul tipo di archiviazione selezionato per la valutazione.

## <a name="how-do-i-run-an-assessment"></a>Come si esegue una valutazione?

Ci sono un paio di modi per eseguire una valutazione.

- Valutare i computer usando i metadati del server raccolti da un'appliance Azure Migrate leggera. L'appliance individua i computer locali. Invia quindi i metadati del computer e i dati sulle prestazioni ad Azure Migrate.It then sends machine metadata and performance data to Azure Migrate.
- Valutare i computer utilizzando i metadati del server importati in un formato CSV (Valori delimitati da virgole).

## <a name="how-do-i-assess-with-the-appliance"></a>Come posso valutare con l'apparecchio?

Se si distribuisce un'appliance di Azure Migrate per individuare i server locali, eseguire la procedura seguente:If you're deploying an Azure Migrate appliance to discover on-premises servers, do the following steps:

1. Configurare Azure e l'ambiente locale per l'utilizzo con la valutazione del server.
1. Per la prima valutazione, creare un progetto azure e aggiungervi lo strumento di valutazione del server.
1. Distribuire un'appliance di Azure Migrate leggera. L'appliance individua continuamente i computer locali e invia i metadati e i dati sulle prestazioni del computer ad Azure Migrate.The appliance continuously discovers on-premises machines and sends machine metadata and performance data to Azure Migrate. Distribuire l'appliance come macchina virtuale o macchina fisica. Non è necessario installare nulla sulle macchine che si desidera valutare.

Dopo che l'appliance inizia l'individuazione del computer, è possibile raccogliere le macchine che si desidera valutare in un gruppo ed eseguire una valutazione per il gruppo.

Seguire le esercitazioni per [VMware](tutorial-prepare-vmware.md), [Hyper-V](tutorial-prepare-hyper-v.md)o [server fisici](tutorial-prepare-physical.md) per provare questi passaggi.

## <a name="how-do-i-assess-with-imported-data"></a>Come posso valutare i dati importati?

Se si valutano i server utilizzando un file CSV, non è necessario un accessorio. Eseguire invece le operazioni seguenti:

1. Configurare Azure per l'utilizzo con la valutazione del server.
1. Per la prima valutazione, creare un progetto azure e aggiungervi lo strumento di valutazione del server.
1. Scaricare un modello CSV e aggiungervi i dati del server.
1. Importare il modello in Valutazione server.
1. Individuare i server aggiunti con l'importazione, raccoglierli in un gruppo ed eseguire una valutazione per il gruppo.

## <a name="what-data-does-the-appliance-collect"></a>Quali dati raccoglie l'apparecchio?

Se si usa l'appliance Azure Migrate per la valutazione, vedere i metadati e i dati sulle prestazioni raccolti per [VMware](migrate-appliance.md#collected-data---vmware) e [Hyper-V.](migrate-appliance.md#collected-data---hyper-v)

## <a name="how-does-the-appliance-calculate-performance-data"></a>In che modo l'appliance calcola i dati sulle prestazioni?

Se si utilizza l'appliance per l'individuazione, vengono raccolti i dati sulle prestazioni per le impostazioni di calcolo con la procedura seguente:If you use the appliance for discovery, it collects performance data for compute settings with these steps:

1. L'apparecchio raccoglie un punto campione in tempo reale.

    - **VMware VMs**: Un punto campione viene raccolto ogni 20 secondi.
    - **Macchine virtuali Hyper-V:** viene raccolto un punto campione ogni 30 secondi.
    - **Server fisici**: viene raccolto un punto campione ogni cinque minuti.

1. L'appliance combina i punti di esempio per creare un singolo punto dati ogni 10 minuti. Per creare il punto dati, l'appliance seleziona i valori di picco da tutti i campioni. Invia quindi il punto dati ad Azure.It then sends the data point to Azure.
1. Server Assessment archivia tutti i punti dati di 10 minuti dell'ultimo mese.
1. Quando si crea una valutazione, Server Assessment identifica il punto dati appropriato da utilizzare per la rightsizzazione. L'identificazione si basa sui valori percentili per *la cronologia delle prestazioni* e *l'utilizzo percentile.*

    - Ad esempio, se la cronologia delle prestazioni è di una settimana e l'utilizzo percentile è il 95esimo percentile, Server Assessment ordina i punti campione di 10 minuti per l'ultima settimana. Li ordina in ordine crescente e sceglie il valore del 95esimo percentile per la rightsizzazione.
    - Il valore del 95esimo percentile assicura di ignorare tutti gli outlier, che potrebbero essere inclusi se si seleziona il 99o percentile.
    - Se si desidera selezionare l'utilizzo massimo per il periodo e non si desidera perdere alcun outlier, selezionare il 99esimo percentile per l'utilizzo percentile.

1. Questo valore viene moltiplicato per il fattore di comfort per ottenere i dati di utilizzo delle prestazioni efficaci per queste metriche raccolte dall'appliance:This value is multiplied for the comfort factor to get the effective performance utilization data for these metrics that the appliance collects:

    - Uso della CPU
    - Utilizzo della RAM
    - Operazioni di I/O al secondo su disco (lettura e scrittura)
    - Velocità effettiva del disco (lettura e scrittura)Disk throughput (read and write)
    - Velocità effettiva di rete (in estrai)Network throughput (in and out)

## <a name="how-are-assessments-calculated"></a>Come vengono calcolate le valutazioni?

Server Assessment utilizza i metadati e i dati sulle prestazioni dei computer locali per calcolare le valutazioni. Se si distribuisce l'appliance di Azure Migrate, la valutazione usa i dati raccolti dall'appliance. Tuttavia, se si esegue una valutazione importata utilizzando un file CSV, si forniscono i metadati per il calcolo.

I calcoli si verificano in queste tre fasi:

1. **Calcolare la conformità**di Azure: valutare se i computer sono adatti per la migrazione ad Azure.Calculate Azure readiness : Assess whether machines are suitable for migration to Azure.
1. **Calcolare le raccomandazioni**di dimensionamento : Stimare il calcolo, l'archiviazione e il dimensionamento della rete.
1. **Calcolare i costi mensili:** calcolare i costi di calcolo e archiviazione mensili stimati per l'esecuzione delle macchine in Azure dopo la migrazione.

I calcoli sono nell'ordine precedente. Un server del computer passa a una fase successiva solo se passa quella precedente. For example, if a server fails the Azure readiness stage, it's marked as unsuitable for Azure. I calcoli di dimensionamento e costo non vengono eseguiti per tale server.

## <a name="whats-in-an-assessment"></a>Elementi inclusi nella valutazione

Ecco cosa è incluso in una valutazione in Server Assessment:

Proprietà | Dettagli
--- | ---
**Posizione di destinazione** | Percorso in cui si desidera eseguire la migrazione. La valutazione del server supporta attualmente queste aree di Azure di destinazione:Server Assessment currently supports these target Azure regions:<br/><br/> Australia Orientale, Australia Sud-Est, Brasile Sud, Canada Centrale, Canada Orientale, India centrale, Stati Uniti centrali, Cina orientale, Cina settentrionale, Asia orientale, Stati Uniti orientali 2, Germania centrale, Germania nord-est, Giappone orientale, Giappone occidentale, Corea centrale, Corea del Sud, Stati Uniti centrali, Nord Europa, Stati Uniti centro-meridionali, Sud-est asiatico, India meridionale, Regno Unito, Regno Unito occidentale, US Gov Arizona, US Gov Texas, US Gov Texas , Stati Uniti centro-occidentali, Europa occidentale, India occidentale, Stati Uniti occidentali e Stati Uniti occidentali 2.
**Disco di archiviazione di destinazione (come è il dimensionamento)** | Tipo di disco da usare per l'archiviazione in Azure.The type of disk to use for storage in Azure. <br/><br/> Specificare il disco di archiviazione di destinazione come gestito da SSD Standard, Standard gestito da Premium o standard gestito dall'HDD.
**Disco di archiviazione di destinazione (dimensioni basate sulle prestazioni)Target storage disk (performance-based sizing)** | Specifica il tipo di disco di archiviazione di destinazione come automatico, gestito da Premium, gestito da HDD standard o gestito sSD standard.<br/><br/> **Automatico**: La raccomandazione del disco si basa sui dati sulle prestazioni dei dischi, ovvero le operazioni di I/O al secondo e la velocità effettiva.<br/><br/>**Premium o Standard**: La valutazione consiglia uno SKU del disco all'interno del tipo di archiviazione selezionato.<br/><br/> Se si vuole un contratto di servizio (SLA) VM a istanza singola del 99,9%, è consigliabile usare dischi gestiti da Premium.If you want a single-instance VM service-level agreement (SLA) of 99.9%, consider using Premium-managed disks. Questo utilizzo garantisce che tutti i dischi nella valutazione siano consigliati come dischi gestiti da Premium.This use ensures that all disks in the assessment are recommended as Premium-managed disks.<br/><br/> Azure Migrate supporta solo dischi gestiti per la valutazione della migrazione.
**Istanze di macchina virtuale riservata di AzureAzure Reserved Virtual Machine Instances** | Specifica le [istanze riservate](https://azure.microsoft.com/pricing/reserved-vm-instances/) in modo che le stime dei costi nella valutazione ne sedano.<br/><br/> Azure Migrate supporta attualmente le istanze di macchine virtuali riservate di Azure solo per le offerte con pagamento in base al pagamento.
**Criteri di dimensionamento** | Utilizzato per ridimensionare a destra la macchina virtuale di Azure.Used to rightsize the Azure VM.<br/><br/> Utilizzare il dimensionamento così com'è o il ridimensionamento basato sulle prestazioni.
**Cronologia delle prestazioni** | Utilizzato con ridimensionamento basato sulle prestazioni. La cronologia delle prestazioni specifica la durata utilizzata quando vengono valutati i dati sulle prestazioni.
**Utilizzo percentile** | Utilizzato con ridimensionamento basato sulle prestazioni. L'utilizzo del percentile specifica il valore percentile dell'esempio di prestazioni utilizzato per la rightsizzazione.
**Serie macchina virtuale** | La serie di macchine virtuali di Azure che si vuole prendere in considerazione per la rightsizzazione. Ad esempio, se non si dispone di un ambiente di produzione che richiede macchine virtuali serie A in Azure, è possibile escludere A-series dall'elenco delle serie.
**Fattore di comfort** | Buffer utilizzato durante la valutazione. Viene applicato ai dati di utilizzo della CPU, della RAM, del disco e della rete per le macchine virtuali. Tiene conto di problemi come l'utilizzo stagionale, la breve cronologia delle prestazioni e i probabili aumenti nell'utilizzo futuro.<br/><br/> Ad esempio, una macchina virtuale a 10 core con un utilizzo del 20% in genere genera una macchina virtuale a due core. Con un fattore di comfort pari a 2.0, il risultato è invece una macchina virtuale a quattro core.
**Offerta** | Offerta [di Azure](https://azure.microsoft.com/support/legal/offer-details/) in cui si è iscritti. Valutazione server stima il costo per l'offerta.
**Valuta** | La valuta di fatturazione per il tuo account.
**Sconto (%)** | Eventuali sconti specifici della sottoscrizione ricevuti in cima all'offerta di Azure.Any subscription-specific discounts you receive on top of the Azure offer. L'impostazione predefinita è 0%.
**Tempo di attività macchina virtuale** | Durata in giorni al mese e ore al giorno per le macchine virtuali di Azure che non verranno eseguite continuamente. Le stime dei costi si basano su tale durata.<br/><br/> I valori predefiniti sono 31 giorni al mese e 24 ore al giorno.
**Vantaggio Azure Hybrid** | Specifica se si dispone della garanzia del software e si sono idonei per il [vantaggio Azure Hybrid.](https://azure.microsoft.com/pricing/hybrid-use-benefit/) Se l'impostazione ha il valore predefinito "Sì", i prezzi di Azure per i sistemi operativi diversi da Windows vengono considerati per le macchine virtuali Windows.If the setting has the default value "Yes," Azure prices for operating systems other than Windows are considered for Windows VMs.

[Esaminare le procedure consigliate](best-practices-assessment.md) per la creazione di una valutazione con la valutazione del server.

## <a name="calculate-readiness"></a>Calcolare la prontezza

Non tutti i computer sono adatti per l'esecuzione in Azure.Not all machines are suitable to run in Azure. Server Assessment valuta tutti i computer locali e assegna loro una categoria di preparazione.

- **Pronto per Azure:** è possibile eseguire la migrazione del computer così com'è in Azure senza alcuna modifica. Verrà avviato in Azure con il supporto completo di Azure.It will start in Azure with full Azure support.
- **Condizionalmente pronto per Azure:** il computer potrebbe avviarsi in Azure ma non con il supporto completo di Azure.Conditionally ready for Azure : The machine might start in Azure but might not have full Azure support. Ad esempio, Azure non supporta un computer che esegue una versione precedente di Windows Server.For example, Azure doesn't support a machine that's running an old version of Windows Server. You must be careful before you migrate these machines to Azure. Per risolvere eventuali problemi di conformità, seguire le indicazioni di correzione suggerite dalla valutazione.
- **Non pronto per Azure:** il computer non verrà avviato in Azure.Not ready for Azure: The machine won't start in Azure. Ad esempio, se il disco di un computer locale archivia più di 64 TB, Azure non può ospitare il computer. Seguire le indicazioni per la correzione per risolvere il problema prima della migrazione.
- **Readiness unknown**: Azure Migrate non è in grado di determinare la disponibilità del computer a causa di metadati insufficienti.

Per calcolare la preparazione, Server Assessment esamina le proprietà del computer e le impostazioni del sistema operativo riepilogate nelle tabelle seguenti.

### <a name="machine-properties"></a>Proprietà del computer

Server Assessment reviews the following properties of an on-premises VM to determine whether it can run on Azure.

Proprietà | Dettagli | Stato di idoneità per Azure
--- | --- | ---
**Tipo di avvio** | Azure supporta le macchine virtuali con un tipo di avvio di BIOS, non UEFI. | Pronto in modo condizionale se il tipo di avvio è UEFI
**Core** | Ogni computer non deve avere più di 128 core, ovvero il numero massimo supportato da una macchina virtuale di Azure.Each machine must have no more than 128 cores, which is the maximum number an Azure VM supports.<br/><br/> Se è disponibile la cronologia delle prestazioni, Azure Migrate prende in considerazione per il confronto i core utilizzati. Se le impostazioni di valutazione specificano un fattore di comfort, il numero di core utilizzati viene moltiplicato per il fattore di comfort.<br/><br/> Se non è presente alcuna cronologia delle prestazioni, Azure Migrate usa i core allocati senza applicare il fattore di comfort. | Pronto se il numero di core è entro il limite
**Ram** | Ogni computer non deve avere più di 3.892 GB di RAM,&nbsp;ovvero la dimensione massima supportata da una serie M di Azure Standard_M128m<sup>2</sup> macchine virtuali. [Altre informazioni](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)<br/><br/> Se la cronologia delle prestazioni è disponibile, Azure Migrate considera la RAM utilizzata per il confronto. Se viene specificato un fattore di comfort, la RAM utilizzata viene moltiplicata per il fattore di comfort.<br/><br/> Se non c'è storia, la RAM allocata viene utilizzata senza l'applicazione di un fattore di comfort.<br/><br/> | Pronto se la quantità di RAM è entro il limite
**Disco di archiviazione** | La dimensione allocata di un disco non deve essere superiore a 32 TB. Anche se Azure supporta dischi da 64 TB con dischi SSD di Azure ultra, Azure Migrate: Server Assessment attualmente controlla 32 TB come limite di dimensioni del disco perché non supporta ancora Ultra SSD. <br/><br/> Il numero di dischi collegati alla macchina, incluso il disco del sistema operativo, deve essere pari o inferiore a 65. | Pronto se le dimensioni e il numero del disco rientrano nei limiti
**Rete** | A una macchina non devono essere collegate più di 32 interfacce di rete (NIC). | Pronto se il numero di schede di interfaccia di rete è entro il limite

### <a name="guest-operating-system"></a>Sistema operativo guest

Oltre a esaminare le proprietà delle macchine virtuali, la valutazione del server esamina il sistema operativo guest di un computer per determinare se può essere eseguita in Azure.Along with reviewing VM properties, Server Assessment looks at the guest operating system of a machine to determine whether it can run on Azure.

> [!NOTE]
> Per gestire l'analisi guest per le macchine virtuali VMware, Server Assessment utilizza il sistema operativo specificato per la macchina virtuale in vCenter Server. Per le macchine virtuali Linux in esecuzione su VMware, Server Assessment attualmente non identifica la versione kernel del sistema operativo guest.

Valutazione del server usa la logica seguente per identificare la disponibilità di Azure in base al sistema operativo:Server Assessment uses the following logic to identify Azure readiness based on the operating system:

**Sistema operativo** | **Dettagli** | **Stato di idoneità per Azure**
--- | --- | ---
Windows Server 2016 e tutti i Service Pack | Azure offre supporto completo. | Pronto per Azure.
Windows Server 2012 R2 e tutti i Service Pack | Azure offre supporto completo. | Pronto per Azure.
Windows Server 2012 e tutti i Service Pack | Azure offre supporto completo. | Pronto per Azure.
Windows Server 2008 R2 con tutti i Service Pack | Azure offre supporto completo.| Pronto per Azure.
Windows Server 2008 (32 bit e 64 bit) | Azure offre supporto completo. | Pronto per Azure.
Windows Server 2003 e Windows Server 2003 R2 | Questi sistemi operativi hanno superato le date di fine del supporto e necessitano di un contratto di supporto personalizzato (CSA) per il supporto in Azure.These operating systems have passed their end-of-support dates and need a [Custom Support Agreement (CSA)](https://aka.ms/WSosstatement) for support in Azure. | Condizionalmente pronto per Azure.Conditionally ready for Azure. Valutare la possibilità di aggiornare il sistema operativo prima di eseguire la migrazione ad Azure.Consider upgrading the OS before migrating to Azure.
Windows 2000, Windows 98, Windows 95, Windows NT, Windows 3.1 e MS-DOS | Questi sistemi operativi hanno superato le date di fine supporto. Il computer potrebbe avviarsi in Azure, ma Azure non fornisce alcun supporto del sistema operativo. | Condizionalmente pronto per Azure.Conditionally ready for Azure. È consigliabile aggiornare il sistema operativo prima di eseguire la migrazione ad Azure.We recommend that you upgrade the OS before migrating to Azure.
Windows 7, Windows 8 e Windows 10 | Azure fornisce supporto solo con una sottoscrizione di Visual Studio.Azure provides support with a [Visual Studio subscription only.](https://docs.microsoft.com/azure/virtual-machines/windows/client-images) | Condizionalmente pronto per Azure.Conditionally ready for Azure.
Windows 10 Pro | Azure fornisce supporto con [i diritti di hosting multi-tenant.](https://docs.microsoft.com/azure/virtual-machines/windows/windows-desktop-multitenant-hosting-deployment) | Condizionalmente pronto per Azure.Conditionally ready for Azure.
Windows Vista e Windows XP Professional | Questi sistemi operativi hanno superato le date di fine supporto. Il computer potrebbe avviarsi in Azure, ma Azure non fornisce alcun supporto del sistema operativo. | Condizionalmente pronto per Azure.Conditionally ready for Azure. È consigliabile aggiornare il sistema operativo prima di eseguire la migrazione ad Azure.We recommend that you upgrade the OS before migrating to Azure.
Linux | Vedere i sistemi operativi Linux approvati da Azure.See the [Linux operating systems](../virtual-machines/linux/endorsed-distros.md) that Azure endorses. Altri sistemi operativi Linux potrebbero avviarsi in Azure.Other Linux operating systems might start in Azure. Tuttavia, è consigliabile aggiornare il sistema operativo a una versione approvata prima di eseguire la migrazione ad Azure.But we recommend that you upgrade the OS to an endorsed version before you migrate to Azure. | Idoneo per Azure se la versione è approvata.<br/><br/>Condizionalmente pronto se la versione non è approvata.
Altri sistemi operativi come Oracle Solaris, Apple macOS e FreeBSD | Azure non approva questi sistemi operativi. Il computer potrebbe avviarsi in Azure, ma Azure non fornisce alcun supporto del sistema operativo. | Condizionalmente pronto per Azure.Conditionally ready for Azure. È consigliabile installare un sistema operativo supportato prima di eseguire la migrazione ad Azure.We recommend that you install a supported OS before migrating to Azure.  
Sistema operativo specificato come **Altro** nel server vCenter | Azure Migrate non è in grado di identificare il sistema operativo in questo caso. | Idoneità sconosciuta. Verificare che Azure supporti il sistema operativo in esecuzione all'interno della macchina virtuale.
Sistemi operativi a 32 bit | Il computer potrebbe avviarsi in Azure, ma Azure potrebbe non fornire supporto completo. | Condizionalmente pronto per Azure.Conditionally ready for Azure. Prendere in considerazione l'aggiornamento a un sistema operativo a 64 bit prima di eseguire la migrazione ad Azure.Consider upgrading to a 64-bit OS before migrating to Azure.

## <a name="calculating-sizing"></a>Calcolo del dimensionamento

Dopo che la macchina è contrassegnata come pronta per Azure, la valutazione del server fornisce suggerimenti per il ridimensionamento. Questi consigli identificano la macchina virtuale di Azure e lo SKU del disco. I calcoli di dimensionamento dipendono dal fatto che si utilizzi il dimensionamento locale o il dimensionamento basato sulle prestazioni.

### <a name="calculate-sizing-as-is-on-premises"></a>Calcolare il ridimensionamento (così come è locale)

 Se si usa il dimensionamento locale così come lo si utilizza, La valutazione del server non considera la cronologia delle prestazioni delle macchine virtuali e dei dischi.

- **Dimensioni di calcolo:** la valutazione del server alloca uno SKU di VM di Azure in base alle dimensioni allocate in locale.
- **Dimensioni su disco e archiviazione**: Valutazione server esamina il tipo di archiviazione specificato nelle proprietà di valutazione e consiglia il tipo di disco appropriato. I tipi di archiviazione possibili sono HDD standard, SSD standard e Premium. Il tipo di archiviazione predefinito è Premium.The default storage type is Premium.
- **Dimensionamento di rete:** Valutazione server considera la scheda di rete nel computer locale.

### <a name="calculate-sizing-performance-based"></a>Calcolare il dimensionamento (basato sulle prestazioni)

Se si utilizza il dimensionamento basato sulle prestazioni, Server Assessment fornisce i suggerimenti per il dimensionamento nel modo seguente:

- Server Assessment considers the performance history of the machine to identify the VM size and disk type in Azure.
- Se si importano server utilizzando un file CSV, vengono utilizzati i valori specificati. Questo metodo è particolarmente utile se è stato sovrpartito la macchina locale, l'utilizzo è basso e si vuole ridimensionare correttamente la macchina virtuale di Azure per risparmiare sui costi.
- Se non si desidera utilizzare i dati sulle prestazioni, reimpostare i criteri di ridimensionamento in modo che sia locale, come descritto nella sezione precedente.

#### <a name="calculate-storage-sizing"></a>Calcolare il dimensionamento dello storage

Per il dimensionamento dell'archiviazione, Azure Migrate tenta di eseguire il mapping di ogni disco collegato al computer a un disco di Azure.For storage sizing, Azure Migrate tries to map each disk that is attached to the machine to an Azure disk. Il dimensionamento funziona come segue:

1. Server Assessment aggiunge le operazioni di I/O al secondo di lettura e scrittura di un disco per ottenere le operazioni di I/O al secondo totali necessarie. Analogamente, aggiunge i valori di velocità effettiva di lettura e scrittura per ottenere la velocità effettiva totale di ogni disco.
1. Se il tipo di archiviazione è stato specificato come automatico, il tipo selezionato si basa sui valori di IOPS e velocità effettiva effettivi. Server Assessment determines whether to map the disk to a Standard HDD, Standard SSD, or Premium disk in Azure. Se il tipo di archiviazione è impostato su uno di questi tipi di disco, Server Assessment tenta di trovare uno SKU del disco all'interno del tipo di archiviazione selezionato.
1. I dischi vengono selezionati come segue:
    - Se la valutazione del server non riesce a trovare un disco con le operazioni di I/O al secondo e la velocità effettiva richieste, contrassegna il computer come non adatto ad Azure.If Server Assessment can't find a disk with the required IOPS and throughput, it marks the machine as unsuitable for Azure.
    - Se Server Assessment trova un set di dischi adatti, seleziona i dischi che supportano il percorso specificato nelle impostazioni di valutazione.
    - Se sono presenti più dischi idonei, Server Assessment seleziona il disco con il costo più basso.
    - Se i dati sulle prestazioni per qualsiasi disco non sono disponibili, la dimensione del disco di configurazione viene usata per trovare un disco SSD standard in Azure.If performance data for any disk is unavailable, the configuration disk size is used to find a Standard SSD disk in Azure.

#### <a name="calculate-network-sizing"></a>Calcolare il dimensionamento della rete

Server Assessment tenta di trovare una macchina virtuale di Azure che supporta il numero e le prestazioni richieste delle schede di rete collegate al computer locale.

- Per ottenere prestazioni di rete effettive della macchina virtuale locale, Server Assessment aggrega la velocità di trasmissione dei dati dal computer (uscita dalla rete) in tutte le schede di rete. Applica quindi il fattore comfort. Usa il valore risultante per trovare una macchina virtuale di Azure in grado di supportare le prestazioni di rete necessarie.
- Oltre alle prestazioni di rete, Server Assessment considera anche se la macchina virtuale di Azure può supportare il numero richiesto di schede di rete.
- Se i dati sulle prestazioni di rete non sono disponibili, Server Assessment considera solo il numero di schede di rete per il dimensionamento delle macchine virtuali.

#### <a name="calculate-compute-sizing"></a>Calcolare il dimensionamento del calcolo

Dopo aver calcolato i requisiti di archiviazione e di rete, Server Assessment considera i requisiti di CPU e RAM per trovare una dimensione di macchina virtuale adatta in Azure.After it calculates storage and network requirements, Server Assessment considers CPU and RAM requirements to find a suitable VM size in Azure.

- Azure Migrate esamina i core e la RAM utilizzati effettivamente per trovare una dimensione di macchina virtuale di Azure adatta.
- Se non si trovano dimensioni adatte, il computer viene contrassegnato come non idoneo per Azure.
- Se si trovano dimensioni adatte, Azure Migrate esegue i calcoli di archiviazione e di rete. Applica quindi le impostazioni di posizione e livello di prezzo per la raccomandazione finale sulle dimensioni della macchina virtuale.
- Se ci sono più dimensioni di VM di Azure idonee, si consiglia di usare quella con il costo più basso.

## <a name="confidence-ratings-performance-based"></a>Classificazioni di attendibilità (basate sulle prestazioni)

Ogni valutazione basata sulle prestazioni in Azure Migrate è associata a una valutazione di attendibilità. La valutazione varia da una (più bassa) a cinque stelle (più alte). La classificazione di attendibilità consente di stimare l'affidabilità delle dimensioni fornite da Azure Migrate.The confidence rating helps you estimate the reliability of the size recommendations Azure Migrate provides.

- La classificazione di confidenza viene assegnata a una valutazione. La classificazione si basa sulla disponibilità dei punti dati necessari per calcolare la valutazione.
- Per il dimensionamento basato sulle prestazioni, la valutazione del server deve:For performance-based sizing, Server Assessment needs:
    - Dati di utilizzo per CPU e RAM VM.
    - Le operazioni di I/O al secondo del disco e i dati di velocità effettiva per ogni disco collegato alla macchina virtuale.
    - I/O di rete per gestire il ridimensionamento basato sulle prestazioni per ogni scheda di rete collegata a una macchina virtuale.

Se uno qualsiasi di questi numeri di utilizzo non è disponibile, i consigli sulle dimensioni potrebbero essere inaffidabili.

> [!NOTE]
> Le classificazioni di attendibilità non vengono assegnate ai server valutati utilizzando un file CSV importato. Anche le valutazioni non sono applicabili per la valutazione locale così com'è.

### <a name="ratings"></a>Classificazioni

Questa tabella mostra le valutazioni di fiducia della valutazione, che dipendono dalla percentuale di punti dati disponibili:

   **Disponibilità dei punti dati** | **Classificazione di attendibilità**
   --- | ---
   0-20% | 1 stella
   21-40% | 2 stelle
   41-60% | 3 stelle
   61-80% | 4 stelle
   81-100% | 5 stelle

### <a name="low-confidence-ratings"></a>Valutazioni di fiducia basse

Ecco alcuni motivi per cui una valutazione potrebbe ottenere un punteggio di confidenza basso:

- L'ambiente non è stato profilato per la durata per cui si sta creando la valutazione. Ad esempio, se si crea la valutazione con la durata delle prestazioni impostata su un giorno, è necessario attendere almeno un giorno dopo aver avviato l'individuazione per tutti i punti dati da raccogliere.
- Alcune macchine virtuali sono state arrestate durante il periodo per il quale è stata calcolata la valutazione. Se le macchine virtuali vengono disattivate per un certo periodo di tempo, la valutazione del server non è in grado di raccogliere i dati sulle prestazioni per tale periodo.
- Alcune macchine virtuali sono state create durante il periodo per il quale è stata calcolata la valutazione. Ad esempio, si supponga di aver creato una valutazione per la cronologia delle prestazioni dell'ultimo mese, ma alcune macchine virtuali sono state create solo una settimana fa. La cronologia delle prestazioni delle nuove macchine virtuali non esisterà per la durata completa.

> [!NOTE]
> Se la classificazione di attendibilità di una valutazione è inferiore a cinque stelle, è consigliabile attendere almeno un giorno affinché l'appliance presenti l'ambiente e quindi ricalcolare la valutazione. In caso contrario, il ridimensionamento basato sulle prestazioni potrebbe essere inaffidabile. In tal caso, è consigliabile passare alla valutazione locale.

## <a name="calculate-monthly-costs"></a>Calcolare i costi mensili

Al termine delle raccomandazioni per il dimensionamento, Azure Migrate calcola i costi di calcolo e archiviazione per dopo la migrazione.

- **Costo di calcolo:** Azure Migrate usa le dimensioni consigliate della macchina virtuale di Azure e l'API di fatturazione di Azure per calcolare il costo mensile per la macchina virtuale.Compute cost : Azure Migrate uses the recommended Azure VM size and the Azure Billing API to calculate the monthly cost for the VM.

    Il calcolo tiene conto di:
    - Sistema operativo
    - Garanzia del software
    - Istanze riservate
    - Tempo di attività macchina virtuale
    - Location
    - Impostazioni valuta

    Server Assessment aggrega il costo in tutti i computer per calcolare il costo di calcolo mensile totale.

- **Costo di**archiviazione : Il costo di archiviazione mensile per una macchina viene calcolato aggregando il costo mensile di tutti i dischi collegati alla macchina.

    Server Assessment calcola i costi totali di archiviazione mensili aggregando i costi di archiviazione di tutti i computer. Attualmente, il calcolo non considera le offerte specificate nelle impostazioni di valutazione.

I costi vengono visualizzati nella valuta specificata nelle impostazioni di valutazione.

## <a name="next-steps"></a>Passaggi successivi

[Esaminare le](best-practices-assessment.md) procedure consigliate per la creazione di valutazioni. 

- Informazioni sull'esecuzione di valutazioni per [macchine virtuali VMware,](tutorial-prepare-vmware.md) [macchine virtuali Hyper-V](tutorial-prepare-hyper-v.md)e [server fisici.](tutorial-prepare-physical.md)
- Informazioni sulla valutazione dei server [importati con un file CSV.](tutorial-assess-import.md)
- Informazioni sull'impostazione della visualizzazione delle [dipendenze](concepts-dependency-visualization.md).
