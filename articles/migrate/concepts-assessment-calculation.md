---
title: Valutazioni nella valutazione del server di migrazione di AzureAssessments in Azure Migrate Server Assessment
description: Informazioni sulle valutazioni in Azure Migrate Server Assessment
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: ae55686f0152d9c2b170ae1b34d7493ed7ac8d94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127783"
---
# <a name="assessments-in-azure-migrateserver-assessment"></a>Valutazioni in Azure Migrate:Server Assessment

Questo articolo offre una panoramica delle valutazioni nello strumento [Azure Migrate:Server Assessment.This](migrate-services-overview.md#azure-migrate-server-assessment-tool) article provides an overview of assessments in the Azure Migrate:Server Assessment tool. Lo strumento di valutazione del server può valutare le macchine virtuali VMware locali, le macchine virtuali Hyper-V e i server fisici per la migrazione ad Azure.The Server Assessment tool can assess on-premises VMware VMs, Hyper-VV VMs, and physical servers, for migration to Azure.

## <a name="whats-an-assessment"></a>Cos'è una valutazione?

Una valutazione con lo strumento di valutazione dei server misura la disponibilità e stima l'impatto della migrazione dei server locali in Azure.An assessment with the Server Assessment tool measures the readiness, and estimates the impact, of migrating on-premises servers to Azure.

## <a name="types-of-assessments"></a>Tipi di valutazioni

Le valutazioni create con la valutazione del server sono uno snapshot temporizzato dei dati. Server Assessment fornisce due tipi di valutazioni.

**Tipo di valutazione** | **Dettagli** | **Dati**
--- | --- | ---
**Basata sulle prestazioni** | Valutazioni che formulano raccomandazioni in base ai dati sulle prestazioni raccolti | La raccomandazione sulle dimensioni della macchina virtuale si basa sui dati di utilizzo della CPU e della memoria.<br/><br/> Il tipo di disco (disco rigido/SSD standard o dischi gestiti da Premium) si basa sulle operazioni di I/O al secondo e sulla velocità effettiva dei dischi locali.
**Così com'è locale** | Valutazioni che non utilizzano i dati sulle prestazioni per formulare raccomandazioni. | Il consiglio per le dimensioni delle macchine virtuali si basa sulle dimensioni della macchina virtuale locale<br/><br> Il tipo di disco consigliato si basa sul tipo di archiviazione selezionato per la valutazione.

## <a name="how-do-i-run-an-assessment"></a>Come si esegue una valutazione?

Esistono un paio di modi per eseguire una valutazione:There are a couple of ways to run an assessment:

- Valutare i computer usando i metadati del server raccolti da un'appliance Azure Migrate leggera. L'appliance individua i computer locali e invia i metadati/i dati sulle prestazioni del computer ad Azure Migrate.The appliance discovers on-premises machines, and sends machine metadata/performance data to Azure Migrate.
- Valutare i computer utilizzando i metadati del server importati in un formato CSV (Valori delimitati da virgole).

## <a name="how-do-i-assess-with-the-appliance"></a>Come posso valutare con l'apparecchio?

Se si distribuisce un'appliance di Azure Migrate per individuare i server locali, eseguire le operazioni seguenti:If you're deploying an Azure Migrate appliance to discover on-premises servers, you do the following:

1. Configurare Azure e l'ambiente locale per l'utilizzo con la valutazione del Server.You set up Azure and your on-premises environment to work with Server Assessment.
2. Per la prima valutazione, si crea un progetto Azure e vi si aggiunge lo strumento di valutazione del server.
3. Distribuire un'appliance di Azure Migrate leggera. L'appliance individua continuamente i computer locali e invia i metadati e i dati sulle prestazioni del computer ad Azure Migrate.The appliance continuously discovers on-premises machines, and sends machine metadata and performance data to Azure Migrate. L'appliance viene distribuita come macchina virtuale o macchina fisica. Non c'è bisogno di installare nulla sulle macchine che si desidera valutare.
4. Dopo che l'appliance inizia l'individuazione del computer, è possibile raccogliere i computer che si desidera valutare in un gruppo ed eseguire una valutazione per il gruppo.

È possibile seguire le esercitazioni per [VMware,](tutorial-prepare-vmware.md) [Hyper-V](tutorial-prepare-hyper-v.md)o [server fisici](tutorial-prepare-physical.md) per provare questi passaggi.

## <a name="how-do-i-assess-with-imported-data"></a>Come posso valutare i dati importati?

Se si valutano i server utilizzando un file CSV, non è necessario un accessorio. Al contrario, eseguire le operazioni seguenti:

1. Configurare Azure per l'utilizzo con la valutazione del Server.You set up Azure to work with Server Assessment.
2. Per la prima valutazione, si crea un progetto Azure e vi si aggiunge lo strumento di valutazione del server.
3. Si scarica un modello CSV e aggiungervi i dati del server.
4. Importare il modello in Valutazione Server.
5. È possibile individuare i server aggiunti con l'importazione, riunirsi in un gruppo ed eseguire una valutazione per il gruppo.

## <a name="what-data-does-the-appliance-collect"></a>Quali dati raccoglie l'apparecchio?

Se si usa l'appliance Azure Migrate per la valutazione, vedere i metadati e i dati sulle prestazioni raccolti per [VMware](migrate-appliance.md#collected-data---vmware) e [Hyper-V.](migrate-appliance.md#collected-data---hyper-v)

## <a name="how-does-the-appliance-calculate-performance-data"></a>In che modo l'appliance calcola i dati sulle prestazioni?

Se si utilizza l'appliance per l'individuazione, i dati sulle prestazioni per le impostazioni di calcolo vengono raccolti come segue:

1. L'apparecchio raccoglie un punto campione in tempo reale:

    - **Macchine virtuali VMware:** l'appliance raccoglie un punto campione in tempo reale a ogni intervallo di 20 secondi.
    - **Macchine virtuali Hyper-V:** il punto di campionamento in tempo reale viene raccolto a ogni intervallo di 30 secondi.
    - **Server fisici**: il punto campione in tempo reale viene raccolto ad ogni intervallo di cinque minuti. 
    
2. L'appliance esegue il rollup dei punti campione (20 secondi, 30 secondi, cinque minuti) per creare un singolo punto dati ogni 10 minuti. To create the single point, the appliance selects the peak value from all the samples, and then sends it to Azure.
3. Server Assessment archivia tutti i punti campione di 10 minuti dell'ultimo mese.
4. Quando si crea una valutazione, Server Assessment identifica il punto dati appropriato da utilizzare per il ridimensionamento corretto, in base ai valori percentili per *Cronologia prestazioni* e *Utilizzo Percentile*.

    - Ad esempio, se la cronologia delle prestazioni è impostata su una settimana e l'utilizzo percentile è il 95esimo percentile, Server Assessment ordina i punti campione di 10 minuti per l'ultima settimana in ordine crescente e seleziona il valore del 95esimo percentile per il ridimensionamento corretto. 
    - Il valore del 95esimo percentile assicura che si ignorino tutti gli outlier, che potrebbero essere inclusi se si sceglie il 99esimo percentile.
    - Se si desidera scegliere l'utilizzo di picco per il periodo e non si desidera perdere alcun outlier, è necessario selezionare il 99esimo percentile per l'utilizzo percentile.

5. Questo valore viene moltiplicato per il fattore di comfort per ottenere i dati di utilizzo delle prestazioni effettivi per ogni metrica (utilizzo della CPU, utilizzo della memoria, Operazioni di I/O al secondo del disco (lettura e scrittura), velocità effettiva del disco (lettura e scrittura) e velocità effettiva della rete (in essenza) dell'apparecchio raccoglie.



## <a name="how-are-assessments-calculated"></a>Come vengono calcolate le valutazioni? 

Le valutazioni nella valutazione del server vengono calcolate utilizzando i dati di metadati/prestazioni per i computer locali. Se si distribuisce l'appliance di Azure Migrate, la valutazione usando i dati raccolti dall'appliance. Se si esegue una valutazione per le macchine importate utilizzando un file . CSV, si forniscono i metadati per il calcolo. I calcoli avvengono in tre fasi:

1. **Calcolare la conformità**di Azure: valutare se i computer sono adatti per la migrazione ad Azure.Calculate Azure readiness : Assess whether machines are suitable for migration to Azure.
2. **Calcolare le raccomandazioni**di dimensionamento : Stimare il calcolo, l'archiviazione e il dimensionamento della rete. 
2. **Calcolare i costi mensili:** calcolare i costi di calcolo e archiviazione mensili stimati per l'esecuzione delle macchine in Azure dopo la migrazione.

I calcoli sono in ordine e un server macchina si sposta in una fase successiva solo se supera quella precedente. Ad esempio, se un server non supera la preparazione di Azure, viene contrassegnato come non adatto per Azure e il ridimensionamento e la determinazione dei costi non vengono eseguiti per tale server.


## <a name="whats-in-an-assessment"></a>Elementi inclusi nella valutazione

Ecco cosa include in una valutazione in Server Assessment.

**Proprietà** | **Dettagli**
--- | ---
**Posizione di destinazione** | Percorso in cui si desidera eseguire la migrazione. La valutazione del server supporta attualmente queste aree di Azure di destinazione:Server Assessment currently supports these target Azure regions:<br/><br/> Australia Est, Australia Sud-Est, Brasile Sud, Canada Centrale, Canada Est, India centrale, Stati Uniti centrali, Cina orientale, Cina nord, Asia orientale, Stati Uniti orientali, Stati Uniti orientali2, Germania centrale, Germania nord-est, Giappone est, Giappone occidentale, Corea centrale, Corea del Sud, Nord Stati Uniti centrali, Nord Europa, Stati Uniti centro-meridionali, Sud-est asiatico, India meridionale, Regno Unito meridionale, Regno Unito occidentale, US Gov Arizona, US Gov Texas, US Gov Virginia, Stati Uniti centro-occidentali, Europa occidentale, India occidentale, Stati Uniti occidentali e Stati Uniti occidentali2.
*Disco di archiviazione di destinazione (come è il dimensionamento)** | Tipo di dischi da usare per l'archiviazione in Azure.The type of disks to use for storage in Azure. <br/><br/> Specificare il disco di archiviazione di destinazione come gestito da un servizio SSD premium, gestito da SSD standard o su HDD standard.
**Disco di archiviazione di destinazione (dimensionamento basato sulle prestazioni)** | Specificare il tipo di disco di archiviazione di destinazione come automatico, gestito premium, gestito hdD standard o SSD standard gestito.<br/><br/> **Automatico:** il suggerimento del disco si basa sui dati sulle prestazioni dei dischi (operazioni di input/output al secondo (IOPS) e velocità effettiva).<br/><br/>**Premium/standard**: La valutazione consiglia uno SKU del disco all'interno del tipo di archiviazione selezionato.<br/><br/> Se si vuole ottenere un'istanza singola del servizio di sla smalto per VM del 99,9%, considerando l'utilizzo di dischi gestiti Premium.If you want to achieve a single instance VM SLA of 99.9%, considering using premium managed disks. Ciò garantisce che tutti i dischi nella valutazione siano consigliati come dischi gestiti dai servizi Premium.This ensures that all disks in the assessment are recommended as premium-managed disks.<br/><br/> Azure Migrate supporta solo dischi gestiti per la valutazione della migrazione.
**Istanze riservate (RI)** | Specificare [Le istanze riservate](https://azure.microsoft.com/pricing/reserved-vm-instances/) in Azure, in modo che le stime dei costi nella valutazione prendano in considerazione gli sconti RI.<br/><br/> Le irdove sono attualmente supportate solo per le offerte con pagamento in base al numero di informazioni in Azure Migrate.RIs are currently supported only for Pay-As-You-Go offers in Azure Migrate.
**Criteri di dimensionamento** | Utilizzato per ridimensionare correttamente la macchina virtuale in Azure.Used to right-size the VM in Azure.<br/><br/> Utilizzare il ridimensionamento così com'è o il ridimensionamento basato sulle prestazioni.
**Cronologia delle prestazioni** | Utilizzato con ridimensionamento basato sulle prestazioni. Specificare la durata utilizzata durante la valutazione dei dati sulle prestazioni.
**Utilizzo percentile** | Utilizzato con ridimensionamento basato sulle prestazioni. Specifica il valore percentile dell'esempio di prestazioni da utilizzare per il ridimensionamento a destra. 
**Serie macchina virtuale** | Specificare la serie di macchine virtuali di Azure che si vuole prendere in considerazione per il ridimensionamento corretto. Ad esempio, se non si dispone di un ambiente di produzione che richiede macchine virtuali serie A in Azure, è possibile escludere A-series dall'elenco o dalla serie.
**Fattore di comfort** | Buffer utilizzato durante la valutazione. Applicato in aggiunta ai dati di utilizzo del computer per le macchine virtuali (CPU, memoria, disco e rete). Tiene conto di problemi quali l'utilizzo stagionale, la breve cronologia delle prestazioni e i probabili aumenti nell'utilizzo futuro.<br/><br/> Ad esempio, una macchina virtuale a 10 core con un utilizzo del 20% in genere genera una macchina virtuale a due core. Con un fattore di comfort di 2,0x, il risultato è invece una macchina virtuale a quattro core.
**Offerta** | Visualizza [l'offerta di Azure](https://azure.microsoft.com/support/legal/offer-details/) in cui si è iscritti. Server Assessment stima il costo di conseguenza.
**Valuta** | Valuta di fatturazione per il tuo account.
**Sconto (%)** | Elenca gli sconti specifici della sottoscrizione ricevuti in cima all'offerta di Azure. L'impostazione predefinita è 0%.
**Tempo di attività macchina virtuale** | Se le macchine virtuali di Azure non vengono eseguite 24 ore al giorno, 7 giorni alla settimana, è possibile specificare la durata (giorni al mese e ore al giorno) che verranno eseguite. Le stime dei costi vengono gestite di conseguenza.<br/><br/> Il valore predefinito è di 31 giorni al mese e di 24 ore al giorno.
**Vantaggio Azure Hybrid** | Specifica se si dispone della garanzia del software e si sono idonei per il [vantaggio Azure Hybrid.](https://azure.microsoft.com/pricing/hybrid-use-benefit/) Se impostato su Sì (impostazione predefinita), i prezzi non di Windows Azure vengono considerati per le macchine virtuali Windows.If set to Yes (default setting), non-Windows Azure prices are considered for Windows VMs.

[Esaminare le procedure consigliate](best-practices-assessment.md) per la creazione di valutazione con la valutazione del server.


## <a name="calculate-readiness"></a>Calcolare la prontezza

Non tutti i computer sono adatti per l'esecuzione in Azure.Not all machines are suitable to run in Azure. Server Assessment valuta ogni computer locale e gli assegna una categoria di conformità. 
- **Pronto per Azure:** è possibile eseguire la migrazione del computer così com'è in Azure senza alcuna modifica. Verrà avviato in Azure con il supporto completo di Azure.It will start in Azure with full Azure support.
- **Condizionalmente pronto per Azure:** il computer potrebbe avviarsi in Azure, ma potrebbe non avere il supporto completo di Azure.Conditionally ready for Azure : The machine might start in Azure, but might not have full Azure support. Ad esempio, un computer che esegue una versione precedente di Windows Server non è supportato in Azure.For example, a machine that's running an older version of Windows Server isn't supported in Azure. You must be careful before you migrate these machines to Azure. Seguire le indicazioni di correzione suggerite nella valutazione per risolvere i problemi di conformità.
- **Non pronto per Azure:** il computer non verrà avviato in Azure.Not ready for Azure: The machine won't start in Azure. Ad esempio, se un disco del computer locale è superiore a 64 TB, non può essere ospitato in Azure.For example, if an on-premises machine disk is more than 64-TBs, it can't be hosted in Azure. Seguire le indicazioni per la correzione per risolvere il problema prima della migrazione. 
- **Readiness sconosciuto:** Azure Migrate non è in grado di determinare la disponibilità di un computer, a causa di metadati insufficienti.

Per calcolare la preparazione, Server Assessment esamina le proprietà del computer e le impostazioni del sistema operativo riepilogate nelle tabelle seguenti. 

### <a name="machine-properties"></a>Proprietà del computer

Server Assessment reviews the following properties of the on-premises VM to determine whether it can run on Azure.

**Proprietà** | **Dettagli** | **Stato di idoneità per Azure**
--- | --- | ---
**Tipo di avvio** | Azure supporta le macchine virtuali con un tipo di avvio di BIOS, non UEFI. | Pronto in modo condizionale se il tipo di avvio è UEFI.
**Core** | The number of cores in the machines must be equal to or less than the maximum number of cores (128) supported for an Azure VM.<br/><br/> Se è disponibile la cronologia delle prestazioni, Azure Migrate prende in considerazione per il confronto i core utilizzati. Se nelle impostazioni di valutazione è specificato un fattore di comfort, il numero di core utilizzati viene moltiplicato per questo fattore.<br/><br/> Se non è presente alcuna cronologia delle prestazioni, Azure Migrate usa i core allocati senza applicare il fattore di comfort. | Idoneo se è minore o uguale ai limiti.
**Memoria** | La dimensione della memoria del computer deve essere uguale o inferiore alla memoria massima&nbsp;(3892 gigabyte [GB] nella serie di Azure M Standard_M128m 2 ) consentita per una macchina virtuale di Azure.The machine memory size must be equal to or less than the maximum memory (3892 gigabyte [GB] on Azure M series Standard_M128m<sup>2</sup>) allowed for an Azure VM. [Scopri di più](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).<br/><br/> Se è disponibile la cronologia delle prestazioni, Azure Migrate prende in considerazione per il confronto la memoria utilizzata. Se è specificato un fattore di comfort, la memoria utilizzata viene moltiplicata per questo fattore.<br/><br/> Se non c'è storia, la memoria allocata viene utilizzata senza applicare il fattore di comfort.<br/><br/> | Idoneo se rientra nei limiti.
**Disco di archiviazione** | Le dimensioni allocate di un disco devono essere pari o inferiori a 32 TB. Anche se Azure supporta dischi da 64 TB con dischi UltraSD, Azure Migrate: Server Assessment attualmente controlla 32 TB come limiti di dimensione del disco in quanto non supporta ancora Ultra SSD. <br/><br/> Il numero di dischi collegati alla macchina deve essere pari o inferiore a 65, incluso il disco del sistema operativo. | Idoneo se rientra nei limiti.
**Rete** | A un computer devono essere collegate al numero di schede di rete (NIC) pari o meno a un computer. | Idoneo se rientra nei limiti.

### <a name="guest-operating-system"></a>Sistema operativo guest
Insieme alle proprietà della macchina virtuale, Server Assessment esamina il sistema operativo guest dei computer per determinare se può essere eseguito in Azure.Along with VM properties, Server Assessment looks at the guest operating system of the machines to determine whether it can run on Azure.

> [!NOTE]
> Per le macchine virtuali VMware, Server Assessment utilizza il sistema operativo specificato per la macchina virtuale in vCenter Server per gestire l'analisi del sistema operativo guest. Per le macchine virtuali Linux in esecuzione su VMware, attualmente non identifica la versione esatta del kernel del sistema operativo guest.

La logica seguente viene usata da Server Assessment per identificare la disponibilità di Azure in base al sistema operativo.

**Sistema operativo** | **Dettagli** | **Stato di idoneità per Azure**
--- | --- | ---
Windows Server 2016 e tutti i Service Pack | Azure offre supporto completo. | Idoneo per Azure
Windows Server 2012 R2 e tutti i Service Pack | Azure offre supporto completo. | Idoneo per Azure
Windows Server 2012 e tutti i Service Pack | Azure offre supporto completo. | Idoneo per Azure
Windows Server 2008 R2 con tutti i Service Pack | Azure offre supporto completo.| Idoneo per Azure
Windows Server 2008 (32 bit e 64 bit) | Azure offre supporto completo. | Idoneo per Azure
Windows Server 2003, 2003 R2 | Questi sistemi operativi hanno superato la data di fine del supporto e necessitano di un contratto di supporto personalizzato (CSA) per il supporto in Azure.These operating systems have passed their end-of-support date and need a [Custom Support Agreement (CSA)](https://aka.ms/WSosstatement) for support in Azure. | Condizionalmente pronto per Azure.Conditionally ready for Azure. Valutare la possibilità di aggiornare il sistema operativo prima di eseguire la migrazione ad Azure.Consider upgrading the OS before migrating to Azure.
Windows 2000, 98, 95, NT, 3.1, MS-DOS | Questi sistemi operativi hanno superato la data di fine del supporto. Il computer potrebbe avviarsi in Azure, ma Azure non fornisce alcun supporto del sistema operativo. | Condizionalmente pronto per Azure.Conditionally ready for Azure. È consigliabile aggiornare il sistema operativo prima di eseguire la migrazione ad Azure.We recommend that you upgrade the OS before migrating to Azure.
Client Windows 7, 8 e 10 | Azure fornisce supporto solo con la sottoscrizione di Visual Studio.Azure provides support with [Visual Studio subscription only.](https://docs.microsoft.com/azure/virtual-machines/windows/client-images) | Idoneo per Azure con condizioni
Windows 10 Pro Desktop | Azure fornisce supporto con [i diritti di hosting multi-tenant.](https://docs.microsoft.com/azure/virtual-machines/windows/windows-desktop-multitenant-hosting-deployment) | Idoneo per Azure con condizioni
Windows Vista, XP Professional | Questi sistemi operativi hanno superato la data di fine del supporto. Il computer potrebbe avviarsi in Azure, ma Azure non fornisce alcun supporto del sistema operativo. | Condizionalmente pronto per Azure.Conditionally ready for Azure. È consigliabile aggiornare il sistema operativo prima di eseguire la migrazione ad Azure.We recommend that you upgrade the OS before migrating to Azure.
Linux | Azure approva questi [sistemi operativi Linux](../virtual-machines/linux/endorsed-distros.md). Altri sistemi operativi Linux potrebbero iniziare in Azure, ma è consigliabile aggiornare il sistema operativo a una versione approvata prima di eseguire la migrazione ad Azure.Other Linux operating systems might start in Azure, but we recommend that you upgrade the OS to an endorsed version before migrating to Azure. | Idoneo per Azure se la versione è approvata.<br/><br/>Idoneo per Azure con condizioni se la versione non è approvata.
Altri sistemi operativi<br/><br/> Ad esempio, Oracle Solaris, Apple macOS ecc., FreeBSD, ecc. | Azure non approva questi sistemi operativi. Il computer potrebbe avviarsi in Azure, ma Azure non fornisce alcun supporto del sistema operativo. | Condizionalmente pronto per Azure.Conditionally ready for Azure. È consigliabile installare un sistema operativo supportato prima di eseguire la migrazione ad Azure.We recommend that you install a supported OS before migrating to Azure.  
Sistema operativo specificato come **Altro** nel server vCenter | In questo caso Azure Migrate non può identificare il sistema operativo. | Idoneità sconosciuta. Verificare che il sistema operativo eseguito nella VM sia supportato in Azure.
Sistemi operativi a 32 bit | Il computer potrebbe avviarsi in Azure, ma Azure potrebbe non fornire supporto completo. | Condizionalmente pronto per Azure.Conditionally ready for Azure. Valutare la possibilità di aggiornare il sistema operativo della macchina dal sistema operativo a 32 bit al sistema operativo a 64 bit prima di eseguire la migrazione ad Azure.Consider upgrading the OS of the machine from 32-bit OS to 64-bit OS before migrating to Azure.

## <a name="calculating-sizing"></a>Calcolo del dimensionamento


Dopo che la macchina è stata contrassegnata come pronta per Azure, la valutazione del server fornisce consigli sul dimensionamento per identificare la macchina virtuale di Azure e lo SKU del disco. I calcoli di dimensionamento dipendono dal fatto che si utilizzi il dimensionamento locale o il ridimensionamento basato sulle prestazioni.

### <a name="calculate-sizing-as-is-on-premises"></a>Calcolare il ridimensionamento (così come è locale)

 Se si usa il dimensionamento locale così come lo si utilizza, La valutazione del server non considera la cronologia delle prestazioni delle macchine virtuali e dei dischi.

- **Dimensioni di calcolo:** alloca uno SKU di una macchina virtuale di Azure in base alle dimensioni allocate in locale.
- **Dimensioni disco/archiviazione**: Valutazione server esamina il tipo di archiviazione specificato nelle proprietà di valutazione (HDD/SSD/premium standard) e consiglia il tipo di disco di conseguenza. Il tipo di archiviazione predefinito è dischi Premium.The default storage type is premium disks.
- **Dimensionamento di rete:** Valutazione server considera la scheda di rete nel computer locale.


### <a name="calculate-sizing-performance-based"></a>Calcolare il dimensionamento (basato sulle prestazioni)

Se si utilizza il dimensionamento basato sulle prestazioni, Server Assessment che presenta i suggerimenti per il dimensionamento nel modo seguente:

- Server Assessment considers the performance history of the machine to identify the VM size and disk type in Azure.
- Se i server sono stati importati utilizzando un file CSV, vengono utilizzati i valori specificati. Questo metodo è particolarmente utile se è stato riassegnato eccessivamente il computer locale, l'utilizzo è effettivamente basso e si vuole ridimensionare correttamente la macchina virtuale in Azure per risparmiare sui costi. 
- Se non si desidera utilizzare i dati sulle prestazioni, reimpostare i criteri di ridimensionamento in modo che sia locale, come descritto nella sezione precedente.

#### <a name="calculate-storage-sizing"></a>Calcolare il dimensionamento dello storage

Per il dimensionamento dell'archiviazione, Azure Migrate tenta di eseguire il mapping di ogni disco collegato al computer a un disco in Azure e funziona come segue:For storage sizing, Azure Migrate tries to map every disk attached to the machine to a disk in Azure, and works as follows:

1. Server Assessment aggiunge le operazioni di I/O al secondo di lettura e scrittura di un disco per ottenere le operazioni di I/O al secondo totali necessarie. Analogamente, aggiunge i valori di velocità effettiva di lettura e scrittura per ottenere la velocità effettiva totale di ogni disco.
2. Se è stato specificato il tipo di archiviazione come Automatico, in base ai valori di IOPS e velocità effettiva effettivi, la valutazione del server determina se il disco deve essere mappato a un disco rigido standard, un SSD standard o un disco Premium in Azure.If you've specified storage type as Automatic, based on the effective IOPS and throughput values, Server Assessment determines whether the disk should be mapped to a standard HDD, standard SSD, or a premium disk in Azure. Se il tipo di archiviazione è impostato su Standard HDD/SSD/Premium, Server Assessment tenta di trovare uno SKU del disco all'interno del tipo di archiviazione selezionato (dischi Standard HDD/SSD/Premium).
3. I dischi vengono selezionati come segue:
    - Se la valutazione del server non riesce a trovare un disco con le operazioni di I/O al secondo e la velocità effettiva richieste, contrassegna il computer come non adatto ad Azure.If Server Assessment can't find a disk with the required IOPS and throughput, it marks the machine as unsuitable for Azure.
    - Se Server Assessment trova un set di dischi adatti, seleziona i dischi che supportano il percorso specificato nelle impostazioni di valutazione.
    - Se sono presenti più dischi idonei, Server Assessment seleziona il disco con il costo più basso.
    - Se i dati sulle prestazioni per qualsiasi disco non sono disponibili, i dati di configurazione del disco (dimensioni del disco) vengono usati per trovare un disco SSD standard in Azure.If performance data for any disk is unavailable, the configuration data of the disk (disk size) is used to find a standard SSD disk in Azure.

#### <a name="calculate-network-sizing"></a>Calcolare il dimensionamento della rete

Server Assessment tenta di trovare una macchina virtuale di Azure in grado di supportare il numero di schede di rete collegate al computer locale e le prestazioni richieste da queste schede di rete.
- Per ottenere le prestazioni di rete effettive della macchina virtuale locale, Server Assessment aggrega i dati trasmessi al secondo (MBps) dal computer (uscita dalla rete) a tutte le schede di rete e applica il fattore di comfort. Usa questo numero per trovare una macchina virtuale di Azure in grado di supportare le prestazioni di rete necessarie.
- Oltre alle prestazioni di rete, Server Assessment considera anche se la macchina virtuale di Azure può supportare il numero richiesto di schede di rete.
- Se non sono disponibili dati sulle prestazioni di rete, Server Assessment considera solo il numero di schede di rete per il dimensionamento delle macchine virtuali.


#### <a name="calculate-compute-sizing"></a>Calcolare il dimensionamento del calcolo

Dopo aver calcolato i requisiti di archiviazione e di rete, Server Assessment considera i requisiti di CPU e memoria per trovare una dimensione di macchina virtuale adatta in Azure.After it calculates storage and network requirements, Server Assessment considers CPU and memory requirements to find a suitable VM size in Azure.
- Azure Migrate esamina i core e la memoria utilizzati effettivamente per trovare una dimensione della macchina virtuale adatta in Azure.Azure Migrate looks at the effective used used the effective used used the effective used using cores and memory to find a suitable VM size in Azure.
- Se non si trovano dimensioni adatte, il computer viene contrassegnato come non idoneo per Azure.
- Se si trovano dimensioni adatte, Azure Migrate esegue i calcoli di archiviazione e di rete. Applica quindi le impostazioni di posizione e piano tariffario per la raccomandazione finale per le dimensioni della macchina virtuale.
- Se ci sono più dimensioni di VM di Azure idonee, si consiglia di usare quella con il costo più basso.


## <a name="confidence-ratings-performance-based"></a>Classificazioni di attendibilità (basate sulle prestazioni)

Ogni valutazione basata sulle prestazioni in Azure Migrate è associata a una classificazione di attendibilità compresa tra una (più bassa) e cinque stelle (più alta). The confidence rating helps you estimate the reliability of the size recommendations provided by Azure Migrate.

- La classificazione di attendibilità viene assegnata a una valutazione in base alla disponibilità dei punti dati necessari per calcolare la valutazione.
- Per il dimensionamento basato sulle prestazioni, la valutazione del server deve:For performance-based sizing, Server Assessment needs:
    - Dati di utilizzo per la memoria della CPU e della macchina virtuale.
    - Le operazioni di I/O al secondo del disco e i dati di velocità effettiva per ogni disco collegato alla macchina virtuale.
    - I/O di rete per gestire il ridimensionamento basato sulle prestazioni per ogni scheda di rete collegata a una macchina virtuale.
    - Se uno di questi numeri di utilizzo non è disponibile, i consigli sulle dimensioni potrebbero non essere affidabili.

> [!NOTE]
> Le classificazioni di attendibilità non vengono assegnate ai server valutati utilizzando un file . CSV. Anche le valutazioni non sono applicabili per la valutazione locale così com'è.
   
### <a name="ratings"></a>Classificazioni

A seconda della percentuale di punti dati disponibili, il tasso di confidenza per la valutazione è il seguente.

   **Disponibilità dei punti dati** | **Classificazione di attendibilità**
   --- | ---
   0-20% | 1 stella
   21-40% | 2 stelle
   41-60% | 3 stelle
   61-80% | 4 stelle
   81-100% | 5 stelle

### <a name="low-confidence-ratings"></a>Valutazioni di fiducia basse

Ecco alcuni motivi per cui una valutazione potrebbe ottenere un punteggio di confidenza basso:

- L'ambiente non è stato profilato per la durata per cui si sta creando la valutazione. Ad esempio, se si crea la valutazione con la durata delle prestazioni impostata su un giorno, è necessario attendere almeno un giorno dopo aver avviato l'individuazione di tutti i punti dati da raccogliere.
- Durante il periodo di calcolo della valutazione sono state arrestate alcune macchine virtuali. Se le macchine virtuali vengono disattivate per un certo periodo di tempo, la valutazione del server non è in grado di raccogliere i dati sulle prestazioni per tale periodo.
- Alcune macchine virtuali sono state create durante il periodo per il quale è stata calcolata la valutazione. Ad esempio, se è stata creata una valutazione per la cronologia delle prestazioni dell'ultimo mese, ma alcune macchine virtuali sono state create nell'ambiente solo una settimana fa, la cronologia delle prestazioni delle nuove macchine virtuali non esisterà per la durata completa.

> [!NOTE]
> Se la classificazione di attendibilità di una valutazione è inferiore a cinque stelle, è consigliabile attendere almeno un giorno affinché l'appliance presenti l'ambiente e quindi ricalcolare la valutazione. In caso contrario, il dimensionamento basato sulle prestazioni potrebbe non essere affidabile. In tal caso, è consigliabile passare alla valutazione locale.

## <a name="calculate-monthly-costs"></a>Calcolare i costi mensili

Al termine delle raccomandazioni per il dimensionamento, Azure Migrate calcola i costi di calcolo e archiviazione per dopo la migrazione.

- **Costo di calcolo**: sulla base delle dimensioni consigliate della macchina virtuale di Azure, Azure Migrate usa l'API di fatturazione per calcolare i costi mensili della macchina virtuale.
    - Per il calcolo vengono prese in considerazione le impostazioni relative al sistema operativo, al programma Software Assurance, alle istanze riservate, al tempo di attività della macchina virtuale, alla posizione e alla valuta.
    - Aggrega il costo in tutte le macchine per calcolare il costo di calcolo mensile totale.
- **Costo di archiviazione**: Il costo di archiviazione mensile per una macchina viene calcolato aggregando il costo mensile di tutti i dischi collegati alla macchina, come indicato di seguito:
    - Server Assessment calcola i costi totali di archiviazione mensili aggregando i costi di archiviazione di tutti i computer.
    - Attualmente, il calcolo non considera le offerte specificate nelle impostazioni di valutazione.

I costi vengono visualizzati nella valuta specificata nelle impostazioni di valutazione.


## <a name="next-steps"></a>Passaggi successivi

[Esaminare le](best-practices-assessment.md) procedure consigliate per la creazione di valutazioni. 


- Informazioni sull'esecuzione di valutazioni per [macchine virtuali VMware,](tutorial-prepare-vmware.md) [macchine virtuali Hyper-V](tutorial-prepare-hyper-v.md)e [server fisici.](tutorial-prepare-physical.md)
- Informazioni sulla valutazione dei server [importati con un file CSV.](tutorial-assess-import.md)
- Informazioni sull'impostazione della visualizzazione delle [dipendenze](concepts-dependency-visualization.md).